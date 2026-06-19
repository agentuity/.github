# Hey, we're Agentuity 👋

We're the full-stack, agent-native platform for AI agents. Bring the framework you already use, and every app gets [runtime services](https://agentuity.dev/services) (e.g., storage, sandboxes, an AI gateway) built in, not bolted on.

From [local government](https://agentuity.com/enterprise) to indie developers, people are already building with us.

## 🚀 Quick Start

```bash
npm create agentuity -- --name my-app --framework nextjs   # or nuxt, sveltekit, astro, hono
cd my-app
npx agentuity dev    # local dev; add --public for a shareable HTTPS URL
npm run deploy       # buildpack deploy to the cloud

# Works with npm, pnpm, yarn, or bun!
# Prefer a global CLI? curl -fsSL https://agentuity.sh | sh
```

## An agent, in any framework

The whole app is a typed AI endpoint you can drop into any framework. The AI Gateway gives you one credential for every provider, with unified billing, and switching models is a simple string change.

**1. The AI call** (`src/translate.ts`)

```typescript
import { AIGatewayClient, getAIGatewayCompletionText } from '@agentuity/aigateway';
import { z } from 'zod';

const Input = z.object({
  text: z.string(),
  toLanguage: z.enum(['Spanish', 'French', 'German']),
});

// One credential covers every provider. The model below is just a string:
// swap it for any other, e.g. 'anthropic/claude-opus-4-8' or 'googleai/gemini-3.5-flash'.
const ai = new AIGatewayClient();

export async function translate(input: z.infer<typeof Input>) {
  const { text, toLanguage } = Input.parse(input);
  const completion = await ai.complete({
    model: 'openai/gpt-5.5',
    messages: [{ role: 'user', content: `Translate to ${toLanguage}:\n\n${text}` }],
  });
  return { translation: getAIGatewayCompletionText(completion) };
}
```

**2. The route, with services built in** (`src/index.ts`)

```typescript
import { serve } from '@hono/node-server';
import { Hono } from 'hono';
import { agentuity } from '@agentuity/hono';
import { translate } from './translate';

const app = new Hono();
app.use('*', agentuity()); // injects c.var.kv, c.var.vector, c.var.logger, c.var.sandbox, and more

app.post('/api/translate', async (c) => {
  const body = await c.req.json();
  const cacheKey = `${body.toLanguage}:${body.text}`;

  // Key-value storage comes ready through the middleware, provisioned and connected for you
  const cached = await c.var.kv.get<{ translation: string }>('translations', cacheKey);
  if (cached.exists) return c.json(cached.data);

  const result = await translate(body);
  await c.var.kv.set('translations', cacheKey, result, { ttl: 3600 });
  return c.json(result);
});

serve({ fetch: app.fetch, port: Number(process.env.PORT ?? 3000) });
```

Prefer Next.js, SvelteKit, or Astro? The same services work there. Import a client like `@agentuity/keyvalue` or `@agentuity/vector` directly, in a route, a script, or a cron job.

## What You Get

Everything you need to build and ship AI agents:

**Build**

- **TypeScript-first**: typed service clients and schemas, autocomplete everywhere, runs on Bun or Node 24+
- **Bring any framework**: Next.js, Nuxt, SvelteKit, Astro, Hono, or your own. `agentuity deploy` auto-detects and builds it
- **Standalone service clients**: import `@agentuity/keyvalue`, `@agentuity/vector`, `@agentuity/queue`, and the rest into any app or script; they work outside Agentuity too
- **Frontend included**: ship your framework's UI alongside your API on a single deploy
- **Bring your own stack**: pair with the [OpenAI Agents SDK, Mastra, and LangChain](https://github.com/agentuity/examples), or the AI SDK; validate with any [Standard Schema](https://github.com/standard-schema/standard-schema)-compatible library, like Zod, Valibot, or ArkType
- **Agent-friendly CLI**: `--json` output, `--explain` previews, `--dry-run` validation, `--services` to scaffold storage on create

**Connect**

- **[AI Gateway](https://agentuity.dev/services/ai-gateway)**: one key for OpenAI, Anthropic, Google AI, Groq, and more
- **Storage and services**: key-value, vector search, object storage, durable streams, queues, schedules, tasks, email, and webhooks, plus Postgres via `DATABASE_URL`
- **[Sandboxes](https://agentuity.dev/services/sandbox)**: isolated Linux containers with language runtimes (e.g., Bun, Node, Python) and coding agents (e.g., opencode); pause and resume with checkpoints, or snapshot a sandbox to reuse later
- **Custom domains**: declare them in `agentuity.json`; DNS is validated on deploy and TLS is provisioned automatically on first request
- **Deploy**: managed cloud with GitHub auto-deploy and PR preview environments; bring-your-own-cloud (VPC, on-prem, edge) via the [Gravity Network](https://agentuity.dev/reference/gravity-network)

**Monitor**

- **[Observability](https://agentuity.dev/services/observability)**: OpenTelemetry traces and structured logs through `@agentuity/telemetry` (or your own OTel collector); the Hono middleware injects `c.var.logger`, `c.var.tracer`, and `c.var.meter`
- **Sessions and logs**: session records, timelines, and deployment logs to inspect in the [Web App](https://app.agentuity.com)
- **Container access**: SSH and SCP into running deployments with `agentuity cloud ssh`

## Coming from v2?

v3 trades `createAgent()` for plain framework code and standalone service clients. Run `npx @agentuity/migrate --v2-to-v3` to convert an existing project, or read the [migration guide](https://agentuity.dev/migration/from-v2). Staying on v2 for now? The [v2 docs](https://v2.agentuity.dev) still cover the v2 SDK in full.

## 📚 Repos & Resources

- [SDK](https://github.com/agentuity/sdk): runtime, CLI, service clients, framework integrations
- [Examples](https://github.com/agentuity/examples): training, integrations, features
- [Docs](https://agentuity.dev): guides and reference (v3/latest)
- [v2 Docs](https://v2.agentuity.dev): reference for projects still on v2
- [Web App](https://app.agentuity.com): manage projects, services, billing, and more
- [Website](https://agentuity.com): product, customers, [pricing](https://agentuity.com/pricing), and our [blog](https://agentuity.com/blog)
- [Discord](https://discord.gg/agentuity): join 250+ developers to chat with us and other builders, or ask for help

## 🤝 Contributing

Our SDK is open source, so contributions are welcome! See the repo for guidelines.
