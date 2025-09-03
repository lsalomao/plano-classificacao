# MVP Frontend - Vue.js Dashboard

## Estrutura do Projeto

```
src/
 ├── components/
 │   ├── Dashboard/
 │   │   ├── ProgressChart.vue      # Gráfico de progresso
 │   │   ├── RoundTable.vue         # Tabela de rodadas
 │   │   └── StatsCards.vue         # Cards com estatísticas
 │   └── Layout/
 │       ├── Header.vue             # Cabeçalho simples
 │       └── Footer.vue             # Rodapé
 ├── services/
 │   └── dashboardService.js        # Chamadas para API
 ├── views/
 │   └── Dashboard.vue              # Página principal
 └── App.vue                        # Componente raiz
```

## Componentes Principais

### 1. Dashboard.vue (View Principal)
- Container principal que orquestra todos os componentes
- Carrega dados da API no mounted()
- Gerencia estado de loading e erro

### 2. StatsCards.vue
- Cards com métricas principais:
  - Total de pontos conquistados
  - Total de pontos planejados
  - Percentual de cumprimento
  - Rodadas jogadas

### 3. RoundTable.vue
- Tabela responsiva com:
  - Número da rodada
  - Adversário
  - Pontos planejados
  - Pontos conquistados
  - Status (cumprido/não cumprido)

### 4. ProgressChart.vue
- Gráfico de linha simples mostrando:
  - Evolução dos pontos planejados vs conquistados
  - Linha de tendência do percentual

## Tecnologias e Bibliotecas

```json
{
  "dependencies": {
    "vue": "^3.3.0",
    "axios": "^1.5.0",
    "chart.js": "^4.4.0",
    "vue-chartjs": "^5.2.0"
  },
  "devDependencies": {
    "@vitejs/plugin-vue": "^4.4.0",
    "vite": "^4.4.0"
  }
}
```

## Serviços

### dashboardService.js
```javascript
import axios from 'axios'

const API_BASE_URL = process.env.VUE_APP_API_URL || 'http://localhost:5000/api'

export const dashboardService = {
  async getDashboardData() {
    const response = await axios.get(`${API_BASE_URL}/dashboard`)
    return response.data
  }
}
```

## Responsividade

- **Mobile First**: Design otimizado para dispositivos móveis
- **Breakpoints**:
  - Mobile: < 768px
  - Tablet: 768px - 1024px  
  - Desktop: > 1024px

## Cores e Tema

```css
:root {
  --primary-color: #2563eb;      /* Azul principal */
  --success-color: #16a34a;      /* Verde (meta cumprida) */
  --warning-color: #d97706;      /* Laranja (próximo da meta) */
  --danger-color: #dc2626;       /* Vermelho (abaixo da meta) */
  --gray-100: #f3f4f6;
  --gray-800: #1f2937;
}
```

## Estados da Interface

1. **Loading**: Skeleton/spinner durante carregamento
2. **Success**: Dashboard com dados carregados
3. **Error**: Mensagem de erro com botão para tentar novamente
4. **Empty**: Estado quando não há dados

## Build e Deploy

```bash
# Desenvolvimento
npm run dev

# Build para produção
npm run build

# Preview da build
npm run preview
```

## Docker

```dockerfile
FROM node:18-alpine as build
WORKDIR /app
COPY package*.json ./
RUN npm ci
COPY . .
RUN npm run build

FROM nginx:alpine
COPY --from=build /app/dist /usr/share/nginx/html
EXPOSE 80
```