# ADR 003: Safari Extension Transport Constraints

## Status

Accepted

## Context

Safari Web Extensions (Manifest V3) impose strict constraints on how extension code can communicate with localhost services. We hit three major blockers during development.

## Decision

### Problem 1: Content Scripts Cannot Connect to localhost

Content scripts injected into `https://music.youtube.com` cannot make `ws://` or `http://` requests to localhost. This is a mixed-content security restriction.

**Solution**: Proxy all WebSocket connections through the background page.

```mermaid
graph LR
    CS[Content Script<br/>HTTPS page] -->|runtime.sendMessage| BG[Background Page<br/>extension context]
    BG -->|WebSocket| BA[Bridge App<br/>localhost:8765]

    style CS fill:#e94560,stroke:#fff,color:#fff
    style BG fill:#533483,stroke:#fff,color:#fff
    style BA fill:#0f3460,stroke:#fff,color:#fff
```

### Problem 2: Service Workers Cannot Access localhost

Safari MV3 service workers (`"service_worker"` in manifest.json) cannot make any network requests to `127.0.0.1` or `localhost`, even with `NSAllowsLocalNetworking` in Info.plist.

**Solution**: Use `"scripts"` instead of `"service_worker"` in manifest.json background configuration. This gives us a persistent background page instead of a service worker.

```json
{
  "background": {
    "scripts": ["background.js"],
    "persistent": false
  }
}
```

### Problem 3: Port-Based Messaging Doesn't Work

Safari MV3 does not support `runtime.onConnect` / `Port`-based messaging between content scripts and background pages.

**Solution**: Use request/response polling pattern via `runtime.sendMessage`:

```mermaid
sequenceDiagram
    participant CS as Content Script
    participant BG as Background Page

    CS->>BG: sendMessage({type: "bridge_socket_open", ws_url: "..."})
    BG->>CS: {ok: true}

    CS->>BG: sendMessage({type: "bridge_socket_send", data: "..."})
    BG->>CS: {ok: true}

    loop Every 250ms
        CS->>BG: sendMessage({type: "bridge_socket_poll"})
        BG->>CS: {state: "open", messages: [...]}
    end

    CS->>BG: sendMessage({type: "bridge_socket_close"})
    BG->>CS: {ok: true}
```

The `ExtensionBridgeSocket` class in `bridge-socket.ts` wraps this polling pattern behind a standard WebSocket-like API (`addEventListener`, `send`, `close`, `readyState`).

## Consequences

- **Pro**: Works reliably in Safari with all security restrictions satisfied
- **Pro**: `ExtensionBridgeSocket` provides a clean abstraction — the rest of the code doesn't know it's polling
- **Con**: 250ms polling adds latency to message delivery
- **Con**: Background page uses more memory than a service worker
- **Con**: These workarounds are Safari-specific; Chrome/Firefox would need different transport code
