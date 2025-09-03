# MVP - Dashboard Classificação Planejada

## Visão Geral

O MVP (Minimum Viable Product) do Sistema Classificação Planejada consiste em um dashboard simples que exibe o progresso de um time em relação às metas planejadas, sem funcionalidades de autenticação ou cadastro.

## Objetivo

Criar uma versão funcional mínima para validar o conceito com dados pré-inseridos no banco, focando na visualização do percentual de cumprimento das metas por rodada.

## Escopo do MVP

### O que ESTÁ incluído:
- Dashboard com visualização de dados
- Cálculo do percentual de cumprimento
- Dados pré-inseridos no banco
- Interface responsiva básica

### O que NÃO está incluído:
- Sistema de autenticação
- Cadastro de usuários
- CRUD de metas (dados inseridos direto no banco)
- Múltiplos times
- Histórico de temporadas

## Funcionalidade Principal

**Dashboard de Acompanhamento** que exibe:
- Tabela com rodadas, pontos planejados e pontos conquistados
- Percentual de cumprimento acumulado
- Gráfico simples de progresso
- Indicadores visuais de performance

## Estrutura de Dados

```sql
-- Tabela principal com dados por rodada
Rodadas:
- id (int, primary key)
- numero_rodada (int)
- pontos_planejados (int)
- pontos_conquistados (int, nullable)
- data_jogo (datetime, nullable)
- adversario (varchar(100), nullable)
```

## Tecnologias

- **Frontend**: Vue.js 3 + Composition API
- **Backend**: ASP.NET Core Web API
- **Banco**: SQL Server ou SQLite
- **Deploy**: Docker containers

## Entregáveis

1. **Frontend**: SPA Vue.js com dashboard responsivo
2. **Backend**: API REST com endpoint para dados do dashboard
3. **Database**: Script de criação + dados de exemplo
4. **Docker**: Containers para desenvolvimento e produção

---

Este MVP permitirá validar rapidamente o conceito e obter feedback dos usuários antes de implementar funcionalidades mais complexas.