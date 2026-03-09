# Deploy Golang
## Install Golang
```
sudo apt-get update
sudo apt-get install golang-go
```
## Check version
```
go version
```
## Build Golang
```
go build [nameFile]
```
# Run with systemd
## create systemd
```
nano /etc/systemd/system/[nameService].service
```
## inside systemd
```
[Unit]
Description=MyApp Go Service
After=network.target

[Service]
Type=simple
User=www-data
Group=www-data
WorkingDirectory=/[directoryGolangApps]
ExecStart=/[directoryGolangApps]/myapp
Restart=always
RestartSec=5

Environment=PORT=8081

CPUQuota=50%
MemoryHigh=384M
MemoryMax=512M
TasksMax=200

StandardOutput=append:/var/log/golang/[nameService].app.log
StandardError=append:/var/log/golang/[nameService].error.log

NoNewPrivileges=yes
PrivateTmp=yes
ProtectSystem=full
ProtectHome=yes

[Install]
WantedBy=multi-user.target
```
## make dir log
```
mkdir /var/log/golang
```
## make configurate after 24hours archived
```
nano /etc/logrotate.d/golang 
```
## inside configurate archived
```
/var/log/goapps/*.app.log /var/log/goapps/*.error.log {
    daily
    rotate 14
    compress
    missingok
    notifempty
    copytruncate
    create 0644 www-data www-data
}
```
# Configurate Nginx
## conf
```
server {
    listen 80;
    server_name [domain];

    location / {
        proxy_pass http://127.0.0.1:[port];
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
        proxy_http_version 1.1;
        proxy_set_header Connection "";
    }
}
```
