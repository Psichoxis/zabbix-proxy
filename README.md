# Zabbix Proxy 7 (Dockerizado)

Deploy rápido de Zabbix Proxy 7.0 usando SQLite3 y Docker.

## Uso general ##
Clonar repositorio:
```bash
git clone https://github.com/lunixsrl/zabbix-proxy.git  && cd zabbix-proxy
```

Ajuste de variables:
```bash
cp .env.example .env
nano .env  # ajustar hostname, server, etc.
```

Generación de PSK:
```bash
openssl rand -hex 32 > zbx_psk.txt
chmod 644 zbx_psk.txt
```
Generación de carpetas:
```bash
mkdir -p data/db_data
mkdir -p logs
chmod -R 777 data logs
```
Iniciar servicio:
```bash
docker compose up -d
```

### **Autores** ✒️

* **Poli** - [Pablo Rodriguez](https://github.com/Psichoxis)
