# Instalación local — fase 1

Este documento conserva la descripción de la primera fase. Para instalar la aplicación completa, usa `sh scripts/start.sh`; también instala Drupal Commerce y configura la tienda. Consulta [la tienda actual](manual-de-usuario.md#tienda-commerce) y [su guía de uso](manual-de-usuario.md#guia-tienda).

Dirigido a estudiantes y docentes. Resultado buscado: Drupal en español con aviso académico, MariaDB persistente y editor n8n accesible. La instalación y las comprobaciones de ejecución se completaron el 10/11/2026.

## Preparar el entorno

1. Abre una terminal en la carpeta del repositorio.
2. Ejecuta `docker info`; debe mostrar información del servidor sin error de conexión.
3. Ejecuta `docker compose version`; debe mostrar Compose v2 o superior.
4. Ejecuta `python3 scripts/init-env.py` una sola vez.
5. Abre `.env` en un editor local si deseas cambiar los puertos o el usuario administrador; conserva las claves generadas.
6. Ejecuta `docker compose config --quiet`.

Resultado esperado: `.env` privado y validación Compose sin errores. No compartas la salida de `docker compose config` sin `--quiet`: puede incluir los secretos interpolados.

## Construir e instalar

1. Ejecuta `sh scripts/start.sh`.
2. Espera las descargas y la compilación de las extensiones PHP.
3. Comprueba que el comando finaliza sin error.

Resultado esperado: MariaDB, Drupal y n8n activos; pruebas de SQL, portada y conectividad exitosas. El contenedor temporal `install` termina y se elimina.

Estos son los comandos internos, útiles para identificar en qué paso falla:

```bash
docker compose build --pull drupal
docker compose up -d --wait mariadb
docker compose stop drupal
docker compose run --rm install
docker compose up -d --wait drupal n8n mailpit
sh tests/smoke.sh
python3 tests/http_checks.py
```

La base debe estar vacía para la primera instalación. El instalador rechaza una base con tablas si Drupal no puede arrancar. Si Drupal ya arranca, conserva su base. Después de la instalación crea una portada provisional y deshabilita el registro público de usuarios.

## Revisar Drupal

1. Abre `http://localhost:8080` en el navegador.
2. Comprueba que aparece el aviso: “Prototipo académico no oficial. Habitaciones, disponibilidad y tarifas de demostración”.
3. Abre `http://localhost:8080/user/login`.
4. Introduce el usuario y contraseña configurados en `.env`.
5. Abre `http://localhost:8080/admin/reports/status` para revisar los requisitos.

Resultado esperado: portada visible y acceso administrativo Drupal. El usuario administrador inicial tiene permisos completos; no es una cuenta para visitantes. En esta fase todavía no hay gestión hotelera.

## Revisar n8n

1. Abre `http://localhost:5678`.
2. Crea la cuenta propietaria en la pantalla de bienvenida con datos de prueba.
3. Accede al editor de workflows.
4. Abre `http://localhost:5678/healthz/readiness`.

Resultado esperado: editor disponible y respuesta de salud sin error. No hay workflows ni chatbot en esta fase. No introduzcas datos de clientes reales. La cuenta propietaria se conserva en el volumen n8n.

## Comprobar persistencia

La prueba automatizada `python3 tests/persistence.py` recrea los contenedores y compara la identidad del sitio Drupal, sus páginas, un archivo temporal y la identidad de usuarios SQLite de n8n. No elimina volúmenes. También puedes revisar manualmente:

1. Ejecuta `docker compose down`.
2. Ejecuta `docker compose up -d --wait`.
3. Ejecuta `sh tests/smoke.sh`.
4. Vuelve a ingresar a Drupal y n8n con las mismas cuentas.

Resultado esperado: portada y cuentas conservadas. **No añadas `-v` a `down`: elimina los volúmenes y los datos.** Los volúmenes son persistencia, no copias de seguridad; no guardes información valiosa sin un respaldo independiente.

## Detener o reconstruir

Para apagar conservando datos: `docker compose down`. Para arrancar nuevamente: `docker compose up -d --wait`.

El código se copia a la imagen, no se monta desde el equipo. Después de modificar código propio o dependencias, reconstruye con `docker compose build drupal` y recrea con `docker compose up -d --force-recreate drupal`. No es necesario reinstalar Drupal.

## Resolver errores

| Síntoma | Causa probable | Acción |
| --- | --- | --- |
| `Cannot connect` o socket Docker ausente | Motor detenido o no disponible | Inicia Docker Desktop o el servicio Docker de tu equipo; repite `docker info`. En Linux con systemd, un administrador puede usar `sudo systemctl start docker`. |
| Permiso denegado al socket | Usuario sin acceso al motor | Revisa la instalación y permisos de Docker con el administrador; no hagas el socket accesible a todos. |
| `Could not resolve host` al construir | DNS o red del motor | Comprueba conectividad del equipo y Docker; vuelve a descargar. No desactives TLS. |
| Puerto ocupado | Otro proceso usa 8080 o 5678 | Cambia `DRUPAL_PORT` o `N8N_PORT` en `.env` y recrea contenedores. |
| Servicio `unhealthy` | Instalación o arranque falló | Ejecuta `docker compose ps` y `docker compose logs --tail=80 drupal mariadb n8n`; revisa antes de reintentar. No publiques logs sin revisar secretos. |
| Base no vacía sin bootstrap | Instalación parcial o credenciales incorrectas | Revisa logs y `.env`. Conserva datos y diagnostica; no borres tablas automáticamente. |
| Credenciales nuevas no funcionan con datos previos | Se modificó `.env` después de crear volúmenes | Recupera el `.env` original o realiza una rotación controlada dentro del servicio. |
| No llegan correos | No hay transporte de correo configurado | Es una limitación de fase 1; usa las credenciales iniciales. |

## Límites de esta instalación

Las interfaces HTTP solo se publican en `127.0.0.1`. `N8N_SECURE_COOKIE=false` se utiliza para este entorno HTTP local; un despliegue público requiere HTTPS, cookies seguras y configuración de dominio. MariaDB no publica un puerto al equipo.

No se activa CORS global ni se abren APIs de escritura. CORS y los orígenes exactos del widget se configurarán junto con la integración en fase 5. No hay claves OpenAI ni tokens de API.

## Glosario

- **Contenedor:** proceso aislado que ejecuta uno de los servicios.
- **Imagen:** paquete con el código y sus dependencias.
- **Volumen:** almacenamiento persistente administrado por Docker.
- **Motor Docker:** servicio que crea y ejecuta los contenedores.
- **Workflow:** flujo de automatización de n8n, pendiente de fase 5.

El n8n instalado conserva el ejecutor JavaScript interno para la demo. Su registro advierte que no hay ejecutor Python y recomienda ejecutores externos para producción. No se usan nodos Python. La cuenta propietaria y el editor autenticado n8n aún requieren completar el asistente inicial; el servidor, SQLite y las rutas HTTP sí se probaron.
