# Referencias de implementación y diseño


<a id="referencias-implementacion"></a>

## Referencias de implementación y límites de consulta

Consulta para completar el proyecto académico: 10/11/2026. El código propio usa las APIs Drupal y la base MariaDB como fuente de verdad. Se consultan las fuentes como guía, sin copiar plantillas de negocio o atribuir funcionalidades no probadas.

- [Drupal Form API](https://www.drupal.org/docs/drupal-apis/form-api/introduction-to-form-api): formularios, validación, sesiones y permisos.
- [Drupal Database API](https://www.drupal.org/docs/develop/drupal-apis/database-api): transacciones, consultas parametrizadas y persistencia.
- [Código oficial del widget @n8n/chat en GitHub](https://github.com/n8n-io/n8n/tree/master/packages/frontend/%40n8n/chat): integración visual y contrato del webhook. Se verificó además el paquete npm 1.46.2 y su integridad SHA-512; se conserva procedencia junto al recurso local.
- [n8n Webhook](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.webhook/), [Respond to Webhook](https://docs.n8n.io/integrations/builtin/core-nodes/n8n-nodes-base.respondtowebhook/), [CLI](https://docs.n8n.io/hosting/cli-commands/): flujos JSON importables, publicación y ejecución de webhooks.
- [Tutorial localizado en YouTube: chatbot con n8n y webhook](https://www.youtube.com/watch?v=gvXZjmgIfvI) y [chatbot que consulta un sitio](https://www.youtube.com/watch?v=mLTqabG0l7c): se localizaron títulos y descripciones mediante búsqueda. La herramienta no pudo reproducir los videos; no se afirma haber seguido o comprobado su contenido audiovisual.
- [Mercado Pago Perú: pruebas de Checkout Pro](https://www.mercadopago.com.pe/developers/en/docs/checkout-pro-preferences/integration-test/test-purchases?scope=prod): referencia para una pasarela opcional. Sin cuenta ni credenciales de prueba del usuario no se puede certificar una transacción externa.

Las referencias de diseño previas están en `diseno-fase-2.md`. Las fotografías son ilustrativas y no representan habitaciones reales del establecimiento.



<a id="referencias-video-movimiento"></a>

## Referencias para video y movimiento

Consulta: 11/11/2026. Se revisaron páginas oficiales y documentación técnica. La extracción de páginas no permite certificar todas sus animaciones ni su comportamiento en cada dispositivo.

| Referencia | Evidencia revisada | Aplicación propuesta |
| --- | --- | --- |
| [Belmond Miraflores Park](https://www.belmond.com/es/hotels/south-america/peru/miraflores-park-lima) | Página oficial con alojamiento, galería y reservas; HTML incluye etiquetas de reproducir y pausar video. | Medio visual protagonista con control de reproducción, sin ocultar las acciones de reserva. No se afirma haber auditado todo su reproductor. |
| [EDITION](https://www.editionhotels.com/) | Página oficial con identidad de marca, imágenes y acción Reserve. El acceso HTTP directo fue rechazado; la página se consultó mediante extracción web. | Jerarquía simple entre marca, contenido visual y reserva. No se atribuyen efectos de scroll específicos que no se hayan comprobado. |
| [Aman](https://www.aman.com/) | Página oficial con destinos y acceso Reserve. | Mantener navegación y reserva identificables; no usar la cantidad de animación como medida de calidad. |
| [web.dev: Video performance](https://web.dev/learn/performance/video-performance) | Compresión, formatos, carga y reproducción automática. | Clip comprimido, sin audio; foto inicial y carga diferida. |
| [W3C: Pause, Stop, Hide](https://www.w3.org/WAI/WCAG22/Understanding/pause-stop-hide) | Movimiento automático de más de cinco segundos junto a contenido requiere mecanismo de pausa, salvo excepciones esenciales. | Botón persistente de pausa; no basta detener el movimiento solo mientras se mantiene el foco. |
| [W3C: Carousel animations](https://www.w3.org/WAI/tutorials/carousels/animations/) | Control explícito de avance automático. | Carrusel controlable y alternativa con movimiento reducido. |

## Criterio aplicado al proyecto

Un solo medio en movimiento en portada: video o carrusel, evitando reproducción simultánea. El texto de marca y la reserva permanecen estables. Clip local de diez segundos, sin audio, 350370 bytes; no se descarga automáticamente en móvil, ahorro de datos o movimiento reducido. Entradas discretas de secciones; el contenido permanece accesible si JavaScript falla.

Las duraciones elegidas (550 ms de entrada, 650 ms de fundido, diez segundos de clip) son decisiones de diseño del proyecto, no valores prescritos por las referencias. La siguiente mejora editorial debe ser material propio o autorizado del hotel y una foto inicial correspondiente al mismo video para evitar un cambio de ambiente al comenzar. El clip de muestra actual y las fotos pertenecen a interiores diferentes.

## Aplicación de las referencias

La imagen inicial de portada ahora se extrae del mismo clip (`assets/video/hotel-poster.jpg`) para evitar un salto entre interiores al comenzar la reproducción. Se añadió una presentación editorial con fotografía local, texto breve y acceso al catálogo; la guía de reserva usa tres bloques numerados. No se copió material de los hoteles de referencia. Conserva la reserva estable, el control del video y la alternativa sin movimiento. Se verificaron cinco anchos de pantalla, cambio a inglés y ausencia de errores JavaScript.

