# gobetween Installation Guide

gobetween is a free and open-source load balancer. Gobetween provides modern, minimalistic load balancer

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
  - CPU: 1 core minimum
  - RAM: 256MB minimum
  - Storage: 100MB for config
  - Network: Various protocols
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 8888 (default gobetween port)
  - API on 8889
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

# Install gobetween
sudo dnf install -y gobetween

# Enable and start service
sudo systemctl enable --now gobetween

# Configure firewall
sudo firewall-cmd --permanent --add-port=8888/tcp
sudo firewall-cmd --reload

# Verify installation
gobetween --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install gobetween
sudo apt install -y gobetween

# Enable and start service
sudo systemctl enable --now gobetween

# Configure firewall
sudo ufw allow 8888

# Verify installation
gobetween --version
```

### Arch Linux

```bash
# Install gobetween
sudo pacman -S gobetween

# Enable and start service
sudo systemctl enable --now gobetween

# Verify installation
gobetween --version
```

### Alpine Linux

```bash
# Install gobetween
apk add --no-cache gobetween

# Enable and start service
rc-update add gobetween default
rc-service gobetween start

# Verify installation
gobetween --version
```

### openSUSE/SLES

```bash
# Install gobetween
sudo zypper install -y gobetween

# Enable and start service
sudo systemctl enable --now gobetween

# Configure firewall
sudo firewall-cmd --permanent --add-port=8888/tcp
sudo firewall-cmd --reload

# Verify installation
gobetween --version
```

### macOS

```bash
# Using Homebrew
brew install gobetween

# Start service
brew services start gobetween

# Verify installation
gobetween --version
```

### FreeBSD

```bash
# Using pkg
pkg install gobetween

# Enable in rc.conf
echo 'gobetween_enable="YES"' >> /etc/rc.conf

# Start service
service gobetween start

# Verify installation
gobetween --version
```

### Windows

```bash
# Using Chocolatey
choco install gobetween

# Or using Scoop
scoop install gobetween

# Verify installation
gobetween --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/gobetween

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
gobetween --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable gobetween

# Start service
sudo systemctl start gobetween

# Stop service
sudo systemctl stop gobetween

# Restart service
sudo systemctl restart gobetween

# Check status
sudo systemctl status gobetween

# View logs
sudo journalctl -u gobetween -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add gobetween default

# Start service
rc-service gobetween start

# Stop service
rc-service gobetween stop

# Restart service
rc-service gobetween restart

# Check status
rc-service gobetween status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'gobetween_enable="YES"' >> /etc/rc.conf

# Start service
service gobetween start

# Stop service
service gobetween stop

# Restart service
service gobetween restart

# Check status
service gobetween status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start gobetween
brew services stop gobetween
brew services restart gobetween

# Check status
brew services list | grep gobetween
```

### Windows Service Manager

```powershell
# Start service
net start gobetween

# Stop service
net stop gobetween

# Using PowerShell
Start-Service gobetween
Stop-Service gobetween
Restart-Service gobetween

# Check status
Get-Service gobetween
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream gobetween_backend {
    server 127.0.0.1:8888;
}

server {
    listen 80;
    server_name gobetween.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name gobetween.example.com;

    ssl_certificate /etc/ssl/certs/gobetween.example.com.crt;
    ssl_certificate_key /etc/ssl/private/gobetween.example.com.key;

    location / {
        proxy_pass http://gobetween_backend;
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
    ServerName gobetween.example.com
    Redirect permanent / https://gobetween.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName gobetween.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/gobetween.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/gobetween.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:8888/
    ProxyPassReverse / http://127.0.0.1:8888/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend gobetween_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/gobetween.pem
    redirect scheme https if !{ ssl_fc }
    default_backend gobetween_backend

backend gobetween_backend
    balance roundrobin
    server gobetween1 127.0.0.1:8888 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R gobetween:gobetween /etc/gobetween
sudo chmod 750 /etc/gobetween

# Configure firewall
sudo firewall-cmd --permanent --add-port=8888/tcp
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
sudo systemctl status gobetween

# View logs
sudo journalctl -u gobetween -f

# Monitor resource usage
top -p $(pgrep gobetween)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/gobetween"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/gobetween-backup-$DATE.tar.gz" /etc/gobetween /var/lib/gobetween

echo "Backup completed: $BACKUP_DIR/gobetween-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop gobetween

# Restore from backup
tar -xzf /backup/gobetween/gobetween-backup-*.tar.gz -C /

# Start service
sudo systemctl start gobetween
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u gobetween -n 100
sudo tail -f /var/log/gobetween/gobetween.log

# Check configuration
gobetween --version

# Check permissions
ls -la /etc/gobetween
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 8888

# Test connectivity
telnet localhost 8888

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep gobetween)

# Check disk I/O
iotop -p $(pgrep gobetween)

# Check connections
ss -an | grep 8888
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  gobetween:
    image: gobetween:latest
    ports:
      - "8888:8888"
    volumes:
      - ./config:/etc/gobetween
      - ./data:/var/lib/gobetween
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update gobetween

# Debian/Ubuntu
sudo apt update && sudo apt upgrade gobetween

# Arch Linux
sudo pacman -Syu gobetween

# Alpine Linux
apk update && apk upgrade gobetween

# openSUSE
sudo zypper update gobetween

# FreeBSD
pkg update && pkg upgrade gobetween

# Always backup before updates
tar -czf /backup/gobetween-pre-update-$(date +%Y%m%d).tar.gz /etc/gobetween

# Restart after updates
sudo systemctl restart gobetween
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/gobetween

# Clean old logs
find /var/log/gobetween -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/gobetween
```

## Additional Resources

- Official Documentation: https://docs.gobetween.org/
- GitHub Repository: https://github.com/gobetween/gobetween
- Community Forum: https://forum.gobetween.org/
- Best Practices Guide: https://docs.gobetween.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
