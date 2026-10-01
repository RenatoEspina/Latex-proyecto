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

* Figuras 1-12 (casos de uso): imágenes `.jpg` dibujadas en draw.io. Falta `04-cu-gestionar-tipos-bulto` (.png o .jpg; ajustar la extensión en `Capitulos/06_casos_uso.tex`).
* Figuras 13-21 (secuencia, colaboración y clases): se generan con PlantUML desde `Figuras/diagramas/fuente/*.puml`:
  `cd Figuras/diagramas/fuente && plantuml -charset UTF-8 -Sdpi=200 -tpng -o .. 1[3-9]-*.puml 2[01]-*.puml`
  (el diagrama de clases también en SVG: `plantuml -charset UTF-8 -tsvg -o ../svg 21-diagrama-clases.puml`).

## Datos por completar
Integrantes, profesor(a) y sección en `main.tex` y `Portadas/portada_principal.tex`.

## Material anterior
`Extras_fuera_del_modelo/` guarda los capítulos, anexos y diagramas de la versión anterior; no se incluyen en el informe.
