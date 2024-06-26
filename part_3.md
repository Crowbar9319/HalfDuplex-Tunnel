 Part 3: Starting the Service, Testing, and Troubleshooting for the Waterwall project with HalfDuplex Tunnel:

# Part 3: Starting the Service, Testing, and Troubleshooting

## 1. Start Waterwall on both servers:

```bash
./waterwall
```

## 2. Set up Waterwall as a systemd service for persistent operation:

a. Create a service file:

```bash
sudo nano /etc/systemd/system/waterwall.service
```

b. Add the following content:

```ini
[Unit]
Description=Waterwall Tunnel Service
After=network.target

[Service]
ExecStart=/path/to/waterwall
WorkingDirectory=/path/to/waterwall/directory
Restart=always
User=root

[Install]
WantedBy=multi-user.target
```

c. Replace "/path/to/" with the actual path to your Waterwall installation.

d. Save and exit the editor.

e. Enable and start the service:

```bash
sudo systemctl enable waterwall.service
sudo systemctl start waterwall.service
```

## 3. Test the connection:

a. From the Iranian server, attempt to connect to a service on the foreign server:

```bash
curl -v http://127.0.0.1:8080
```

b. Replace 8080 with the appropriate port if different.

## 4. Monitor logs:

```bash
tail -f log/core.log
tail -f log/network.log
```

## 5. Monitor system resources:

```bash
top
```

or

```bash
htop
```

## 6. Troubleshooting:

a. If connection fails:
   - Check firewall settings on both servers.
   - Verify IP addresses and ports in config files.
   - Ensure the Waterwall service is running on both servers.

b. If performance is poor:
   - Adjust worker count in core.json.
   - Check network latency between servers.
   - Consider upgrading server resources.

## 7. Security considerations:

- Regularly update the Waterwall software.
- Use strong, unique passwords for the Reality server.
- Implement IP whitelisting where possible.
- Keep server operating systems and firewalls updated.

## 8. Advanced monitoring (optional):

Set up Prometheus and Grafana for detailed metrics:

a. Install Prometheus:

```bash
wget https://github.com/prometheus/prometheus/releases/download/v2.30.3/prometheus-2.30.3.linux-amd64.tar.gz
tar xvfz prometheus-*.tar.gz
cd prometheus-*
```

b. Configure Prometheus to scrape Waterwall metrics (add to prometheus.yml):

```yaml
scrape_configs:
  - job_name: 'waterwall'
    static_configs:
      - targets: ['localhost:9100']
```

c. Install and configure Grafana for visualization.

## 9. Scaling (if needed):

- For high traffic, consider setting up multiple foreign servers.
- Implement load balancing on the Iranian server to distribute traffic.

## 10. Backup and Recovery:

- Regularly backup configuration files:

```bash
cp core.json core.json.bak
cp config.json config.json.bak
```

## 11. Stay updated:

- Follow the Waterwall project on GitHub: https://github.com/radkesvat/Waterwall
- Check for new releases and update accordingly.

