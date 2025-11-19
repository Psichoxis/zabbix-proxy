# Zabbix Proxy 6.4 (Dockerizado)

Deploy rápido de Zabbix Proxy 6.4 usando SQLite3 y Docker.

## Uso general ##
Clonar repositorio:
mkdir -p /u/docker/zabbix-proxy && cd /u/docker/zabbix-proxy
git clone https://github.com/Psichoxis/zabbix-proxy.git

Ajuste de variables:
cp .env.example .env
nano .env  # ajustar hostname, server, etc.

Generación de PSK:
openssl rand -hex 32 > zbx_psk.txt
chmod 644 zbx_psk.txt

Generación de carpetas:
mkdir -p data/db_data
mkdir -p logs
chmod -R 777 data logs

Iniciar servicio:
docker compose up -d
