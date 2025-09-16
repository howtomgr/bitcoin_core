# bitcoin-core Installation Guide

bitcoin-core is a free and open-source Bitcoin node. Bitcoin Core provides full Bitcoin node implementation

## Table of Contents
1. [Prerequisites](#prerequisites)
2. [Supported Operating Systems](#supported-operating-systems)
3. [Installation](#installation)
4. [Configuration](#configuration)
5. [Service Management](#service-management)
6. [Troubleshooting](#troubleshooting)
7. [Security Considerations](#security-considerations)
8. [Performance Tuning](#performance-tuning)
9. [Backup and Restore](#backup-and-restore)
10. [System Requirements](#system-requirements)
11. [Support](#support)
12. [Contributing](#contributing)
13. [License](#license)
14. [Acknowledgments](#acknowledgments)
15. [Version History](#version-history)
16. [Appendices](#appendices)

## 1. Prerequisites

- **Hardware Requirements**:
  - CPU: 2+ cores
  - RAM: 2GB minimum
  - Storage: 350GB+ for blockchain
  - Network: P2P protocol
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8333 (default bitcoin-core port)
  - RPC on 8332
- **Dependencies**:
  - See official documentation for specific requirements
- **System Access**: root or sudo privileges required


## 2. Supported Operating Systems

This guide supports installation on:
- RHEL 8/9 and derivatives (CentOS Stream, Rocky Linux, AlmaLinux)
- Debian 11/12
- Ubuntu 20.04/22.04/24.04 LTS
- Arch Linux (rolling release)
- Alpine Linux 3.18+
- openSUSE Leap 15.5+ / Tumbleweed
- SUSE Linux Enterprise Server (SLES) 15+
- macOS 12+ (Monterey and later) 
- FreeBSD 13+
- Windows 10/11/Server 2019+ (where applicable)

## 3. Installation

### RHEL/CentOS/Rocky Linux/AlmaLinux

```bash
# Install EPEL repository if needed
sudo dnf install -y epel-release

# Install bitcoin-core
sudo dnf install -y bitcoin_core

# Enable and start service
sudo systemctl enable --now bitcoin-core

# Configure firewall
sudo firewall-cmd --permanent --add-port=8333/tcp
sudo firewall-cmd --reload

# Verify installation
bitcoin-core --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install bitcoin-core
sudo apt install -y bitcoin_core

# Enable and start service
sudo systemctl enable --now bitcoin-core

# Configure firewall
sudo ufw allow 8333

# Verify installation
bitcoin-core --version
```

### Arch Linux

```bash
# Install bitcoin-core
sudo pacman -S bitcoin_core

# Enable and start service
sudo systemctl enable --now bitcoin-core

# Verify installation
bitcoin-core --version
```

### Alpine Linux

```bash
# Install bitcoin-core
apk add --no-cache bitcoin_core

# Enable and start service
rc-update add bitcoin-core default
rc-service bitcoin-core start

# Verify installation
bitcoin-core --version
```

### openSUSE/SLES

```bash
# Install bitcoin-core
sudo zypper install -y bitcoin_core

# Enable and start service
sudo systemctl enable --now bitcoin-core

# Configure firewall
sudo firewall-cmd --permanent --add-port=8333/tcp
sudo firewall-cmd --reload

# Verify installation
bitcoin-core --version
```

### macOS

```bash
# Using Homebrew
brew install bitcoin_core

# Start service
brew services start bitcoin_core

# Verify installation
bitcoin-core --version
```

### FreeBSD

```bash
# Using pkg
pkg install bitcoin_core

# Enable in rc.conf
echo 'bitcoin-core_enable="YES"' >> /etc/rc.conf

# Start service
service bitcoin-core start

# Verify installation
bitcoin-core --version
```

### Windows

```bash
# Using Chocolatey
choco install bitcoin_core

# Or using Scoop
scoop install bitcoin_core

# Verify installation
bitcoin-core --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/bitcoin_core

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
bitcoin-core --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable bitcoin-core

# Start service
sudo systemctl start bitcoin-core

# Stop service
sudo systemctl stop bitcoin-core

# Restart service
sudo systemctl restart bitcoin-core

# Check status
sudo systemctl status bitcoin-core

# View logs
sudo journalctl -u bitcoin-core -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add bitcoin-core default

# Start service
rc-service bitcoin-core start

# Stop service
rc-service bitcoin-core stop

# Restart service
rc-service bitcoin-core restart

# Check status
rc-service bitcoin-core status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'bitcoin-core_enable="YES"' >> /etc/rc.conf

# Start service
service bitcoin-core start

# Stop service
service bitcoin-core stop

# Restart service
service bitcoin-core restart

# Check status
service bitcoin-core status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start bitcoin_core
brew services stop bitcoin_core
brew services restart bitcoin_core

# Check status
brew services list | grep bitcoin_core
```

### Windows Service Manager

```powershell
# Start service
net start bitcoin-core

# Stop service
net stop bitcoin-core

# Using PowerShell
Start-Service bitcoin-core
Stop-Service bitcoin-core
Restart-Service bitcoin-core

# Check status
Get-Service bitcoin-core
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream bitcoin_core_backend {
    server 127.0.0.1:8333;
}

server {
    listen 80;
    server_name bitcoin_core.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name bitcoin_core.example.com;

    ssl_certificate /etc/ssl/certs/bitcoin_core.example.com.crt;
    ssl_certificate_key /etc/ssl/private/bitcoin_core.example.com.key;

    location / {
        proxy_pass http://bitcoin_core_backend;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

### Apache Configuration

```apache
<VirtualHost *:80>
    ServerName bitcoin_core.example.com
    Redirect permanent / https://bitcoin_core.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName bitcoin_core.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/bitcoin_core.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/bitcoin_core.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8333/
    ProxyPassReverse / http://127.0.0.1:8333/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend bitcoin_core_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/bitcoin_core.pem
    redirect scheme https if !{ ssl_fc }
    default_backend bitcoin_core_backend

backend bitcoin_core_backend
    balance roundrobin
    server bitcoin_core1 127.0.0.1:8333 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R bitcoin_core:bitcoin_core /etc/bitcoin_core
sudo chmod 750 /etc/bitcoin_core

# Configure firewall
sudo firewall-cmd --permanent --add-port=8333/tcp
sudo firewall-cmd --reload

# Enable SELinux policies (if applicable)
sudo setsebool -P httpd_can_network_connect on
```

## Database Setup

See official documentation for database configuration requirements.

## Performance Optimization

### System Tuning

```bash
# Basic system tuning
echo 'net.core.somaxconn = 65535' | sudo tee -a /etc/sysctl.conf
echo 'net.ipv4.tcp_max_syn_backlog = 65535' | sudo tee -a /etc/sysctl.conf
sudo sysctl -p
```

## Monitoring

### Basic Monitoring

```bash
# Check service status
sudo systemctl status bitcoin-core

# View logs
sudo journalctl -u bitcoin-core -f

# Monitor resource usage
top -p $(pgrep bitcoin_core)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/bitcoin_core"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/bitcoin_core-backup-$DATE.tar.gz" /etc/bitcoin_core /var/lib/bitcoin_core

echo "Backup completed: $BACKUP_DIR/bitcoin_core-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop bitcoin-core

# Restore from backup
tar -xzf /backup/bitcoin_core/bitcoin_core-backup-*.tar.gz -C /

# Start service
sudo systemctl start bitcoin-core
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u bitcoin-core -n 100
sudo tail -f /var/log/bitcoin_core/bitcoin_core.log

# Check configuration
bitcoin-core --version

# Check permissions
ls -la /etc/bitcoin_core
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8333

# Test connectivity
telnet localhost 8333

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep bitcoin_core)

# Check disk I/O
iotop -p $(pgrep bitcoin_core)

# Check connections
ss -an | grep 8333
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  bitcoin_core:
    image: bitcoin_core:latest
    ports:
      - "8333:8333"
    volumes:
      - ./config:/etc/bitcoin_core
      - ./data:/var/lib/bitcoin_core
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update bitcoin_core

# Debian/Ubuntu
sudo apt update && sudo apt upgrade bitcoin_core

# Arch Linux
sudo pacman -Syu bitcoin_core

# Alpine Linux
apk update && apk upgrade bitcoin_core

# openSUSE
sudo zypper update bitcoin_core

# FreeBSD
pkg update && pkg upgrade bitcoin_core

# Always backup before updates
tar -czf /backup/bitcoin_core-pre-update-$(date +%Y%m%d).tar.gz /etc/bitcoin_core

# Restart after updates
sudo systemctl restart bitcoin-core
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/bitcoin_core

# Clean old logs
find /var/log/bitcoin_core -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/bitcoin_core
```

## Additional Resources

- Official Documentation: https://docs.bitcoin_core.org/
- GitHub Repository: https://github.com/bitcoin_core/bitcoin_core
- Community Forum: https://forum.bitcoin_core.org/
- Best Practices Guide: https://docs.bitcoin_core.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
