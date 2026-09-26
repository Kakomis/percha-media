# Generación de contenido — Lectura

Scripts para generar los dos manifests de Lectura que la app sincroniza desde
este repositorio. No se ejecutan en la app — son herramientas de escritorio
que corres tú antes de subir el JSON resultante.

## `lecturas_manifest.json` — libros

Convierte texto crudo de un libro (con furigana en notación `漢字(かな)`,
igual a la que ya usas en tus mazos de Anki) al JSON que la app espera.

```
python3 procesar_libro.py entrada.txt salida.json --id ID_DEL_LIBRO --titulo "TÍTULO" --autor "AUTOR" --nivel introductorio
```

Ejemplo:

```
python3 procesar_libro.py kaijin_completo.txt lecturas_manifest.json --id fuuin-demo --titulo "怪人二十面相" --autor "江戸川乱歩" --nivel introductorio
```

**Formato del texto de entrada:**
- Furigana: `漢字(かな)` — se convierte a `<ruby>漢字<rt>かな</rt></ruby>`
- Vocabulario tocable: `**palabra**` — se convierte a `<span class="vocab">palabra</span>`
- Capítulos: una línea `#### Título del capítulo` antes de cada uno

**Formato de salida:** un objeto por libro. Si `lecturas_manifest.json` va a
contener más de un libro, es un **array** `[ {...}, {...} ]` — envuelve el
resultado a mano si generas los libros por separado.

## `articulos_manifest.json` — artículos (Wikipedia)

Trae artículos de Wikipedia en japonés por categoría, filtra los que no
sirven para practicar (listas, perfiles de empresa/producto, textos
demasiado abstractos o demasiado cortos) y genera el JSON del feed.

```
python3 procesar_articulos.py articulos_manifest.json --n-por-categoria 5
```

El número es por categoría, no el total — el resultado final suele ser
menor, porque el script descarta automáticamente:
- el artículo que define el concepto raíz de la categoría (ej. "経済" dentro
  de `Category:経済`) — casi siempre demasiado abstracto
- artículos con menos de 5 párrafos o más de 20 minutos de lectura estimados
- colas de lista al final del texto (nombres de comités, enlaces externos,
  referencias) — se recortan automáticamente, no descartan el artículo entero

**Formato de salida:** ya es un array completo, no hace falta envolverlo.

**Curado manual:** el filtrado automático no detecta perfiles de empresa o
producto disfrazados de artículo de enciclopedia (ej. una reseña de una
herramienta comercial). Revisa el resultado antes de subirlo — quita los que
no sean prosa genuina sobre un concepto o tema.

**Categorías actuales** (`CATEGORIAS` en el script — editar ahí para
agregar/quitar):

| id | kanji | Categoría de Wikipedia |
|---|---|---|
| keizai | 経 | Category:経済 |
| seiji | 政 | Category:政治 |
| bunka | 文 | Category:文化 |
| seikatsu | 活 | Category:生活 |
| kenkou | 健 | Category:健康 |
| kyouiku | 教 | Category:教育 |
| kankyou | 環 | Category:環境 |
| roudou | 労 | Category:労働 |

## Requisitos

```
pip install requests
```

Solo lo necesita `procesar_articulos.py` (llama a la API pública de
Wikipedia). `procesar_libro.py` no tiene dependencias externas.
