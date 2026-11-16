# Facturación electrónica: entorno beta

Esta fase incorpora pruebas técnicas de factura y boleta UBL 2.1, firma digital académica, envío SOAP al servicio beta y conservación de XML y respuestas CDR. **No emite comprobantes tributarios reales ni representa operaciones del establecimiento.** El código no tiene una opción de producción.

Se utiliza Greenter Lite **5.4.0**, bloqueado en Composer. Se eligió la biblioteca reducida para integrar generación XML, firma y transporte sin añadir herramientas externas de PDF ni dependencias de validación de otra versión Symfony. La imagen Docker conserva PHP 8.4, Drupal 11.4.12 y MariaDB 11.4, e incorpora SOAP. Las representaciones PDF se imprimen desde el navegador.

## Lo verificado

Se recibieron CDR con **código 0** para la factura de prueba **F901-1** y la boleta de prueba **B901-1**. Las comunicaciones se hicieron manualmente desde un entorno local aislado con TLS verificado y datos genéricos. Los resultados están en [la evidencia del servicio beta](verificacion-sunat-beta-real.json).

La primera respuesta incluía un directorio vacío `dummy/` y un XML. La validación inicial lo rechazó localmente: no se presentó como aceptado. Se conservó la respuesta de un segundo intento, se admitió el directorio vacío seguro y se revisó ese CDR sin reenviar. El historial retiene los intentos inciertos y la revisión local que confirmó el código recibido. La boleta posterior fue aceptada directamente. Se realizaron tres solicitudes de bajo volumen en total; no hubo pruebas de carga.

[SUNAT explica que beta solo prueba estructuras XML y no verifica la consistencia de los datos](https://cpe.sunat.gob.pe/noticias/servicio-beta-para-realizar-pruebas-ubl-21). Una aceptación aquí no acredita una venta, un pago, un certificado fiscal autorizado ni cumplimiento del régimen tributario del hotel.

## Revisar la demostración

Mientras el laboratorio local esté iniciado, puedes abrir:

- `http://127.0.0.1:18088/admin/roma/sunat-beta`
- Documento de factura: `/admin/roma/sunat-beta/1`
- Documento de boleta: `/admin/roma/sunat-beta/2`

Usa las credenciales Drupal del archivo privado de accesos. Este servidor local escucha solo en loopback; no es accesible desde otros equipos. La fase sigue pendiente de publicación en EC2 porque la IP anterior no responde. El despliegue requiere recuperar el acceso a la instancia; no se modificaron servicios AWS sin conexión.

También están los [ejemplos exportados](ejemplos-sunat-beta/README.md) para revisar sin abrir la aplicación.

![Factura de prueba con respuesta aceptada en beta.](capturas/sunat-beta-aceptado.png)

## Preparar una prueba firmada

Requisitos: permiso SUNAT beta, reserva confirmada y cuenta de demostración abierta, con cargos positivos y saldo cero. Este primer ejercicio corresponde a una cuenta saldada de contado; no calcula obligaciones fiscales de anticipos ni créditos.

1. Abre **Cuentas y saldos**.
2. Entra a la cuenta saldada.
3. Pulsa **Preparar documento de prueba SUNAT beta**.
4. Selecciona **Factura de prueba UBL 2.1** o **Boleta de prueba UBL 2.1**.
5. Marca que entiendes el carácter académico.
6. Pulsa **Preparar y firmar XML de prueba**.

**Resultado:** verás el número beta, su estado y la huella SHA-256 del XML. Todavía no se habrá enviado. Preparar nuevamente desde un formulario nuevo asigna otro número; reenviar el mismo formulario no duplica su documento.

El XML usa exclusivamente el emisor genérico de pruebas, un destinatario genérico y un escenario de IGV del 18 %. No se exportan nombres, correos, teléfonos ni documentos personales del huésped. Esa tasa es un ejercicio técnico y no determina el tratamiento tributario del hospedaje. Los números F901/B901 son series de demostración; los ensayos retirados pueden dejar huecos y no constituyen una numeración fiscal de producción.

## Enviar al servicio beta

El envío requiere SOAP y las credenciales genéricas de prueba en el servidor. No se admiten claves SOL reales. El certificado se genera para el ejercicio: es autofirmado y no es un certificado fiscal autorizado.

1. Abre el documento preparado.
2. Revisa el importe, número y estado.
3. Pulsa **Revisar y enviar a SUNAT beta**.
4. Marca la confirmación de datos genéricos.
5. Pulsa **Enviar solo a SUNAT beta**.

**Resultado:** se conserva el intento y su respuesta. **Aceptado en beta** requiere un CDR interpretable que corresponda al mismo número. Un error o interrupción aparece como **Respuesta no verificable**, nunca como aceptación. El rechazo conserva el código recibido.

Se permite un envío por minuto para toda la instalación y hasta tres intentos manuales por documento. No hay procesamiento masivo ni reintento automático. Cambiar la cuenta después de preparar el XML impide enviarlo: prepara otra prueba para conservar la evidencia original. Un documento aceptado o rechazado definitivamente no se reenvía.

## Resolver una respuesta incierta

Si el documento tiene un CDR conservado en su respuesta actual, puedes pulsar **Revisar el CDR guardado sin reenviar** y confirmar. Esa acción valida la misma evidencia localmente, sin llamar al servicio, y registra el cambio de estado con usuario y huella del CDR. No implica por sí sola que la respuesta sea aceptada: primero debe corresponder al número y contener un resultado válido.

Si un proceso quedó en envío durante más de cinco minutos, puedes recuperar su estado como incierto. Revisa el historial antes de reenviar; no se da por rechazado ni aceptado automáticamente. Una respuesta tardía se conserva en su intento sin sobrescribir un resultado más reciente. Los CDR de cada intento pueden descargarse desde el historial para revisión técnica.

## Descargar o imprimir

1. Abre el documento.
2. Pulsa **Descargar XML firmado de prueba** para guardar el XML.
3. Si existe respuesta, pulsa **Descargar CDR recibido**.
4. Pulsa **Imprimir representación de prueba** para imprimir o guardar como PDF.

**Resultado:** obtendrás archivos del ejercicio. Las descargas requieren permiso, se sirven como adjuntos sin caché y el XML no contiene la clave privada. La representación conserva el aviso **SIN VALIDEZ TRIBUTARIA**; no sustituye un comprobante válido de producción.

## Instalar o actualizar

Configura en el `.env` privado del servidor únicamente las credenciales genéricas beta publicadas en la [guía oficial del proyecto Greenter](https://greenter.dev/starter/), en las variables `ROMA_SUNAT_BETA_USER` y `ROMA_SUNAT_BETA_PASSWORD`. El código comprueba esa identidad de prueba y rechaza usuarios arbitrarios. No pegues credenciales en GitHub ni utilices una clave SOL del negocio.

La carpeta privada y la clave académica quedan en el volumen `sunat_beta_private`, fuera de la raíz web, con permisos 700 y 600. El volumen se comparte con el servicio de instalación. Antes de actualizar una base existente, conserva una copia de su base y volúmenes.

```bash
docker compose build drupal
docker compose stop drupal
docker compose run --rm --no-deps --entrypoint sh install -c 'chown -R www-data:www-data /var/www/private/sunat-beta && chmod 700 /var/www/private/sunat-beta && vendor/bin/drush en roma_sunat_beta --yes && vendor/bin/drush updatedb --yes'
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush cache:rebuild
```

El instalador general habilita también el módulo en proyectos nuevos. En instalaciones que ya tengan una versión del módulo, `updatedb` aplica sus actualizaciones. Los roles públicos no reciben el permiso fiscal; el responsable hotelero sí lo recibe en esta demo.

## Pruebas disponibles

```bash
docker compose cp tests/sunat-beta.php drupal:/tmp/roma-sunat-beta.php
docker compose exec -T drupal vendor/bin/drush php:script /tmp/roma-sunat-beta.php
deno run --cached-only --allow-all tests/sunat-beta-browser.mjs
```

Estas pruebas no envían documentos a SUNAT. Utilizan transportes simulados para las reglas y solo preparan/descargan documentos en navegador. La prueba manual `tests/sunat-beta-live.php` está separada de `verify-all.sh` y exige `ROMA_ALLOW_BETA_LIVE=1`; úsala solo para una verificación puntual, nunca para carga. `ROMA_BETA_TEST_TYPE` permite seleccionar `01` o `03`.

Se aprobaron 44 comprobaciones de reglas, 17 de navegador y las regresiones locales de cuentas (40) y recepción (29). Consulta [las limitaciones y detalles de la verificación](verificacion.md#verificacion-sunat-beta).

## Errores frecuentes

| Situación | Acción |
| --- | --- |
| La cuenta tiene saldo | Completa el ejercicio de pago simulado antes de preparar una prueba de contado. |
| Configuración beta incompleta | Revisa SOAP y las variables de credenciales genéricas; no introduzcas una clave real. |
| Espera entre envíos | Respeta el minuto de intervalo; no ejecutes pruebas masivas. |
| La cuenta cambió | Conserva el documento anterior y prepara una nueva prueba. |
| No hay CDR | Revisa el intento; un fallo de transporte no prueba aceptación ni rechazo. |
| Carpeta o firma no disponibles | Revisa el volumen privado, su propietario y permisos; no copies claves al directorio público. |
| El navegador no puede descargar | Inicia sesión con una cuenta autorizada. No hay enlaces públicos de descarga. |

**Glosario:** UBL es el formato XML del documento; XML firmado incluye una firma y certificado público; CDR es la respuesta de recepción; correlativo es el número secuencial de la serie beta; respuesta incierta significa que la aplicación todavía no puede establecer el resultado.

Referencias: [Greenter Lite y su guía](https://greenter.dev/starter/), [código de Greenter](https://github.com/thegreenter/greenter), [condiciones del servicio beta SUNAT](https://cpe.sunat.gob.pe/noticias/servicio-beta-para-realizar-pruebas-ubl-21). Esta entrega no implementa producción, notas de crédito/débito, resúmenes diarios, contingencia ni integración con OSE/PSE.
