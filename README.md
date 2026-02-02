ws-client
=========
Successor to [websocket-client-simple](https://github.com/ruby-jp/websocket-client-simple) with OpenSSL 3.x fixes and improved connection reliability.

- https://github.com/carter2099/ws-client
- https://rubygems.org/gems/ws-client

Installation
------------

    gem install ws-client

Usage
-----
```ruby
require 'websocket-client-simple'

ws = WebSocket::Client::Simple.connect 'ws://example.com:8888'

ws.on :message do |msg|
  puts msg.data
end

ws.on :open do
  ws.send 'hello!!!'
end

ws.on :close do |e|
  p e
  exit 1
end

ws.on :error do |e|
  p e
end

loop do
  ws.send STDIN.gets.strip
end
```

`connect` runs a given block before connecting websocket

```ruby
WebSocket::Client::Simple.connect 'ws://example.com:8888' do |ws|
  ws.on :open do
    puts "connect!"
  end

  ws.on :message do |msg|
    puts msg.data
  end
end
```

### SSL Options

Pass a custom `OpenSSL::SSL::SSLContext` for full control over TLS settings:

```ruby
ctx = OpenSSL::SSL::SSLContext.new
ctx.verify_mode = OpenSSL::SSL::VERIFY_PEER

ws = WebSocket::Client::Simple.connect 'wss://example.com', ssl_context: ctx
```

By default, `OP_IGNORE_UNEXPECTED_EOF` is enabled on OpenSSL 3.x to prevent `SSL_read: unexpected eof while reading` errors when servers close connections without a TLS `close_notify` alert.


Sample
------
[websocket chat](https://github.com/carter2099/ws-client/tree/master/sample)


Test
----

    % gem install bundler
    % bundle install
    % export WS_PORT=8888
    % rake test


Contributing
------------
1. Fork it
2. Create your feature branch (`git checkout -b my-new-feature`)
3. Commit your changes (`git commit -am 'Add some feature'`)
4. Push to the branch (`git push origin my-new-feature`)
5. Create new Pull Request
