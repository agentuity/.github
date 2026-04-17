# Hey, we're Agentuity 👋

We're the full-stack, agent-native platform for AI agents. Every agent gets [runtime services](https://agentuity.dev/services) (e.g. storage, sandboxes, observability) built in, not bolted on.

From local government to indie developers, people are already building with us.

## 🚀 Quick Start

```bash
curl -fsSL https://agentuity.sh | sh
agentuity create --name my-agent-app # full-stack agent + typed React frontend
cd my-agent-app
agentuity dev # Vite HMR, also available as bun run dev
agentuity deploy # ship it to the cloud, also available as bun run deploy
```

## A Full-Stack Agent

A typed agent, an API route, and a React client, with [end-to-end type safety](https://agentuity.dev/agents/schema-libraries) flowing from handler to route client.

**1. The Agent** (`src/agent/translate/index.ts`)

```typescript
import { createAgent } from '@agentuity/runtime';
import { s } from '@agentuity/schema'; // our built-in, lightweight schema library
import OpenAI from 'openai';

const agent = createAgent('translate', {
  description: 'Translates text between languages',
  schema: {
    input: s.object({
      text: s.string(),
      toLanguage: s.enum(['Spanish', 'French', 'German']),
    }),
    output: s.object({ translation: s.string() }),
  },
  setup: async () => ({ client: new OpenAI() }), // lazy init so discovery works without the API key
  handler: async (ctx, { text, toLanguage }) => {
    // AI Gateway handles routing, observability, and billing
    const completion = await ctx.config.client.chat.completions.create({
      model: 'gpt-5.4-nano',
      messages: [{ role: 'user', content: `Translate to ${toLanguage}:\n\n${text}` }],
    });
    return { translation: completion.choices[0]?.message?.content ?? '' };
  },
});

export default agent;
```

**2. The API Route** (`src/api/index.ts`)

```typescript
import { Hono } from 'hono'; // Hono is baked in: bring middleware, validators, streaming
import type { Env } from '@agentuity/runtime';
import translate from '../agent/translate';

// validator() enforces the agent's input schema at the route boundary
const api = new Hono<Env>().post('/translate', translate.validator(), async (c) => {
  return c.json(await translate.run(c.req.valid('json')));
});

export type ApiRouter = typeof api;
export default api;
```

**3. The React Client** (`src/web/App.tsx`)

```tsx
import { hc } from 'hono/client';
import type { ApiRouter } from '../api';
import { useState } from 'react';

const client = hc<ApiRouter>('/api'); // routes, bodies, and responses stay in sync

export function App() {
  const [result, setResult] = useState<string>();

  const translate = async () => {
    // If the route name or input schema changes, TypeScript catches this call
    const res = await client.translate.$post({
      json: { text: 'Hello, world!', toLanguage: 'Spanish' },
    });
    const { translation } = await res.json();
    setResult(translation);
  };

  return (
    <>
      <button type="button" onClick={translate}>Translate</button>
      {result && <p>{result}</p>}
    </>
  );
}
```

## What You Get

Everything you need to build and ship full-stack AI agents:

**Build**

- **TypeScript-first**: type-safe schemas, autocomplete everywhere, powered by Bun
- **Infrastructure as code**: HTTP, cron, email/webhooks, WebSocket, SSE, WebRTC. Rollback-friendly deploys.
- **[Frontend](https://agentuity.com/product/react-frontend)**: deploy React apps alongside your agents with a typed Hono `hc()` client
- **Bring your own framework**: use integrations like [OpenAI Agents SDK, Mastra, and LangChain](https://github.com/agentuity/examples/tree/main/integrations), or drop the SDK into your existing app
- **Multi-agent**: coordinate agents with type-safe calls and shared session context
- **Agent-friendly CLI**: `--json` output, `--explain` previews, `--dry-run` validation, schema and capability discovery

**Connect**

- **[AI Gateway](https://agentuity.com/product/ai-gateway)**: one key for OpenAI, Anthropic, Google, Groq, and more
- **Storage & services**: key-value, vector, object, Postgres, durable streams, queues
- **[Sandboxes](https://agentuity.com/product/sandboxes)**: isolated language runtimes (e.g. Bun, Python), browser automation (e.g. Playwright, Agent Browser), and coding agents (e.g. Codex, Claude Code)
- **Custom domains**: define domains in config, then deploy with automatic SSL and DNS validation
- **Deploy anywhere**: our cloud, your VPC, on-prem, or edge via [our Gravity Network](https://agentuity.dev/reference/gravity-network)

**Monitor**

- **[Observability](https://agentuity.com/product/observability)**: OpenTelemetry traces, structured logs, real-time analytics
- **Sessions & threads**: requests grouped for conversation-level visibility
- **Container access**: SSH and SCP into running deployments

## 📚 Repos & Resources

- [SDK](https://github.com/agentuity/sdk): runtime, CLI, schema, services, frontend utilities
- [Examples](https://github.com/agentuity/examples): training, integrations, features
- [Docs](https://agentuity.dev): guides and reference
- [Discord](https://discord.gg/agentuity): join 250+ developers to chat with us and other builders, or ask for help
- [Web App](https://app.agentuity.com): manage projects, services, billing, and more

## 🤝 Contributing

Our SDK is open source, so contributions are welcome! See the repo for guidelines.
