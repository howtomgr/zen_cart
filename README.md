# zen-cart Installation Guide

zen-cart is a free and open-source e-commerce software. Zen Cart provides user-friendly e-commerce software

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
  - RAM: 1GB minimum
  - Storage: 5GB for data
  - Network: HTTP/HTTPS
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 80 (default zen-cart port)
  - None
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

# Install zen-cart
sudo dnf install -y zen_cart

# Enable and start service
sudo systemctl enable --now zen-cart

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
zen-cart --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install zen-cart
sudo apt install -y zen_cart

# Enable and start service
sudo systemctl enable --now zen-cart

# Configure firewall
sudo ufw allow 80

# Verify installation
zen-cart --version
```

### Arch Linux

```bash
# Install zen-cart
sudo pacman -S zen_cart

# Enable and start service
sudo systemctl enable --now zen-cart

# Verify installation
zen-cart --version
```

### Alpine Linux

```bash
# Install zen-cart
apk add --no-cache zen_cart

# Enable and start service
rc-update add zen-cart default
rc-service zen-cart start

# Verify installation
zen-cart --version
```

### openSUSE/SLES

```bash
# Install zen-cart
sudo zypper install -y zen_cart

# Enable and start service
sudo systemctl enable --now zen-cart

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
sudo firewall-cmd --reload

# Verify installation
zen-cart --version
```

### macOS

```bash
# Using Homebrew
brew install zen_cart

# Start service
brew services start zen_cart

# Verify installation
zen-cart --version
```

### FreeBSD

```bash
# Using pkg
pkg install zen_cart

# Enable in rc.conf
echo 'zen-cart_enable="YES"' >> /etc/rc.conf

# Start service
service zen-cart start

# Verify installation
zen-cart --version
```

### Windows

```bash
# Using Chocolatey
choco install zen_cart

# Or using Scoop
scoop install zen_cart

# Verify installation
zen-cart --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/zen_cart

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
zen-cart --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable zen-cart

# Start service
sudo systemctl start zen-cart

# Stop service
sudo systemctl stop zen-cart

# Restart service
sudo systemctl restart zen-cart

# Check status
sudo systemctl status zen-cart

# View logs
sudo journalctl -u zen-cart -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add zen-cart default

# Start service
rc-service zen-cart start

# Stop service
rc-service zen-cart stop

# Restart service
rc-service zen-cart restart

# Check status
rc-service zen-cart status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'zen-cart_enable="YES"' >> /etc/rc.conf

# Start service
service zen-cart start

# Stop service
service zen-cart stop

# Restart service
service zen-cart restart

# Check status
service zen-cart status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start zen_cart
brew services stop zen_cart
brew services restart zen_cart

# Check status
brew services list | grep zen_cart
```

### Windows Service Manager

```powershell
# Start service
net start zen-cart

# Stop service
net stop zen-cart

# Using PowerShell
Start-Service zen-cart
Stop-Service zen-cart
Restart-Service zen-cart

# Check status
Get-Service zen-cart
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream zen_cart_backend {
    server 127.0.0.1:80;
}

server {
    listen 80;
    server_name zen_cart.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name zen_cart.example.com;

    ssl_certificate /etc/ssl/certs/zen_cart.example.com.crt;
    ssl_certificate_key /etc/ssl/private/zen_cart.example.com.key;

    location / {
        proxy_pass http://zen_cart_backend;
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
    ServerName zen_cart.example.com
    Redirect permanent / https://zen_cart.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName zen_cart.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/zen_cart.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/zen_cart.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:80/
    ProxyPassReverse / http://127.0.0.1:80/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend zen_cart_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/zen_cart.pem
    redirect scheme https if !{ ssl_fc }
    default_backend zen_cart_backend

backend zen_cart_backend
    balance roundrobin
    server zen_cart1 127.0.0.1:80 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R zen_cart:zen_cart /etc/zen_cart
sudo chmod 750 /etc/zen_cart

# Configure firewall
sudo firewall-cmd --permanent --add-port=80/tcp
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
sudo systemctl status zen-cart

# View logs
sudo journalctl -u zen-cart -f

# Monitor resource usage
top -p $(pgrep zen_cart)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/zen_cart"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/zen_cart-backup-$DATE.tar.gz" /etc/zen_cart /var/lib/zen_cart

echo "Backup completed: $BACKUP_DIR/zen_cart-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop zen-cart

# Restore from backup
tar -xzf /backup/zen_cart/zen_cart-backup-*.tar.gz -C /

# Start service
sudo systemctl start zen-cart
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u zen-cart -n 100
sudo tail -f /var/log/zen_cart/zen_cart.log

# Check configuration
zen-cart --version

# Check permissions
ls -la /etc/zen_cart
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 80

# Test connectivity
telnet localhost 80

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep zen_cart)

# Check disk I/O
iotop -p $(pgrep zen_cart)

# Check connections
ss -an | grep 80
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  zen_cart:
    image: zen_cart:latest
    ports:
      - "80:80"
    volumes:
      - ./config:/etc/zen_cart
      - ./data:/var/lib/zen_cart
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update zen_cart

# Debian/Ubuntu
sudo apt update && sudo apt upgrade zen_cart

# Arch Linux
sudo pacman -Syu zen_cart

# Alpine Linux
apk update && apk upgrade zen_cart

# openSUSE
sudo zypper update zen_cart

# FreeBSD
pkg update && pkg upgrade zen_cart

# Always backup before updates
tar -czf /backup/zen_cart-pre-update-$(date +%Y%m%d).tar.gz /etc/zen_cart

# Restart after updates
sudo systemctl restart zen-cart
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/zen_cart

# Clean old logs
find /var/log/zen_cart -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/zen_cart
```

## Additional Resources

- Official Documentation: https://docs.zen_cart.org/
- GitHub Repository: https://github.com/zen_cart/zen_cart
- Community Forum: https://forum.zen_cart.org/
- Best Practices Guide: https://docs.zen_cart.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
