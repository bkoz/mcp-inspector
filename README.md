# MCP Inspector Tutorial (work in Progress)

## Table of Contents

- [Overview](#overview)
- [What is MCP Inspector?](#what-is-mcp-inspector)
- [Prerequisites](#prerequisites)
- [Project Structure](#project-structure)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Building the Project](#building-the-project)
  - [Running the Server](#running-the-server)
- [Using MCP Inspector](#using-mcp-inspector)
- [Adding Custom Tools](#adding-custom-tools)
- [Development Workflow](#development-workflow)
- [Troubleshooting](#troubleshooting)
- [Resources](#resources)
- [License](#license)

## Overview

Model Context Protocol (MCP) is a protocol that enables AI applications to interact with external tools and data sources in a standardized way.

This tutorial provides an introduction to using the MCP Inspector. You'll learn:

- What is the Inspector and why is it useful.
- How to use it to test and verify a simple server.

[↑ Back to Table of Contents](#table-of-contents)

## What is MCP Inspector?

MCP Inspector is a developer tool for testing and debugging MCP servers. It allows you to:

- Connect to MCP servers running locally or remotely.
- Inspect available tools, resources, and prompts that a server supports.
- Test tool invocations interactively.
- Debug server responses and behavior.
- It operates in both CLI and UI modes.

[↑ Back to Table of Contents](#table-of-contents)

## Prerequisites

- **Linux or MacOS**
  - It should run on Windows but I've not tested it.
- **Node.js**: tested with v25.2.1
- **npm**: Included with Node.js

[↑ Back to Table of Contents](#table-of-contents)

## Project Structure

```
mcp-inspector/
├── src/
│   └── index.ts          # Main MCP server implementation
├── dist/                 # Compiled JavaScript (generated)
├── package.json          # Project dependencies and scripts
├── tsconfig.json         # TypeScript configuration
├── CLAUDE.md            # Developer guide for AI assistants
└── README.md            # This file
```

[↑ Back to Table of Contents](#table-of-contents)

## Getting Started

### Installation

Clone this repository, install dependencies then build and run the sample MCP server.

```bash
git clone <this-repository-url>
cd mcp-inspector
npm install
```

### Building the Project

Transpile the TypeScript code to JavaScript:

```bash
npm run build
```

This creates a `dist/` directory with the compiled code.

### Running the Server

After building, run the MCP server:

```bash
node dist/index.js
```

Expected Output

```console
MCP Inspector Tutorial server running on stdio
```
Use **ctl-c** to kill the server.

[!NOTE] This example server is written using the stdio transport which is intended for
local development. For production scenarios the streamable-http transport should be used. SSE is a third transport that has been recently deprecated. We'll show
how the Inspector can be used with both the stdio and streamable-http transports. 

[↑ Back to Table of Contents](#table-of-contents)

## Using MCP Inspector

The Inspector provides a CLI and a UI mode.

1. Build your server:
```bash
npm run build
```

2. Let's use the CLI mode to list the *tools* that our server provides. The Inspector plays the role of an MCP client and invokes the server as a 
sub process to establish the stdio connection.

```bash
npx @modelcontextprotocol/inspector --cli --method=tools/list -- node dist/index.js
```

```json
{
  "tools": [
    {
      "name": "echo",
      "description": "Echoes back the provided message",
      "inputSchema": {
        "type": "object",
        "properties": {
          "message": {
            "type": "string",
            "description": "The message to echo back"
          }
        },
        "required": [
          "message"
        ]
      }
    }
  ]
}
```

Note that the server is advertizing a tool called *echo* that accepts
one required argument named *message* that is a *string* type.

3. Now we'll test the tool's *echo* method.
```bash
npx @modelcontextprotocol/inspector --cli --method=tools/call --tool-name=echo --tool-arg=message=hello -- node dist/index.js
```

Expected output
```json
{
  "content": [
    {
      "type": "text",
      "text": "Echo: hello"
    }
  ]
}
```

[↑ Back to Table of Contents](#table-of-contents)

## Development Workflow

For active development, use watch mode:

```bash
npm run dev
```

This automatically recompiles TypeScript files when you make changes.

**Recommended workflow:**
1. Make code changes in `src/index.ts`
2. Watch mode automatically rebuilds
3. Restart your server (or MCP Inspector connection)
4. Test changes in MCP Inspector
5. Iterate

**Type checking without building:**
```bash
npm run typecheck
```

**Clean build artifacts:**
```bash
npm run clean
```

[↑ Back to Table of Contents](#table-of-contents)

## Troubleshooting

### Server won't start
- Ensure you've run `npm install`
- Verify you've built the project with `npm run build`
- Check that Node.js version is 18 or higher: `node --version`

### TypeScript errors
- Run `npm run typecheck` to see detailed errors
- Ensure `@modelcontextprotocol/sdk` is properly installed

### MCP Inspector can't connect
- Verify the server path is correct in inspector configuration
- Check that the server builds without errors
- Ensure the server process isn't already running

[↑ Back to Table of Contents](#table-of-contents)

## Resources

- [Model Context Protocol Documentation](https://modelcontextprotocol.io)
- [MCP TypeScript SDK](https://github.com/modelcontextprotocol/typescript-sdk)
- [MCP Inspector](https://github.com/modelcontextprotocol/inspector)

[↑ Back to Table of Contents](#table-of-contents)

## License

This project is licensed under the Apache License 2.0. See the [LICENSE](LICENSE) file for details.

[↑ Back to Table of Contents](#table-of-contents)