 additional configuration steps for Part 2:

## 6. Foreign Server's Reverse Reality Configuration

Replace the content of `config.json` on the foreign server with:

```json
{
  "name": "reverse_reality_grpc_client_hd_multiport_client",
  "nodes": [
    {"name": "outbound_to_core", "type": "TcpConnector", "settings": {"nodelay": true, "address": "127.0.0.1", "port": "dest_context->port"}},
    {"name": "header", "type": "HeaderServer", "settings": {"override": "dest_context->port"}, "next": "outbound_to_core"},
    {"name": "bridge1", "type": "Bridge", "settings": {"pair": "bridge2"}, "next": "header"},
    {"name": "bridge2", "type": "Bridge", "settings": {"pair": "bridge1"}, "next": "reverse_client"},
    {"name": "reverse_client", "type": "ReverseClient", "settings": {"minimum-unused": 16}, "next": "pbclient"},
    {"name": "pbclient", "type": "ProtoBufClient", "settings": {}, "next": "h2client"},
    {"name": "h2client", "type": "Http2Client", "settings": {"host": "sahab.ir", "port": 443, "path": "/", "content-type": "application/grpc", "concurrency": 64}, "next": "halfc"},
    {"name": "halfc", "type": "HalfDuplexClient", "next": "reality_client"},
    {"name": "reality_client", "type": "RealityClient", "settings": {"sni": "sahab.ir", "password": "passwd"}, "next": "outbound_to_iran"},
    {"name": "outbound_to_iran", "type": "TcpConnector", "settings": {"nodelay": true, "address": "1.1.1.1", "port": 443}}
  ]
}
```

## 7. Optimize Worker Configuration

In the `core.json` file on both servers, adjust the "workers" parameter:
- For the Iranian server: Set it to the number of CPU cores.
- For the foreign server: Set it to 4 times the number on the Iranian server.

Example:

```json
"misc": {
  "workers": 4,
  "ram-profile": "server",
  "libs-path": "libs/"
}
```

## 8. (Optional) Implement BGP4 Tunnel

If you want to use BGP4 tunneling, replace the `config.json` on the Iranian server with:

```json
{
  "name": "bgp_client",
  "nodes": [
    {"name": "input", "type": "TcpListener", "settings": {"address": "0.0.0.0", "port": 443, "nodelay": true}, "next": "bgp_client"},
    {"name": "bgp_client", "type": "Bgp4Client", "settings": {}, "next": "output"},
    {"name": "output", "type": "TcpConnector", "settings": {"nodelay": true, "address":"1.1.1.1", "port":179}}
  ]
}
```

And on the foreign server:

```json
{
  "name": "bgp_server",
  "nodes": [
    {"name": "input", "type": "TcpListener", "settings": {"address": "0.0.0.0", "port": 179, "nodelay": true}, "next": "bgp_server"},
    {"name":"bgp_server", "type":"Bgp4Server", "settings":{}, "next":"output"},
    {"name": "output", "type": "TcpConnector", "settings": {"nodelay": true, "address": "127.0.0.1", "port": 443}}
  ]
}
```

This completes the configuration of both servers with advanced settings. The next part will cover starting the service, testing, and troubleshooting.

