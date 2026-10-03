# Inconsistencias entre los diagramas

Revisión cruzada de los 12 diagramas de casos de uso (draw.io), el diagrama de clases (PlantUML), los diagramas de secuencia (Fig. 13-16) y los de colaboración (Fig. 17-20, generados a partir de las secuencias). El texto del informe ya está alineado con los diagramas. Lo que sigue son contradicciones **entre diagramas**, que se corrigen en el dibujo.

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
| Fig. 4 Gestionar tipos de bultos | «Asignar Características de Bulto» (include de Crear tipo bulto) no tiene atributo ni operación en `TipoBulto`, que solo tiene `nombre`, `codigo` y `activo`. Además, el título dice «Tipos de Bultos» y el de alto nivel (Fig. 1), «Tipos de Bulto». |
| Fig. 5 Gestionar clientes | «Registrar Contacto Cliente» no tiene clase ni operación. `Cliente` solo tiene un `email` (y `obtenerDestinatarios(): String[]` devuelve varios). |
| Fig. 6 Gestionar trabajadores | «Asignar Cuadrilla» (include de Crear trabajador) tiene el mismo nombre que `GestorProgramacion.asignarCuadrilla`, que asigna una cuadrilla a una **programación**. Para trabajadores corresponde `Cuadrilla.agregarTrabajador`. Además, ningún caso de uso crea o mantiene `Cuadrilla`. |
| Fig. 7 Importar manifiesto | «**Cargar archivo** de manifiesto» sugiere que el usuario sube un archivo, pero el diseño lo **descarga** del web service (`importarManifiesto(nave, viaje, fecha)` → `ServicioAduana.descargarXML`). «Notificar resultado de importación» hacia Aduanas y «Generar reporte de errores» no tienen operación en ninguna clase. |
| Fig. 8 Gestionar programaciones | «Eliminar programación» no existe: la clase solo tiene `anular()`. «Modificar estado» tampoco tiene operación (solo `reprogramar`/`anular`). Crear programación no muestra la asignación de cuadrilla y jefe tarjador, aunque el diseño (`asignarCuadrilla`) y el narrativo sí la incluyen. |
| Fig. 9 Gestionar operaciones | «Modificar Estado Operación» no tiene operación: `Operacion` solo cambia de estado con `iniciar()`/`cerrar()`, y `EstadoOperacion` solo tiene `EN_EJECUCION`/`FINALIZADA`. |
| Fig. 10 Completar información | El caso base es «Iniciar sesión App» (descomposición funcional). Iniciar sesión no es un objetivo del actor. «Registrar datos de operación» agrupa `registrarSello`, `registrarLote` y `cerrarOperacion`, que no aparecen. No hay caso de uso para la **tarja ciega**, aunque existe `OrigenDatos.TARJA_CIEGA`. |
| Fig. 11 Aprobar reporte | El extend «Aprobar Reporte» se llama igual que el caso de alto nivel. «Rechazar Reporte» no tiene operación: el diseño rechaza **una fotografía** (`rechazarFotografia`). «Registrar Observación» no tiene atributo en `Reporte`; solo existe `Fotografia.motivoRechazo`. |
| Fig. 12 Visualizar estado | El actor dice «Cliente del deposito» (sin tilde). En los demás dice «depósito». |
| Actores administrativos | Los 5 CRUD del Administrador y *Visualizar estado* no tienen controlador (el informe lo declara como simplificación en el cap. 8). |

## 4. Diagramas de secuencia (Fig. 13-16) vs. clases y casos de uso

Las secuencias ya usan los controladores y entidades del diagrama de clases. Quedan estas diferencias, que se corrigen en los dibujos:

| Diagrama | Problema |
|---|---|
| Fig. 13 Importar manifiesto | `agregarContenedor()` y `agregarLinea()` aparecen como mensajes de `:GestorManifiesto` a sí mismo, pero son operaciones de `Manifiesto` y de `Contenedor`. Faltan las líneas de vida `:Contenedor` y `:LineaCarga` y el `loop` por contenedor y por línea. |
| Fig. 13 Importar manifiesto | La guarda dice «esquema válido», pero nunca se llama a `Manifiesto.validarEsquema(xml)`. |
| Fig. 13 vs. Fig. 7 | No aparecen *Notificar resultado de importación* (a Aduanas) ni *Generar reporte de errores*: la rama de error solo devuelve «error de formato / manifiesto no disponible». |
| Fig. 14 vs. clases | `GestorProgramacion` envía mensajes a `Cuadrilla` y `Trabajador`, pero el diagrama de clases no tiene esas dependencias (solo `..> Programacion` y `..> Contenedor`). |
| Fig. 14 vs. Fig. 8 | La secuencia asigna cuadrilla y jefe tarjador al crear, pero el CU *Crear programación* (Fig. 8) no tiene el include correspondiente. |
| Fig. 15 Registrar datos de operación | `verificar(declarado)` aparece como mensaje de `:GestorOperacion` a sí mismo, pero es una operación de `SelloSeguridad`. Tampoco aparecen `:Operacion` (`iniciar`, `cerrar`, `agregarFotografia`), `:LineaCarga` (`contrastarCantidad`), `:Incidencia` ni `:Fotografia` (`comprimir`). |
| Fig. 15 Registrar datos de operación | `generarBorrador()` aparece como mensaje a sí mismo de `:GestorOperacion`, pero es `GestorReportes.generarBorrador(idOperacion)`: falta `:GestorReportes` y el parámetro. |
| Fig. 15 Registrar datos de operación | `registrarSello` y `registrarLote` no muestran sus retornos (`EstadoSello`, diferencia `int`), pero el `opt [diferencia != 0]` depende de ese valor. Tampoco está el caso del sello alterado (narrativo A1). |
| Fig. 15 vs. clases | Hay diferencias de nombres: `registrarIncidencia(..., cantidad, ...)` en la secuencia y `cantidadAfectada` en la clase; `encolar(fotografias)` (plural) en la secuencia y `encolar(f: Fotografia)` en la clase. Además, el diagrama de clases no tiene la dependencia `GestorOperacion ..> SincronizadorOffline`. |
| Fig. 16 Validar reporte | `obtenerDatos()` no existe en `Reporte`; la operación de la clase es `generarPDF()`. |
| Fig. 16 Validar reporte | El envío ocurre dentro de `aprobarReporte`, pero la clase tiene una operación separada `enviarReporte(idReporte)`. No se llama a `Cliente.obtenerDestinatarios()` (¿de dónde salen los `destinatarios`?) ni a `Reporte.registrarEnvio(...)`, así que no queda registrado el `estadoEnvio`. Además, `GestorReportes` no tiene dependencia hacia `Cliente`. |
| Fig. 16 Validar reporte | Al rechazar no se llama a `Fotografia.marcarRechazada(motivo)`, así que el motivo (la «observación» de la Fig. 11) no queda guardado en ningún objeto. |
| Fig. 16 vs. Fig. 9 / Fig. 11 | Al aprobar, el sistema **envía el PDF automáticamente**. En los CU, el envío solo aparece como *Enviar PDF* del Jefe de faena (Fig. 9), y *Aprobar reporte* (Fig. 11) no lo muestra. El informe trata el envío como automático al aprobar y *Enviar PDF* como **reenvío** manual. |
| Fig. 16 vs. Fig. 11 | «Marcar evidencia defectuosa» y «Registrar observación» no aparecen como mensajes; se entienden incluidos en `rechazarFotografia(idReporte, idFoto, motivo)`. |

`Guia_de_diagramas.md` quedó desactualizada respecto de todos los diagramas actuales.
