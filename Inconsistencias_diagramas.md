# Inconsistencias entre los diagramas

Revisión cruzada de los 11 diagramas de casos de uso (draw.io), el diagrama de clases (PlantUML) y los diagramas de secuencia y colaboración (Fig. 13-20). El texto del informe ya está alineado con los diagramas. Lo que sigue son contradicciones **entre diagramas**, que se corrigen en el dibujo.

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
| Fig. 1 vs. Fig. 9 / Fig. 11 | En la Fig. 1 el envío del PDF queda en *Gestionar operaciones* (Jefe de faena), no en *Aprobar reporte*. El informe ya refleja eso, pero `Guia_de_diagramas.md` (Fig. 9 y 11) todavía dice lo contrario. |
| Actores administrativos | Los 5 CRUD del Administrador y *Visualizar estado* no tienen controlador (el informe lo declara como simplificación en el cap. 8). |

## 4. Secuencia/colaboración vs. diagrama de clases (dependencias faltantes)

Los diagramas de interacción usan solo operaciones que existen en las clases. Sin embargo, los controladores envían mensajes a clases con las que **no tienen dependencia** en el diagrama de clases:

* `GestorProgramacion` → `Cuadrilla`, `Trabajador` (`estaDisponible`).
* `GestorOperacion` → `SelloSeguridad`, `LineaCarga`, `Incidencia`, `Fotografia`, `SincronizadorOffline`, `GestorReportes` (solo tiene `..> Operacion`).
* `GestorReportes` → `Fotografia` (`marcarRechazada`) y `Cliente` (`obtenerDestinatarios`).

Agregar esas flechas `..>` al diagrama de clases (o redirigir los mensajes a través de `Operacion`).
