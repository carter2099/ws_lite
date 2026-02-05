# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Project Overview

ws_lite is a lightweight WebSocket client library for Ruby (successor to websocket-client-simple). It provides an event-driven API for connecting to WebSocket servers with support for secure connections (WSS) and custom SSL options. Requires Ruby >= 3.3.0.

## Commands

```bash
bundle install              # Install dependencies
bundle exec rake            # Run tests and linting (default task)
bundle exec rake test       # Run tests only
bundle exec rake rubocop    # Run linting only
```

## Architecture

The library uses an event-driven pattern built on the `event_emitter` gem:

- **Entry point**: `lib/ws_lite.rb` - Module-level `connect` method creates Client instances
- **Core implementation**: `lib/ws_lite/client.rb` - Contains all WebSocket logic

**Connection flow**:
1. `WSLite.connect(url, options)` creates a new `WSLite::Client`
2. Client parses URL and opens TCP socket
3. For `wss://`, wraps socket with SSL (OpenSSL 3.x compatible)
4. Performs WebSocket handshake
5. Spawns reader thread that loops on `socket.getc`, emitting events:
   - `:open` - Connection established
   - `:message` - Frame received (data in `event.data`)
   - `:close` - Connection closed
   - `:error` - Error occurred

**Key methods**: `send(data, opt)`, `close()`, `open?`, `closed?`

## Testing

Tests use Minitest with an EventMachine-based echo server (`test/echo_server.rb`). Run individual tests with:

```bash
ruby -Ilib:test test/test_websocket_client_simple.rb
```

## Code Style

RuboCop configured for Ruby 3.3+ with 120-character line limit. The client.rb file has relaxed method length/complexity metrics due to inherent WebSocket protocol complexity.
