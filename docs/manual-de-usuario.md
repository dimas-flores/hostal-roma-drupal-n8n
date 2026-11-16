# Manual de uso del sistema


<a id="guia-tienda"></a>

## Guía rápida de la tienda y reservas de demostración

**Proyecto:** Golden Roma · prototipo académico independiente. **Versión:** 1.1, 10/11/2026. **Autores:** Dimas Mijael Flores Yerba y Jords Williams Calla Mamani, Universidad Nacional de Juliaca.

Esta guía está dirigida al visitante y al administrador de la demostración. Usa únicamente nombres y correos ficticios. Los precios y los pagos son de prueba. Necesitas la aplicación iniciada y un navegador; el administrador necesita su cuenta asignada.

## Elegir una estadía

1. Abre [la tienda local](http://localhost:8080/tienda).
2. Pulsa **Elegir fechas** en una habitación.
3. Selecciona **Llegada**.
4. Selecciona **Salida**, posterior a la llegada.
5. Indica **Huéspedes en esta habitación**, dentro de su capacidad.
6. Pulsa **Agregar al carrito**.

**Resultado:** verás la estadía, sus fechas, huéspedes, noches e importe ficticio. Todavía no se registra una reserva. Para agregar otra habitación, usa **Seguir eligiendo estadías**.

![Figura 1. Catálogo de habitaciones de demostración.](capturas/tienda-commerce.png)

## Revisar o quitar una estadía

1. Abre **Carrito** desde la navegación.
2. Revisa las fechas, los huéspedes y el total de cada estadía.
3. Pulsa **Quitar estadía** si deseas eliminar una línea.

**Resultado:** el carrito muestra las líneas restantes y actualiza el total. Para modificar fechas, quita esa estadía y agrégala nuevamente. En móvil, abre el menú mediante el botón de tres líneas.

![Figura 2. Carrito de demostración en un teléfono.](capturas/carrito-commerce-movil.png)

## Completar tus datos y revisar el pedido

1. Pulsa **Continuar al checkout** en el carrito.
2. Completa **Nombre completo**.
3. Completa **Correo electrónico**.
4. Deja vacío **Teléfono (opcional)** o escribe un número ficticio válido.
5. Marca **Utilizo datos ficticios y entiendo que el pedido y el pago son de demostración**.
6. Pulsa **Revisar pedido**.
7. Pulsa **Elegir pago de prueba** después de revisar el resumen.

**Resultado:** aparecerán las opciones del pago de demostración. Si un campo es inválido, verás su explicación junto al campo y conservarás los datos ingresados.

## Probar el pago y guardar tus códigos

1. Selecciona **Aprobar pago de prueba**.
2. Pulsa **Registrar pedido de prueba**.
3. Guarda el **Código de solicitud** de cada habitación en un lugar privado.
4. Guarda su **Clave privada de consulta** junto al código correspondiente.

**Resultado:** aparecerá **Pedido de demostración registrado**. Las solicitudes estarán pendientes de confirmación administrativa. No se habrá cobrado dinero.

Para ensayar otros resultados, selecciona **Rechazar pago de prueba** o **Cancelar operación de prueba** antes de enviar. Verás el aviso correspondiente; el carrito se conserva y no se registran reservas. Puedes volver al carrito o elegir aprobar después.

## Consultar una solicitud

1. Abre **Consultar mis solicitudes con código y clave** desde el recibo.
2. Introduce el código de una solicitud.
3. Introduce su clave privada.
4. Envía el formulario de consulta.

**Resultado:** verás el estado vigente de esa solicitud. Consulta cada habitación con su propio código. El chatbot orienta, pero no solicita tus claves privadas ni confirma habitaciones.

## Confirmar o cancelar como administrador

1. Accede con la cuenta administrativa mediante **Acceso administrativo**, al pie de página.
2. Abre **Reservas** en el panel hotelero.
3. Localiza el código de la solicitud de prueba.
4. Pulsa **Confirmar** o **Cancelar**.
5. Confirma la acción en la pantalla siguiente.
6. Abre **Pedidos de la tienda** después de la siguiente ejecución de n8n, programada cada minuto.

**Resultado:** cada reserva refleja la decisión. Si todas las reservas del pedido se confirman, el pedido muestra la confirmación; si todas se cancelan, muestra la cancelación. Las decisiones mixtas requieren revisar las solicitudes individuales.

## Pedir ayuda al asistente

1. Abre el botón flotante del chat.
2. Escribe una pregunta, por ejemplo **¿Cómo uso el carrito de la tienda?**.
3. Lee la respuesta y utiliza el enlace correspondiente.

**Resultado:** recibirás orientación del flujo n8n conectado al sistema. Para consultar disponibilidad, incluye llegada, salida y número de personas. Los mensajes de prueba se pueden revisar en la [bandeja local](http://localhost:8025).

## Resolver problemas

| Síntoma | Causa probable | Qué hacer |
| --- | --- | --- |
| El teléfono no admite letras | Se escribieron letras en el campo opcional | Déjalo vacío o usa números y separadores permitidos. |
| La habitación ya está en tu carrito | Hay dos líneas superpuestas para la misma habitación | Quita la anterior o elige otra habitación. |
| Una habitación dejó de estar disponible | Cambió la disponibilidad antes de finalizar | Vuelve al carrito y elige otra habitación o fechas. |
| La tarifa o cantidad cambió | El precio se actualizó durante el proceso | Vuelve al carrito y revisa el total antes de continuar. |
| La sesión no es válida | El formulario está vencido o pertenece a otra sesión | Recarga la página y vuelve a enviarlo. |
| Se alcanzó el límite de intentos | Se hicieron muchos envíos seguidos | Espera 15 minutos antes de volver a intentar. |
| El pedido está pendiente tras aprobar el pago de prueba | Falta la decisión administrativa | Confirma cada solicitud desde el panel autorizado. |

**Glosario:** el *carrito* agrupa estadías; el *checkout* es el proceso para registrar el pedido; el *pedido* reúne solicitudes; una *solicitud pendiente* espera al administrador; el *pago de prueba* ensaya un resultado sin dinero real.

**Fuentes de la guía:** pantallas, textos del módulo de tienda y pruebas de navegador del repositorio. Las capturas del catálogo y del carrito acompañan el documento; se omite la captura del recibo para conservar privadas sus claves. No quedan funciones de esta guía por confirmar; la pasarela bancaria externa queda fuera de la demostración local.



<a id="tienda-commerce"></a>

## Tienda de estadías con Drupal Commerce

La entrega utiliza Drupal como la opción PHP de la consigna compartida. Drupal Commerce añade productos, variaciones, carrito, pedidos, checkout y pagos de prueba a la aplicación hotelera existente. Esta implementación corresponde a la opción Drupal; las otras plataformas enumeradas son alternativas del listado.

## Componentes y versiones

Composer resolvió `drupal/commerce` **3.3.14**, fijado con sus dependencias en `drupal/composer.lock`. Commerce 3.x admite Drupal 11 según el [proyecto oficial](https://www.drupal.org/project/commerce). Se conserva Drupal 11.4.12 y PHP 8.4; se incorpora la extensión BCMath requerida por Commerce. La instalación dentro de Docker comprueba los requisitos de Composer.

El módulo propio `roma_shop` adapta los productos de Commerce a las habitaciones existentes. Usa el carrito y el control de acceso de Commerce, un flujo de checkout con paneles propios y una pasarela académica que solo ofrece modo `test`. La arquitectura sigue el [sistema de checkout](https://docs.drupalcommerce.org/v2/developer-guide/checkout/) y la [API de pasarelas de Commerce](https://docs.drupalcommerce.org/v2/developer-guide/payments/creating-payment-gateway/).

## Recorrido disponible

| Pantalla | Función |
| --- | --- |
| `/tienda` | Catálogo del inventario activo con imágenes ilustrativas, capacidad y tarifa ficticia. |
| `/tienda/agregar` | Selección de habitación, llegada, salida y huéspedes mediante formulario protegido. |
| `/carrito` | Varias estadías, fechas y huéspedes por línea, noches, importes en PEN y eliminación mediante POST. |
| `/checkout/{pedido}` | Datos ficticios, revisión, resultado del pago de prueba y recibo privado. |
| `/admin/roma/pedidos` | Pedidos y sus solicitudes relacionadas; exige permiso administrativo de Commerce. |
| `/admin/commerce/orders` | Administración nativa de pedidos de Drupal Commerce. |

El carrito permite hasta seis estadías. Una línea representa una habitación y un intervalo de fechas; su cantidad corresponde al número de noches. El procesador de pedidos recalcula cantidad, tarifa y título desde los datos hoteleros. No acepta una cantidad arbitraria para cambiar la duración de una estadía.

El precio, las fechas y la disponibilidad se verifican al agregar y finalizar. No se permite agregar la misma habitación con fechas superpuestas dentro del mismo carrito. Las reservas confirmadas se vuelven a consultar antes de escribir. Continúa aplicándose el intervalo `[ingreso, salida)`.

## Persistencia y autorización

Cada habitación tiene una variación Commerce con SKU estable. `roma_shop_booking` enlaza cada línea del pedido con una solicitud hotelera; no publica datos de clientes. El total se calcula con precios del servidor, en PEN.

El pago local y todas las solicitudes del pedido se escriben dentro de una transacción, usando el mismo bloqueo InnoDB del inventario. Los identificadores de envío son deterministas por pedido y línea. Un fallo revierte todas las solicitudes y el pago. Un reenvío no genera otra aprobación ni solicitudes duplicadas.

El checkout anónimo añade token CSRF por pedido y paso, señuelo contra envíos automatizados, validación y límite de intentos. La ruta alternativa `/cart` redirige al carrito propio con fechas y protección CSRF explícita. Commerce comprueba la pertenencia del carrito y del recibo a la sesión o al usuario. Conocer el número del pedido no concede acceso desde otra sesión. El recibo conserva las claves privadas en la sesión del comprador; no se incluyen en el chatbot ni en las evidencias versionadas.

El resultado de prueba aprobado **no confirma habitaciones**. Cada solicitud permanece pendiente hasta la decisión administrativa. El pedido usa los estados carrito, solicitudes pendientes, reservas confirmadas y reservas canceladas.

## n8n y avisos

Los pedidos generan los mismos eventos transaccionales de reserva que el resto del sistema. El workflow `roma-automation.json` llama al endpoint protegido, procesa avisos en Mailpit y reconcilia el pedido con los estados de sus reservas. Cuando todas se confirman, el pedido refleja la confirmación; cuando todas se cancelan, refleja la cancelación. Los estados mixtos quedan pendientes de revisión.

El bot responde preguntas sobre tienda, carrito y checkout. Las consultas de tarifas y disponibilidad siguen resolviéndose en Drupal/MariaDB a través de n8n. Los tres flujos exportables siguen en `n8n/workflows/`.

## Pago académico y proveedor externo

El comprador puede elegir aprobar, rechazar o cancelar una operación de prueba. La aprobación registra una entidad de pago Commerce en modo `test`; rechazo y cancelación muestran un aviso, conservan el carrito y no crean reservas ni pagos aprobados. Los intentos quedan registrados en los datos privados del pedido. No se recopilan tarjetas ni se contacta a un proveedor bancario.

**La pasarela es local y académica. No es Mercado Pago, no realiza cobros ni emite comprobantes comerciales.** La consigna compartida exige tienda, CMS, chat n8n y Docker; no identifica un proveedor de pagos. Una integración externa requiere desarrollo del conector, cuenta y credenciales de prueba del estudiante, notificaciones HTTPS y pruebas con el proveedor. No se declara realizada esa integración.

## Arranque y pruebas

```bash
python3 scripts/init-env.py
sh scripts/start.sh
sh scripts/verify-all.sh
```

Al actualizar, el arranque detiene el contenedor Drupal anterior durante la instalación de módulos. Así evita que una imagen sin Commerce regenere cachés mientras cambia la base. La instalación conserva inventario y tarifas existentes. Para repetir solo las comprobaciones nuevas:

```bash
docker compose cp tests/commerce.php drupal:/tmp/roma-commerce.php
docker compose exec -T drupal vendor/bin/drush php:script /tmp/roma-commerce.php
deno run --cached-only --allow-all tests/commerce-browser.mjs
```

Las pruebas usan datos ficticios y cancelan sus solicitudes conservando el historial. La prueba de navegador utiliza las credenciales locales sin imprimirlas. Sus capturas muestran catálogo y carrito, sin claves privadas. Ver `verificacion-commerce-domain.json` y `verificacion-commerce-browser.json` para los resultados de ejecución.



<a id="fase-3-gestion-hotelera"></a>

## Fase 3 — Gestión hotelera funcional

Se implementó el módulo `roma_booking` mediante Schema API, Database API, Form API, rutas y permisos de Drupal. MariaDB almacena tipos, habitaciones, tarifas en céntimos, servicios y sus asociaciones, estados, reservas, historial de cambios y mensajes. Los usuarios y roles pertenecen al sistema de acceso de Drupal. La semilla crea seis habitaciones ficticias y no importa clientes ni reservas del negocio real.

## Funciones disponibles

- `/disponibilidad`: consulta fechas y ocupación contra las reservas confirmadas de la demo.
- `/reservar`: guarda una solicitud pendiente y entrega código y clave privada de consulta.
- `/mi-reserva`: exige código y clave; devuelve estado, fechas y estimado, sin datos personales.
- `/contacto`: guarda mensajes de prueba para el administrador; no envía correos al negocio.
- `/admin/roma`: indicadores y acceso al panel.
- `/admin/roma/catalogo`: tipos, habitaciones, tarifas y servicios; permite crear, editar y desactivar.
- `/admin/roma/reservas`: revisión de las últimas 200 solicitudes y formularios POST para confirmar o cancelar.
- `/admin/roma/mensajes`: consulta protegida de mensajes de prueba.

La portada obtiene categorías, tarifas y servicios de las tablas hoteleras; la configuración visual antigua ya no es su fuente de catálogo. Cada habitación tiene un código propio. Las tarifas nuevas no alteran el precio estimado almacenado en solicitudes anteriores.

## Integridad y acceso

Ingreso inclusivo y salida exclusiva: un ingreso en la fecha de salida de otra reserva está permitido. Las confirmadas vigentes bloquean. La ampliación de [recepción](manual-de-usuario.md#recepcion) incorpora mantenimiento y preparación de hoy; las estadías finalizadas conservan su historia sin bloquear nuevas entradas. Las pendientes no son bloqueos ni garantías de habitación. Antes de registrar y confirmar se comprueban fechas, capacidad, actividad y ocupación. Una transacción InnoDB bloquea una fila de coordinación antes de cualquier modificación de inventario o reserva; la comprobación y la actualización ocurren dentro de ese bloqueo. Se prioriza la consistencia sobre el paralelismo para esta demo académica.

No se permite reactivar una cancelada, reducir capacidad por debajo de una confirmada vigente ni confirmar una habitación desactivada. Se rechazan estadías de menos de una noche o más de 180 noches. Los importes se calculan en el servidor usando enteros en céntimos. Un identificador único impide repetir un mismo envío de reserva.

Los formularios de administración usan CSRF de Drupal. Los formularios anónimos crean una sesión con marcador sin datos personales y añaden una validación CSRF específica, honeypot y límites de intentos por identificador derivado de IP. Los registros duraderos de reserva guardan el hash de la clave de consulta; el secreto se muestra en el recibo del envío y nunca se añade a una URL o a los logs de prueba. El estado temporal del formulario puede conservar el recibo durante su sesión de presentación.

El rol `roma_manager` tiene permisos hoteleros; un usuario autenticado sin ese rol no obtiene acceso administrativo. No se expone ningún listado público de clientes. La API externa, n8n y los pagos aún no están implementados.

## Pruebas ejecutadas

- 21 pruebas de dominio contra MariaDB real: fechas inexistentes o pasadas, capacidad, ocupación fraccionaria, teléfono inválido, estados, cálculo exacto, privacidad, idempotencia, superposición, fechas contiguas, cambios de tarifa, cancelación y desactivación. Las fixtures temporales se eliminan al terminar.
- Dos procesos Drupal intentaron confirmar solicitudes superpuestas simultáneamente: exactamente uno confirmó y el otro fue rechazado. Se verificó una sola confirmada en la base.
- Un token CSRF anónimo manipulado fue rechazado sin crear reservas.
- Un usuario autenticado sin permisos fue rechazado en cuatro rutas de administración.
- Prueba de navegador de extremo a extremo: disponibilidad, solicitud pendiente, recibo, consulta privada, acceso al panel, confirmación, consulta de estado confirmado, cancelación y contacto. Se comprobó la adaptación móvil de los cuatro formularios públicos. Resultado en `verificacion-fase-3-browser.json`.

Las pruebas dejaron dos solicitudes ficticias canceladas y un mensaje de prueba para revisión. No corresponden a clientes reales. Durante el desarrollo se corrigieron la denominación del argumento `form_state` requerida por el resolvedor de formularios y la persistencia de sesiones anónimas, necesaria para los tokens. La prueba de navegador se corrigió para separar las sesiones del visitante y administrador.

## Comandos

```bash
docker compose build drupal
docker compose run --rm install
docker compose up -d --wait drupal
docker compose exec -T drupal vendor/bin/drush cache:rebuild
docker compose cp tests/hotel-domain.php drupal:/tmp/roma-hotel-domain.php
docker compose exec -T drupal vendor/bin/drush php:script /tmp/roma-hotel-domain.php
python3 tests/hotel-concurrency.py
python3 tests/hotel-security.py
deno run --cached-only --allow-all tests/hotel-browser.mjs
```

Para revisar manualmente, solicita una reserva usando datos ficticios, guarda código y clave, inicia sesión administrativa, confirma desde el panel y consulta nuevamente el estado con esas claves. Cancela después para liberar el inventario de prueba. No hay pagos ni reservas comerciales.

Guardar con Git el módulo, los cambios del tema y de instalación, las pruebas y la documentación. No guardar `.env`, sesiones, cachés o volúmenes. El repositorio todavía no tiene remoto de GitHub identificado.

## Aclaración de búsquedas por capacidad

Se añadió una explicación cuando los huéspedes solicitados superan la capacidad máxima de las habitaciones activas. La pantalla indica que la búsqueda corresponde a una sola habitación y ofrece ajustar la ocupación conservando fechas; para un grupo se requieren solicitudes independientes por habitación. No se cambió el inventario ni se simula capacidad adicional.

Verificación del 10/11/2026: la búsqueda del 11 al 15 de noviembre para tres huéspedes explica el límite actual de dos personas, no ofrece reservas incompatibles y conserva ambas fechas en el enlace de ajuste. Al consultar para dos huéspedes aparecen habitaciones y enlaces de solicitud. Se comprobó el enlace en navegador a 1440 y 390 píxeles, sin desbordamientos. Evidencia visual: `capturas/disponibilidad-capacidad.png`.



<a id="recepcion"></a>

## Guía de recepción y calendario

Dirigida al administrador y al responsable hotelero del prototipo académico. Usa únicamente huéspedes ficticios. Necesitas iniciar sesión y tener permiso de gestión de recepción. La información no representa la operación oficial del hospedaje.

## Consultar habitaciones por fecha

1. Abre **Recepción y calendario** desde el panel hotelero.
2. Selecciona **Desde**.
3. Selecciona **Mostrar**: 7, 14 o 31 días.
4. Pulsa **Actualizar calendario**.

**Resultado:** verás una fila por habitación y una columna por noche. En verde aparecen confirmadas, en amarillo solicitudes pendientes y en rojo mantenimiento. Las pendientes no bloquean. En móvil puedes desplazar horizontalmente el calendario. Las habitaciones inactivas conservan su historial y no admiten nuevas solicitudes.

![Calendario de recepción del prototipo.](capturas/recepcion-aws.png)

## Registrar una entrada

La solicitud debe estar confirmada, la llegada prevista debe haber ocurrido y la salida prevista debe ser posterior a hoy. La habitación debe estar activa, lista, sin mantenimiento ni otra entrada pendiente de salida.

1. Abre **Recepción y calendario**.
2. Busca la reserva en **Llegadas, salidas y ocupación por resolver**.
3. Pulsa **Registrar entrada**.
4. Revisa el registro que aparece en la confirmación.
5. Pulsa **Confirmar operación**.

**Resultado:** la estadía pasa a **Hospedado** y se guarda la hora de entrada y el usuario responsable. Repetir la misma entrada no crea eventos duplicados. La entrada no cobra dinero ni emite boletas o facturas.

## Registrar una salida

La estadía debe tener una entrada registrada. La salida conserva el historial comercial y el importe estimado original: no calcula devoluciones ni altera pagos.

1. Busca la estadía **Hospedado** en recepción.
2. Pulsa **Registrar salida**.
3. Pulsa **Confirmar operación**.

**Resultado:** verás **Salida registrada** y la habitación quedará **Pendiente de limpieza**. Una salida anticipada libera las noches restantes; para recibir huéspedes hoy también debes marcar la habitación lista. No canceles una reserva con entrada o salida registrada para modificar su historial.

## Preparar una habitación

No puedes cambiar su limpieza mientras tenga una entrada sin salida. La limpieza de hoy restringe entradas para hoy; no impide solicitudes para fechas futuras.

1. Localiza la habitación en la primera columna del calendario.
2. Pulsa **En limpieza** para iniciar la preparación.
3. Confirma la operación.
4. Al terminar, pulsa **Lista**.
5. Confirma la operación.

**Resultado:** queda preparada para una entrada, siempre que se cumplan las demás reglas de disponibilidad. Si necesitas volver a limpiarla, selecciona **Pendiente de limpieza**.

## Programar mantenimiento

El intervalo no puede superponerse con reservas confirmadas vigentes ni otro mantenimiento activo. Resuelve primero cualquier conflicto; no se cancelan huéspedes automáticamente.

1. Pulsa **Programar mantenimiento**.
2. Selecciona **Habitación**.
3. Completa **Desde**.
4. Completa **Hasta (exclusivo)**.
5. Escribe un **Motivo** de 5 a 200 caracteres, sin datos personales.
6. Pulsa **Programar mantenimiento**.

**Resultado:** la habitación queda fuera de disponibilidad para ese intervalo, tanto en la web como en la API, el carrito y el chatbot. La noche de la fecha final vuelve a estar disponible si no tiene otros bloqueos.

Para terminarlo, filtra un periodo que incluya el bloqueo, busca **Mantenimientos del periodo**, pulsa **Liberar** y confirma. La disponibilidad se calcula nuevamente; liberar no garantiza una habitación libre si existe otro impedimento.

## Registrar que un huésped no se presentó

Solo aplica a una reserva confirmada cuya fecha de llegada ya ocurrió y que no tiene entrada.

1. Busca la reserva en recepción.
2. Pulsa **No se presentó**.
3. Confirma la operación.

**Resultado:** la recepción registra **No se presentó** y la reserva se cancela con auditoría para liberar el inventario. No se realizan devoluciones automáticas. El historial se consulta en las operaciones auditadas.

## Resolver problemas

| Síntoma | Motivo | Acción |
| --- | --- | --- |
| No aparece una llegada futura | La lista de recepción se centra en hoy y asuntos pendientes | Usa el calendario para consultar días futuros. |
| Entrada rechazada | Fecha incorrecta, habitación ocupada, sucia, desactivada o en mantenimiento | Revisa la reserva y resuelve la condición indicada. |
| Mantenimiento rechazado | Hay una reserva confirmada o bloqueo superpuesto | Cambia las fechas o resuelve la reserva desde el panel. |
| Confirmación de reserva rechazada | Cambió la disponibilidad desde la solicitud | Revisa mantenimiento y estado de limpieza; las pendientes no garantizan plaza. |
| No puedes entrar al calendario | La cuenta no tiene el permiso necesario | Solicita al administrador el rol responsable hotelero o el permiso de recepción. |
| Aparece una salida vencida | No se registró el checkout del huésped | Verifica la situación y registra la salida antes de recibir otro huésped. |

**Glosario:** *confirmada* es el estado de la reserva; *hospedado* indica una entrada sin salida; *lista* es una habitación preparada; *mantenimiento* es un bloqueo por intervalo; *auditoría* registra quién realizó cada operación y cuándo, con hora de Perú.

Fuentes: módulo de recepción, reglas compartidas de disponibilidad y pruebas ejecutadas en AWS. La facturación SUNAT y los cobros reales continúan pendientes de sus fases de integración; esta pantalla no los implementa.



<a id="cuentas-y-saldos"></a>

## Guía de cuentas, adelantos y saldos

Para el administrador o responsable hotelero con permiso financiero. Necesitas una solicitud existente y datos ficticios. **Esta fase es un libro de demostración: no cobra dinero, no verifica transferencias bancarias y no emite comprobantes SUNAT.** Los pagos de prueba de Commerce se conservan aparte y no se suman como ingresos de esta cuenta.

## Abrir la cuenta de una estadía

1. Abre **Cuentas y saldos** en el panel hotelero.
2. Localiza la solicitud.
3. Pulsa **Ver cuenta**.
4. Revisa el estimado original.
5. Marca **Entiendo que esta operación es una simulación y que no se moverá dinero**.
6. Pulsa **Abrir cuenta de demostración**.

**Resultado:** se registra el cargo inicial a partir del estimado de la solicitud. Abrirla nuevamente no duplica el cargo. Consultar la pantalla antes de pulsar el botón no crea movimientos. También puedes acceder mediante **Ver cuenta** en Reservas o **Ver cuenta y saldo** en recepción.

## Registrar un adelanto o completar el saldo

La reserva debe estar confirmada. El importe no puede superar el saldo pendiente. Los medios disponibles son simulados y no solicitan información de tarjetas.

1. Abre la cuenta.
2. Selecciona **Adelanto / pago simulado** en **Movimiento**.
3. Completa **Importe en PEN**, por ejemplo `25.00` para un ejercicio.
4. Selecciona el **Medio simulado**.
5. Escribe el **Motivo del movimiento**.
6. Marca el consentimiento de simulación.
7. Pulsa **Registrar movimiento simulado**.

**Resultado:** aumenta **Pagado neto** y disminuye **Saldo pendiente**. Un reenvío del mismo movimiento no lo duplica. Estos valores representan el ejercicio, no dinero recibido por el hotel.

## Registrar cargos y descuentos

1. Selecciona **Cargo adicional** o **Descuento**.
2. Completa un importe positivo con hasta dos decimales.
3. Escribe el motivo de al menos cinco caracteres.
4. Marca el consentimiento.
5. Pulsa **Registrar movimiento simulado**.

**Resultado:** cambia **Cargos netos** y se recalcula el saldo. Un descuento no puede hacer negativos los cargos netos. Si se descuenta después de haber pagado, puede aparecer **Saldo a favor del huésped**; no se devuelve dinero automáticamente.

## Registrar una devolución simulada

Una devolución no puede superar el pagado neto disponible. Cancelar la reserva no genera una devolución automática ni borra la cuenta.

1. Selecciona **Devolución simulada**.
2. Completa el importe a devolver.
3. Selecciona el medio simulado.
4. Escribe el motivo.
5. Marca el consentimiento.
6. Pulsa **Registrar movimiento simulado**.

**Resultado:** disminuye **Pagado neto**. El saldo puede volver a quedar pendiente si no se corrigen también los cargos correspondientes. Es una simulación contable y no una orden de devolución bancaria.

## Corregir un movimiento

La anulación conserva el original y añade su efecto inverso. No es una nota de crédito SUNAT ni una devolución bancaria. No se anula una contrapartida; registra una nueva corrección según corresponda.

1. Localiza el movimiento en el historial.
2. Pulsa **Anular con motivo**.
3. Revisa el identificador del original.
4. Explica el **Motivo de la anulación**.
5. Pulsa **Registrar contrapartida de demostración**.

**Resultado:** aparece una nueva línea que referencia el original y se recalculan los saldos. Si la corrección dejaría pagos o cargos netos negativos, se rechaza: debes resolver primero los movimientos relacionados. No se puede anular el mismo original dos veces.

## Imprimir una constancia interna

1. Localiza el movimiento.
2. Pulsa **Constancia interna**.
3. Revisa su estado, importe y motivo.
4. Pulsa **Imprimir constancia interna**.
5. Selecciona la impresora o **Guardar como PDF** en el navegador.

**Resultado:** obtendrás una representación interna marcada **SIN VALIDEZ TRIBUTARIA**. Un movimiento anulado se identifica como tal. Este documento no acredita un pago real.

## Consultar movimientos por periodo

1. Abre **Cuentas y saldos**.
2. Selecciona **Desde** y **Hasta (incluido)**.
3. Pulsa **Consultar periodo**.

**Resultado:** verás la variación de cargos, pagos netos simulados y número de movimientos dentro de esas fechas, con horario de Perú. Un periodo con devoluciones puede mostrar un flujo negativo. La tabla inferior muestra las últimas 100 solicitudes y sus saldos actuales, independientemente del periodo seleccionado. Los importes históricos se conservan aunque se modifiquen tarifas después.

## Entender los avisos de n8n

Cada cargo inicial, pago, descuento, devolución y contrapartida encola un aviso. El workflow de automatización ya existente los procesa hacia **Mailpit** para el administrador ficticio. No envía dinero ni correos a Internet. Reenviar un movimiento no vuelve a encolar el mismo aviso. El aviso muestra el saldo actual al procesarse, no una fotografía del saldo a la hora original del movimiento.

## Resolver problemas

| Mensaje o síntoma | Motivo | Qué hacer |
| --- | --- | --- |
| No aparece el apartado financiero | La cuenta no tiene permiso financiero | Pide al administrador el permiso correspondiente; no se concede a visitantes. |
| Pago supera el saldo | Se ingresó un importe mayor al pendiente | Revisa el saldo actualizado y corrige el importe. |
| Devolución supera lo pagado | Ya se devolvió o anuló parte del pago | Revisa el historial antes de registrar otro movimiento. |
| La reserva debe estar confirmada | Aún está pendiente o fue cancelada | Resuelve su estado desde Reservas; no se confirma automáticamente por este libro. |
| Anulación dejaría un neto negativo | Hay descuentos o devoluciones posteriores | Corrige primero los movimientos vinculados. |
| No aparece un aviso inmediatamente | El flujo periódico aún no se ejecutó o tiene una cola pendiente | Espera la siguiente ejecución y revisa n8n y Mailpit. |

**Glosario:** *cargo neto* es el cargo inicial más adicionales menos descuentos y sus correcciones; *pagado neto* es lo registrado como pago simulado menos devoluciones y correcciones; *saldo* es cargos netos menos pagado neto; *contrapartida* es una nueva línea que invierte el efecto de un movimiento conservando su historial.

Fuentes: módulo financiero, formularios y pruebas de reglas, concurrencia, navegador y avisos n8n. La integración bancaria y la facturación electrónica pertenecen a fases posteriores.



<a id="pagos"></a>

## Pagos académicos y pasarela externa

La entrega también incluye la pasarela académica del checkout de **Drupal Commerce**. Consulta [tienda y pagos de prueba](manual-de-usuario.md#tienda-commerce). Ese flujo permite registrar varias solicitudes en un pedido y conserva las reservas pendientes hasta su confirmación administrativa. La ruta `/pago-demo` descrita a continuación se mantiene para solicitudes individuales confirmadas.

La ruta `/pago-demo` permite simular un pago aprobado, rechazado o cancelado sobre una reserva confirmada. Exige código, clave privada, sesión CSRF y consentimiento explícito. No solicita tarjetas, credenciales bancarias, Yape o Plin, no contacta a una pasarela y no cobra dinero. Los registros usan estados `demo_approved`, `demo_declined` y `demo_cancelled`; el código `DEMO-...` no es boleta ni factura.

La simulación se guarda en MariaDB, valida que la reserva siga confirmada dentro del mismo bloqueo de escritura, toma el importe del servidor y evita duplicar una aprobación mediante reenvíos. La consulta privada muestra el último resultado. Cancelar una reserva no genera un reembolso: en este entorno nunca existió un cargo.

## Mercado Pago

La integración con una pasarela externa no puede declararse funcional sin una cuenta del estudiante y credenciales de prueba válidas. No se creó una cuenta, no se adivinaron tokens y no se utilizaron credenciales de otro proyecto. La [documentación oficial para pruebas de Checkout Pro en Perú](https://www.mercadopago.com.pe/developers/en/docs/checkout-pro-preferences/integration-test/test-purchases?scope=prod) se consultó como referencia.

Antes de añadir esa integración será necesario acordar la cuenta, moneda PEN, canal HTTPS para notificaciones, validación de firmas, verificación servidor a servidor de estado/importe/moneda y pruebas de duplicados, rechazo, cancelación y reembolso. No debe marcarse una reserva como pagada a partir de parámetros de regreso del navegador. Los datos personales y las credenciales de la pasarela permanecerán fuera del JSON público y Git.

Alcance de esta entrega: tienda de estadías con carrito, checkout y pagos académicos persistentes; la pasarela externa queda pendiente de conector, credenciales y pruebas. No se realizan cobros reales.

## Verificación local

Pasaron seis pruebas de integración de pagos ficticios: pendiente rechazada, clave privada incorrecta rechazada, rechazo simulado, importe calculado en el servidor, aprobación sin duplicación y rechazo tras cancelación. En navegador se completó una aprobación de demostración usando una reserva confirmada. No se realizó ninguna llamada a una pasarela externa.

