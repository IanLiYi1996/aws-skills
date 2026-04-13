---
name: aws-agentic-ai
aliases:
  - bedrock-agentcore
description: This skill should be used when the user works with any AWS Bedrock AgentCore service including Gateway, Runtime, Memory, Identity, Code Interpreter, Browser, Observability, Agent Registry, Evaluations, or any AgentCore component. This skill should also be used when the user asks to "create a registry", "register an MCP server", "search agent registry", "approve registry record", "sync MCP server", "discover agents and tools", "evaluate agent", "create evaluator", "set up online evaluation", "deploy agent to runtime", "create gateway target", or "manage agent credentials".
context: fork
model: sonnet
skills:
  - aws-mcp-setup
allowed-tools:
  - mcp__aws-mcp__*
  - mcp__awsdocs__*
  - mcp__acdocs__search_agentcore_docs
  - mcp__acdocs__fetch_agentcore_doc
  - Bash(aws bedrock-agentcore *)
  - Bash(aws bedrock-agentcore-control *)
  - Bash(aws bedrock-agentcore-runtime *)
  - Bash(aws bedrock *)
  - Bash(aws s3 cp *)
  - Bash(aws s3 ls *)
  - Bash(aws secretsmanager *)
  - Bash(aws sts get-caller-identity)
hooks:
  PreToolUse:
    - matcher: Bash(aws bedrock-agentcore-control create-*)
      command: aws sts get-caller-identity --query Account --output text
      once: true
---

# AWS Bedrock AgentCore

AWS Bedrock AgentCore provides a complete platform for deploying and scaling AI agents with nine core services. This skill covers service selection, deployment patterns, and integration workflows using AWS CLI.

## AWS Documentation Requirement

Always verify AWS facts using MCP tools before answering. Two documentation sources are available:
- **AgentCore-specific docs** (`mcp__acdocs__*`) — bundled with this plugin, provides `search_agentcore_docs` and `fetch_agentcore_doc` for AgentCore documentation
- **General AWS docs** (`mcp__aws-mcp__*` or `mcp__*awsdocs*__*`) — loaded via the `aws-mcp-setup` dependency for broader AWS documentation

Prefer the AgentCore docs MCP for AgentCore-specific questions. If MCP tools are unavailable, guide the user through the `aws-mcp-setup` skill's setup flow.

## When to Use This Skill

Applicable scenarios:
- Deploy REST APIs as MCP tools for AI agents (Gateway)
- Execute agents in serverless runtime (Runtime)
- Add conversation memory to agents (Memory)
- Manage API credentials and authentication (Identity)
- Enable agents to execute code securely (Code Interpreter)
- Allow agents to interact with websites (Browser)
- Monitor and trace agent performance (Observability)
- Catalog, discover, and govern AI agents, MCP servers, and tools (Agent Registry)
- Evaluate agent quality with LLM-as-a-Judge scoring (Evaluations)

## Available Services

| Service | Use For | Documentation |
|---------|---------|---------------|
| **Gateway** | Converting REST APIs to MCP tools | [`services/gateway/README.md`](services/gateway/README.md) |
| **Runtime** | Deploying and scaling agents | [`services/runtime/README.md`](services/runtime/README.md) |
| **Memory** | Managing conversation state | [`services/memory/README.md`](services/memory/README.md) |
| **Identity** | Credential and access management | [`services/identity/README.md`](services/identity/README.md) |
| **Code Interpreter** | Secure code execution in sandboxes | [`services/code-interpreter/README.md`](services/code-interpreter/README.md) |
| **Browser** | Web automation and scraping | [`services/browser/README.md`](services/browser/README.md) |
| **Observability** | Tracing and monitoring | [`services/observability/README.md`](services/observability/README.md) |
| **Agent Registry** | Catalog, discover, and govern agents/tools (Preview) | [`services/registry/README.md`](services/registry/README.md) |
| **Evaluations** | Automated agent quality assessment (LLM-as-a-Judge) | [`services/evaluations/README.md`](services/evaluations/README.md) |

## Common Workflows

### Deploying a Gateway Target

**MANDATORY - READ DETAILED DOCUMENTATION**: See [`services/gateway/README.md`](services/gateway/README.md) for complete Gateway setup guide including deployment strategies, troubleshooting, and IAM configuration.

**Quick Workflow**:
1. Upload OpenAPI schema to S3
2. *(API Key auth only)* Create credential provider and store API key
3. Create gateway target linking schema (and credentials if using API key)
4. Verify target status and test connectivity

> **Note**: Credential provider is only needed for API key authentication. Lambda targets use IAM roles, and MCP servers use OAuth.

### Managing Credentials

**MANDATORY - READ DETAILED DOCUMENTATION**: See [`cross-service/credential-management.md`](cross-service/credential-management.md) for unified credential management patterns across all services.

**Quick Workflow**:
1. Use Identity service credential providers for all API keys
2. Link providers to gateway targets via ARN references
3. Rotate credentials quarterly through credential provider updates
4. Monitor usage with CloudWatch metrics

### Discovering Agents and Tools (Agent Registry)

**MANDATORY - READ DETAILED DOCUMENTATION**: See [`services/registry/README.md`](services/registry/README.md) for complete Agent Registry guide including governance workflows, MCP endpoint configuration, and sync setup.

**Quick Workflow**:
1. Create a registry to catalog your organization's AI resources
2. Register resources (MCP servers, agents, skills, custom) with descriptive metadata
3. Submit records for approval (auto-approve for dev, manual for production)
4. Search and discover approved resources via CLI or MCP endpoint

> **Note**: Agent Registry is in Preview. Available in us-east-1, us-west-2, eu-west-1, ap-northeast-1, ap-southeast-2.

### Evaluating Agent Quality

**MANDATORY - READ DETAILED DOCUMENTATION**: See [`services/evaluations/README.md`](services/evaluations/README.md) for complete Evaluations guide including built-in/custom evaluators, online/on-demand modes, and IAM setup.

**Quick Workflow**:
1. Instrument the agent with OpenTelemetry (ADOT) for trace collection
2. Create evaluators (use built-in like `Builtin.Helpfulness` or create custom)
3. Set up online evaluation with sampling rate and data source
4. Monitor scores in CloudWatch dashboards; investigate low-scoring sessions

### Monitoring Agents

**MANDATORY - READ DETAILED DOCUMENTATION**: See [`services/observability/README.md`](services/observability/README.md) for comprehensive monitoring setup.

**Quick Workflow**:
1. Enable observability for agents
2. Configure CloudWatch dashboards for metrics
3. Set up alarms for error rates and latency
4. Use X-Ray for distributed tracing

## Service-Specific Documentation

For detailed documentation on each AgentCore service, see the following resources:

### Gateway Service
- **Overview**: [`services/gateway/README.md`](services/gateway/README.md)
- **Deployment Strategies**: [`services/gateway/deployment-strategies.md`](services/gateway/deployment-strategies.md)
- **Troubleshooting**: [`services/gateway/troubleshooting-guide.md`](services/gateway/troubleshooting-guide.md)

### Agent Registry Service
- **Overview**: [`services/registry/README.md`](services/registry/README.md)
- **Getting Started**: [`services/registry/getting-started.md`](services/registry/getting-started.md)
- **MCP Endpoint Guide**: [`services/registry/mcp-endpoint.md`](services/registry/mcp-endpoint.md)
- **Governance Workflows**: [`services/registry/governance-workflows.md`](services/registry/governance-workflows.md)
- **Sync Configuration**: [`services/registry/sync-configuration.md`](services/registry/sync-configuration.md)

### Runtime, Memory, Identity, Code Interpreter, Browser, Observability, Evaluations
Each service has comprehensive documentation in its respective directory:
- [`services/runtime/README.md`](services/runtime/README.md)
- [`services/memory/README.md`](services/memory/README.md)
- [`services/identity/README.md`](services/identity/README.md)
- [`services/code-interpreter/README.md`](services/code-interpreter/README.md)
- [`services/browser/README.md`](services/browser/README.md)
- [`services/observability/README.md`](services/observability/README.md)
- [`services/evaluations/README.md`](services/evaluations/README.md)

### Advanced Runtime & OAuth References

Deep-dive reference documentation for Runtime internals, deployment, OAuth integration, and communication protocols. Read these when building production Runtime deployments or configuring OAuth authentication:

- **OAuth Integration**: [`references/agentcore-oauth-integration.md`](references/agentcore-oauth-integration.md) - Three-layer OAuth architecture (Inbound JWT, Outbound Credential Provider, Gateway OAuth), Cognito configuration, supported IdPs, end-to-end CDK examples
- **Runtime Core Mechanisms**: [`references/agentcore-runtime-core.md`](references/agentcore-runtime-core.md) - Container contract, MicroVM Session model, Agent lifecycle (per-request vs per-session), tool integration (MCP/HTTP), startup flow
- **Runtime Deployment & Operations**: [`references/agentcore-runtime-deploy.md`](references/agentcore-runtime-deploy.md) - CDK deployment (L1/L2 constructs), multi-Runtime architecture, security model, observability (OTel/CloudWatch), BedrockAgentCoreApp vs FastAPI comparison
- **Runtime Protocol Reference**: [`references/agentcore-runtime-protocols.md`](references/agentcore-runtime-protocols.md) - HTTP, MCP, A2A, AG-UI protocol specifications with container contracts, endpoint specs, and selection guide

### Runnable Script Templates

Production-ready templates in [`scripts/`](scripts/) for common deployment patterns:

| Script | Protocol | Description |
|--------|----------|-------------|
| [`Dockerfile.runtime-template`](scripts/Dockerfile.runtime-template) | — | ARM64 multi-stage Docker build for AgentCore Runtime |
| [`runtime-fastapi-template.py`](scripts/runtime-fastapi-template.py) | HTTP | FastAPI Runtime with SSE streaming and MCPClient |
| [`mcp-server-template.py`](scripts/mcp-server-template.py) | MCP | MCP Server with Streamable HTTP transport |
| [`a2a-server-template.py`](scripts/a2a-server-template.py) | A2A | A2A Server with Agent Card discovery |
| [`agui-server-template.py`](scripts/agui-server-template.py) | AG-UI | AG-UI Server with standard AG-UI event stream |
| [`gateway-custom-resource-lambda.py`](scripts/gateway-custom-resource-lambda.py) | — | CDK Custom Resource Lambda for Gateway lifecycle |

## Cross-Service Resources

For patterns and best practices that span multiple AgentCore services:

- **Credential Management**: [`cross-service/credential-management.md`](cross-service/credential-management.md) - Unified credential patterns, security practices, rotation procedures
- **Registry Integration**: [`cross-service/registry-integration.md`](cross-service/registry-integration.md) - Cross-service patterns with Gateway, Identity, Runtime
- **Security & Resource Policies**: [`cross-service/security-resource-policies.md`](cross-service/security-resource-policies.md) - Resource-based policies, cross-account access, VPC/IP restrictions

## Additional Resources

- **AWS Documentation**: [Amazon Bedrock AgentCore](https://docs.aws.amazon.com/bedrock-agentcore/latest/devguide/what-is-bedrock-agentcore.html)
- **API Reference**: [Bedrock AgentCore Control Plane API](https://docs.aws.amazon.com/bedrock-agentcore-control/latest/APIReference/)
- **AWS CLI Reference**: [bedrock-agentcore-control commands](https://awscli.amazonaws.com/v2/documentation/api/latest/reference/bedrock-agentcore-control/index.html)

