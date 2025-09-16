# mysqld-exporter Installation Guide

mysqld-exporter is a free and open-source Prometheus exporter for MySQL server metrics. MySQL Exporter extracts metrics from MySQL/MariaDB servers for Prometheus monitoring, providing database performance insights

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
  - RAM: 128MB minimum
  - Storage: 50MB for installation
  - Network: MySQL and HTTP access
- **Operating System**: 
  - Linux: Any modern distribution (RHEL, Debian, Ubuntu, CentOS, Fedora, Arch, Alpine, openSUSE)
  - macOS: 10.14+ (Mojave or newer)
  - Windows: Windows Server 2016+ or Windows 10
  - FreeBSD: 11.0+
- **Network Requirements**:
  - Port 9104 (default mysqld-exporter port)
  - MySQL connection
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

# Install mysqld-exporter
sudo dnf install -y prometheus-mysqld-exporter

# Enable and start service
sudo systemctl enable --now mysqld_exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9104/tcp
sudo firewall-cmd --reload

# Verify installation
mysqld_exporter --version
```

### Debian/Ubuntu

```bash
# Update package index
sudo apt update

# Install mysqld-exporter
sudo apt install -y prometheus-mysqld-exporter

# Enable and start service
sudo systemctl enable --now mysqld_exporter

# Configure firewall
sudo ufw allow 9104

# Verify installation
mysqld_exporter --version
```

### Arch Linux

```bash
# Install mysqld-exporter
sudo pacman -S prometheus-mysqld-exporter

# Enable and start service
sudo systemctl enable --now mysqld_exporter

# Verify installation
mysqld_exporter --version
```

### Alpine Linux

```bash
# Install mysqld-exporter
apk add --no-cache prometheus-mysqld-exporter

# Enable and start service
rc-update add mysqld_exporter default
rc-service mysqld_exporter start

# Verify installation
mysqld_exporter --version
```

### openSUSE/SLES

```bash
# Install mysqld-exporter
sudo zypper install -y prometheus-mysqld-exporter

# Enable and start service
sudo systemctl enable --now mysqld_exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9104/tcp
sudo firewall-cmd --reload

# Verify installation
mysqld_exporter --version
```

### macOS

```bash
# Using Homebrew
brew install prometheus-mysqld-exporter

# Start service
brew services start prometheus-mysqld-exporter

# Verify installation
mysqld_exporter --version
```

### FreeBSD

```bash
# Using pkg
pkg install prometheus-mysqld-exporter

# Enable in rc.conf
echo 'mysqld_exporter_enable="YES"' >> /etc/rc.conf

# Start service
service mysqld_exporter start

# Verify installation
mysqld_exporter --version
```

### Windows

```bash
# Using Chocolatey
choco install prometheus-mysqld-exporter

# Or using Scoop
scoop install prometheus-mysqld-exporter

# Verify installation
mysqld_exporter --version
```

## Initial Configuration

### Basic Configuration

```bash
# Create configuration directory
sudo mkdir -p /etc/prometheus-mysqld-exporter

# Set up basic configuration
# See official documentation for detailed configuration options

# Test configuration
mysqld_exporter --version
```

## 5. Service Management

### systemd (RHEL, Debian, Ubuntu, Arch, openSUSE)

```bash
# Enable service
sudo systemctl enable mysqld_exporter

# Start service
sudo systemctl start mysqld_exporter

# Stop service
sudo systemctl stop mysqld_exporter

# Restart service
sudo systemctl restart mysqld_exporter

# Check status
sudo systemctl status mysqld_exporter

# View logs
sudo journalctl -u mysqld_exporter -f
```

### OpenRC (Alpine Linux)

```bash
# Enable service
rc-update add mysqld_exporter default

# Start service
rc-service mysqld_exporter start

# Stop service
rc-service mysqld_exporter stop

# Restart service
rc-service mysqld_exporter restart

# Check status
rc-service mysqld_exporter status
```

### rc.d (FreeBSD)

```bash
# Enable in /etc/rc.conf
echo 'mysqld_exporter_enable="YES"' >> /etc/rc.conf

# Start service
service mysqld_exporter start

# Stop service
service mysqld_exporter stop

# Restart service
service mysqld_exporter restart

# Check status
service mysqld_exporter status
```

### launchd (macOS)

```bash
# Using Homebrew services
brew services start prometheus-mysqld-exporter
brew services stop prometheus-mysqld-exporter
brew services restart prometheus-mysqld-exporter

# Check status
brew services list | grep prometheus-mysqld-exporter
```

### Windows Service Manager

```powershell
# Start service
net start mysqld_exporter

# Stop service
net stop mysqld_exporter

# Using PowerShell
Start-Service mysqld_exporter
Stop-Service mysqld_exporter
Restart-Service mysqld_exporter

# Check status
Get-Service mysqld_exporter
```

## Advanced Configuration

See the official documentation for advanced configuration options.

## Reverse Proxy Setup

### nginx Configuration

```nginx
upstream prometheus-mysqld-exporter_backend {
    server 127.0.0.1:9104;
}

server {
    listen 80;
    server_name prometheus-mysqld-exporter.example.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name prometheus-mysqld-exporter.example.com;

    ssl_certificate /etc/ssl/certs/prometheus-mysqld-exporter.example.com.crt;
    ssl_certificate_key /etc/ssl/private/prometheus-mysqld-exporter.example.com.key;

    location / {
        proxy_pass http://prometheus-mysqld-exporter_backend;
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
    ServerName prometheus-mysqld-exporter.example.com
    Redirect permanent / https://prometheus-mysqld-exporter.example.com/
</VirtualHost>

<VirtualHost *:443>
    ServerName prometheus-mysqld-exporter.example.com
    
    SSLEngine on
    SSLCertificateFile /etc/ssl/certs/prometheus-mysqld-exporter.example.com.crt
    SSLCertificateKeyFile /etc/ssl/private/prometheus-mysqld-exporter.example.com.key
    
    ProxyRequests Off
    ProxyPreserveHost On
    
    ProxyPass / http://127.0.0.1:9104/
    ProxyPassReverse / http://127.0.0.1:9104/
</VirtualHost>
```

### HAProxy Configuration

```haproxy
frontend prometheus-mysqld-exporter_frontend
    bind *:80
    bind *:443 ssl crt /etc/ssl/certs/prometheus-mysqld-exporter.pem
    redirect scheme https if !{ ssl_fc }
    default_backend prometheus-mysqld-exporter_backend

backend prometheus-mysqld-exporter_backend
    balance roundrobin
    server prometheus-mysqld-exporter1 127.0.0.1:9104 check
```

## Security Configuration

### Basic Security Setup

```bash
# Set appropriate permissions
sudo chown -R prometheus-mysqld-exporter:prometheus-mysqld-exporter /etc/prometheus-mysqld-exporter
sudo chmod 750 /etc/prometheus-mysqld-exporter

# Configure firewall
sudo firewall-cmd --permanent --add-port=9104/tcp
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
sudo systemctl status mysqld_exporter

# View logs
sudo journalctl -u mysqld_exporter -f

# Monitor resource usage
top -p $(pgrep prometheus-mysqld-exporter)
```

## 9. Backup and Restore

### Backup Script

```bash
#!/bin/bash
# Basic backup script
BACKUP_DIR="/backup/prometheus-mysqld-exporter"
DATE=$(date +%Y%m%d_%H%M%S)

mkdir -p "$BACKUP_DIR"
tar -czf "$BACKUP_DIR/prometheus-mysqld-exporter-backup-$DATE.tar.gz" /etc/prometheus-mysqld-exporter /var/lib/prometheus-mysqld-exporter

echo "Backup completed: $BACKUP_DIR/prometheus-mysqld-exporter-backup-$DATE.tar.gz"
```

### Restore Procedure

```bash
# Stop service
sudo systemctl stop mysqld_exporter

# Restore from backup
tar -xzf /backup/prometheus-mysqld-exporter/prometheus-mysqld-exporter-backup-*.tar.gz -C /

# Start service
sudo systemctl start mysqld_exporter
```

## 6. Troubleshooting

### Common Issues

1. **Service won't start**:
```bash
# Check logs
sudo journalctl -u mysqld_exporter -n 100
sudo tail -f /var/log/prometheus-mysqld-exporter/prometheus-mysqld-exporter.log

# Check configuration
mysqld_exporter --version

# Check permissions
ls -la /etc/prometheus-mysqld-exporter
```

2. **Connection issues**:
```bash
# Check if service is listening
sudo ss -tlnp | grep 9104

# Test connectivity
telnet localhost 9104

# Check firewall
sudo firewall-cmd --list-all
```

3. **Performance issues**:
```bash
# Check resource usage
top -p $(pgrep prometheus-mysqld-exporter)

# Check disk I/O
iotop -p $(pgrep prometheus-mysqld-exporter)

# Check connections
ss -an | grep 9104
```

## Integration Examples

### Docker Compose Example

```yaml
version: '3.8'
services:
  prometheus-mysqld-exporter:
    image: prometheus-mysqld-exporter:latest
    ports:
      - "9104:9104"
    volumes:
      - ./config:/etc/prometheus-mysqld-exporter
      - ./data:/var/lib/prometheus-mysqld-exporter
    restart: unless-stopped
```

## Maintenance

### Update Procedures

```bash
# RHEL/CentOS/Rocky/AlmaLinux
sudo dnf update prometheus-mysqld-exporter

# Debian/Ubuntu
sudo apt update && sudo apt upgrade prometheus-mysqld-exporter

# Arch Linux
sudo pacman -Syu prometheus-mysqld-exporter

# Alpine Linux
apk update && apk upgrade prometheus-mysqld-exporter

# openSUSE
sudo zypper update prometheus-mysqld-exporter

# FreeBSD
pkg update && pkg upgrade prometheus-mysqld-exporter

# Always backup before updates
tar -czf /backup/prometheus-mysqld-exporter-pre-update-$(date +%Y%m%d).tar.gz /etc/prometheus-mysqld-exporter

# Restart after updates
sudo systemctl restart mysqld_exporter
```

### Regular Maintenance

```bash
# Log rotation
sudo logrotate -f /etc/logrotate.d/prometheus-mysqld-exporter

# Clean old logs
find /var/log/prometheus-mysqld-exporter -name "*.log" -mtime +30 -delete

# Check disk usage
du -sh /var/lib/prometheus-mysqld-exporter
```

## Additional Resources

- Official Documentation: https://docs.prometheus-mysqld-exporter.org/
- GitHub Repository: https://github.com/prometheus-mysqld-exporter/prometheus-mysqld-exporter
- Community Forum: https://forum.prometheus-mysqld-exporter.org/
- Best Practices Guide: https://docs.prometheus-mysqld-exporter.org/best-practices

---

**Note:** This guide is part of the [HowToMgr](https://howtomgr.github.io) collection. Always refer to official documentation for the most up-to-date information.
