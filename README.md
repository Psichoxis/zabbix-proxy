# Zabbix Proxy 6.4 (Dockerizado)

Deploy rápido de Zabbix Proxy 6.4 usando SQLite3 y Docker.

## Uso
mkdir -p /u/docker/docker-zabbixproxy && cd /u/docker/docker-zabbixproxy
git clone https://github.com/Psichoxis/zabbix-proxy.git

cp .env.example .env
nano .env  # ajustar hostname, server, etc.

openssl rand -hex 32 > zbx_psk.txt  # generar PSK
chmod 600 zbx_psk.txt

docker compose up -d
