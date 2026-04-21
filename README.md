# Newsletter Template

Template minimalista de landing page para captura de emails de newsletter.
Visual dark com background em vídeo, manifesto expansível e form de inscrição
persistido em Upstash Redis / Vercel KV.

**Preview em produção:** <https://newsletter-madureira.vercel.app>

## Stack

- [Next.js 15](https://nextjs.org/) (App Router) + React 19
- [Tailwind CSS 3](https://tailwindcss.com/) + [shadcn/ui](https://ui.shadcn.com/) (Radix primitives)
- [Framer Motion 12](https://www.framer.com/motion/) — animações de entrada/saída
- [Upstash Redis](https://upstash.com/) via `@upstash/redis` — armazenamento da lista de emails
- [react-hook-form](https://react-hook-form.com/) + [Zod](https://zod.dev/) — validação
- TypeScript 5 + ESLint 9

## Como clonar (fork / deploy próprio)

### 1. Clone e instale

```bash
git clone https://github.com/gmadureiraa/newsletter-template.git my-newsletter
cd my-newsletter
bun install    # ou: pnpm install / npm install
```

### 2. Configure as variáveis de ambiente

```bash
cp .env.example .env.local
```

Edite `.env.local` e preencha as credenciais do Upstash Redis. Veja
[Configurar o Upstash](#configurar-o-upstash-vercel-kv) abaixo.

### 3. Rode localmente

```bash
bun run dev
```

Abra [http://localhost:3000](http://localhost:3000).

### 4. Personalize

| O que alterar                          | Arquivo                                |
| -------------------------------------- | -------------------------------------- |
| Título da marca ("Synecdoche®")        | `app/layout.tsx` + `components/newsletter.tsx` |
| Descrição / OG / SEO                   | `app/layout.tsx`                       |
| Texto do manifesto                     | `components/newsletter.tsx`            |
| Links sociais (Instagram / X / GitHub) | `lib/constants.ts`                     |
| Vídeo de background                    | `app/page.tsx` (prop `src` do `Background`) |
| Placeholder estático                   | `public/alt-placeholder.png`           |
| OpenGraph image                        | `app/opengraph-image.png`              |
| Cores / radius / tema                  | `tailwind.config.js` + `app/globals.css` |

## Configurar o Upstash (Vercel KV)

Duas formas equivalentes — qualquer uma funciona:

**Opção A — via Vercel Marketplace (recomendado)**

1. No dashboard do seu projeto Vercel: aba **Storage** → **Create Database** → **Upstash Redis**.
2. Conecte ao projeto. A Vercel injeta automaticamente `KV_REST_API_URL` e `KV_REST_API_TOKEN`
   nas variáveis de ambiente.
3. Para dev local, rode `vercel env pull .env.local` depois de instalar o [Vercel CLI](https://vercel.com/docs/cli).

**Opção B — direto no Upstash**

1. Crie uma conta em <https://console.upstash.com/>.
2. Redis → **Create Database** (qualquer region próxima do seu público).
3. Na aba **REST API**, copie `UPSTASH_REDIS_REST_URL` e `UPSTASH_REDIS_REST_TOKEN`
   para as vars `KV_REST_API_URL` e `KV_REST_API_TOKEN` do `.env.local` / Vercel.

Os emails ficam armazenados como um array no Redis sob a chave `email-list`
(configurável em `lib/constants.ts`).

## Deploy 1-click na Vercel

[![Deploy with Vercel](https://vercel.com/button)](https://vercel.com/new/clone?repository-url=https%3A%2F%2Fgithub.com%2Fgmadureiraa%2Fnewsletter-template&env=KV_REST_API_URL,KV_REST_API_TOKEN&envDescription=Credenciais%20do%20Upstash%20Redis%20(ou%20Vercel%20KV).%20Crie%20um%20DB%20em%20console.upstash.com%20ou%20pela%20aba%20Storage%20da%20Vercel.&envLink=https%3A%2F%2Fgithub.com%2Fgmadureiraa%2Fnewsletter-template%23configurar-o-upstash-vercel-kv&project-name=newsletter&repository-name=newsletter)

Ou manual:

```bash
bun install -g vercel
vercel link
vercel env add KV_REST_API_URL
vercel env add KV_REST_API_TOKEN
vercel --prod
```

## Variáveis de ambiente

| Variável            | Obrigatória | Descrição                                                     |
| ------------------- | ----------- | ------------------------------------------------------------- |
| `KV_REST_API_URL`   | Sim         | REST URL do banco Upstash Redis / Vercel KV.                  |
| `KV_REST_API_TOKEN` | Sim         | REST token do banco Upstash Redis / Vercel KV.                |

Sem essas vars, o server action `subscribe()` entra em modo demo e retorna
`"Missing required setup"` em vez de persistir o email — útil pra testar UI
sem configurar Redis.

## Scripts

```bash
bun run dev     # next dev
bun run build   # next build
bun run start   # next start (servir build de produção)
bun run lint    # eslint .
```

## Estrutura

```
app/                # App Router (layout, page, OG image)
components/
  background.tsx    # vídeo de background com fallback para imagem
  newsletter.tsx    # hero + form + manifesto expansível
  form-newsletter.tsx # react-hook-form + Zod + server action
  footer.tsx        # links sociais
  ui/               # shadcn/ui components
lib/
  subscribe.ts      # server action: grava email no Redis
  schema.ts         # Zod schema do email
  constants.ts      # tabela Redis + social links
  actions.ts        # env check (dev only)
public/             # assets estáticos
```

## Licença

MIT — use, fork e adapte à vontade.
