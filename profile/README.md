# Hey, we're Agentuity 👋

A cloud platform for building, deploying, and operating AI agents at scale.

We're building the mission control for the AI agent revolution, because agents should be as easy to ship as web apps. From local government to indie developers, teams are already building with us.

Let's see this in action, with a basic agent that streams back responses:

```typescript
import { AgentHandler } from '@agentuity/sdk';
import { streamText } from 'ai';
import { openai } from '@ai-sdk/openai';

const handler: AgentHandler = async (request, response, context) => {
  const { message } = await request.data.json();

  const { textStream } = streamText({
    model: openai('gpt-5.1'),
    prompt: message,
  });

  return response.stream(textStream);
};

export default handler;
```

It's that simple. Run `agentuity dev` to test locally, then `agentuity deploy` to production. 🚀

## 📦 Key Repositories

| Repository | Description |
|------------|-------------|
| [**sdk-js**](https://github.com/agentuity/sdk-js) | JavaScript/TypeScript SDK |
| [**sdk-py**](https://github.com/agentuity/sdk-py) | Python SDK |
| [**cli**](https://github.com/agentuity/cli) | Command-line tools for development and deployment |
| [**kitchen-sink-ts**](https://github.com/agentuity/kitchen-sink-ts) | Demo project showcasing all SDK features |
| [**examples**](https://github.com/agentuity/examples) | Example agents and integrations |
| [**docs**](https://github.com/agentuity/docs) | Documentation website |
| [**agents-spotlight**](https://github.com/agentuity/agents-spotlight) | Display your agent in our agent showroom |

## 🚀 Quick Start

```bash
# Install the CLI
curl -fsS https://agentuity.sh | sh

# Create a new project
agentuity create

# Start developing
cd my-project && agentuity dev

# Deploy to the cloud
agentuity deploy
```

## What You Get

Everything you need to build and ship agents:

- **Multi-language**: JavaScript/TypeScript or Python
- **Framework-agnostic**: Use any AI library (e.g. Vercel AI SDK, LangChain, Mastra) or bring your own
- **AI Gateway**: Access all major LLM providers (e.g. OpenAI, Anthropic, Google) with just one API key
- **Triggers**: Webhooks, cron, email, SMS
- **Storage**: Key-value, vector, object storage
- **Observability**: OpenTelemetry tracing, structured logging, real-time analytics

## 📚 Resources

- [Documentation](https://agentuity.dev): Guides, tutorials, and API reference
- [Getting Started](https://youtube.com/watch?v=AI1tfCIVOiU): See the full dev workflow
- [Build Your First Agent](https://youtube.com/watch?v=Z9QAdFO2LYE): Step-by-step tutorial
- [Kitchen Sink Demo](https://youtu.be/gcxqdMWY-x4): Interactive walkthrough of the full SDK
- [Discord](https://discord.gg/agentuity): Join 200+ developers to chat, ask for help, and share what you're building

## 🤝 Contributing

Our SDKs, CLI, docs, and examples are open source. We welcome contributions -- see individual repos for guidelines.
