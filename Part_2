

# Part 2: Foreign Server Configuration and Advanced Settings

## Foreign Server Setup

1. On the foreign server, repeat steps 1-5 from Part 1 to set up the `core.json` file.

2. Create the `config.json` file for the foreign server:

```bash
nano config.json
```

3. Paste the following content into `config.json`:

```json
{
  "name": "simple_multiport_hd_server",
  "nodes": [
    {
      "name": "input",
      "type": "TcpListener",
      "settings": {
        "address": "0.0.0.0",
        "port": 443,
        "nodelay": true
      },
      "next": "halfs"
    },
    {
      "name": "halfs",
      "type": "HalfDuplexServer",
      "settings": {},
      "next": "port_header"
    },
    {
      "name":"port_header",
      "type": "HeaderServer",
      "settings": {
        "override": "dest_context->port"
      },
      "next": "output"
    },
    {
      "name": "output",
      "type": "TcpConnector",
      "settings": {
        "nodelay": true,
        "address":"127.0.0.1",
        "port":"dest_context->port"
      }
    }
  ]
}
```

4. Save and exit the editor.

## Implement Reverse Reality Configuration (Optional)

If you want to use the reverse reality configuration, replace the content of `config.json` on the Iranian server with:

```json
{
  "name": "reverse_reality_grpc_hd_multiport_server",
  "nodes": [
    {"name": "users_inbound", "type": "TcpListener", "settings": {"address": "0.0.0.0", "port": [23,65535], "nodelay": true}, "next": "header"},
    {"name": "header", "type": "HeaderClient", "settings": {"data": "src_context->port"}, "next": "bridge2"},
    {"name": "bridge2", "type": "Bridge", "settings": {"pair": "bridge1"}},
    {"name": "bridge1", "type": "Bridge", "settings": {"pair": "bridge2"}},
    {"name": "reverse_server", "type": "ReverseServer", "settings": {}, "next": "bridge1"},
    {"name": "pbserver", "type": "ProtoBufServer", "settings": {}, "next": "reverse_server"},
    {"name": "h2server", "type": "Http2Server", "settings": {}, "next": "pbserver"},
    {"name": "halfs", "type": "HalfDuplexServer", "settings": {}, "next": "h2server"},
    {"name": "reality_server", "type": "RealityServer", "settings": {"destination": "reality_dest", "password": "passwd"}, "next": "halfs"},
    {"name": "kharej_inbound", "type": "TcpListener", "settings": {"address": "0.0.0.0", "port": 443, "nodelay": true, "whitelist": ["2.2.2.2/32"]}, "next": "reality_server"},
    {"name": "reality_dest", "type": "TcpConnector", "settings": {"nodelay": true, "address": "sahab.ir", "port": 443}}
  ]
}
```

