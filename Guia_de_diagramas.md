# Guía para dibujar los 21 diagramas

Cada figura del informe ya está referenciada en el LaTeX. Mientras el archivo no exista, el PDF muestra un recuadro "[Diagrama pendiente]". Al guardar el PNG con el **nombre exacto** en `Figuras/diagramas/`, el recuadro se reemplaza solo.

**Regla que sigue toda la guía:** una conexión existe solo si aparece en un caso de uso narrativo, en el diccionario o en otra figura. Al final de cada figura indico "No dibujar" para lo que sería tentador agregar y sobra.

**Convenciones (iguales a los ejemplos):** draw.io para casos de uso, secuencia y colaboración; clases en draw.io o PlantUML. PNG de al menos 1600 px de ancho, fondo blanco, letra legible al 70 % del ancho de página. Los nombres van **exactamente** como se escriben abajo, porque el texto y el diccionario los usan.

---

## A. Casos de uso (Figuras 1 a 12)

En todos: marco del sistema con el título indicado; el actor fuera del marco; la asociación actor–caso de uso es una línea simple sin flecha. `<<extend>>` y `<<include>>` son flechas punteadas. **`<<extend>>` apunta del caso opcional hacia el caso base; `<<include>>` apunta del caso base hacia el incluido.**

### Fig. 1 · `01-cu-alto-nivel.png` · marco "BeeTracer"
Casos de uso (11, nombres idénticos al Capítulo 5): Gestionar usuarios · Gestionar roles · Gestionar tipos de bulto · Gestionar clientes · Gestionar trabajadores · Importar manifiesto · Gestionar programaciones · Gestionar operaciones · Completar información de consolidado/desconsolidado · Aprobar reporte · Visualizar estado.

| Actor | Se conecta con |
|---|---|
| Administrador del depósito | Gestionar usuarios, roles, tipos de bulto, clientes, trabajadores (5 líneas) |
| Jefe de faena | Importar manifiesto, Gestionar programaciones, Gestionar operaciones (3) |
| Servicio Nacional de Aduanas (etiquetar «externo») | Importar manifiesto (1) |
| Jefe tarjador | Completar información de consolidado/desconsolidado (1) |
| Supervisor del reporte | Aprobar reporte (1) |
| Cliente del depósito | Visualizar estado (1) |

Total: 12 líneas. **No dibujar** include/extend (es el nivel alto), ni los actores Cuadrilla ni Servidor de correo (no son actores en el informe).

### Patrón de las figuras 2, 4 y 5 (CRUD estándar)
Actor **Administrador del depósito**, con dos líneas: a *Crear X* y a *Buscar X*. Tres `<<extend>>` hacia *Buscar X*: *Editar X*, *Eliminar X*, *Modificar estado X*. Justificación: no se puede editar, eliminar ni cambiar el estado de algo sin haberlo localizado (así lo dice la narrativa "Editar usuario").

* **Fig. 2** `02-cu-gestionar-usuarios.png`, marco "Gestionar usuarios": X = usuario.
* **Fig. 4** `04-cu-gestionar-tipos-bulto.png`, marco "Gestionar tipos de bulto": X = tipo de bulto.
* **Fig. 5** `05-cu-gestionar-clientes.png`, marco "Gestionar clientes": X = cliente.

### Fig. 3 · `03-cu-gestionar-roles.png` · marco "Gestionar roles"
Patrón CRUD con X = rol, más **Crear rol `<<include>>` Asignar permisos** (un rol sin permisos no tiene sentido; corresponde a `Rol.agregarPermiso` y la relación Rol–Permiso del diagrama de clases). *Editar rol* no lleva include.

### Fig. 6 · `06-cu-gestionar-trabajadores.png` · marco "Gestionar trabajadores"
Administrador con **tres** líneas: Crear trabajador, Buscar trabajador y **Conformar cuadrilla** (existe la clase Cuadrilla y el Capítulo 5 dice "conformar las cuadrillas"). `<<extend>>` hacia Buscar trabajador: Editar trabajador, Eliminar trabajador, Modificar estado trabajador.

### Fig. 7 · `07-cu-importar-manifiesto.png` · marco "Importar manifiesto"
Dos actores: **Jefe de faena** (izquierda) y **Servicio Nacional de Aduanas** (derecha, «externo»), ambos con línea a *Importar manifiesto*. Un `<<include>>` de *Importar manifiesto* a *Validar esquema del XML* (paso 4 de la narrativa; corresponde a `Manifiesto.validarEsquema`). **No dibujar** "Registrar carga en tarja ciega" aquí; va en la Fig. 10.

### Fig. 8 · `08-cu-gestionar-programaciones.png` · marco "Gestionar programaciones"
Actor **Jefe de faena** con líneas a *Crear programación* y *Buscar programación*. `<<include>>` de Crear programación a **Asignar cuadrilla** (pasos 6–8 de la narrativa). `<<extend>>` hacia Buscar programación: **Reprogramar programación** y **Anular programación** (corresponden a `Programacion.reprogramar` y `anular`).

### Fig. 9 · `09-cu-gestionar-operaciones.png` · marco "Gestionar operaciones"
Actor **Jefe de faena** con una línea a *Buscar operación*. `<<extend>>` hacia Buscar operación: **Visualizar evidencia fotográfica** y **Descargar reporte PDF** (Capítulo 5: "visualizar su evidencia y descargar su reporte"). **No dibujar** Enviar PDF (el envío lo hace el Supervisor) ni Modificar estado (el estado lo cambia el jefe tarjador al cerrar).

### Fig. 10 · `10-cu-completar-informacion.png` · marco "Completar información de consolidado/desconsolidado"
Actor **Jefe tarjador** con **una sola línea**, a *Registrar desconsolidado*. Desde *Registrar desconsolidado*, cuatro `<<include>>`: **Seleccionar operación**, **Registrar apertura**, **Registrar separación por cliente**, **Registrar cierre**. Tres `<<extend>>`:
* **Registrar incidencia** → Registrar apertura (curso A1: sello alterado).
* **Registrar incidencia** → Registrar separación por cliente (curso A2: diferencia o daño).
* **Registrar carga en tarja ciega** → Registrar desconsolidado (curso A5).

**No dibujar** líneas del actor a los casos incluidos (los alcanza a través del principal) ni "Iniciar sesión" (no es una función del negocio). Sincronizar datos tampoco: es función oculta.

### Fig. 11 · `11-cu-aprobar-reporte.png` · marco "Aprobar reporte"
Actor **Supervisor del reporte** con líneas a *Revisar reporte* y a *Enviar reporte al cliente*. `<<extend>>` hacia Revisar reporte: **Aprobar reporte** y **Rechazar fotografía**. Un `<<include>>` de Rechazar fotografía a **Notificar recaptura** (el sistema avisa al jefe tarjador, curso A1). **No dibujar** al Cliente ni al Servidor de correo.

### Fig. 12 · `12-cu-visualizar-estado.png` · marco "Visualizar estado"
Actor **Cliente del depósito** con una línea a *Buscar operación*. `<<extend>>` hacia Buscar operación: **Consultar estado** y **Descargar reporte PDF**.

---

## B. Diagramas de secuencia (Figuras 13 a 16)

Formato: actor arriba a la izquierda, objetos como `:Clase` (o `nombre:Clase` cuando se crea), líneas de vida punteadas, barras de activación, mensajes con la firma **tal como está en el diccionario**, retornos punteados. Mensajes de creación con flecha a la cabecera del objeto. Usar fragmentos `alt`, `opt` y `loop` con su condición entre corchetes. Los ejemplos con mejor evaluación los usaron; los simples (sin fragmentos) perdieron puntos.

### Fig. 13 · `13-sec-importar-manifiesto.png`
Líneas de vida: **Jefe de faena**, **:GestorManifiesto**, **:ServicioAduana**, **:Manifiesto**, **:Contenedor**, **:LineaCarga**.

1. Jefe de faena → GestorManifiesto: `importarManifiesto(nave, viaje, fecha)`
2. GestorManifiesto → ServicioAduana: `descargarXML(nave, viaje, fecha)`; retorno `xml`
3. **alt** `[servicio responde con manifiesto]`
   1. GestorManifiesto → :Manifiesto (creación): `crear(nave, viaje)`
   2. GestorManifiesto → Manifiesto: `validarEsquema(xml)`; retorno `boolean`
   3. **alt** `[esquema válido]`
      * **loop** `[por cada contenedor declarado]`: Manifiesto → :Contenedor (creación) `agregarContenedor(numero, tamano, nroSelloDeclarado, fechaArribo)`
        * **loop** `[por cada línea de carga]`: Contenedor → :LineaCarga (creación) `agregarLinea(descripcion, cantidadDeclarada, cliente, tipo)`
      * retorno `manifiesto` al Jefe de faena (resumen)
   4. `[esquema inválido]`: retorno "error de formato" (sin crear nada)
4. `[servicio no disponible o sin manifiesto]`: retorno "manifiesto no disponible" al Jefe de faena

### Fig. 14 · `14-sec-crear-programacion.png`
Líneas de vida: **Jefe de faena**, **:GestorProgramacion**, **:Contenedor**, **:Programacion**, **:Cuadrilla**, **:Trabajador**.

1. Jefe de faena → GestorProgramacion: `listarContenedoresPendientes()`; retorno `Contenedor[]` (mensaje interno a :Contenedor omitido: es una consulta)
2. Jefe de faena → GestorProgramacion: `crearProgramacion(idContenedor, fecha, horaInicio, horaTermino)`
3. GestorProgramacion → sí mismo: `hayConflicto(fecha, horaInicio, horaTermino)`; retorno `boolean`
4. **alt** `[sin conflicto]`: GestorProgramacion → :Programacion (creación) `crear(...)`; retorno `programacion` (estado PROGRAMADA) — **[franja ocupada]**: retorno "conflicto de agenda"
5. Jefe de faena → GestorProgramacion: `asignarCuadrilla(idProgramacion, idCuadrilla, idJefeTarjador)`
6. GestorProgramacion → Cuadrilla: `estaDisponible(fecha, horaInicio, horaTermino)`; retorno `boolean`
7. GestorProgramacion → Trabajador: `estaDisponible(fecha, horaInicio, horaTermino)`; retorno `boolean`
8. **alt** `[ambos disponibles]`: GestorProgramacion → Programacion: `asignarCuadrilla(c, jefe)`; retorno "programación asignada" — **[no disponible]**: retorno "rechazo: cuadrilla o jefe no disponible"

### Fig. 15 · `15-sec-registrar-desconsolidado.png`
Líneas de vida: **Jefe tarjador**, **:GestorOperacion**, **:Operacion**, **:SelloSeguridad**, **:LineaCarga**, **:Incidencia**, **:Fotografia**, **:SincronizadorOffline**, **:GestorReportes**.

1. Jefe tarjador → GestorOperacion: `iniciarOperacion(idProgramacion)`; GestorOperacion → :Operacion (creación) y luego `iniciar()`; retorno `operacion`
2. **Apertura.** Jefe tarjador → GestorOperacion: `registrarSello(idOperacion, nroSello, imagen)`
   1. GestorOperacion → :Fotografia (creación) `crear(imagen)`; Fotografia → sí misma `comprimir()`
   2. GestorOperacion → Operacion: `agregarFotografia(f)`
   3. GestorOperacion → SincronizadorOffline: `encolar(f)`
   4. GestorOperacion → :SelloSeguridad (creación) y `verificar(declarado)`; retorno `EstadoSello`
   5. **alt** `[estado ≠ INTACTO]`: GestorOperacion → sí mismo `registrarIncidencia(...)` → :Incidencia (creación, tipo SELLO_ALTERADO) — **[INTACTO]**: sin acción
   6. retorno `EstadoSello` al actor
3. **loop** `[por cada lote separado por cliente]`: Jefe tarjador → GestorOperacion: `registrarLote(idOperacion, idLinea, cantidadRecibida, imagen)`
   1. GestorOperacion → LineaCarga: `contrastarCantidad(recibida)`; retorno `diferencia`
   2. **alt** `[diferencia ≠ 0 o daño visible]`: GestorOperacion → sí mismo `registrarIncidencia(...)`; → :Incidencia (creación); → Incidencia `adjuntarFotografia(f)` — **[sin diferencia]**: sin acción
   3. GestorOperacion → :Fotografia (creación) `crear(imagen)`, `comprimir()`; → Operacion `agregarFotografia(f)`; → SincronizadorOffline `encolar(f)`
   4. retorno `diferencia` al actor
4. **Cierre.** Jefe tarjador → GestorOperacion: `cerrarOperacion(idOperacion, imagenVacio)`
   1. Fotografía del contenedor vacío: crear, `agregarFotografia`, `encolar` (igual que 3.3)
   2. GestorOperacion → SincronizadorOffline: `hayConectividad()`; retorno `boolean`
   3. **alt** `[hay conectividad]`: SincronizadorOffline → Fotografia (por cada pendiente, **loop**) `marcarSubida(url)`; `sincronizarPendientes()` retorna cantidad — **[sin conectividad]**: las fotografías permanecen en cola
   4. GestorOperacion → Operacion: `cerrar()`
   5. GestorOperacion → GestorReportes: `generarBorrador(idOperacion)`; retorno `reporte`
   6. retorno "faena cerrada" al actor

### Fig. 16 · `16-sec-revisar-reporte.png`
Líneas de vida: **Supervisor del reporte**, **:GestorReportes**, **:Reporte**, **:Fotografia**, **:Cliente**, **:ServicioCorreo**, y el actor **Jefe tarjador** (a la derecha, solo recibe un mensaje).

1. Supervisor → GestorReportes: `abrirReporte(idReporte)`; GestorReportes → Reporte: `generarPDF()`; retorno `reporte`
2. **alt** `[evidencia correcta]`
   1. Supervisor → GestorReportes: `aprobarReporte(idReporte, aprobador)`; → Reporte: `aprobar(aprobador)`
   2. Supervisor → GestorReportes: `enviarReporte(idReporte)`
   3. GestorReportes → Cliente: `obtenerDestinatarios()`; retorno `String[]`
   4. GestorReportes → ServicioCorreo: `enviar(destinatarios, asunto, adjunto)`; retorno `boolean`
   5. GestorReportes → Reporte: `registrarEnvio(exitoso, destinatarios)`
   6. **alt** interno `[exitoso]`: retorno `true` — **[falla]**: retorno `false` ("envío fallido, reintentar")
3. **[fotografía defectuosa]**
   1. Supervisor → GestorReportes: `rechazarFotografia(idReporte, idFoto, motivo)`
   2. GestorReportes → :Fotografia: `marcarRechazada(motivo)`
   3. GestorReportes → Reporte: `pasarACorreccion()`
   4. GestorReportes → Jefe tarjador (mensaje al actor): "solicitar recaptura (idFoto, motivo)"
   5. **opt** `[llega la fotografía de reemplazo]`: GestorReportes → Reporte: `versionar()`

---

## C. Diagramas de colaboración (Figuras 17 a 20)

Mismos objetos que su secuencia, sin líneas de vida. Cada **enlace** es una línea simple entre dos objetos que se envían mensajes; sobre él van los mensajes con **numeración decimal**, con flecha en el sentido de la llamada y los retornos como texto (`retorno: …`) o flecha punteada. Solo se dibuja el curso normal; los `alt`/`loop` se indican con el texto entre corchetes en el mensaje (por ejemplo `3*[por cada lote]:`), y **no se agregan objetos ni enlaces que no estén en la secuencia**.

### Fig. 17 · `17-col-importar-manifiesto.png`
Objetos: Jefe de faena, :GestorManifiesto, :ServicioAduana, :Manifiesto, :Contenedor, :LineaCarga. Enlaces: JefeFaena–GestorManifiesto, GestorManifiesto–ServicioAduana, GestorManifiesto–Manifiesto, Manifiesto–Contenedor, Contenedor–LineaCarga.
1: `importarManifiesto(nave, viaje, fecha)` · 1.1: `descargarXML(...)` · 1.2: `crear(nave, viaje)` · 1.3: `validarEsquema(xml)` · 1.4\*\[por contenedor]: `agregarContenedor(...)` · 1.4.1\*\[por línea]: `agregarLinea(...)` · 2: retorno `manifiesto`.

### Fig. 18 · `18-col-crear-programacion.png`
Objetos: Jefe de faena, :GestorProgramacion, :Programacion, :Cuadrilla, :Trabajador (:Contenedor solo si se dibuja la consulta 1). Enlaces: JefeFaena–GestorProgramacion, GestorProgramacion–Programacion, GestorProgramacion–Cuadrilla, GestorProgramacion–Trabajador. El autoenlace de `hayConflicto` se dibuja como flecha curva sobre GestorProgramacion.
1: `crearProgramacion(...)` · 1.1: `hayConflicto(...)` · 1.2: `crear(...)` · 2: `asignarCuadrilla(idProgramacion, idCuadrilla, idJefeTarjador)` · 2.1: `Cuadrilla.estaDisponible(...)` · 2.2: `Trabajador.estaDisponible(...)` · 2.3: `Programacion.asignarCuadrilla(c, jefe)`.

### Fig. 19 · `19-col-registrar-desconsolidado.png`
Objetos: Jefe tarjador, :GestorOperacion, :Operacion, :SelloSeguridad, :LineaCarga, :Incidencia, :Fotografia, :SincronizadorOffline, :GestorReportes. Enlaces: JefeTarjador–GestorOperacion, y desde GestorOperacion hacia cada uno de los otros ocho objetos (uno por objeto, ya que el controlador es quien invoca todos los mensajes en la secuencia).
1: `iniciarOperacion(idProgramacion)` · 1.1: `Operacion.iniciar()` · 2: `registrarSello(...)` · 2.1: `Fotografia.crear(imagen)` · 2.2: `SincronizadorOffline.encolar(f)` · 2.3: `SelloSeguridad.verificar(declarado)` · 3\*: `registrarLote(...)` · 3.1: `LineaCarga.contrastarCantidad(recibida)` · 3.2 \[si hay diferencia]: `Incidencia.crear / adjuntarFotografia(f)` · 3.3: `Fotografia.crear(imagen)` · 3.4: `SincronizadorOffline.encolar(f)` · 4: `cerrarOperacion(...)` · 4.1: `SincronizadorOffline.sincronizarPendientes()` · 4.2: `Operacion.cerrar()` · 4.3: `GestorReportes.generarBorrador(idOperacion)`.

### Fig. 20 · `20-col-revisar-reporte.png`
Objetos: Supervisor del reporte, :GestorReportes, :Reporte, :Cliente, :ServicioCorreo. Enlaces: Supervisor–GestorReportes, GestorReportes–Reporte, GestorReportes–Cliente, GestorReportes–ServicioCorreo. (Curso de aprobación y envío; el de rechazo queda en la Fig. 16.)
1: `abrirReporte(idReporte)` · 1.1: `Reporte.generarPDF()` · 2: `aprobarReporte(idReporte, aprobador)` · 2.1: `Reporte.aprobar(aprobador)` · 3: `enviarReporte(idReporte)` · 3.1: `Cliente.obtenerDestinatarios()` · 3.2: `ServicioCorreo.enviar(destinatarios, asunto, adjunto)` · 3.3: `Reporte.registrarEnvio(exitoso, destinatarios)`.

---

## D. Diagrama de clases (Figura 21) · `21-diagrama-clases.png`

Formato horizontal (la página va apaisada). 23 clases con tres compartimentos (nombre, atributos, operaciones) **exactamente como en el Capítulo 9**. Los tipos enumerados no se dibujan (están en la última tabla del diccionario). Visibilidad `-` para atributos y `+` para operaciones.

Agrupar visualmente en tres zonas: **maestros y seguridad**, **carga y operación**, **controladores y servicios**.

### Asociaciones (línea simple, con nombre y multiplicidad en ambos extremos)

| # | Origen (mult.) | Destino (mult.) | Nombre | Por qué existe |
|---|---|---|---|---|
| 1 | Usuario (`*`) | Rol (`1`) | tiene | atributo `Usuario.rol` |
| 2 | Rol (`1..*`) | Permiso (`0..*`) | otorga | `Rol.agregarPermiso`, `tienePermiso` |
| 3 | Trabajador (`0..1`) | Usuario (`0..1`) | tiene cuenta | el jefe tarjador opera la app con cuenta |
| 4 | Programacion (`0..1`) | Contenedor (`1`) | planifica | Fig. 14 y narrativa Crear programación |
| 5 | Programacion (`*`) | Cuadrilla (`0..1`) | asigna | `asignarCuadrilla` |
| 6 | Programacion (`*`) | Trabajador (`0..1`) | jefe tarjador | `asignarCuadrilla(c, jefe)` |
| 7 | Operacion (`0..1`) | Programacion (`1`) | ejecuta | `iniciarOperacion(idProgramacion)` |
| 8 | Incidencia (`0..*`) | LineaCarga (`0..1`) | afecta a | incidencia por diferencia o daño de un lote |
| 9 | Incidencia (`1`) | Fotografia (`1..*`) | respaldada por | `adjuntarFotografia` |
| 10 | LineaCarga (`*`) | Cliente (`1`) | consignada a | el cliente ve solo su carga |
| 11 | LineaCarga (`*`) | TipoBulto (`1`) | clasificada como | `agregarLinea(..., tipo)` |
| 12 | Reporte (`0..1`) | Operacion (`1`) | resume | `generarBorrador(idOperacion)` |
| 13 | Reporte (`*`) | Usuario (`0..1`) | aprobado por | `Reporte.aprobadoPor` |
| 14 | SincronizadorOffline (`1`) | Fotografia (`0..*`) | encola | `colaPendientes` |

### Agregaciones y composiciones (rombo en el lado del "todo")

| Tipo | Todo | Parte | Mult. parte |
|---|---|---|---|
| Agregación (rombo vacío) | Cuadrilla | Trabajador | `1..*` |
| Composición (rombo lleno) | Manifiesto | Contenedor | `1..*` |
| Composición | Contenedor | LineaCarga | `1..*` |
| Composición | Operacion | Fotografia | `0..*` |
| Composición | Operacion | SelloSeguridad | `0..1` |
| Composición | Operacion | Incidencia | `0..*` |

### Dependencias (flecha punteada del controlador a la clase que usa)

| Controlador | Depende de | Fig. que lo justifica |
|---|---|---|
| GestorManifiesto | ServicioAduana, Manifiesto | 13 |
| GestorProgramacion | Programacion, Contenedor, Cuadrilla, Trabajador | 14 |
| GestorOperacion | Programacion, Operacion, SelloSeguridad, LineaCarga, Incidencia, Fotografia, SincronizadorOffline, GestorReportes | 15 |
| GestorReportes | Reporte, Fotografia, Cliente, ServicioCorreo | 16 |

**No dibujar:** herencia (no hay), Usuario ligado a los controladores (la autorización se resuelve al iniciar sesión mediante Rol y Permiso; por eso Usuario no lleva métodos `intentar…`), relaciones entre los controladores salvo la de `GestorOperacion → GestorReportes`, ni flechas de Cliente a Operacion (se llega por LineaCarga).

---

## E. Cuando me los envíes

Revisaré para cada figura: (1) que el nombre y el título coincidan con el texto, (2) que cada actor, caso de uso, objeto, mensaje y relación aparezca en esta guía o en el capítulo correspondiente, (3) que no sobre ninguna conexión, (4) que el tipo de flecha y el sentido (extend/include, dependencia, agregación/composición) sean los correctos, y (5) que las firmas de los mensajes y las operaciones de las clases sean idénticas a las del diccionario. Si algo no cuadra te diré si conviene cambiar el dibujo o el texto.
