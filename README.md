 Part 1: Installation and Initial Setup for the Waterwall project with HalfDuplex Tunnel:

# Waterwall with HalfDuplex Tunnel Setup Guide

## Part 1: Installation and Initial Setup

### Prerequisites:
- Linux-based server (both in Iran and abroad)
- Root or sudo access
- Basic knowledge of JSON configuration

### 1. Download Waterwall:

```bash
wget https://github.com/radkesvat/Waterwall/releases/download/v1.18/Waterwall-linux-64.zip
```

### 2. Extract the archive:

```bash
unzip Waterwall-linux-64.zip
```

### 3. Create the core.json file:

```bash
nano core.json
```

### 4. Paste the following content into core.json:

```json
{
  "log": {
    "path": "log/",
    "core": {"loglevel": "DEBUG", "file": "core.log", "console": true},
    "network": {"loglevel": "DEBUG", "file": "network.log", "console": true},
    "dns": {"loglevel": "SILENT", "file": "dns.log", "console": false}
  },
  "dns": {},
  "misc": {"workers": 0, "ram-profile": "server", "libs-path": "libs/"},
  "configs": ["config.json"]
}
```

### 5. Save and exit the editor (Ctrl+X, Y, Enter in nano).

### 6. Create the config.json file for the Iranian server:

```bash
nano config.json
```

### 7. Paste the following content into config.json:

```json
{
  "name": "simple_multiport_hd_client",
  "nodes": [
    {
      "name": "input",
      "type": "TcpListener",
      "settings": {
        "address": "0.0.0.0",
        "port": [23,65535],
        "nodelay": true
      },
      "next": "port_header"
    },
    {
      "name": "port_header",
      "type": "HeaderClient",
      "settings": {
        "data": "src_context->port"
      },
      "next": "halfc"
    },
    {
      "name": "halfc",
      "type": "HalfDuplexClient",
      "next": "output"
    },
    {
      "name": "output",
      "type": "TcpConnector",
      "settings": {
        "nodelay": true,
        "address": "1.1.1.1",
        "port": 443
      }
    }
  ]
}
```

### 8. Replace "1.1.1.1" with the actual IP address of your foreign server.

### 9. Save and exit the editor.

### 10. Set appropriate permissions:

```bash
chmod +x waterwall
```

