# Arquitetura Front-End – Vue.js

Este documento descreve as boas práticas recomendadas para projetos Vue.js neste repositório. O objetivo é garantir consistência, escalabilidade e manutenibilidade do código.

---

## 1. Estrutura de Pastas

```
src/
 ├── assets/          # Imagens, fontes, estilos globais
 ├── components/      # Componentes reutilizáveis (Base*)
 ├── views/           # Páginas de alto nível mapeadas nas rotas
 ├── modules/         # Domínios funcionais com componentes + store + services
 ├── router/          # Configuração das rotas
 ├── store/           # (Pinia) Stores globais ou compartilhados
 ├── composables/     # Funções reutilizáveis (Composition API)
 ├── services/        # Chamadas HTTP / SDKs externos
 └── utils/           # Helpers genéricos
```

*Princípio*: **Feature-first** – agrupe arquivos por domínio de negócio sempre que possível.

---

## 2. Composition API

- Prefira **Composition API** a Options API para melhor reutilização de código.
- Crie composables específicos em `src/composables/` para lógica reutilizável.
- Use Tipos do TypeScript para todas as props, emits e retornos.

```ts
// composables/usePagination.ts
export function usePagination(initialPage = 1) {
  const page = ref(initialPage)
  function next() { page.value++ }
  function prev() { page.value = Math.max(1, page.value - 1) }
  return { page, next, prev }
}
```

---

## 3. Componentes

1. **Single Responsibility** – um componente = uma responsabilidade.
2. Prefixe componentes reutilizáveis com `Base`: `BaseButton.vue`, `BaseModal.vue`.
3. Use **PascalCase** nos nomes de arquivos de componentes (`UserCard.vue`).
4. Organize testes unitários ao lado do componente (`UserCard.spec.ts`).

---

## 4. State Management (Pinia)

- Adote **Pinia** para stores.
- Separe stores por domínio e evite stores monolíticas.
- Utilize `defineStore` com tipos explícitos.
- Persistência: utilize plugins como `pinia-plugin-persistedstate` apenas onde necessário.

---

## 5. Comunicação HTTP

- Centralize chamadas em `src/services/`.
- Utilize **Axios** configurado com interceptors (token, logging de erro).
- Nunca faça chamadas HTTP diretamente em componentes.

```ts
// services/userService.ts
import http from '@/services/http'
export const fetchUsers = () => http.get<User[]>('/users')
```

---

## 6. Rotas

- Mantenha definição de rotas em `router/index.ts`.
- Utilize **lazy loading** (`() => import('...')`) para dividir código por view.
- Proteja rotas com **navigation guards** para autenticação/autorização.

---

## 7. Estilo de Código

- **ESLint + Prettier** obrigatórios (config padronizada no projeto).
- Use **kebab-case** nos atributos de template; **camelCase** no script.
- Evite lógica complexa no template; mova para computed ou métodos.

---

## 8. Testes

- **Unitários**: Vitest ou Jest + @vue/test-utils.
- **E2E**: Cypress ou Playwright.
- Cobertura mínima recomendada: **80%**.

---

## 9. Performance

- Divisão automática de código via `vite`/`webpack`.
- Utilize `v-once`, `defineAsyncComponent`, `Suspense` onde fizer sentido.
- Evite watchers profundos, prefira computed.

---

## 10. Acessibilidade (a11y)

- Sempre use atributos ARIA adequados.
- Mantenha contraste de cor mínimo WCAG AA.
- Teste de acessibilidade automatizado (axe-core + Cypress plugin).

---

## 11. Segurança

- Escape de HTML (Vue já faz por padrão).
- Validação de entrada no frontend não substitui validação no backend.
- Armazene tokens apenas em **HTTP-only cookies** quando possível.

---

## 12. Deploy & CI

- Build único via `npm run build` (Vite).
- Pipelines CI: lint → test → build → preview.
- Geração de artefatos estáticos para CDN/SPA hosting.

---

## 13. Convenções de Commits (opcional)

- Adote **Conventional Commits** (`feat:`, `fix:`, etc.) para gerar changelogs automáticos.

---

> Seguindo estas práticas, garantimos um código limpo, testável e escalável em Vue.js.
