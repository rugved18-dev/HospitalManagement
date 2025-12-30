# Deployment Guide

This guide covers deploying the Hospital Patient Management System to production environments.

## Pre-Deployment Checklist

- [ ] All tests passing locally
- [ ] Code reviewed and merged to main branch
- [ ] Environment variables configured for production
- [ ] Database backed up
- [ ] SSL certificates obtained (for HTTPS)
- [ ] Firewall rules configured
- [ ] Server hardware specs verified
- [ ] Monitoring and logging configured
- [ ] Disaster recovery plan in place

## Production Deployment Steps

### 1. Server Setup

#### Recommended Server Specifications

**Minimum:**
- CPU: 2 cores
- RAM: 2GB
- Storage: 20GB SSD
- OS: Linux (Ubuntu 20.04+) or Windows Server 2019+

**Recommended:**
- CPU: 4 cores
- RAM: 4GB
- Storage: 50GB SSD
- OS: Ubuntu 22.04 LTS

#### Operating System Setup

**Ubuntu/Linux:**
```bash
sudo apt update
sudo apt upgrade
sudo apt install curl wget git
```

**Windows Server:**
```powershell
# Use Windows Update for OS updates
# Install chocolatey first:
Set-ExecutionPolicy Bypass -Scope Process
iex ((New-Object System.Net.ServicePointManager).SecurityProtocol = 3072; iex(New-Object Net.ServicePointClient).DownloadString('https://community.chocolatey.org/install.ps1'))
```

### 2. Install Required Software

#### Node.js and npm

**Ubuntu/Linux:**
```bash
curl -fsSL https://deb.nodesource.com/setup_18.x | sudo -E bash -
sudo apt install nodejs
node --version
npm --version
```

**Windows:**
```powershell
choco install nodejs
```

#### IBM DB2

Follow [IBM DB2 Installation Guide](https://www.ibm.com/docs/en/db2) for your OS.

Minimum version: DB2 11.5.x

### 3. Clone and Setup Application

```bash
# Clone repository
git clone https://github.com/your-repo/HospitalMangement-main.git
cd HospitalMangement-main

# Install backend dependencies
cd backend
npm ci --production  # Use ci for reproducible builds

# Install frontend dependencies
cd ../frontend
npm ci --production
```

### 4. Build Frontend

```bash
cd frontend
npm run build

# Output in frontend/dist/
```

### 5. Configure Environment

```bash
# Copy .env.example to .env
cp .env.example .env

# Edit with production values
nano .env  # or use your preferred editor
```

Update the following for production:

```env
# Production Database Connection
DB2_CONN_STRING=DATABASE=HOSPDB;HOSTNAME=prod-db.example.com;PORT=25000;PROTOCOL=TCPIP;UID=produser;PWD=<secure-password>;

# Server Port
PORT=4000

# Environment
NODE_ENV=production

# Frontend URL
FRONTEND_URL=https://your-domain.com
```

### 6. Set Up Database

```bash
# Connect to production DB2
db2 connect to HOSPDB user produser using <password>

# Create tables
db2 -tvf backend/db_scripts/create_table.sql
db2 -tvf backend/db_scripts/create_queue_table.sql

# Verify
db2 "SELECT TABNAME FROM SYSCAT.TABLES WHERE TABNAME IN ('PATIENT_MASTER', 'QUEUE_MASTER')"
```

### 7. Configure Reverse Proxy (Nginx)

Create `/etc/nginx/sites-available/hospital-app`:

```nginx
upstream backend {
    server localhost:4000;
}

server {
    listen 80;
    server_name your-domain.com;
    return 301 https://$server_name$request_uri;
}

server {
    listen 443 ssl http2;
    server_name your-domain.com;

    ssl_certificate /etc/ssl/certs/your-domain.crt;
    ssl_certificate_key /etc/ssl/private/your-domain.key;
    ssl_protocols TLSv1.2 TLSv1.3;
    ssl_ciphers HIGH:!aNULL:!MD5;

    # Frontend
    location / {
        root /var/www/hospital-app/frontend/dist;
        try_files $uri $uri/ /index.html;
    }

    # Backend API
    location /api {
        proxy_pass http://backend;
        proxy_http_version 1.1;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'upgrade';
        proxy_set_header Host $host;
        proxy_cache_bypass $http_upgrade;
    }

    # Socket.io
    location /socket.io {
        proxy_pass http://backend/socket.io;
        proxy_http_version 1.1;
        proxy_buffering off;
        proxy_set_header Upgrade $http_upgrade;
        proxy_set_header Connection 'Upgrade';
        proxy_set_header Host $host;
    }

    # Security headers
    add_header X-Frame-Options "SAMEORIGIN" always;
    add_header X-Content-Type-Options "nosniff" always;
    add_header X-XSS-Protection "1; mode=block" always;
    add_header Referrer-Policy "no-referrer-when-downgrade" always;
}
```

Enable the site:

```bash
sudo ln -s /etc/nginx/sites-available/hospital-app /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```

### 8. Set Up Process Manager (PM2)

```bash
sudo npm install -g pm2

# Create ecosystem.config.js in project root
cat > ecosystem.config.js << EOF
module.exports = {
  apps: [{
    name: 'hospital-backend',
    script: './backend/src/index.js',
    cwd: '/path/to/HospitalMangement-main',
    instances: 'max',
    exec_mode: 'cluster',
    env: {
      NODE_ENV: 'production'
    },
    error_file: './logs/error.log',
    out_file: './logs/out.log',
    log_date_format: 'YYYY-MM-DD HH:mm:ss Z'
  }]
};
EOF

# Start with PM2
pm2 start ecosystem.config.js

# Enable startup on system reboot
pm2 startup
pm2 save
```

### 9. Set Up SSL Certificate (Let's Encrypt)

```bash
# Install Certbot
sudo apt install certbot python3-certbot-nginx

# Create certificate
sudo certbot certonly --nginx -d your-domain.com

# Auto-renewal (already configured)
sudo systemctl enable certbot.timer
```

### 10. Configure Logging

Create logging directory:

```bash
mkdir -p /var/log/hospital-app
sudo chown -R app-user:app-group /var/log/hospital-app
```

### 11. Set Up Firewall

**Ubuntu UFW:**

```bash
sudo ufw enable
sudo ufw default deny incoming
sudo ufw default allow outgoing
sudo ufw allow 22/tcp  # SSH
sudo ufw allow 80/tcp  # HTTP
sudo ufw allow 443/tcp # HTTPS
```

**Windows Firewall:**

```powershell
New-NetFirewallRule -DisplayName "Allow HTTP" -Direction Inbound -LocalPort 80 -Protocol TCP -Action Allow
New-NetFirewallRule -DisplayName "Allow HTTPS" -Direction Inbound -LocalPort 443 -Protocol TCP -Action Allow
```

## Database Backup & Recovery

### Automated Backups

```bash
# Create backup script
cat > /usr/local/bin/backup-hospital-db.sh << 'EOF'
#!/bin/bash
BACKUP_DIR="/var/backups/db2"
DATE=$(date +%Y%m%d_%H%M%S)
db2 connect to HOSPDB
db2 backup db HOSPDB to $BACKUP_DIR on $DATE
db2 disconnect HOSPDB
EOF

chmod +x /usr/local/bin/backup-hospital-db.sh

# Schedule via cron (daily at 2 AM)
(crontab -l; echo "0 2 * * * /usr/local/bin/backup-hospital-db.sh") | crontab -
```

### Restore from Backup

```bash
db2 restore db HOSPDB from /var/backups/db2 taken at <timestamp>
```

## Monitoring & Logging

### PM2 Monitoring

```bash
# View real-time logs
pm2 logs hospital-backend

# View status
pm2 status

# Restart on file change
pm2 reload hospital-backend
```

### Application Health Check

```bash
# Check API health
curl https://your-domain.com/health

# Expected response:
# {"success": true, "message": "Hospital Patient Visit Tracking System is running"}
```

### Server Monitoring

**Install monitoring tools:**

```bash
sudo apt install htop
sudo apt install nethogs
```

### Log Aggregation (Optional)

Consider setting up ELK Stack (Elasticsearch, Logstash, Kibana) for centralized logging in production.

## Performance Optimization

### Database Optimization

```sql
-- Add indexes for common queries
CREATE INDEX IDX_PATIENT_NAME ON PATIENT_MASTER(NAME);
CREATE INDEX IDX_QUEUE_STATUS ON QUEUE_MASTER(STATUS);
CREATE INDEX IDX_QUEUE_DEPT ON QUEUE_MASTER(DEPARTMENT);

-- Check index usage
SELECT * FROM SYSCAT.INDEXES WHERE TABNAME = 'PATIENT_MASTER';
```

### Frontend Optimization

- Enable gzip compression in Nginx
- Set appropriate cache headers
- Use CDN for static assets
- Minify and bundle assets (already done by Vite)

### Backend Optimization

- Use connection pooling (DB2 default)
- Implement caching for frequent queries
- Use pagination for large result sets
- Monitor and optimize slow queries

## Security Hardening

### Backend Security

```javascript
// Ensure these are configured in production:
- HTTPS only
- Secure CORS headers
- Rate limiting
- Input validation
- SQL injection prevention (parameterized queries)
- XSS protection
```

### Database Security

```bash
# Change default database password
db2 UPDATE DBM CFG USING SYSADM_GROUP db2iadm1

# Enable audit logging
db2 UPDATE DB CFG FOR HOSPDB USING AUDIT_BUF_SZ 100

# Set appropriate permissions
db2 GRANT DBADM ON DATABASE TO USER produser
```

### OS Security

```bash
# Disable unnecessary services
sudo systemctl disable cups
sudo systemctl disable avahi-daemon

# Keep system updated
sudo apt upgrade -y

# Configure automatic security updates
sudo apt install unattended-upgrades
sudo systemctl enable unattended-upgrades
```

## Scaling Considerations

### Horizontal Scaling

For high traffic, consider:

1. **Load Balancer**: Deploy multiple backend instances
2. **Database Replication**: Set up DB2 replication
3. **CDN**: Use CDN for static assets
4. **Session Management**: Implement Redis for session storage

### Vertical Scaling

- Increase server CPU and RAM
- Upgrade to faster storage (SSD)
- Optimize database indexes

## Health Checks

### Regular Monitoring

```bash
# Database connectivity
node test-db.js

# API responsiveness
curl -I https://your-domain.com/health

# Real-time functionality
curl https://your-domain.com/socket.io/

# Check logs
pm2 logs hospital-backend
```

## Disaster Recovery

### Backup Strategy

- Daily database backups
- Weekly full system backups
- Off-site backup storage
- Backup verification monthly

### Recovery Plan

1. Restore latest backup
2. Verify database integrity
3. Test application functionality
4. Monitor for issues

```bash
# Test recovery (on staging environment)
db2 restore db HOSPDB from /var/backups/db2 taken at <timestamp>
npm test
curl https://staging.your-domain.com/health
```

## Troubleshooting Production Issues

### High CPU Usage

```bash
# Check database locks
db2 list applications

# Check slow queries
db2 "SELECT * FROM TABLE(MON_GET_PKG_CACHE_STMT(NULL, NULL, NULL, -2))"
```

### Database Connection Issues

```bash
# Check connection string
cat .env | grep DB2_CONN_STRING

# Test connectivity
db2 connect to HOSPDB
```

### WebSocket Issues

```bash
# Check Socket.io connection
curl https://your-domain.com/socket.io/?transport=websocket

# Monitor connections
netstat -an | grep :4000
```

## Version Updates

### Update Procedure

```bash
# 1. Stop application
pm2 stop hospital-backend

# 2. Backup database
db2 backup db HOSPDB

# 3. Pull latest code
git pull origin main

# 4. Update dependencies
npm install --production

# 5. Rebuild frontend
cd frontend && npm run build && cd ..

# 6. Run migrations (if any)
# Custom migration scripts here

# 7. Restart application
pm2 restart hospital-backend

# 8. Verify
curl https://your-domain.com/health
```

## Support & Maintenance

### Monthly Tasks

- Review logs and error rates
- Check database performance
- Verify backup integrity
- Update security patches

### Quarterly Tasks

- Performance optimization review
- Security audit
- Capacity planning
- Documentation updates

## Contact

For deployment issues or questions, open an issue on GitHub or contact system administrators.

---

**Last Updated**: December 30, 2024

**Version**: 1.0.0
