# Informe 2 — Modelamiento UML (versión LaTeX)

Versión LaTeX de `informe/INFORME-2-UML.md`, con el formato de la Escuela de
Ingeniería Informática PUCV (`pucv_inf_2024.sty`) y el encabezado institucional
de la portada.

## Compilar

Requiere una distribución LaTeX con `biber`:

```bash
cd informe/latex
pdflatex main.tex
biber main
pdflatex main.tex
pdflatex main.tex
```

O, en un solo paso:

```bash
latexmk -pdf main.tex
```

El resultado es `main.pdf`.

## Estructura

| Archivo | Contenido |
|---|---|
| `main.tex` | Documento maestro y **datos de portada** (título, asignatura, profesor, sección, fecha). |
| `pucv_inf_2024.sty` | Formato PUCV. Copia sin modificar del paquete original. |
| `estilo_informe.tex` | Comandos y estilos propios del informe (cajas de nota y de pendientes, estilo de tablas, rótulos en español). No toca el `.sty`. |
| `referencias.bib` | Bibliografía en formato BibLaTeX / APA. |
| `Portadas/portada_principal.tex` | Portada con el encabezado PUCV. |
| `Capitulos/` | Capítulos 1 a 10 y las notas pendientes de la bibliografía. |
| `Anexos/` | Anexo A (trazabilidad) y Anexo B (regeneración de diagramas). |
| `Figuras/encabezado_pucv.png` | Encabezado institucional de la portada. |
| `Figuras/diagramas/` | Copia de los PNG de `informe/diagramas/`. |

## Qué falta completar

Los bloques naranjos rotulados **POR COMPLETAR** dentro del PDF marcan lo que
el equipo debe terminar; provienen de las mismas marcas del documento Markdown
original. Los datos de portada pendientes (integrantes, profesor, sección) se
editan en la sección «DATOS DE PORTADA» de `main.tex` y en
`Portadas/portada_principal.tex`.

## Separación silábica en español

`main.tex` carga `babel` con español **solo si está instalado** en la
distribución. Si no lo está, el documento compila igual, pero la partición de
palabras usa las reglas del inglés. Para habilitarla:

```bash
# Arch / CachyOS
sudo pacman -S texlive-langspanish
```

## Si se regeneran los diagramas

Tras regenerar los PNG con PlantUML (ver Anexo B), hay que actualizar la copia
usada por el documento:

```bash
cp informe/diagramas/*.png informe/latex/Figuras/diagramas/
```
