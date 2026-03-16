# VPN Personal con WireGuard (wg-easy)

VPN personal usando WireGuard con interfaz web, lista para desplegar en Coolify.

## Qué incluye

- **WireGuard** — VPN rápida y moderna
- **wg-easy** — Interfaz web para gestionar clientes (crear, eliminar, ver QR)
- Estadísticas de tráfico en tiempo real

## Despliegue en Coolify

### 1. Crear el proyecto en Coolify

1. En Coolify, andá a **Projects** → **New Project**
2. Elegí **Docker Compose** como tipo de recurso
3. Conectá tu repositorio de Git (o pegá el `docker-compose.yml` directamente)

### 2. Generar el hash de contraseña

Antes de desplegar, necesitás generar el hash de tu contraseña. Corré esto en tu VPS:

```bash
docker run -it ghcr.io/wg-easy/wg-easy wgpw 'TU_CONTRASEÑA_SEGURA'
```

Copiá el resultado. **Importante:** reemplazá cada `$` por `$$` cuando lo pegues en las variables de entorno.

### 3. Configurar variables de entorno en Coolify

En la configuración del servicio en Coolify, agregá estas variables:

| Variable | Valor | Requerido |
|---|---|---|
| `WG_HOST` | IP pública de tu VPS o dominio | Sí |
| `PASSWORD_HASH` | El hash generado en el paso 2 (con `$$`) | Sí |
| `WG_PORT` | `51820` | No (default) |
| `UI_PORT` | `51821` | No (default) |
| `WG_DEFAULT_DNS` | `1.1.1.1, 8.8.8.8` | No (default) |
| `WG_ALLOWED_IPS` | `0.0.0.0/0, ::/0` | No (default) |

### 4. Abrir puertos en el firewall

Asegurate de que estos puertos estén abiertos en tu VPS:

```bash
# Puerto WireGuard (UDP)
sudo ufw allow 51820/udp

# Puerto UI web (TCP) — solo si no usás proxy reverso
sudo ufw allow 51821/tcp
```

### 5. Desplegar

Dale click a **Deploy** en Coolify y esperá a que levante.

## Conectar desde Android

1. Instalá **WireGuard** desde [Google Play Store](https://play.google.com/store/apps/details?id=com.wireguard.android)
2. Abrí la UI web: `http://TU_IP:51821`
3. Logueate con tu contraseña
4. Creá un nuevo cliente (ponele un nombre como "mi-android")
5. Escaneá el código QR desde la app de WireGuard en tu celular
6. Activá la conexión y listo

## Conectar desde PC

1. Descargá [WireGuard para Windows/Mac/Linux](https://www.wireguard.com/install/)
2. Desde la UI web, creá un cliente y descargá el archivo `.conf`
3. Importá el archivo en la app de WireGuard
4. Conectá

## Seguridad

- Usá una contraseña fuerte para la UI web
- Considerá poner la UI web detrás de un proxy reverso con HTTPS (Coolify puede hacer esto automáticamente)
- Podés restringir el acceso a la UI web solo a la VPN cerrando el puerto 51821 externamente después de configurar tus clientes

## Troubleshooting

- **No conecta:** Verificá que el puerto 51820/UDP esté abierto en el firewall de tu VPS y en el panel de tu proveedor
- **UI no carga:** Verificá que el puerto 51821/TCP esté abierto
- **Lento:** Probá cambiar los DNS a `1.1.1.1` o `8.8.8.8`
