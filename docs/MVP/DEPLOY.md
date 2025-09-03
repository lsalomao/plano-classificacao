# MVP - Deploy e Configuração

## Docker Compose para Desenvolvimento

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile.dev
    ports:
      - "3000:3000"
    volumes:
      - ./frontend:/app
      - /app/node_modules
    environment:
      - VUE_APP_API_URL=http://localhost:5000/api
    depends_on:
      - backend

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile.dev
    ports:
      - "5000:80"
    volumes:
      - ./backend:/app
      - ./data:/app/data
    environment:
      - ASPNETCORE_ENVIRONMENT=Development
      - ConnectionStrings__DefaultConnection=Data Source=/app/data/database.db
    depends_on:
      - database

  database:
    image: alpine:latest
    volumes:
      - ./data:/data
    command: ["sh", "-c", "touch /data/database.db && tail -f /dev/null"]
```

## Docker Compose para Produção

```yaml
version: '3.8'

services:
  frontend:
    build:
      context: ./frontend
      dockerfile: Dockerfile
    ports:
      - "80:80"
    environment:
      - VUE_APP_API_URL=http://localhost:5000/api
    depends_on:
      - backend

  backend:
    build:
      context: ./backend
      dockerfile: Dockerfile
    ports:
      - "5000:80"
    volumes:
      - ./data:/app/data
    environment:
      - ASPNETCORE_ENVIRONMENT=Production
      - ConnectionStrings__DefaultConnection=Data Source=/app/data/database.db
    restart: unless-stopped

volumes:
  data:
```

## Scripts de Deploy

### deploy.sh
```bash
#!/bin/bash

echo "🚀 Iniciando deploy do MVP..."

# Parar containers existentes
docker-compose down

# Limpar imagens antigas
docker system prune -f

# Build e start dos containers
docker-compose up --build -d

# Aguardar containers ficarem prontos
echo "⏳ Aguardando containers..."
sleep 10

# Verificar se estão rodando
if docker-compose ps | grep -q "Up"; then
    echo "✅ Deploy realizado com sucesso!"
    echo "🌐 Frontend: http://localhost"
    echo "🔧 Backend: http://localhost:5000"
else
    echo "❌ Erro no deploy. Verificando logs..."
    docker-compose logs
fi
```

### health-check.sh
```bash
#!/bin/bash

echo "🔍 Verificando saúde da aplicação..."

# Verificar backend
if curl -f http://localhost:5000/api/dashboard > /dev/null 2>&1; then
    echo "✅ Backend: OK"
else
    echo "❌ Backend: ERRO"
    exit 1
fi

# Verificar frontend
if curl -f http://localhost > /dev/null 2>&1; then
    echo "✅ Frontend: OK"
else
    echo "❌ Frontend: ERRO"
    exit 1
fi

echo "🎉 Aplicação funcionando corretamente!"
```

## Configuração de Ambiente

### .env.development
```bash
# Frontend
VUE_APP_API_URL=http://localhost:5000/api
VUE_APP_ENVIRONMENT=development

# Backend
ASPNETCORE_ENVIRONMENT=Development
ConnectionStrings__DefaultConnection=Data Source=./data/database.db
CORS_ORIGINS=http://localhost:3000,http://localhost:5173
```

### .env.production
```bash
# Frontend
VUE_APP_API_URL=https://api.classificacaoplanejada.com/api
VUE_APP_ENVIRONMENT=production

# Backend
ASPNETCORE_ENVIRONMENT=Production
ConnectionStrings__DefaultConnection=Data Source=/app/data/database.db
CORS_ORIGINS=https://classificacaoplanejada.com
```

## Nginx (Produção)

### nginx.conf
```nginx
server {
    listen 80;
    server_name classificacaoplanejada.com;

    # Frontend (SPA)
    location / {
        proxy_pass http://frontend:80;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
    }

    # Backend API
    location /api/ {
        proxy_pass http://backend:80/api/;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }

    # Gzip compression
    gzip on;
    gzip_types text/plain text/css application/json application/javascript text/xml application/xml application/xml+rss text/javascript;
}
```

## CI/CD Pipeline (GitHub Actions)

### .github/workflows/deploy.yml
```yaml
name: Deploy MVP

on:
  push:
    branches: [ main ]

jobs:
  deploy:
    runs-on: ubuntu-latest
    
    steps:
    - uses: actions/checkout@v3
    
    - name: Setup Node.js
      uses: actions/setup-node@v3
      with:
        node-version: '18'
        
    - name: Setup .NET
      uses: actions/setup-dotnet@v3
      with:
        dotnet-version: '7.0'
    
    - name: Test Frontend
      run: |
        cd frontend
        npm ci
        npm run test:unit
        
    - name: Test Backend
      run: |
        cd backend
        dotnet test
    
    - name: Build and Deploy
      run: |
        docker-compose build
        docker-compose up -d
        
    - name: Health Check
      run: |
        sleep 30
        ./scripts/health-check.sh
```

## Monitoramento Básico

### docker-compose.monitoring.yml
```yaml
version: '3.8'

services:
  # Aplicação principal
  app:
    extends:
      file: docker-compose.yml
      service: backend
      
  # Monitoramento simples
  watchtower:
    image: containrrr/watchtower
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    command: --interval 300 --cleanup
    
  # Logs centralizados
  logspout:
    image: gliderlabs/logspout
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - "8000:80"
```

## Comandos Úteis

```bash
# Desenvolvimento
docker-compose -f docker-compose.dev.yml up --build

# Produção
docker-compose up -d --build

# Ver logs
docker-compose logs -f

# Parar tudo
docker-compose down

# Limpar volumes
docker-compose down -v

# Backup do banco
docker cp $(docker-compose ps -q backend):/app/data/database.db ./backup/

# Restore do banco
docker cp ./backup/database.db $(docker-compose ps -q backend):/app/data/
```

## Checklist de Deploy

- [ ] Variáveis de ambiente configuradas
- [ ] Banco de dados inicializado com dados de exemplo
- [ ] Frontend buildado e servindo arquivos estáticos
- [ ] Backend respondendo na rota `/api/dashboard`
- [ ] CORS configurado corretamente
- [ ] Logs sendo coletados
- [ ] Health checks funcionando
- [ ] Backup do banco configurado

## Troubleshooting

### Problemas Comuns

1. **CORS Error**: Verificar `CORS_ORIGINS` no backend
2. **Database not found**: Verificar se o volume está montado corretamente
3. **Frontend não carrega**: Verificar se `VUE_APP_API_URL` está correto
4. **API 500**: Verificar logs do backend com `docker-compose logs backend`

### Logs Importantes

```bash
# Ver logs do backend
docker-compose logs -f backend

# Ver logs do frontend
docker-compose logs -f frontend

# Ver todos os logs
docker-compose logs -f
```