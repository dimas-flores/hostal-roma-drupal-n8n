# Registro de verificaciones


<a id="verificacion-aws"></a>

## Verificación del despliegue en AWS Academy

**Fecha:** 10/11/2026, hora de Perú. **Entorno:** AWS Academy Learner Lab, región us-east-1. **URL de demostración:** https://roma-demo.18-208-158-190.sslip.io/

El prototipo se instaló en una instancia EC2 `t3.medium`, Ubuntu Server 24.04 LTS x86_64, aproximadamente 4 GiB de RAM y volumen raíz de 30 GiB gp3. Se transfirió el código por SSH desde el repositorio local mediante un archivo de la revisión Git, sin copiar las credenciales de AWS, la llave SSH ni el `.env` local al código publicado. Las credenciales de las aplicaciones se generaron en el servidor y se conservan fuera de Git.

## Servicios y HTTPS

- Drupal, MariaDB, n8n y Mailpit iniciaron con comprobaciones de salud aprobadas.
- Caddy publica la web por HTTPS; la comprobación externa validó el certificado, sin desactivar TLS. HTTP redirige a HTTPS.
- La portada, `/tienda` y `/api/roma/v1/catalog` respondieron HTTP 200 desde fuera de EC2.
- Solo el proxy publica 80/443. Drupal directo, n8n y Mailpit enlazan a loopback; MariaDB solo está en la red Docker. Las conexiones externas a 8080, 5678, 8025 y 3306 no tuvieron acceso.
- Drupal reconoce únicamente la IP fija de Caddy como proxy de confianza. La sesión administrativa real presentó una cookie `Secure` y `HttpOnly`.
- Se configuró un propietario local de n8n, se comprobó el inicio de sesión y se verificaron los tres workflows ROMA en su cuenta. Su correo es ficticio y no ofrece recuperación por email; se puede cambiar desde el perfil.

## Pruebas ejecutadas en la instalación de EC2

| Grupo | Comprobaciones aprobadas | Evidencia |
| --- | ---: | --- |
| API pública y escritura autorizada | 15 | [Resultados del servidor](verificacion-aws-servidor.json) |
| Chat Drupal → n8n → Drupal | 13 | Mismo archivo; incluye consulta de datos y rechazo de origen/CSRF inválidos |
| Workflow de solicitud de reserva | 2 | Mismo archivo |
| Reglas de Drupal Commerce y transacciones | 18 | Mismo archivo; incluye integridad de importes y rollback |
| Navegador sobre HTTPS público | 38 | [Resultados de navegador](verificacion-aws-browser.json) |

Las 38 comprobaciones de navegador utilizaron el flujo real: catálogo, fechas, carrito de dos habitaciones, checkout, rechazo y cancelación de pago simulado, aprobación, recibo privado, ausencia de duplicados, aislamiento entre sesiones y revisión administrativa. Se confirmó y canceló el pedido de ensayo, sincronizándolo mediante el webhook real de n8n. Se evaluaron anchos de 1440, 768 y 390 píxeles. No se detectaron errores JavaScript.

Se reutilizó la prueba de navegador del repositorio con la URL pública y las operaciones de mantenimiento dirigidas a EC2 mediante SSH; se añadió la comprobación de cookie segura. Las credenciales temporales usadas por esa ejecución quedaron en un directorio local restringido fuera del repositorio. Las capturas y resultados publicados omiten las claves privadas de consulta.

![Tienda de demostración servida desde EC2.](capturas/aws-tienda.png)

## Entrar a las aplicaciones

La web y el panel Drupal usan el dominio HTTPS de arriba. El editor n8n permanece privado. Desde la computadora administradora, ejecuta en el repositorio:

```bash
sh scripts/aws-tunnel.sh 18.208.158.190 /ruta/local/roma-lab.pem
```

Mientras ese comando siga abierto, visita `http://localhost:15678` para n8n y `http://localhost:18025` para los correos ficticios. Las contraseñas se entregan en un archivo local con permisos 600 fuera del repositorio; no se incluyen en este documento.

## Límites de la demostración

El subdominio temporal usa el servicio DNS [sslip.io](https://sslip.io/), que resuelve nombres que contienen una IP y admite certificados individuales. No se compró un dominio ni se modificó el DNS del establecimiento. El servicio depende del laboratorio y de la instancia activa. Al detener y volver a iniciar EC2, la IP pública puede cambiar; en ese caso hay que actualizar el nombre temporal, la URL pública, los hosts permitidos, el proxy y el destino del túnel. No representa alojamiento permanente.

Los pagos siguen siendo simulados, sin cobros ni proveedor bancario externo. La aprobación de pago no confirma habitaciones. Las reservas, datos y tarifas son ficticios; las notificaciones se capturan en Mailpit y no se envían a clientes reales. No se habilitó OpenAI.

Los volúmenes Docker conservan datos entre recreaciones de contenedores. El respaldo inicial SQL es una copia restringida fuera de Git; no equivale a una estrategia completa de recuperación y no se ensayó su restauración en este despliegue. Para uso operativo deben respaldarse también los archivos Drupal, datos n8n y su clave de cifrado, además de probar la recuperación.

## Cambios que acompañan al despliegue

`compose.yaml` incorpora una red para Caddy y Drupal con direcciones fijas; `settings.php` recibe hosts y proxies de confianza desde el entorno. `scripts/aws-tunnel.sh` abre los puertos locales privados con SSH. Estos archivos, la guía y las evidencias pueden guardarse en Git; `.env`, llaves, contraseñas y respaldos se mantienen fuera.

## Textos de la experiencia de reserva

Se simplificaron los títulos de disponibilidad, contacto, tienda y carrito, junto con los botones «Solicitar reserva» y «Consultar disponibilidad». Los formularios presentan «Nombre completo», «Correo electrónico», «Teléfono» y «Datos del huésped»; los importes se muestran como estimados. La navegación usa «Habitaciones», «Servicios» y «Contacto». Se conservan el aviso del prototipo, la identificación de las tarifas e imágenes referenciales, el consentimiento y la explicación del pago simulado. Los cambios de presentación no habilitan cobros ni convierten el catálogo en una oferta oficial del establecimiento.

La comprobación sobre HTTPS, incluyendo formularios, carrito y checkout móvil, se registra en [los resultados de textos](verificacion-textos-aws.json). Para atender reservas del establecimiento se necesita su autorización y confirmar el inventario, las tarifas y el procedimiento de confirmación. Una pasarela real debe integrarse y verificarse antes de mostrar que un pago cobra dinero.



<a id="verificacion-continuacion"></a>

## Verificación final de la continuación — 10/11/2026

## Resultado

La demo local está funcionando. Docker se inició, la imagen se construyó con los requisitos Composer completos y se instalaron `roma_api` y `roma_payments` sobre la base existente. Se importaron las credenciales internas sin imprimirlas y se publicaron los tres workflows n8n. Drupal, MariaDB, n8n y Mailpit quedaron activos y saludables.

| Grupo | Evidencia observada |
| --- | --- |
| API | 15 comprobaciones: catálogo, disponibilidad, origen, fechas, autenticación, JSON, manipulación de precio/estado, consulta privada y ausencia de listados públicos. |
| Reservas | 21 pruebas de dominio con MariaDB, incluidas superposición, fechas contiguas, capacidad, idempotencia y cambios de tarifas. |
| Seguridad | CSRF manipulado rechazado sin crear reservas. Dos confirmaciones simultáneas: exactamente una aceptada. |
| Chat n8n | 12 comprobaciones de respuestas y seguridad; una tarifa modificada temporalmente en MariaDB se reflejó en el bot y se restauró. |
| Reserva por n8n | Webhook privado creó una pendiente con precio calculado por Drupal; reenvío no duplicó la solicitud. |
| Avisos | Captura local de solicitud, confirmación, recordatorio y cancelación; repetición sin duplicar eventos. |
| Programación | Se observaron ejecuciones n8n con `mode=trigger` y `status=success`. |
| Pagos ficticios | Seis pruebas, sin tarjetas ni dinero real. |
| Navegador | 28 comprobaciones del widget, reserva, panel, consulta privada, pago simulado, cancelación y formularios a 1440, 768, 390 y 360 píxeles; cero errores JavaScript. |
| Persistencia | Recreación completa sin eliminar volúmenes; identidad del sitio, páginas, archivo de prueba y usuarios SQLite conservados. |
| Requisitos Drupal | Informe de severidad de error sin incidencias. |

Resultados legibles por herramientas: `verificacion-api.json`, `verificacion-n8n.json`, `verificacion-final-browser.json` y `verificacion-ejecucion.json`. Capturas: `capturas/final-1440.png`, `capturas/final-390.png` y `capturas/panel-final.png`. La página pública y el panel se revisaron visualmente.

## Incidencia de la prueba de navegador

La primera pasada se detuvo al llegar al simulador de pagos porque el test no seleccionaba el resultado requerido. El navegador impidió enviar el formulario, correctamente. Se corrigió la prueba para elegir explícitamente la aprobación de demostración y se repitió el recorrido completo con éxito. La fixture confirmada de esa pasada se canceló sin borrar su historial.

## Límites

Los correos se capturan en Mailpit, no se envían a personas reales. El pago es un simulador y no equivale a una pasarela externa ni a boleta/factura. La alternativa OpenAI está desactivada y no se probó contra un proveedor por ausencia de credenciales. No hay remoto GitHub identificado y no se publicó el proyecto. Se conservaron la propuesta Word/PDF y sus datos académicos.

## Repetir

```bash
sh scripts/verify-all.sh
python3 tests/persistence.py
```

Las pruebas generan datos ficticios y el navegador deja una solicitud cancelada con una aprobación simulada para revisión. No utilizar el conjunto sobre una operación comercial.



<a id="verificacion-fase-1"></a>

## Resultados de verificación — fase 1

Fecha: 10/11/2026. Directorio: `/home/sae/hostal-roma-drupal-n8n`.

## Comprobaciones ejecutadas

| Comprobación | Resultado observado |
| --- | --- |
| Git en `/home/sae` | No era un repositorio. Se creó uno local independiente en la carpeta del proyecto, rama `main`, sin remoto ni commit. |
| `docker --version` | Docker 29.8.2 instalado. |
| `docker compose version` | Compose 5.6.0 instalado. |
| Plantilla oficial Composer | Se descargó `drupal/recommended-project:11.4.12`. |
| Resolución e instalación de dependencias | Composer instaló 89 paquetes, incluido Drupal 11.4.12 y Drush 13.8.0; generó el lock y no reportó avisos de vulnerabilidad conocidos. |
| `composer validate --strict --working-dir=drupal` | Correcto después del ajuste de restricciones a `~11.4.12`. |
| `drupal/vendor/bin/drush --version` | `Drush Commandline Tool 13.8.0.0`. Esto prueba la herramienta CLI, no el bootstrap del sitio. |
| `docker compose config --quiet` | Correcto después de generar `.env`. No requiere motor Docker. |
| `php -l` de settings y configuración inicial | Sin errores de sintaxis. |
| `sh -n` de arranque, instalador y smoke | Sin errores de sintaxis. |
| Generación de `.env` | Correcta; permisos `600`. |
| `git check-ignore .env drupal/vendor/autoload.php` | Ambos excluidos. |
| Inspección del modelo Compose resuelto | Puertos Drupal/n8n en `127.0.0.1`, MariaDB sin puerto publicado; contraseña inicial Drupal solo en el instalador. |

El PHP local es 8.5 y carece de GD y PDO MySQL. La instalación local de dependencias se ejecutó con `--ignore-platform-req=ext-gd` exclusivamente para descargar y revisar el código. Composer resuelve usando la plataforma PHP 8.4 declarada. Esto **no demuestra** compatibilidad de ejecución en el equipo. La imagen Docker instala GD y PDO MySQL y ejecuta `composer check-platform-reqs` sin omitir requisitos.

## Errores analizados

- Composer falló inicialmente por resolución DNS bajo el sandbox; se repitió con autorización de red y terminó correctamente.
- El generador `.env` usaba inicialmente `Path.open(opener=...)`, parámetro no admitido. Se corrigió a la función incorporada `open()`; la siguiente ejecución creó el archivo correctamente con modo `600`.
- Las restricciones exactas en `composer.json` generaban avisos en la validación estricta. Se cambiaron a `~11.4.12`, manteniendo la versión exacta en el lock; se actualizó su hash y la validación estricta pasó.
- `docker info` falló por acceso al socket. Se repitió con ejecución autorizada y el resultado fue socket `/var/run/docker.sock` ausente. No existe un contexto Docker alternativo configurado. El intento de `docker compose build drupal` también falló al conectar con el motor. La advertencia adicional indica que falta el plugin buildx, pero el impedimento principal es el motor inaccesible.

## Ejecución completada después de activar Docker

El usuario inició el motor Docker y se repitió la instalación. Resultado final: **fase 1 funcional y comprobada**.

| Comprobación real | Resultado |
| --- | --- |
| Construcción de imagen Drupal | Correcta con PHP 8.4.27; requisitos Composer sin omisiones. |
| Instalación Drupal en MariaDB | Correcta, Drupal 11.4.12 y MariaDB 11.4.13; idioma `es`, zona `America/Lima`. |
| Traducciones | Más de 8.900 traducciones importadas durante la instalación. |
| Estado Compose | Drupal, MariaDB y n8n `healthy`. |
| Consulta SQL real desde Drupal | `SELECT 1` correcto; bootstrap `Successful`, base `Connected`. |
| Portada HTTP | 200 y aviso académico exacto presente. |
| Login administrador por formulario HTTP | Correcto; acceso autenticado al informe de estado con HTTP 200. |
| Panel sin autenticación | Acceso rechazado. |
| Informe de requisitos Drupal | `drush core:requirements --severity=2` sin errores. |
| Drupal → n8n | Ruta interna de disponibilidad del servidor HTTP accesible. |
| n8n → Drupal | Formulario de login HTTP accesible mediante DNS Compose. |
| Salud e interfaz n8n | `/healthz/readiness` responde `{"status":"ok"}` y la interfaz devuelve HTTP 200. |
| SQLite n8n | Base legible y con registro inicial de usuario; se comprobó su persistencia. |
| Recreación completa sin eliminar volúmenes | Conservó identidad del sitio, páginas, archivo de prueba y registros de usuarios n8n. |
| Repetición de pruebas después de recrear | Pruebas de servicios y HTTP correctas. |
| Reejecución del instalador | Detectó sitio instalado; no reinstaló la base; quedó una sola página inicial. |

### Correcciones derivadas de la ejecución

La primera portada devolvió HTTP 500. El registro Drupal identificó un tipo de contenido ausente: el perfil Standard de esta versión no creaba `page`. El instalador se corrigió para crear explícitamente el tipo Página básica, almacenamiento, campo de contenido y sus formularios y visualización. Se reparó el nodo inicial existente mediante API Drupal, sin duplicarlo ni reinstalar la base. La portada pasó después de la corrección.

Se añadió la protección `.htaccess` de Drupal en `config/sync` durante la construcción para evitar que el proceso web intentara generarla en un directorio de solo lectura. Se cambió la comprobación de salud Drupal para verificar la portada, que es la ruta principal del proyecto.

Se sustituyó `WEBHOOK_URL` por `N8N_WEBHOOK_URL` después del aviso de deprecación de n8n. Se deshabilitó la instalación de paquetes comunitarios no verificados. La configuración vigente de webhooks está descrita en el [repositorio oficial de n8n](https://github.com/n8n-io/n8n-hosting/blob/main/charts/n8n/README.md).

La instalación mostró un aviso de cliente `mysql` ausente. La base ya estaba creada por MariaDB y se verificó vacía mediante PDO antes de instalar; la instalación y las consultas SQL terminaron correctamente. Esta imagen no incluye herramientas CLI de copia de seguridad de MySQL.

Docker construyó mediante el constructor clásico porque no está instalado buildx; la construcción terminó correctamente. Apache emite un aviso de nombre global de servidor sin afectar el acceso local. n8n mantiene avisos de deprecación del ejecutor interno y de valores predeterminados futuros; el ejecutor JavaScript se registró correctamente. El ejecutor Python no está instalado y no se utiliza en este proyecto.

### Cómo repetir las pruebas

```bash
sh tests/smoke.sh
python3 tests/http_checks.py
python3 tests/persistence.py
```

La última prueba apaga y recrea servicios temporalmente, conservando los volúmenes. El script de arranque ejecuta las dos primeras. Las pruebas HTTP utilizan las credenciales de `.env` sin imprimirlas.

### Pendiente y límites

La cuenta propietaria de n8n debe crearse en `http://localhost:5678`; no se registró una cuenta en nombre del usuario. Su editor autenticado y la creación de workflows no se han probado todavía. No hay workflows ni integración de negocio en esta fase. No se hicieron capturas ni una revisión visual en navegador: la portada, formulario de login y panel se probaron mediante solicitudes HTTP reales.

Las versiones de imagen y sus digests están registradas en [imágenes verificadas](imagenes-verificadas.txt). Los servicios se dejaron encendidos en localhost.

## Documentación y revisión

La guía se basa en los archivos creados, la plantilla oficial y los comandos ejecutados. No incluye capturas; no se realizó una sesión de navegador visual. Se distingue cada resultado observado de los resultados esperados. No se incluye documentación de funciones hoteleras todavía inexistentes.

No se publicó nada en GitHub: falta identificar el repositorio remoto. Los comandos de preparación del commit y vinculación aparecen en el README. No se generaron workflows ni se inició otra fase.



<a id="verificacion-finanzas"></a>

## Verificación de cuentas y saldos

Segunda ampliación funcional tras recepción. `roma_finance` incorpora un libro de movimientos en MariaDB con acceso exclusivo mediante permisos Drupal. No integra todavía una pasarela bancaria ni emisión SUNAT.

## Comportamiento implementado

- Cuenta por solicitud con cargo inicial basado en el estimado guardado. Consultar no crea registros y abrir nuevamente no duplica el cargo.
- Cargos, descuentos, adelantos/pagos y devoluciones, todos en modo `demo`. Céntimos enteros PEN, sin cálculos monetarios con coma flotante.
- Saldo = cargos netos − pagado neto. Los descuentos posteriores pueden producir saldo a favor; las devoluciones son operaciones explícitas.
- Historial que no se edita ni elimina desde la aplicación. Corregir agrega una contrapartida, vinculada de forma única al original, con motivo, usuario y hora.
- Rechazo de pagos sobre el saldo, devoluciones superiores al pagado, medios reales y anulaciones que dejarían netos negativos.
- Reserva confirmada requerida para pagos simulados; una cancelación no borra la cuenta ni devuelve automáticamente.
- Claves de idempotencia con huella del contenido y bloqueo InnoDB compartido con reservas. Un reenvío idéntico reutiliza el movimiento; reutilizar su clave con otros datos se rechaza.
- Reporte por fecha peruana, separando el flujo del periodo y los saldos actuales de las últimas 100 solicitudes.
- Constancias internas imprimibles identificadas como simulación sin validez tributaria; no se genera una factura o boleta.
- Avisos financieros encolados en la misma transacción y entregados por el workflow n8n existente solo al administrador ficticio en Mailpit.

Los pagos académicos de Commerce no se importan como ingresos del libro. Registrar otra simulación sirve para ensayar adelantos; no verifica ni reproduce un cobro bancario.

## Pruebas en AWS

La ejecución final aprobó **103 comprobaciones de servidor**:

| Grupo | Aprobadas |
| --- | ---: |
| Reglas financieras y cola de avisos | 40 |
| Carreras reales de pagos, anulaciones e idempotencia | 3 |
| Workflow n8n y recepción SMTP Mailpit | 3 |
| Regresión API | 15 |
| Regresión chatbot n8n | 13 |
| Regresión recepción | 29 |

[Evidencia del servidor](verificacion-finanzas-servidor.json). Las reglas se ensayaron con transacciones revertidas. Las pruebas de concurrencia y avisos retiraron sus propias reservas y cuentas. Mailpit conserva mensajes ficticios de ensayo; no se envían a Internet.

La prueba de navegador verifica acceso anónimo rechazado, usuario autenticado sin permiso rechazado, administración, apertura protegida por CSRF, adelanto, cargo, descuento, devolución, contrapartida, constancia, impresión, reporte y uso a 390 píxeles. [Evidencia del navegador](verificacion-finanzas-browser.json).

![Cuenta financiera de demostración.](capturas/cuenta-financiera.png)

Durante la validación se normalizaron explícitamente identificadores e importes leídos por PDO para mantener el mismo contrato de tipos al crear un movimiento y al recuperarlo por idempotencia. También se comprobó que los scripts temporales se copien al contenedor después de recrearlo. La lectura de cuentas consulta primero si existe la apertura, evitando presentar como abierta una cuenta sin su cargo inicial durante una apertura concurrente.

## Instalar en una base existente

```bash
docker compose build drupal
docker compose stop drupal
docker compose run --rm --no-deps --entrypoint vendor/bin/drush install en roma_finance --yes
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush cache:rebuild
```

El instalador general lo habilita también en instalaciones nuevas. Se añaden las tablas `roma_finance_account` y `roma_finance_entry`; no se reemplazan los datos hoteleros. El rol responsable hotelero recibe `manage roma finance`; los roles públicos no lo reciben.

## Repetir comprobaciones

```bash
docker compose cp tests/finance.php drupal:/tmp/roma-finance.php
docker compose exec -T drupal vendor/bin/drush php:script /tmp/roma-finance.php
python3 tests/finance-concurrency.py
python3 tests/finance-notifications.py
deno run --cached-only --allow-all tests/finance-browser.mjs
```

La prueba de navegador usa el mismo entorno Deno, Puppeteer y Brave de las pruebas previas. `ROMA_BROWSER_BASE` permite cambiar la URL; la CLI Docker y el `.env` de pruebas deben corresponder a esa misma instalación. En AWS se dirigieron esas operaciones mediante SSH, guardando los secretos fuera de Git.

## Límites y siguiente fase

No hay cobros reales, conciliación bancaria ni comprobantes electrónicos. Las constancias internas no son documentos tributarios. Los avisos SMTP se omiten después de una entrega registrada; un fallo entre aceptación SMTP y guardado puede repetir un correo, identificable mediante `X-ROMA-Event`. Esto no duplica el movimiento del libro.

La siguiente fase es facturación en el servicio beta de SUNAT, con biblioteca PHP evaluada, XML UBL firmado, correlativos y respuestas CDR auditables. La fecha de emisión, tratamiento de anticipos e impuestos deben configurarse para el contribuyente antes de una puesta en producción. No debe inferirse que una solicitud o pago simulado obliga a emitir un comprobante real.



<a id="verificacion-recepcion"></a>

## Verificación de la mejora de recepción

Primera fase de ampliación tras el despliegue en AWS Academy: calendario, check-in, check-out, limpieza y mantenimiento. Conserva Drupal, MariaDB, Commerce y n8n; no incorpora otro CMS.

## Reglas verificadas

- Calendario protegido de 7, 14 o 31 noches, con separación entre solicitudes pendientes, reservas confirmadas y mantenimiento.
- Entrada solo de reservas confirmadas y vigentes, con habitación activa, lista y sin otro huésped sin salida.
- Salida solo tras entrada. Guarda hora, conserva historia y deja limpieza pendiente. Repetir una salida no vuelve a ensuciar una habitación ya preparada.
- Una salida anticipada libera inventario después de la limpieza requerida para hoy, sin modificar las fechas e importes comerciales originales ni efectuar devoluciones.
- No se cancelan estadías con entrada/salida registrada. No presentado cancela la reserva sin cobrar ni reembolsar.
- Mantenimiento sin superposición con confirmadas vigentes u otros bloqueos. Intervalos con salida exclusiva.
- Solicitar y confirmar revalidan mantenimiento y preparación de hoy. API, chatbot y Commerce usan la misma disponibilidad.
- Escrituras serializadas mediante el mismo bloqueo InnoDB del sistema de reservas; auditoría con usuario y hora. Los roles públicos no reciben permiso de recepción.

## Ejecución en EC2

Se aprobaron **29 comprobaciones de reglas de recepción**, ejecutadas dentro de una transacción revertida al terminar. Se volvieron a aprobar 15 de API, 13 de chat n8n y 18 de Commerce: **75 comprobaciones del servidor**, además de las verificaciones existentes de CSRF y confirmaciones simultáneas. Véase [evidencia del servidor](verificacion-recepcion-servidor.json).

La prueba de navegador sobre HTTPS comprobó acceso anónimo rechazado, acceso administrativo, periodo del calendario, llegada de hoy, ausencia de mutación en GET, rechazo del token manipulado, entrada, salida, limpieza, creación/liberación de mantenimiento reflejada en API y desplazamiento móvil. Los resultados están en [evidencia de navegador](verificacion-recepcion-browser.json). Los registros de ensayo se retiraron al terminar y se conservó la información previa.

## Repetir las pruebas

```bash
docker compose cp tests/reception.php drupal:/tmp/roma-reception.php
docker compose exec -T drupal vendor/bin/drush php:script /tmp/roma-reception.php
deno run --cached-only --allow-all tests/reception-browser.mjs
```

El navegador requiere Deno, Puppeteer disponible en caché y el ejecutable Brave del entorno de desarrollo, igual que las pruebas previas. Por defecto usa la instalación local; `ROMA_BROWSER_BASE` permite cambiar la URL. Las operaciones auxiliares de Docker deben apuntar al mismo servidor que esa URL. La verificación de AWS utilizó SSH para esas operaciones, sin publicar secretos.

## Instalación existente

Construye la imagen actualizada, detén Drupal durante la habilitación y conserva sus volúmenes:

```bash
docker compose build drupal
docker compose stop drupal
docker compose run --rm --no-deps --entrypoint vendor/bin/drush install en roma_reception --yes
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush cache:rebuild
```

La instalación del módulo crea tablas nuevas; no reemplaza las habitaciones ni las reservas existentes. El instalador general también lo habilita en instalaciones nuevas. El usuario raíz de Drupal puede acceder; el rol responsable hotelero recibe el permiso específico.

## Fases siguientes

Esta entrega corresponde a recepción y disponibilidad operativa. Los pagos continúan simulados y Mailpit captura las notificaciones. La siguiente fase debe separar cargos, adelantos, saldos y devoluciones antes de conectar una pasarela. La integración SUNAT debe comenzar en beta, con XML firmado, correlativos, respuestas CDR y estados auditables; la emisión de producción requiere la configuración y autorización del contribuyente. No se emiten comprobantes tributarios en esta fase.



<a id="verificacion-sunat-beta"></a>

## Verificación de SUNAT beta

**Fecha: 11/11/2026. Estado: publicado en AWS tras recuperar acceso por la nueva IP.**

Actualización: imagen Docker construida correctamente, módulo habilitado, sin actualizaciones de base pendientes y 44 comprobaciones sin conexiones SUNAT aprobadas en EC2. Portada HTTPS: 200; panel beta anónimo: 403. URL: https://roma-demo.98-91-2-63.sslip.io/admin/roma/sunat-beta. Se guardó un respaldo previo a la instalación. Las evidencias de aceptación CDR siguientes corresponden al entorno local; no se hicieron nuevos envíos desde AWS.

### Registro de la verificación local anterior
 La IP anterior no respondió a SSH ni HTTPS. No se cambiaron servicios de EC2 sin conexión ni se reutilizó su base de datos activa: la verificación local partió de una copia de demostración en otra base MariaDB.

## Comunicación con el servicio beta

Se prepararon dos documentos con emisor y destinatarios genéricos, cuenta de demostración saldada y certificado académico autofirmado. Se utilizó únicamente `https://e-beta.sunat.gob.pe/ol-ti-itcpfegem-beta/billService`, con comprobación TLS del servidor activa.

| Documento | Resultado final | Código CDR | Evidencia |
| --- | --- | --- | --- |
| Factura F901-1 | Aceptado en beta | 0 | XML, CDR y PDF en [ejemplos](ejemplos-sunat-beta/README.md) |
| Boleta B901-1 | Aceptado en beta | 0 | XML, CDR y PDF en el mismo directorio |

Los [resultados del servicio](verificacion-sunat-beta-real.json) incluyen las huellas SHA-256 del XML y CDR. Los PDF son representaciones académicas imprimibles, no documentos tributarios válidos. No se usaron nombres ni documentos de personas del hotel.

SUNAT advierte que [beta sirve para probar estructuras XML y no verifica la consistencia de los datos](https://cpe.sunat.gob.pe/noticias/servicio-beta-para-realizar-pruebas-ubl-21). La aceptación no verifica un régimen tributario, una venta, un pago ni la autorización fiscal del certificado. Tampoco valida el procedimiento de emisión de producción, plazos, resúmenes o contingencias.

## Pruebas sin conexiones a SUNAT

- **44 comprobaciones de reglas:** firma y alteración de XML, confidencialidad del huésped, correlativos, claves de idempotencia, integridad, estados, reintento manual, límites, recuperación, respuestas tardías, revisión de CDR, permisos de archivos y rechazo de ZIP/XML peligrosos. Transporte simulado, separado de la comunicación beta real. [Resultados](verificacion-sunat-beta-reglas.json).
- **17 comprobaciones de navegador:** permisos anónimo/autenticado, cuenta saldada, CSRF, preparación explícita, descarga privada, ausencia de clave y datos del huésped, consentimiento de envío, impresión, boleta y vista móvil. Estas pruebas no envían documentos. [Resultados](verificacion-sunat-beta-browser.json).
- **69 regresiones locales:** 40 de cuentas y 29 de recepción. Sus fixtures se revierten. [Resultados](verificacion-sunat-beta-regresion.json).
- Composer verificó las extensiones y dependencias; el informe Drupal no presentó errores de severidad 2 en el entorno local.

Las pruebas de navegador retiran sus propios documentos y usuarios. Los correlativos beta de esos ensayos no se reutilizan: pueden dejar huecos en esta base exclusivamente académica. Los ejemplos aceptados permanecen para revisar en la interfaz.

## Entorno realmente usado

La instancia de AWS estaba inaccesible. Se preparó una base separada con MariaDB nativo **13.0.2**, puerto **33077**, enlazado solo a loopback, y PHP **8.5.10** con SOAP, PDO MySQL, Intl y GD habilitados para este proceso. GD se cargó desde un paquete oficial Arch de PHP 8.5.12, compatible con el módulo comprobado; no se cambiaron extensiones del sistema ni la configuración global PHP. Composer validó esta plataforma. El servidor HTTP local escucha en `127.0.0.1:18088`.

La configuración de producción académica de Docker **conserva PHP 8.4 y MariaDB 11.4**; añade SOAP y un volumen de firma privado. Aún falta construir y verificar esta revisión en EC2 cuando se recupere su IP y acceso. No se afirma que el nuevo módulo ya esté disponible en el enlace público de AWS.

## Incidencias analizadas y resueltas

1. Twig requiere una estrategia de escape identificada por nombre. Se corrigió la configuración del constructor XML y se verificó la firma del documento generado.
2. El CDR beta contenía una carpeta vacía `dummy/` y un XML. La validación inicial rechazó conservadoramente esa respuesta. El primer intento quedó incierto sin CDR conservado; se guardó la respuesta del segundo intento y se ajustó únicamente la admisión de directorios vacíos seguros. La revisión posterior utilizó el mismo CDR, sin una nueva solicitud, registrando el cambio de estado y su responsable.
3. Se conservó TLS verificado: se sustituyeron expresamente las opciones inseguras por defecto del cliente SOAP del SDK.
4. Se protegieron tamaño comprimido y descomprimido, nombres de archivos, rutas, número de XML y entidades externas antes de que el SDK interprete el CDR.
5. Se comprobó que la cuenta del huésped no se exporte al XML: cliente, emisor y dirección son genéricos. No se incluyen contraseñas ni claves privadas en los ejemplos.
6. Las pruebas de interfaz seleccionan explícitamente el tipo antes de comprobar CSRF; así se prueba un POST al servidor y no solo la validación nativa del navegador.

Se hicieron tres solicitudes beta de bajo volumen: dos intentos sobre la factura durante el diagnóstico y uno sobre la boleta. Las pruebas restantes usan transporte simulado o preparación/lectura local. La suite general no ejecuta la prueba beta real.

## Archivos y comandos

El módulo `roma_sunat_beta` añade contadores, documentos, intentos y auditoría de revisiones. Los XML y respuestas se guardan en MariaDB y se descargan mediante rutas protegidas. El certificado privado se conserva fuera de la raíz pública, en un volumen independiente; no se incluye en Git ni en la imagen.

La [guía de uso e instalación](sunat-beta.md) contiene los comandos para habilitar el módulo, configurar las credenciales genéricas y reproducir las pruebas. El permiso es `manage roma sunat beta`; el rol responsable hotelero lo recibe, los visitantes no.

## Límites de esta entrega

No se implementa producción, certificado fiscal autorizado, clave SOL real, OSE/PSE, notas de crédito/débito, resúmenes diarios ni determinación del régimen tributario. El escenario gravado al 18 % es solo un ejercicio de XML. El envío individual beta de una boleta no demuestra el procedimiento legal para emitirla en producción.

Una respuesta tardía queda en su intento sin modificar un estado más reciente. Si solo se conserva en un intento, debe descargarse para revisión técnica; no se convierte automáticamente en aceptación. Un timeout no prueba que SUNAT haya rechazado el XML, por eso se mantiene el estado incierto y se evita repetir automáticamente.



<a id="verificacion-tienda"></a>

## Verificación de la tienda académica — 10/11/2026

## Resultado

La opción implementada es Drupal/PHP con Drupal Commerce, chat integrado con n8n y Docker Compose. La tienda permite elegir estadías, reunirlas en un carrito, completar un checkout, ensayar resultados de pago y consultar las solicitudes generadas. La confirmación hotelera sigue protegida por los permisos de Drupal.

| Parte de la consigna | Implementación | Evidencia |
| --- | --- | --- |
| Tienda online | Catálogo, carrito, varias estadías, checkout, pedidos y pasarela local de prueba | `verificacion-commerce-browser.json` |
| CMS y PHP | Drupal 11.4.12, Commerce 3.3.14 y módulos propios | `composer.lock`, `roma_shop`, `roma_booking` |
| Chat con n8n | Widget oficial, consultas a Drupal, orientación hacia tienda y carrito | `verificacion-n8n.json` |
| Automatizaciones | Avisos capturados en Mailpit y conciliación entre reservas y pedidos por n8n | Prueba de confirmación/cancelación en navegador |
| Docker Compose | Drupal, MariaDB, n8n y Mailpit, con datos persistentes | Recreación completa sin eliminar volúmenes |
| Control de acceso | Sesiones separadas, recibo privado, CSRF, permisos y límites de intentos | Pruebas API, de navegador y de seguridad |
| Documentación | Guía de uso, instalación, arquitectura, API y guion para el profesor | Archivos en `docs/` |

## Comprobaciones ejecutadas

- **18 comprobaciones de integración Commerce:** total en PEN, manipulación de cantidades/precios/importes, consentimiento, pago test, solicitudes pendientes, reenvío sin duplicados, cambios administrativos y reversión completa si falla una línea.
- **37 comprobaciones de navegador:** catálogo, carrito, eliminación, fechas peruanas, alias protegido, checkout, datos inválidos, aprobación/rechazo/cancelación, aislamiento de sesiones, recibo privado, permisos administrativos y sincronización n8n. Se verificaron páginas reales con HTTP 200 a 1440, 768 y 390 píxeles, antes de cancelar el pedido. Cero errores JavaScript.
- **Regresión completa:** pasaron las 15 comprobaciones API, 21 hoteleras, seis pagos individuales, 13 de chat, dos de reserva n8n, 28 del navegador anterior, prueba CSRF y confirmaciones concurrentes.
- **Persistencia:** `down` y `up` sin `-v` conservaron productos, pedidos, pagos, enlaces de reservas, datos Drupal, archivos y usuarios SQLite n8n.
- **Plataforma:** requisitos Composer correctos, BCMath disponible, informe Drupal sin incidencias de severidad de error y cuatro servicios saludables.

Las fixtures se cancelaron conservando su historial. Las evidencias no incluyen claves privadas de consulta. Las capturas del catálogo y carrito se revisaron visualmente. La propuesta académica Word/PDF existente se conservó.

## Incidencias resueltas

El PHP del equipo carecía de GD; la resolución Composer se realizó dentro de Docker. Commerce también requería BCMath, incorporado a la imagen. Durante la primera activación, la imagen anterior regeneró una caché sin los campos de Commerce. Se reconstruyó la caché con la imagen nueva y el arranque ahora detiene Drupal durante la instalación de módulos. No se reinstaló ni se vació MariaDB.

La revisión visual detectó que el refresco de Commerce reemplazaba el título de las líneas y ocultaba sus fechas. El procesador propio ahora conserva habitación, fechas y huéspedes en el resumen. La ruta `/cart` redirige al carrito con controles anónimos explícitos.

Dos primeras comprobaciones de integración usaban nombres de métodos ajenos a esta versión de Commerce; se corrigieron contra sus interfaces instaladas. La prueba también se ajustó para recargar la versión del pedido actualizada por el subsistema de pagos. La pasada final terminó sin esos errores.

## Alcance del pago

El pago es **local y académico**, guardado en Commerce en modo `test`. No se realizó una llamada a Mercado Pago ni a otro proveedor bancario. No hay cargos, tarjetas ni comprobantes comerciales. La integración bancaria externa y OpenAI siguen fuera de la ejecución verificada; requerirían credenciales y pruebas propias.

## Repetir y presentar

```bash
sh scripts/start.sh
sh scripts/verify-all.sh
python3 tests/persistence.py
```

Para la presentación, sigue [la guía de la tienda](manual-de-usuario.md#guia-tienda) y [el guion del profesor](guion-demostracion.md). Las fuentes técnicas y versiones se explican en [tienda Commerce](manual-de-usuario.md#tienda-commerce).



<a id="correccion-formulario-reserva"></a>

## Corrección del formulario de reserva — 10/11/2026

El formulario rechazaba un teléfono que contenía letras, pero asignaba el error a la habitación. El aviso aparecía en la parte superior y podía quedar fuera de la vista al enviar desde el final del formulario. No se registraba una solicitud inválida.

Se asocia cada error al campo correspondiente: fechas, huéspedes, nombre o teléfono. Se habilita el módulo oficial de Drupal `inline_form_errors`, se muestra una explicación junto al campo y se desplaza el foco hasta el primer error visible. Los datos ingresados se conservan para corregirlos. El teléfono incluye instrucciones y sigue siendo opcional.

Cuando el envío es válido, el recibo también recibe el foco. Se entrega el código de solicitud y su clave privada, y se muestra el estado pendiente. La confirmación sigue correspondiendo al administrador. Se conservan la validación del servicio, la protección CSRF, el límite de intentos y las comprobaciones de disponibilidad en MariaDB.

## Verificación ejecutada

`tests/booking-feedback.mjs` completó 16 comprobaciones en Brave sin errores JavaScript, con vistas de 1440 y 390 píxeles. Se reprodujo el teléfono con letras, se comprobaron errores junto a los campos, conservación de valores, foco y ausencia de escrituras inválidas. Al corregir los campos y dejar el teléfono vacío, se creó exactamente una solicitud pendiente en MariaDB y apareció el recibo con código y clave. La solicitud ficticia de esa prueba se canceló conservando su historial.

También pasaron la comprobación de CSRF manipulado sin escrituras y las pruebas HTTP de portada, acceso anónimo, login administrativo y n8n. Los cuatro servicios permanecieron saludables.

Evidencia: [resultados](verificacion-reserva-feedback.json) y [captura del error](capturas/reserva-error-telefono.png). No se publican claves privadas de solicitudes.

## Aplicar y repetir

```bash
docker compose build drupal
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush en inline_form_errors --yes
docker compose exec -T drupal vendor/bin/drush cache:rebuild
deno run --cached-only --allow-all tests/booking-feedback.mjs
python3 tests/hotel-security.py
python3 tests/http_checks.py
```

El navegador de pruebas requiere Brave en `/opt/brave-bin/brave` y Puppeteer en la caché de Deno, igual que las demás comprobaciones del proyecto. El recorrido envía tres formularios y utiliza los límites de intentos normales de la aplicación.

Para verificarlo manualmente, recargar `/reservar`, completar datos ficticios, escribir letras en el teléfono y enviar. Debe aparecer la explicación junto al teléfono. Dejarlo vacío o introducir un número ficticio válido y reenviar: deben mostrarse el recibo y el estado pendiente. Guardar el código y la clave privada para consultar `/mi-reserva`.



<a id="diseno-fase-2"></a>

## Fase 2 — Diseño web ROMA · Altiplano

## Objetivo y alcance

Reemplazar la portada provisional por una experiencia visual elegante y responsive, implementada como tema nativo Drupal con Twig, CSS y JavaScript. No se añadió inventario, reservas, API ni automatizaciones de negocio: corresponden a las fases siguientes.

La dirección visual combina marfil, verde profundo, tonos de arena, fotografía referencial y tipografía editorial. Los componentes propios incluyen identidad tipográfica, encabezado, hero, planificador visual, catálogo, filtros, detalles en ventanas modales, servicios, ilustración del altiplano, ubicación general, pie y asistente en modo demostración. El login utiliza los formularios originales de Drupal; el tema administrativo sigue siendo Claro.

## Referencias consultadas

| Fuente | Referencia | Aplicación en este proyecto |
| --- | --- | --- |
| Web | [The Hoxton · habitaciones](https://thehoxton.com/london/holborn/rooms/) | Jerarquía de habitaciones, fotografía y lectura clara de categorías. Se revisó la página; no se reutilizó su contenido ni sus imágenes. |
| GitHub | [Nocturne Hotel](https://github.com/lowrencekhanna69/nocturne-hotel) | Referencia de presentación boutique y énfasis en responsive, accesibilidad y rendimiento descritos en su repositorio. |
| GitHub | [OceanBlue Hotel Template](https://github.com/sanusiaz/hotel-website-flow) | Referencia de organización de secciones y navegación de una web hotelera. Se revisó su README. No se copiaron su código ni sus integraciones de pagos. |
| YouTube | [Create A Responsive Hotel Website Design Using HTML - CSS - JAVASCRIPT](https://www.youtube.com/watch?v=9OVD-HA-zAI) | Recurso educativo localizado mediante búsqueda. No fue posible reproducir ni obtener el video; no se afirma haber seguido sus pasos. |
| YouTube | [Responsive Hotel Booking Website Using HTML, CSS & JavaScript](https://www.youtube.com/watch?v=zqLs3Ei4cAc) | Segundo recurso localizado; mismo límite de acceso al video. |

La implementación del repositorio es propia y conserva Drupal. Las referencias no validan funcionalidades de reservas ni datos del establecimiento.

## Archivos creados y modificados

- `drupal/web/themes/custom/roma_elegante/`: definición del tema, bibliotecas, preprocess, plantillas Twig, estilos, JavaScript, configuración inicial, esquema y recursos locales.
- `docker/drupal/design.php`: instala y activa el tema de forma idempotente, preservando el contenido existente.
- `docker/drupal/install.sh` y `Dockerfile`: incorporan la activación y copian el tema a la imagen.
- `scripts/start.sh`: reconstruye cachés después de recrear el contenedor web.
- `tests/design-browser.mjs`: comprobaciones de navegador y capturas.
- `docs/verificacion-fase-2.json` y `docs/capturas/`: resultados y evidencia visual.
- `docs/recursos-visuales.md`: procedencia y licencias.

## Interacciones disponibles

El planificador valida que la salida sea posterior a la llegada y que la llegada no sea anterior a hoy en Lima. Muestra noches, huéspedes y fechas `dd/mm/aaaa`. No envía datos al servidor ni consulta disponibilidad. Los campos nativos de fecha pueden mostrar el orden visual definido por el idioma del navegador; el resumen siempre se presenta en formato peruano.

Los filtros muestran una o dos categorías según capacidad ilustrativa. Las tarjetas abren una ventana con detalles y tarifa ficticia. El menú se adapta al móvil y se cierra al navegar. El asistente informa explícitamente que todavía no está conectado con n8n; no simula respuestas de un sistema conectado.

Contacto presenta información del proyecto, sin inventar teléfono o correo del negocio. El usuario identificó el establecimiento de referencia mediante un enlace de Google Maps: Hostal ROMA, Nueva Zelandia 196, Juliaca 21101, Perú. La sección de ubicación muestra esa dirección y un botón de indicaciones hacia el enlace compartido, sin seguimiento UTM. Google Maps no pudo abrirse mediante la herramienta de consulta; la identificación procede de la confirmación del usuario, no de una verificación independiente. La ilustración andina sigue siendo conceptual: no representa un mapa ni una localización exacta.

## Catálogo configurable

Las categorías y tarifas visibles se leen de la configuración Drupal `roma_elegante.settings`, almacenada en MariaDB después de instalar el tema. El YAML de `config/install` aporta valores iniciales; modificar ese archivo no cambia una instalación existente. No constituye inventario ni un modelo de reservas.

Ejemplo de modificación explícita de una tarifa ficticia en la instalación actual:

```bash
docker compose exec -T drupal vendor/bin/drush config:set roma_elegante.settings demo_rooms.0.price 90 --yes
docker compose exec -T drupal vendor/bin/drush cache:rebuild
```

El ejemplo no se ejecutó: la entrega conserva las tarifas ficticias 85, 130 y 150 PEN. La gestión hotelera mediante permisos y formularios se realizará en fase 3.

## Comandos para instalar o actualizar

Desde la raíz del proyecto, con `.env` existente:

```bash
docker compose build drupal
docker compose run --rm install
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush cache:rebuild
sh tests/smoke.sh
python3 tests/http_checks.py
```

El instalador conserva la base existente. También puede utilizarse `sh scripts/start.sh` para el proceso completo. La reconstrucción de caché posterior a recrear el servidor evita conservar el registro de plantillas generado por un contenedor con código anterior.

## Verificación ejecutada

Se utilizó Brave headless con Puppeteer. Pasaron 35 comprobaciones: portada HTTP 200, una cabecera principal, tres categorías iniciales, carga de imágenes y ausencia de desbordamientos a 1440, 1024, 768, 390 y 360 píxeles; menú móvil; filtros; modal y Escape; resumen de tres noches con fechas peruanas; rechazo de salida igual a llegada; aviso y cierre del asistente; y formulario Drupal en escritorio y móvil. No se detectaron errores JavaScript ni recursos HTTP fallidos durante esa prueba.

Los resultados exactos quedan en `verificacion-fase-2.json`. El navegador se ejecuta con:

```bash
deno run --cached-only --allow-all tests/design-browser.mjs
```

Ese comando usa el Puppeteer ya disponible en la caché local y Brave en `/opt/brave-bin/brave`. En otro equipo se debe instalar Puppeteer Core 25.10.0 en la caché de Deno y definir `ROMA_BROWSER` con la ruta del navegador Chromium compatible. La instalación Docker no depende de estas herramientas de prueba.

La revisión detectó primero un registro de plantillas obsoleto después del cambio de imagen; se corrigió reconstruyendo caché en el contenedor actualizado. También se ajustó la prueba para cargar expresamente las imágenes diferidas antes de revisarlas: no eran errores de descarga.

La prueba HTTP de login administrativo y las comprobaciones de SQL, requisitos y comunicación Drupal/n8n se repitieron después de instalar el tema. La persistencia ya se comprobó en fase 1; no se modificaron sus volúmenes.

## Revisión manual y Git

Abre `http://localhost:8080` y utiliza `Ctrl+Shift+R` si conservas la portada anterior. Comprueba el menú móvil, los filtros, las ventanas de detalles, el planificador y el asistente. Las capturas muestran la vista pública, sin credenciales.

Guardar con Git el tema completo, recursos y licencias, scripts, pruebas, documentación y capturas. No guardar `.env`, cachés de navegador, dependencias o datos de volúmenes.

```bash
git add drupal/web/themes/custom/roma_elegante docker/drupal scripts/start.sh tests/design-browser.mjs docs README.md
git diff --cached --stat
git diff --cached
git commit -m "feat: tema Drupal ROMA con diseño editorial responsive"
```

El repositorio aún no tiene commit inicial; para incluir también la infraestructura usa primero la lista completa de archivos del README. No se publicó en GitHub porque no se ha indicado un remoto.



<a id="fase-4-api"></a>

## Fase 4 — API segura

Objetivo: exponer catálogo y disponibilidad de demostración, registrar solicitudes autorizadas y consultar estado con una clave privada, sin listados de clientes.

Archivos: módulo `roma_api` con rutas, proveedor de autenticación Bearer asociado a un usuario Drupal, controlador JSON y permisos; script de aprovisionamiento `docker/drupal/integrations.php`; variables de ejemplo en `.env.example`; pruebas `tests/api.py`; contrato en `docs/api.md`.

Se ejecutaron comprobaciones de sintaxis PHP, compilación Python y validación Compose. Después de activar Docker se construyó la imagen, se instaló el módulo y pasaron 15 pruebas HTTP de contrato y seguridad con MariaDB. La evidencia se guarda en `verificacion-api.json`.
Comandos después de iniciar el motor:

```bash
docker compose build drupal
docker compose run --rm install
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush cache:rebuild
python3 tests/api.py
```

Guardar con Git el módulo, pruebas, aprovisionamiento, Compose y documentación. No guardar `.env`, secretos de integración ni datos de bases.



<a id="mejoras-usabilidad"></a>

## Mejoras de uso y personalización

Publicación: https://hoteles.3-91-159-139.sslip.io

- Disponibilidad y solicitud muestran los tres pasos: fechas, habitación, datos. La navegación distingue estadías adicionales y selección de habitaciones.
- `/admin/roma/fotografias` permite hasta tres imágenes por galería de portada y por tipo de habitación, con permisos de catálogo. JPEG, PNG o WebP, máximo 2 MB por archivo; Drupal limita dimensiones a 1920×1920. Descripción accesible obligatoria. Los archivos guardados se vuelven permanentes y registran uso; las imágenes ajenas a la galería mantienen sus usos existentes.
- Las fotos ilustrativas permanecen como respaldo; cargar una fotografía no acredita que sea del establecimiento. Las galerías de una sola foto no muestran flechas ni avance automático.
- La historia se muestra solo si el hotel proporciona texto; en su ausencia se explica el recorrido de reserva. No se inventa historia empresarial.
- Contexto del chat: fechas y ocupación durante 30 minutos en la sesión Drupal; no guarda transcripción, nombre, correo ni teléfono. Comprende seguimientos «somos dos», «llegamos mañana» y «dos noches», o fechas completas. «Reiniciar» borra el contexto. No se confirma disponibilidad desde la memoria: se consulta MariaDB.
- CSS separado en base (`roma.css`), experiencia, galerías e identidad para mantener los siguientes cambios por componente.

## Comprobaciones

Compilación Docker y caché Drupal completadas en EC2; informe de requisitos sin errores de severidad 2. Contexto del chat verificado desde navegador público con los cuatro mensajes consecutivos. Acceso administrativo a fotografías, subida de JPEG, guardado y aparición en portada comprobados. Imagen temporal de ensayo retirada y portada restaurada. La prueba automática de eliminación mediante navegación no era adecuada porque Drupal utiliza AJAX; no se presenta como una comprobación de ese botón.

Pendiente para una puesta en producción: fotografías autorizadas del hotel, historia proporcionada por el responsable, traducciones del contenido propio y pagos/facturación de producción. CSV y personalización completa de identidad no forman parte de esta actualización.

