# Hey, we're Agentuity 👋

The full-stack platform for AI agents. Build with type-safe schemas, frontend hooks, and real-time routes. Ship agents as easily as web apps.

From local government to indie developers, people are already building with us.

Let's see this in action, with a basic agent that streams back responses:

```typescript
import { createAgent } from '@agentuity/runtime';
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';
import { z } from 'zod';

const agent = createAgent({
  schema: {
    input: z.object({ message: z.string() }),
    stream: true,
  },
  handler: async (ctx, { message }) => {
    const { textStream } = streamText({
      model: openai('gpt-5-mini'),
      prompt: message,
    });
    return textStream;
  },
});

export default agent;
```

It's that simple. Use `bun run dev` to test locally, then `bun run deploy` to production. 🚀

## 📦 Key Repositories

Check out our [**sdk**](https://github.com/agentuity/sdk) (runtime, CLI, frontend hooks, server utilities) and [**docs**](https://github.com/agentuity/docs).

## 🚀 Quick Start

```bash
# Install the CLI
curl -fsS https://agentuity.sh | sh

# Create a new project
agentuity create

# Start developing
cd my-project && bun run dev

# Deploy to the cloud
bun run deploy
```

## What You Get

Everything you need to build and ship full-stack AI agents:

**Build**
- **TypeScript-first**: Type-safe schemas, autocomplete everywhere, powered by Bun
- **Frontend**: Deploy web apps alongside your agents with built-in hooks
- **Framework-agnostic**: Use any AI library (e.g. Vercel AI SDK, LangChain, Mastra) or bring your own
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

- [Documentation](https://agentuity.dev): Guides, examples, and reference
- [Discord](https://discord.gg/agentuity): Join 200+ developers to chat, ask for help, and share what you're building

## 🤝 Contributing

Our SDK is open source. Contributions welcome! See the repo for guidelines.
