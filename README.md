# Sistema Web de Reservas y Atención Automatizada para Hostal ROMA


## Presentación

El proyecto desarrolla una plataforma de reservas hoteleras y comercio electrónico mediante Drupal, integrada con un asistente de consultas y automatizaciones en n8n. Se toma como referencia un establecimiento de Juliaca, Puno, Perú, y se permite configurar el contenido para otros alojamientos.

El trabajo responde a la consigna de implementar una tienda en línea, integrarla con un chat mediante n8n y ejecutar sus servicios con Docker o Docker Compose.

## Objetivo

Integrar la consulta de habitaciones, el registro de solicitudes de reserva y la atención automatizada en un sistema administrable, con información almacenada en una base de datos común.

## Tecnologías

| Componente | Tecnología |
| --- | --- |
| Gestión de contenido y backend | Drupal 11, PHP 8.4 |
| Comercio electrónico | Drupal Commerce 3 |
| Base de datos | MariaDB 11.4 |
| Interfaz | Twig, HTML, CSS y JavaScript |
| Automatización y chat | n8n y widget @n8n/chat |
| Interpretación de consultas | Gemini, de configuración opcional |
| Entorno de ejecución | Docker y Docker Compose |
| Control de versiones | Git y GitHub |

Las dependencias PHP se registran en `drupal/composer.lock`. Los servicios y sus imágenes se definen en `compose.yaml`.

## Funcionalidades

- Catálogo de habitaciones, capacidad, servicios y tarifas en soles peruanos.
- Consulta de disponibilidad por fechas y número de huéspedes.
- Solicitudes de reserva con seguimiento privado mediante código y clave.
- Tienda de estadías, carrito, pedidos y pago simulado.
- Panel administrativo para gestionar inventario, tarifas y reservas.
- Recepción, calendario de ocupación, entradas, salidas y limpieza.
- Registro de cargos, saldos y movimientos de prueba.
- Chat conectado a Drupal mediante n8n, con interpretación opcional de preguntas mediante Gemini.
- Configuración del contenido, fotografías, ubicación y contacto del alojamiento.

El sistema valida las fechas y la capacidad de cada habitación. Las reservas confirmadas no pueden superponerse: el ingreso es inclusivo y la salida exclusiva. Una solicitud pendiente no constituye una confirmación.

## Arquitectura

El navegador accede a Drupal, que administra la interfaz, las operaciones y la persistencia en MariaDB. El chat utiliza una API de Drupal para comunicarse con n8n. Los flujos consultan el catálogo y la disponibilidad antes de responder. Gemini puede interpretar preguntas, mientras que Drupal mantiene el control de los datos comerciales.

Los servicios se ejecutan en contenedores. Los avisos por correo se capturan en Mailpit para su revisión local.

## Instalación local

Se requiere Git, Python 3 y Docker con el complemento Docker Compose. El motor de Docker debe estar en ejecución.

```bash
git clone https://github.com/dimas-flores/hostal-roma-drupal-n8n.git
cd hostal-roma-drupal-n8n
python3 scripts/init-env.py
sh scripts/start.sh
```

El primer comando de preparación genera el archivo `.env` con la configuración y las credenciales locales. El script de arranque construye la imagen de Drupal, instala el sitio e importa los flujos principales de n8n.

| Servicio | Dirección local |
| --- | --- |
| Página web | http://localhost:8080 |
| Inicio de sesión en Drupal | http://localhost:8080/user/login |
| Panel hotelero | http://localhost:8080/admin/roma |
| Editor n8n | http://localhost:5678 |
| Correos de prueba | http://localhost:8025 |

Las credenciales de Drupal se consultan en `.env`, en las variables `DRUPAL_ADMIN_USER` y `DRUPAL_ADMIN_PASSWORD`. El propietario de n8n se configura desde su asistente inicial. Las claves de proveedores de IA se guardan en las credenciales de n8n.

## Verificación

Las comprobaciones básicas del entorno pueden ejecutarse con:

```bash
sh tests/smoke.sh
python3 tests/api.py
python3 tests/http_checks.py
```

El directorio `tests/` contiene comprobaciones adicionales de reservas, permisos, concurrencia, comercio y automatización. Los resultados y requisitos de las pruebas se encuentran en `docs/`.

## Estructura del repositorio

```text
drupal/          Proyecto Composer, módulos y tema propios
docker/          Configuración de imágenes y servicios
n8n/workflows/   Flujos exportables de automatización
docs/            Documentación técnica y de uso
scripts/         Preparación, instalación y administración
tests/           Comprobaciones del sistema
compose.yaml     Definición del entorno de contenedores
.env.example     Variables de configuración de referencia
```

## Alcance académico

El desarrollo es independiente y no constituye el sistema oficial del establecimiento de referencia. Los datos de prueba y recursos visuales permiten demostrar el funcionamiento sin utilizar información de sus clientes. Los pagos son simulados y no realizan cobros. La extensión SUNAT beta permite ensayar documentos electrónicos, sin validez tributaria.

Las credenciales, archivos `.env`, bases de datos y dependencias instaladas se excluyen del repositorio. La procedencia de los recursos visuales se registra en la documentación.

## Documentación complementaria

- [Instalación](docs/instalacion.md).
- [Manual de uso](docs/manual-de-usuario.md).
- [Arquitectura](docs/arquitectura.md).
- [API](docs/api.md) e [integración n8n](docs/n8n.md).
- [Configuración de IA](docs/fase-6-ia-opcional.md).
- [SUNAT beta](docs/sunat-beta.md).
- [Despliegue en AWS](docs/despliegue-aws-ec2.md).
- [Verificaciones](docs/verificacion.md).
- [Referencias](docs/referencias.md) y [recursos visuales](docs/recursos-visuales.md).
- [Guion de presentación](docs/guion-demostracion.md).
