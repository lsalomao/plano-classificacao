# Arquitetura Back-End – .NET (C#)

Este documento consolida as melhores práticas recomendadas para uma API back-end utilizando **ASP.NET Core** com C#. O objetivo é garantir desempenho, segurança, testabilidade e facilidade de manutenção.

---

## 1. Estrutura de Solução

```
src/
 ├── Api/                   # ASP.NET Core Web API (controllers)
 ├── Application/           # Casos de uso, DTOs, validadores
 ├── Domain/                # Entidades de negócio, agregados, interfaces
 ├── Infrastructure/        # Implementações (EF Core, serviços externos)
 ├── Tests/                 # Testes unitários e de integração
 └── Shared/                # Constantes, utilitários, extensões
```

*Princípio*: **Clean Architecture** (camadas independentes, dependência de dentro para fora).

---

## 2. Domain-Driven Design (DDD)

- Entidades com invariantes internas.
- **Value Objects** para tipos imutáveis (ex: `Email`, `Money`).
- Agregados com **root** claro; mantenha consistência dentro do agregado.

---

## 3. CQRS + MediatR

- Separe **Commands** (escrita) de **Queries** (leitura).
- Utilize **MediatR** para desacoplar controladores e handlers.
- Commands devem ser **idempotentes**; Queries **side-effect free**.

---

## 4. Validação e Notificações

- **FluentValidation** para validação de Commands/DTOs.
- Notificações de domínio via eventos (Domain Events) sempre que necessário.

---

## 5. Persistência de Dados

- **Entity Framework Core** como ORM.
- Mapeamento por **Configurations** em vez de Data Annotations.
- Migrações gerenciadas por `dotnet ef migrations`.
- Use **Unit of Work** apenas se for necessário unir múltiplos repositórios.

---

## 6. Padrão Repository

- Interface `IRepository<TEntity>` exposta no **Domain**.
- Implementação concreta em **Infrastructure**.
- Evite expor o **DbContext** fora da camada Infrastructure.

---

## 7. Autenticação e Autorização

- **JWT Bearer** ou **OAuth2** (IdentityServer) para emissão de tokens.
- Use **Policies** para regras de autorização.
- Armazene Secrets com **dotnet user-secrets** ou KeyVault.

---

## 8. Logging e Observabilidade

- **Serilog** como provider de logging estruturado.
- Correlation ID por requisição.
- Use **OpenTelemetry** + Jaeger/Zipkin para tracing distribuído.

---

## 9. Testes

- **Unitários**: xUnit + Moq/FluentAssertions.
- **Integração**: WebApplicationFactory + banco em memória ou TestContainers.
- Cobertura mínima recomendada: **80%**.

---

## 10. Manipulação de Erros

- Middleware global de exceções (`UseExceptionHandler`).
- Retorne **ProblemDetails** (RFC 7807) em erros.
- Nunca exponha stack traces em produção.

---

## 11. Segurança

- HTTPS obrigatório.
- **Rate limiting** (AspNetCoreRateLimit) para endpoints sensíveis.
- Valide e sanitize entradas para evitar injeções.

---

## 12. Performance & Caching

- **Response Caching** e **ETags** onde aplicável.
- Cache de dados com **Redis** para leituras frequentes.
- Use **Async/Await** e **IAsyncEnumerable** para streams.

---

## 13. CI/CD e Deploy

- Pipelines: restore → build → test → publish → docker build/push.
- Contêineres **Docker** multi-stage; use **dotnet publish --no-restore**.
- Deploy em Kubernetes ou App Service com health checks.

---

## 14. Convenções de Código

- **StyleCop** para estilo e regras.
- **Nullable Reference Types** ativado.
- Trate **WARNINGS** como erros (`-warnaserror`).

---

> Seguindo esta arquitetura, o back-end mantém-se modular, escalável e pronto para crescimento futuro.
