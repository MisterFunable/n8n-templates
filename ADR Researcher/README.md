# ADR Researcher - Engineering Documentation Assistant

An interactive AI-powered workflow for DevOps, CloudOps, and Development research. Creates Architecture Decision Records (ADRs), procedures, runbooks, and technical documentation with source citations.

## Features

- 🔍 **Web Research** - Searches technical documentation and best practices
- 📋 **ADR Generation** - Creates structured ADRs following MADR template
- 📝 **Procedure/Runbook Creation** - Step-by-step guides with prerequisites
- 💬 **Interactive Conversation** - Handles clarification questions
- 🔄 **Iteration Support** - Memory for follow-up questions and refinements
- 📚 **Source Citations** - Tracks and cites all research sources
- 🎯 **Context Awareness** - Handles version/platform/permission complexity

## Templates

| File | Description |
|------|-------------|
| `template.json` | Main version using OpenAI GPT-4o |
| `template-anthropic.json` | Alternative using Anthropic Claude |

## Setup

### 1. Required Credentials

#### OpenAI API (for `template.json`)
1. Get API key from [OpenAI Platform](https://platform.openai.com/api-keys)
2. In n8n, create OpenAI credential with your API key

#### Anthropic API (for `template-anthropic.json`)
1. Get API key from [Anthropic Console](https://console.anthropic.com/)
2. In n8n, create Anthropic credential with your API key

#### SerpAPI (for web search)
1. Get API key from [SerpAPI](https://serpapi.com/)
2. In n8n, create SerpAPI credential with your API key

### 2. Import Workflow

1. Open n8n
2. Go to Workflows → Import
3. Select the template JSON file
4. Configure credentials for each node

### 3. Configuration Options

Edit the **Configuration** node to customize:

```json
{
  "outputFormat": "markdown",     // markdown, json, adr
  "memoryWindowSize": 10,         // conversation context length
  "maxSearchResults": 5,          // results per search
  "includeSourceUrls": true,      // include sources in output
  "adrTemplate": "madr"           // ADR template style
}
```

## Usage

### Starting a Conversation

The workflow uses n8n's Chat Trigger. After activating:

1. Find the webhook URL in the Chat Trigger node
2. Use the n8n Chat UI or integrate with your own frontend
3. Start asking questions!

### Example Queries

#### ADR Research
```
Create an ADR for choosing between Kubernetes and ECS for our containerized microservices.
Context: AWS environment, team familiar with Docker, ~50 services, need auto-scaling.
```

#### Procedure Creation
```
Write a procedure for setting up Prometheus alerting rules for a Kubernetes cluster.
We're using Prometheus Operator on EKS 1.28.
```

#### Investigation
```
How do I create a Terraform module for AWS S3 with versioning and encryption?
Using Terraform 1.5+ and AWS provider 5.x.
```

#### Clarification Flow
```
User: How do I set up monitoring for my database?

AI: I'd be happy to help! To provide the most relevant guidance, I need some clarification:

1. **Database type**: Which database? (PostgreSQL, MySQL, MongoDB, Redis, etc.)
2. **Environment**: Is this running on:
   - Managed service (RDS, Cloud SQL, Atlas)?
   - Self-hosted on Kubernetes?
   - Self-hosted on VMs?
3. **Monitoring stack**: Do you have existing monitoring? (Prometheus, Datadog, CloudWatch?)
4. **Metrics focus**: Are you most interested in:
   - Performance metrics (query latency, connections)?
   - Resource metrics (CPU, memory, disk)?
   - Replication/availability metrics?

User: PostgreSQL on RDS, we use Prometheus with Grafana, mainly interested in query performance.

AI: [Provides detailed procedure with sources...]
```

## Output Formats

### Markdown (default)
Standard markdown with headers, code blocks, and tables. Best for documentation systems.

### JSON
Structured output with metadata, useful for integration with other systems:

```json
{
  "response": "...",
  "format": "json",
  "sources": ["https://...", "https://..."],
  "metadata": {
    "timestamp": "2024-01-15T10:30:00Z",
    "sessionId": "abc123",
    "query": "original question"
  },
  "structuredADR": {
    "title": "ADR Title",
    "status": "Proposed",
    "context": "...",
    "decision": "...",
    "sources": [...]
  }
}
```

### ADR Template (MADR)
Follows the [Markdown Any Decision Records](https://adr.github.io/madr/) format:

```markdown
# ADR-001: Title

## Status
Proposed

## Context
[Problem description]

## Decision Drivers
- Driver 1
- Driver 2

## Considered Options
1. Option A
2. Option B
3. Option C

## Decision Outcome
Chosen option with rationale...

### Consequences
**Good:**
- Benefit 1
- Benefit 2

**Bad:**
- Tradeoff 1

## Sources
- [Official Docs](https://...) - Primary reference
- [Community Guide](https://...) - Best practices
```

## Workflow Architecture

```
┌─────────────┐     ┌───────────────┐     ┌──────────────────┐
│ Chat Trigger├────►│ Configuration ├────►│ AI Agent         │
└─────────────┘     └───────────────┘     │                  │
                                          │ ┌──────────────┐ │
                                          │ │ LLM Model    │ │
                                          │ └──────────────┘ │
                                          │ ┌──────────────┐ │
                                          │ │ Memory       │ │
                                          │ └──────────────┘ │
                                          │ ┌──────────────┐ │
                                          │ │ Tools:       │ │
                                          │ │ - SerpAPI    │ │
                                          │ │ - HTTP Fetch │ │
                                          │ └──────────────┘ │
                                          └────────┬─────────┘
                                                   │
                                          ┌────────▼─────────┐
                                          │ Format Output    │
                                          └────────┬─────────┘
                                                   │
                                          ┌────────▼─────────┐
                                          │ Respond to Chat  │
                                          └──────────────────┘
```

## Common Use Cases

### 1. Creating New Alerts
"How do I create a Slack alert for high CPU in Datadog?"
- Researches Datadog alerting API
- Provides step-by-step procedure
- Includes Terraform/API examples

### 2. Infrastructure Decisions
"ADR: Should we use Terraform or Pulumi for our IaC?"
- Compares options objectively
- Considers your context (team skills, existing stack)
- Provides structured decision record

### 3. Runbook Creation
"Create a runbook for responding to database connection pool exhaustion"
- Prerequisites and access requirements
- Step-by-step diagnostic and resolution
- Verification and rollback procedures

### 4. Tool Configuration
"How do I configure GitLab CI to deploy to EKS with OIDC?"
- Version-specific instructions
- Security best practices
- Complete YAML examples

## Tips for Best Results

1. **Provide Context**: Include versions, platforms, and existing infrastructure
2. **Be Specific**: "PostgreSQL 15 on RDS" > "database"
3. **State Constraints**: Mention any limitations (permissions, compliance, budget)
4. **Iterate**: Ask follow-up questions to refine the output
5. **Request Formats**: Specify if you want ADR, procedure, or comparison

## Troubleshooting

| Issue | Solution |
|-------|----------|
| No search results | Check SerpAPI credentials and quota |
| Generic responses | Provide more context (version, platform) |
| Missing sources | Ask explicitly: "Include source URLs" |
| Too verbose | Ask for "concise" or "summary" format |
| Wrong format | Specify format in query: "Create an ADR for..." |

## Contributing

Feel free to submit improvements:
- Additional tool integrations (Wikipedia, GitHub)
- New output formats
- Better prompts for specific domains
- Alternative LLM configurations
