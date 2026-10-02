# Informe 2 - BeeTracer (Modelamiento de Software)

Estructura idéntica a los ejemplos: Portada, Índice, Lista de Figuras, Lista de Tablas, y los capítulos 1 a 11
(Introducción, Definición del problema, Descripción general, Clientes y usuarios, Funciones del sistema,
Diagramas de casos de uso, Diagramas de secuencia y colaboración, Diagrama de clases, Diccionario de clases,
Conclusiones, Referencias bibliográficas).

## Compilar
`pdflatex main.tex` → `biber main` → `pdflatex main.tex` (dos veces). Requiere biblatex con backend biber.

## Diagramas
Todos se guardan en `Figuras/diagramas/` con los nombres indicados en `Guia_de_diagramas.md`.
Mientras un archivo no exista, el PDF muestra un recuadro "[Diagrama pendiente]".

* Figuras 1-16 (casos de uso y secuencia): imágenes `.jpg` entregadas por el equipo.
* Figuras 17-21 (colaboración y clases): se generan con PlantUML desde `Figuras/diagramas/fuente/*.puml`:
  `cd Figuras/diagramas/fuente && plantuml -charset UTF-8 -Sdpi=200 -tpng -o .. 1[7-9]-*.puml 2[01]-*.puml`
  Los de colaboración replican los objetos y mensajes de las secuencias 13-16; si cambia una secuencia, hay que actualizar su colaboración.
  (el diagrama de clases también en SVG: `plantuml -charset UTF-8 -tsvg -o ../svg 21-diagrama-clases.puml`).

## Datos por completar
Integrantes en `Portadas/portada_principal.tex`; profesor, paralelo y fecha en `main.tex`.

## Material anterior
`Extras_fuera_del_modelo/` guarda los capítulos, anexos y diagramas de la versión anterior; no se incluyen en el informe.
