# Inconsistencias entre los diagramas

Revisión cruzada de los casos de uso (Fig. 1-12, draw.io), las secuencias (Fig. 13-16, Mermaid), las colaboraciones (Fig. 17-20, PlantUML) y el diagrama de clases (Fig. 21, PlantUML). Las fuentes están en `Figuras/diagramas/fuente/`.

## 1. Ya corregido

### Diagrama de clases (`21-diagrama-clases.puml`)

| Problema | Corrección |
|---|---|
| `Fotografia` compuesta por `Operacion` y por `Incidencia` | `Incidencia o-- Fotografia` (agregación); el todo es `Operacion` |
| Multiplicidades que no admiten el estado inicial | `Programacion "1" -- "0..1" Operacion`, `--> "0..1" Cuadrilla`, `--> "0..1" Trabajador`, `Reporte --> "0..1" Usuario : aprobadoPor` |
| Atributos que duplicaban asociaciones (`rol`, `integrantes`, `aprobadoPor`, `colaPendientes`) | Se quitaron los atributos y se mantuvieron las asociaciones |
| `Incidencia` sin relación con la línea afectada | `Incidencia --> "0..1" LineaCarga` (la de sello alterado no tiene línea) |
| `Usuario` sin relación con `Trabajador` ni `Cliente` | `Usuario --> "0..1" Trabajador` y `Usuario --> "0..1" Cliente` |
| `SelloSeguridad` sin identificador | `idSello: int` |
| Estados repetidos en dos enumerados | Un solo enumerado `Estado`, compartido por `Programacion` y `Operacion` |
| Tipos `Imagen`, `Filtro` y `File` sin definir | Dependencias `GestorOperacion ..> Imagen`, `..> Filtro`, `Reporte ..> File`, `GestorManifiesto ..> File` |
| Controladores que envían mensajes a clases con las que no tenían relación | Dependencias de `GestorManifiesto`, `GestorProgramacion`, `GestorOperacion` y `GestorReportes` hacia todas las clases que usan en las secuencias |
| CU Fig. 7 sin operaciones | `ServicioAduana.notificarResultadoImportacion(nave, viaje, exitoso)` y `GestorManifiesto.generarReporteErrores(xml): File` |
| CU Fig. 8 sin operaciones | `GestorProgramacion.eliminarProgramacion(idProgramacion)` y `Programacion.modificarEstado(e)` |
| CU Fig. 9 «Modificar estado operación» sin operación | `Operacion.modificarEstado(e)` |
| CU Fig. 11 «Solicitar corrección de evidencia» sin operación | `GestorReportes.solicitarCorreccionEvidencia(idFoto, motivo)` (antes `asignarEvidencia`) |
| La operación no podía registrar sus incidencias | `Operacion.agregarIncidencia(i)` |
| ¿De dónde salen los destinatarios del reporte? | `Reporte.obtenerClientes(): Cliente[]`, navegable porque `Programacion -- Operacion` ahora es bidireccional |

### Secuencias (Fig. 13-16) y colaboraciones (Fig. 17-20)

| Diagrama | Corrección |
|---|---|
| Fig. 13 / 17 Importar manifiesto | La respuesta de `descargarXML` quedó dentro del `alt`. Se agregaron `notificarResultadoImportacion` (rama válida) y `generarReporteErrores` (rama inválida, con nota de que no se registra información parcial). |
| Fig. 14 / 18 Crear programación | Se agregó `listarContenedoresPendientes()` (pasos 1-2 de la narrativa). El rechazo indica que la programación queda `PROGRAMADA` sin asignar (A3/A4). |
| Fig. 15 / 19 Registrar datos | `iniciarOperacion` hace `<<create>>` de la `Operacion` (antes no existía). Cada incidencia se agrega a la operación (`agregarIncidencia`) y sus fotos también (`agregarFotografia`). La de sello alterado se crea sin línea (`null`). Antes de cada `opt [hay conectividad]` se llama a `hayConectividad()`. Al cerrar, primero `cerrar()` y después `generarBorrador(idOperacion)`, que retorna el `Reporte`. |
| Fig. 16 / 20 Validar reporte | `enviarReporte` obtiene los clientes del reporte, hace un `loop` por cliente para obtener sus destinatarios y regenera el PDF ya aprobado. Hay un `alt` por envío exitoso o fallido (A4). La solicitud de corrección es `solicitarCorreccionEvidencia(idFoto, motivo)`. |
| Fig. 17-20 | Se regeneraron a partir de las secuencias nuevas. Ahora muestran todos los objetos que participan, no solo el controlador. |

## 2. Pendiente (solo en los dibujos de casos de uso, draw.io)

| Diagrama | Problema | Cómo corregirlo |
|---|---|---|
| Fig. 8 Gestionar programaciones | La secuencia (Fig. 14) y la narrativa (pasos 6-8) asignan la cuadrilla y el jefe tarjador, pero el CU no lo muestra. | Agregar el caso **Asignar cuadrilla** y un `<<include>>` desde *Crear programación*. |
| Fig. 7 Importar manifiesto | «Cargar archivo de manifiesto» sugiere que el usuario sube un archivo, pero el sistema lo descarga del web service. | Renombrar a «Solicitar manifiesto» (opcional). |
| Fig. 10 Completar información | El caso base es «Iniciar sesión App» (descomposición funcional) y no hay caso para la tarja ciega (`OrigenDatos.TARJA_CIEGA`). | Conectar al actor con *Registrar datos de operación* y agregar *Registrar carga en tarja ciega* como `<<extend>>` (opcional). |
| Fig. 11 Aprobar reporte | «Rechazar reporte» se realiza con `rechazarFotografia` (marcar la foto, guardar el motivo y pasar el reporte a corrección). | Se acepta tal como está; el texto del cap. 7 explica la correspondencia. |
| Fig. 2, 4, 5, 6 (CRUD) | «Editar/Modificar usuario» duplicados; «Asignar características de bulto» y «Registrar contacto cliente» sin atributo; los CRUD no tienen controlador (simplificación declarada en el cap. 8). | Opcional. |
