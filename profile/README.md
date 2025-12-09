# Hey, we're Agentuity 👋

The full-stack platform for AI agents. Build with type-safe schemas, frontend hooks, and real-time routes. Ship agents as easily as web apps.

> **Now in public preview** — Feedback welcome on [Discord](https://discord.gg/agentuity) and [GitHub Discussions](https://github.com/agentuity/sdk/discussions).

From local government to indie developers, people are already building with us.

Let's see this in action - a complete flow from agent to API to React frontend:

**1. The Agent** (`src/agent/chat/agent.ts`)

```typescript
import { createAgent } from '@agentuity/runtime';
import { s } from '@agentuity/schema';
import { generateText } from 'ai';
import { openai } from '@ai-sdk/openai';

const agent = createAgent('chat', {
  description: 'A simple chat agent',
  schema: {
    input: s.object({ message: s.string() }),
    output: s.object({ response: s.string() }),
  },
  handler: async (ctx, { message }) => {
    const { text } = await generateText({
      model: openai('gpt-5-mini'),
      prompt: message,
    });
    return { response: text };
  },
});

export default agent;
```

**2. The API Route** (`src/api/index.ts`)

```typescript
import { createRouter } from '@agentuity/runtime';
import chatAgent from '../agent/chat/agent';

const router = createRouter();

router.post('/chat', chatAgent.validator(), async (c) => {
  const data = c.req.valid('json');
  const result = await chatAgent.run(data);
  return c.json(result);
});

export default router;
```

**3. The React Hook** (`src/web/components/Chat.tsx`)

```tsx
import { useAPI } from '@agentuity/react';

export function Chat() {
  const { invoke, data, isLoading } = useAPI('POST /api/chat');

  return (
    <div>
      <button onClick={() => invoke({ message: 'Hello!' })}>
        {isLoading ? 'Thinking...' : 'Send'}
      </button>
      {data && <p>{data.response}</p>}
    </div>
  );
}
```

Use `agentuity dev` to test locally, then `agentuity deploy` to production. 🚀

## 📦 Key Repositories

Check out our [**SDK**](https://github.com/agentuity/sdk) (runtime, CLI, frontend hooks, server utilities) and [**docs**](https://github.com/agentuity/docs).

## 🚀 Quick Start

```bash
# Install the CLI
curl -fsS https://v1.agentuity.sh | sh

# Create a new project
agentuity create

# Start developing
cd my-project && agentuity dev

# Deploy to the cloud
agentuity deploy
```

## What You Get

Everything you need to build and ship full-stack AI agents:

**Build**
- **TypeScript-first**: Type-safe schemas, autocomplete everywhere, powered by Bun
- **Frontend**: Deploy web apps alongside your agents with built-in hooks
- **Framework-agnostic**: Use any AI library (e.g. Vercel AI SDK, Mastra) or bring your own
- **Multi-agent**: Coordinate agents with type-safe calls between them
- **Offline-ready**: Start building immediately, no account required
- **Agent-friendly CLI**: `--json` output, `--explain` previews, `--dry-run` validation, schema discovery

**Connect**
- **AI Gateway**: Access all major LLM providers (e.g. OpenAI, Anthropic, Google) with just one API key
- **Infrastructure as code**: HTTP, cron, email, SMS, WebSocket, and SSE. Rollback-friendly deployments.
- **Storage**: Key-value, vector, object, durable streams. BYO supported.
- **Custom domains**: Automated SSL certificates and DNS management
- **Deploy anywhere**: Public cloud, private cloud, on-prem, or edge

**Monitor**
- **Observability**: OpenTelemetry tracing, structured logging, real-time analytics
- **Evaluations**: Automated quality checks after each agent run
- **Container access**: SSH and SCP into running deployments

## 📚 Resources

- [Documentation](https://preview.agentuity.dev/): Guides, examples, and reference
- [Discord](https://discord.gg/agentuity): Join 200+ developers to chat, ask for help, and share what you're building

## 🤝 Contributing

Our SDK is open source. Contributions welcome! See the repo for guidelines.
