# MCP Inspector Tutorial (work in Progress)

## Table of Contents

- [Overview](#overview)
- [What is MCP Inspector?](#what-is-mcp-inspector)
- [Prerequisites](#prerequisites)
- [Getting Started](#getting-started)
  - [Installation](#installation)
  - [Building the Project](#building-the-project)
  - [Running the Server](#running-the-server)
- [Project Structure](#project-structure)
- [Understanding the Example Server](#understanding-the-example-server)
  - [Server Initialization](#server-initialization)
  - [Defining Tools](#defining-tools)
  - [Handling Tool Calls](#handling-tool-calls)
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
- **Node.js**: v24.11.1
- **npm**: Included with Node.js

[↑ Back to Table of Contents](#table-of-contents)

## Getting Started

### Installation

Clone this repository and install dependencies:

```bash
git clone <repository-url>
cd mcp-inspector
npm install
```

### Building the Project

Compile the TypeScript code to JavaScript:

```bash
npm run build
```

This creates a `dist/` directory with the compiled code.

### Running the Server

After building, run the MCP server:

```bash
node dist/index.js
```

The server will start and listen for MCP protocol messages on stdio (standard input/output).

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

## Understanding the Example Server

The example server in `src/index.ts` demonstrates the core concepts of building an MCP server.

### Server Initialization

```typescript
const server = new Server(
  {
    name: "mcp-inspector-tutorial",
    version: "0.1.0",
  },
  {
    capabilities: {
      tools: {},
    },
  }
);
```

The server is created with:
- **Metadata**: Name and version for identification
- **Capabilities**: Declares what features the server supports (in this case, tools)

### Defining Tools

Tools are defined by handling the `ListToolsRequestSchema`:

```typescript
server.setRequestHandler(ListToolsRequestSchema, async () => {
  return {
    tools: [
      {
        name: "echo",
        description: "Echoes back the provided message",
        inputSchema: {
          type: "object",
          properties: {
            message: {
              type: "string",
              description: "The message to echo back",
            },
          },
          required: ["message"],
        },
      },
    ],
  };
});
```

Each tool definition includes:
- **name**: Unique identifier for the tool
- **description**: Explains what the tool does
- **inputSchema**: JSON Schema defining the tool's parameters

### Handling Tool Calls

Tool execution is handled via `CallToolRequestSchema`:

```typescript
server.setRequestHandler(CallToolRequestSchema, async (request) => {
  if (request.params.name === "echo") {
    const message = String(request.params.arguments?.message ?? "");
    return {
      content: [
        {
          type: "text",
          text: `Echo: ${message}`,
        },
      ],
    };
  }
  throw new Error(`Unknown tool: ${request.params.name}`);
});
```

The handler:
1. Checks which tool is being called
2. Extracts arguments from the request
3. Executes the tool logic
4. Returns results as content blocks

[↑ Back to Table of Contents](#table-of-contents)

## Using MCP Inspector

To inspect your server using the MCP Inspector:

1. **Build your server**: Run `npm run build` to compile the code

2. **Configure MCP Inspector**: Add your server to the inspector's configuration. The server can be invoked as:
   ```bash
   node /path/to/mcp-inspector/dist/index.js
   ```

3. **Connect and Test**: Use the MCP Inspector interface to:
   - View available tools
   - Call the "echo" tool with different messages
   - Observe request/response patterns
   - Debug any issues

[↑ Back to Table of Contents](#table-of-contents)

## Adding Custom Tools

To add new tools to the server:

1. **Add the tool definition** in the `ListToolsRequestSchema` handler:
   ```typescript
   {
     name: "my-tool",
     description: "Description of what my tool does",
     inputSchema: {
       type: "object",
       properties: {
         param1: { type: "string", description: "First parameter" },
         param2: { type: "number", description: "Second parameter" }
       },
       required: ["param1"]
     }
   }
   ```

2. **Implement the tool logic** in the `CallToolRequestSchema` handler:
   ```typescript
   if (request.params.name === "my-tool") {
     const param1 = String(request.params.arguments?.param1 ?? "");
     const param2 = Number(request.params.arguments?.param2 ?? 0);

     // Your tool logic here

     return {
       content: [
         {
           type: "text",
           text: `Result: ${/* your result */}`,
         },
       ],
     };
   }
   ```

3. **Rebuild and test**: Run `npm run build` and test with MCP Inspector

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