# ADR 002: Command Execution Flow

## Status

Accepted

## Context

When an MCP client asks to perform an action (e.g., "pause playback"), the request must travel through three processes and return a result. We need a reliable, low-latency command dispatch mechanism.

## Decision

### Command Flow

```mermaid
sequenceDiagram
    participant MC as MCP Client
    participant GO as Go MCP Server
    participant BA as Bridge App
    participant EXT as Safari Extension

    MC->>GO: tools/call (pause)
    GO->>BA: POST /bridge/action<br/>{"action":"pause","client_id":"..."}
    BA->>BA: Queue command for client
    Note over BA,EXT: Extension is long-polling<br/>or WebSocket-connected

    alt WebSocket path
        BA->>EXT: WS message: {"type":"command","action":"pause"}
    end

    EXT->>EXT: Execute DOM action<br/>(video.pause())
    EXT->>BA: WS message: {"type":"command_result","success":true}
    BA->>GO: HTTP response with result
    GO->>MC: tools/call response
```

### State Heartbeat Flow

The extension sends playback state to the bridge every 2.5 seconds, independent of commands:

```mermaid
sequenceDiagram
    participant EXT as Safari Extension
    participant BA as Bridge App
    participant GO as Go MCP Server

    loop Every 2.5s
        EXT->>BA: WS: {"type":"state","paused":false,"track_title":"..."}
        BA->>BA: Store latest state for client
    end

    GO->>BA: GET /bridge/clients
    BA->>GO: Latest state for all connected clients
```

### WebSocket Connection Lifecycle

```mermaid
stateDiagram-v2
    [*] --> Connecting: Page load / visibility change
    Connecting --> Open: WebSocket connected
    Connecting --> WaitReconnect: Connection failed

    Open --> Open: Heartbeat (2.5s)
    Open --> Open: Command received & executed
    Open --> WaitReconnect: Connection lost
    Open --> Closed: Page unload

    WaitReconnect --> Connecting: Backoff elapsed (0.8s-20s)
    WaitReconnect --> Closed: Page unload

    Closed --> [*]
```

### Reconnection Backoff

- Initial delay: 800ms
- Maximum delay: 20s
- Strategy: exponential with jitter
- Resets to initial on successful connection
- Triggers native messaging to auto-launch bridge app if not running

## Consequences

- **Pro**: Commands execute in < 100ms typical round-trip
- **Pro**: State is always fresh (2.5s heartbeat)
- **Pro**: Resilient to transient disconnects (auto-reconnect)
- **Con**: Extension must be active on a YouTube Music page for commands to work
- **Con**: State is eventually consistent (up to 2.5s stale)
