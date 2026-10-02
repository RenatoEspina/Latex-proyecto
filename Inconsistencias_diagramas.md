# Inconsistencias entre los diagramas

Revisión cruzada de los 11 diagramas de casos de uso (draw.io), el diagrama de clases (PlantUML) los diagramas de secuencia del sistema (Fig. 13-16) y los de colaboración (Fig. 17-20, generados a partir de las secuencias). El texto del informe ya está alineado con los diagramas. Lo que sigue son contradicciones **entre diagramas**, que se corrigen en el dibujo.

## 1. Errores dentro del diagrama de clases (ya corregidos en `21-diagrama-clases.puml`)

| Original | Corregido |
|---|---|
| `idIndicencia` (Incidencia) | `idIncidencia` |
| `pasarACorrecion()` (Reporte) | `pasarACorreccion()` |
| `EN_CORRECION` (EstadoReporte) | `EN_CORRECCION` |
| `uuid: String #CAMBIAR a CLASE` (Fotografia) | `uuid: String` (se quitó la nota; **queda pendiente decidir** si `uuid` pasa a ser una clase) |
| `@startuml` / `@enduml` duplicados | uno solo |

## 2. Diagrama de clases: problemas de modelado

1. **Fotografia compuesta por dos todos.** `Operacion *-- Fotografia` e `Incidencia *-- Fotografia` son composiciones a la vez. En UML una parte pertenece a un solo compuesto. Sugerencia: `Incidencia o-- Fotografia` (agregación) o una asociación simple.
2. **Multiplicidades que no admiten los estados iniciales:**
   * `Programacion --> "1" Operacion`: una programación `PROGRAMADA` todavía no tiene operación → `0..1`.
   * `Programacion --> "1" Cuadrilla` y `--> "1" Trabajador`: el narrativo *Crear programación* (A3) permite dejarla sin cuadrilla → `0..1`.
   * `Reporte --> "1" Usuario` (aprobadoPor): un `BORRADOR` no tiene aprobador → `0..1`.
3. **Atributos que duplican asociaciones:** `Usuario.rol`, `Reporte.aprobadoPor`, `Cuadrilla.integrantes` y `SincronizadorOffline.colaPendientes` aparecen como atributo y como relación a la vez. Hay que dejar uno de los dos.
4. **Incidencia no se relaciona con LineaCarga.** `registrarIncidencia(..., idLinea, ...)` recibe la línea, y el narrativo dice que la incidencia queda asociada al lote y al cliente. Sin embargo, no hay relación `Incidencia → LineaCarga`, así que no se puede saber a qué cliente afecta.
5. **Usuario no se relaciona con Trabajador ni con Cliente.** El jefe tarjador inicia sesión como `Usuario`, pero la programación se asigna a un `Trabajador`, así que no hay forma de saber cuál es su *operación asignada* (Fig. 10). Lo mismo pasa con el *Cliente del depósito*: inicia sesión (Fig. 12), pero ningún `Usuario` está ligado a un `Cliente` para filtrar «sus» operaciones.
6. **Tipos sin definir:** `Imagen`, `Filtro` y `File` se usan en firmas pero no están en el diagrama.
7. **SelloSeguridad no tiene identificador**, a diferencia de las demás entidades.
8. **Estados repetidos:** `EstadoProgramacion` incluye `EN_EJECUCION` y `FINALIZADA`, que ya son de `EstadoOperacion`.

## 3. Casos de uso vs. diagrama de clases

| Diagrama de CU | Problema |
|---|---|
| Fig. 2 Gestionar usuarios | «Editar Usuario» y «Modificar Usuario» son el mismo caso. En los demás CRUD es «Modificar **Estado** X». |
| Fig. 4 Gestionar tipos de bulto | **Falta el diagrama.** En el PDF aparece como «[Diagrama pendiente]». |
| Fig. 5 Gestionar clientes | «Registrar Contacto Cliente» no tiene clase ni operación. `Cliente` solo tiene un `email` (y `obtenerDestinatarios(): String[]` devuelve varios). |
| Fig. 6 Gestionar trabajadores | «Asignar Cuadrilla» (include de Crear trabajador) tiene el mismo nombre que `GestorProgramacion.asignarCuadrilla`, que asigna una cuadrilla a una **programación**. Para trabajadores corresponde `Cuadrilla.agregarTrabajador`. Además, ningún caso de uso crea o mantiene `Cuadrilla`. |
| Fig. 7 Importar manifiesto | «**Cargar archivo** de manifiesto» sugiere que el usuario sube un archivo, pero el diseño lo **descarga** del web service (`importarManifiesto(nave, viaje, fecha)` → `ServicioAduana.descargarXML`). «Notificar resultado de importación» hacia Aduanas y «Generar reporte de errores» no tienen operación en ninguna clase. |
| Fig. 8 Gestionar programaciones | «Eliminar programación» no existe: la clase solo tiene `anular()`. «Modificar estado» tampoco tiene operación (solo `reprogramar`/`anular`). Crear programación no muestra la asignación de cuadrilla y jefe tarjador, aunque el diseño (`asignarCuadrilla`) y el narrativo sí la incluyen. |
| Fig. 9 Gestionar operaciones | «Modificar Estado Operación» no tiene operación: `Operacion` solo cambia de estado con `iniciar()`/`cerrar()`, y `EstadoOperacion` solo tiene `EN_EJECUCION`/`FINALIZADA`. |
| Fig. 10 Completar información | El caso base es «Iniciar sesión App» (descomposición funcional). Iniciar sesión no es un objetivo del actor. «Registrar datos de operación» agrupa `registrarSello`, `registrarLote` y `cerrarOperacion`, que no aparecen. No hay caso de uso para la **tarja ciega**, aunque existe `OrigenDatos.TARJA_CIEGA`. |
| Fig. 11 Aprobar reporte | El extend «Aprobar Reporte» se llama igual que el caso de alto nivel. «Rechazar Reporte» no tiene operación: el diseño rechaza **una fotografía** (`rechazarFotografia`). «Registrar Observación» no tiene atributo en `Reporte`; solo existe `Fotografia.motivoRechazo`. |
| Fig. 12 Visualizar estado | El actor dice «Cliente del deposito» (sin tilde). En los demás dice «depósito». |
| Actores administrativos | Los 5 CRUD del Administrador y *Visualizar estado* no tienen controlador (el informe lo declara como simplificación en el cap. 8). |

## 4. Diagramas de secuencia (Fig. 13-16) vs. casos de uso y clases

En el informe, las secuencias se presentan como **diagramas de secuencia del sistema** (el sistema como caja negra). La Tabla 9 del cap. 7 relaciona cada evento con las operaciones del diagrama de clases. Aun así, quedan estas diferencias, que se corrigen en los dibujos:

| Diagrama | Problema |
|---|---|
| Fig. 16 Revisar reporte vs. Fig. 9 / Fig. 11 | En la secuencia, al aprobar, el sistema **envía el PDF automáticamente** (`enviarPDFAlCliente()`). En los CU, el envío solo aparece como *Enviar PDF* del Jefe de faena (Fig. 9), y *Aprobar reporte* (Fig. 11) no lo menciona. Para conciliarlos, el informe trata el envío como automático al aprobar y *Enviar PDF* como **reenvío** manual. Si no es lo que quieren, hay que cambiar uno de los dos diagramas. |
| Fig. 16 Revisar reporte | Rechazar devuelve «notifica solicitud de recaptura a terreno» como respuesta **al Supervisor**: la flecha apunta al actor equivocado, porque la notificación va al Jefe tarjador. «Registrar observación» y «Marcar evidencia defectuosa» (Fig. 11) no aparecen; solo está `rechazarFotografia(motivo)`. |
| Fig. 16 vs. clases | `aprobarReporte()` no recibe parámetros (en la clase: `aprobarReporte(idReporte, aprobador)`). `estado = "En Corrección"` no coincide con el literal del enumerado `EN_CORRECCION`. |
| Fig. 15 Registrar desconsolidado | No incluye el **registro y verificación del sello** ni la apertura, aunque el narrativo, el CU y la clase (`registrarSello`, `SelloSeguridad`) sí los tienen. |
| Fig. 15 vs. cap. 2-3 | Todo se guarda en la base local y se sincroniza **solo al finalizar** (`sincronizarAlServidor()`). Los cap. 2 y 3 explican que el envío en lote al final provocaba caídas y que la app **sube cada foto inmediatamente**. Sugerencia: agregar en el `loop` un `opt [hay conectividad] subirFotografia()`. |
| Fig. 15 vs. clases | `iniciarFaena`/`finalizarFaena` en la secuencia y `iniciarOperacion`/`cerrarOperacion` en la clase. `registrarLoteYFotografia(cantidad)` no indica la línea de carga (en la clase: `registrarLote(idOperacion, idLinea, cantidadRecibida, imagen)`). `:Base de Datos` no es una clase del diseño. |
| Fig. 15 vs. Fig. 10 | El título dice «Registrar Desconsolidado», pero el CU se llama «Registrar Datos de Operación». |
| Fig. 14 Crear programación | El fragmento `alt [Con conflictos]` solo cubre el conflicto de horario. La no disponibilidad del personal (narrativo A4) no tiene rama. `crearProgramacion(contenedor, fecha, hora)` usa una sola hora; la clase usa `horaInicio, horaTermino`. |
| Fig. 14 vs. Fig. 8 | La secuencia incluye `asignarPersonal(cuadrilla, jefeTarjador)` dentro de crear, pero el CU *Crear programación* (Fig. 8) no tiene ese include. |
| Fig. 13 Importar manifiesto | `importarManifiesto(nave, viaje)` no lleva fecha (en la clase: `importarManifiesto(nave, viaje, fecha)`). No aparecen *Notificar resultado de importación* ni *Generar reporte de errores* (Fig. 7); la rama de error solo «muestra error». |
| Fig. 16 vs. Fig. 11 | El título dice «Revisar Reporte», pero el CU se llama «Validar Reporte». El actor se llama «Supervisor», no «Supervisor del reporte». |

`Guia_de_diagramas.md` quedó desactualizada respecto de todos los diagramas actuales.
