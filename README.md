# Zabbix Proxy Docker

Docker Compose para desplegar **Zabbix Proxy 7.0 LTS** utilizando SQLite y autenticación TLS mediante PSK.


## Requisitos

- Docker
- Docker Compose
- Conectividad con el Zabbix Server
- PSK configurada previamente en el Zabbix Server

## Instalación

Clonar el repositorio:

```bash
git clone https://github.com/Psichoxis/zabbix-proxy.git
cd zabbix-proxy
```

Crear la configuración local:

```bash
cp .env.example .env
nano .env
```

Configurar como mínimo:

```dotenv
ZBX_HOSTNAME=ProxyCLIENTE
ZBX_SERVER_HOST=10.25.35.20
ZBX_TLSPSKIDENTITY=ProxyCLIENTE
```

El valor de `ZBX_HOSTNAME` debe coincidir exactamente con el nombre configurado para el proxy en Zabbix Server.

## PSK

Crear el archivo:

```bash
nano zbx_psk.txt
```

Ingresar únicamente la PSK correspondiente al proxy.

El archivo `zbx_psk.txt` se encuentra excluido mediante `.gitignore` y no debe almacenarse en el repositorio.

## Iniciar

```bash
docker compose pull
docker compose up -d
```

Verificar:

```bash
docker ps
docker logs --tail 100 zbx-proxy
```

Para seguir los logs:

```bash
docker logs -f zbx-proxy
```

## Persistencia

La base SQLite se almacena en un volumen administrado por Docker:

```text
zbx-db:/var/lib/zabbix/db_data
```

Esto evita problemas de permisos asociados a bind mounts y Docker `userns-remap`.

No se recomienda montar directamente todo `/var/lib/zabbix`, ya que el directorio contiene archivos y estructuras proporcionadas por la propia imagen de Zabbix.

Para visualizar los volúmenes:

```bash
docker volume ls
```

Para identificar el volumen utilizado:

```bash
docker inspect zbx-proxy
```

## Actualización

La versión de Zabbix está fijada explícitamente en `docker-compose.yml`.

Para aplicar cambios del repositorio:

```bash
git pull
docker compose pull
docker compose up -d
```

Verificar posteriormente:

```bash
docker ps
docker logs --tail 100 zbx-proxy
```

Las actualizaciones de versión deben realizarse modificando explícitamente el tag de la imagen en `docker-compose.yml`.

## Migración desde instalaciones anteriores

Las instalaciones antiguas pueden utilizar:

```yaml
- ./data:/var/lib/zabbix
- ./logs:/var/log/zabbix
```

La configuración actual utiliza un volumen Docker exclusivamente para SQLite y logs mediante stdout.

Al migrar un proxy existente no es necesario conservar la base SQLite local si se acepta que el proxy vuelva a sincronizar su configuración desde Zabbix Server.

Actualizar el repositorio y recrear el proxy:

```bash
docker compose down
git pull
docker compose pull
docker compose up -d
```

No utilizar:

```bash
docker compose down -v
```

salvo que se quiera eliminar intencionalmente la base SQLite almacenada en el volumen.

Una vez comprobado el correcto funcionamiento del proxy, los antiguos directorios `data/` y `logs/` pueden eliminarse.

## Estructura

```text
zabbix-proxy/
├── docker-compose.yml
├── .env.example
├── .env
├── zbx_psk.txt
├── .gitignore
└── README.md
```

`.env` y `zbx_psk.txt` son archivos locales y no deben subirse al repositorio.

## Verificación

Un proxy funcionando correctamente debería mostrar:

```bash
docker ps
```

con el contenedor `zbx-proxy` en estado `Up`.

En los logs debería observarse la sincronización con Zabbix Server:

```text
received configuration data from server
```

## Notas

La red Docker utiliza MTU 1400 para mantener compatibilidad con entornos donde la comunicación con Zabbix Server atraviesa VPNs o túneles.

El límite de archivos abiertos del contenedor se establece en `65536` para permitir una mayor concurrencia de procesos de discovery y evitar limitaciones del valor predeterminado.