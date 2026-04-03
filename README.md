# Unified MCP Stack (OrbStack Optimized)

This project provides a fully containerized, secure, and unified Model Context Protocol (MCP) stack using **Nginx as a high-performance proxy**. It is specifically optimized to run on **macOS Apple Silicon** using **OrbStack**, avoiding common issues with Docker socket dependencies.

## Architecture

The stack consists of three primary services running in isolated Docker containers:

1.  **Unified Nginx Proxy**: Acts as a central gateway on `${GATEWAY_PORT}`. It routes traffic to the appropriate MCP server based on the URL path.
2.  **Context7 MCP Server**: Provides documentation and code context (HTTP transport).
3.  **SearXNG MCP Server**: Provides web search capabilities (HTTP transport).

## Key Features

*   **Zero Docker Socket Dependency**: The proxy does not require `/var/run/docker.sock`, significantly improving security.
*   **Unified Port**: Both MCP servers are aggregated behind a single port (`${GATEWAY_PORT}`).
*   **Stable Routing**:
    *   **Context7**: Reachable at `http://localhost:${GATEWAY_PORT}/context7/mcp`
    *   **SearXNG**: Reachable at `http://localhost:${GATEWAY_PORT}/searxng/mcp`
*   **Hardened Security**:
    *   Containers run with `read_only: true` filesystems.
    *   Capabilities are dropped (`cap_drop: ALL`).
    *   No privilege escalation allowed (`no-new-privileges: true`).
    *   Internal servers are not exposed to the host/internet; only the Proxy is reachable.

## Getting Started

### Prerequisites
*   [OrbStack](https://orbstack.dev/) (or Docker Desktop)
*   An Olares instance for SearXNG (configured in `.env`)

### Installation

1.  **Start the stack:**
    ```bash
    docker-compose up -d
    ```

2.  **Verify the services:**
    ```bash
    docker-compose ps
    ```

3.  **Check the logs:**
    ```bash
    docker-compose logs -f proxy
    ```

## Connecting to Clients

### Zed
Zed is already configured to use the following endpoints:

*   **Context7**: `http://localhost:${GATEWAY_PORT}/context7/mcp`
*   **SearXNG**: `http://localhost:${GATEWAY_PORT}/searxng/mcp`

You can verify these settings in your Zed `settings.json` file (`cmd+,`).

## Configuration Files

*   **`docker-compose.yml`**: The main orchestration file.
*   **`nginx.conf`**: Defines the routing and proxy settings.
*   **`.env`**: Store your secrets (like `SEARXNG_URL`) and port configurations here.

## Troubleshooting

*   **Port Busy**: If `${GATEWAY_PORT}` is already in use, run `docker-compose down --remove-orphans` before starting the stack.
*   **No Tools in Zed**: Ensure that the `Context7` and `SearXNG` servers are showing as "Enabled" in the Zed MCP menu.
