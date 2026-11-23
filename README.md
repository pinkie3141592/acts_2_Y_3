# Búsqueda A\* vs búsqueda no informada (BFS)

Tarea: **Opción A — Búsqueda A\***
Implementación de A\* en JavaScript sobre una cuadrícula de 24 × 14 con muros, comparada contra una búsqueda no informada (BFS) sobre la misma instancia, con visualización paso a paso.

🎥 **Video** https://youtu.be/srgFSAWdZZg

## Contenido

```
.
├── README.md
├── astar_demo.html    # implementación + visualización interactiva
└── docs/              # capturas de los 3 escenarios
    ├── sin-muros.png
    ├── muro-en-u.png
    └── muros-aleatorios.png
```

## Cómo ejecutar

No requiere instalación ni dependencias: abre `astar_demo.html` con doble clic en cualquier navegador moderno.

- **Paso ▸**: expande un solo nodo en cada algoritmo.
- **▶ Auto**: corre hasta terminar (el control «Velocidad» ajusta el ritmo).
- **Reiniciar**: vuelve a empezar con los mismos muros.
- **Muro en U / Muros aleatorios / Limpiar muros**: cambian el laberinto.
- **Mouse**: clic o arrastre sobre cualquier cuadrícula para dibujar o borrar muros.

Debajo de cada cuadrícula se muestra el nodo que se acaba de expandir con su `g`, `h` y `f`, y los contadores de nodos expandidos y tamaño de la frontera.

**Leyenda:** azul = inicio, rojo = meta, amarillo = frontera (con su valor `f`), azul verdoso = explorado, verde = camino final, negro = muro.

## Problema

- **Espacio de estados:** celdas `(x, y)` de una cuadrícula de 24 columnas × 14 filas.
- **Acciones:** moverse arriba, abajo, izquierda o derecha (4 direcciones) a una celda sin muro.
- **Costo:** 1 por paso.
- **Inicio:** `(2, 7)`. **Meta:** `(21, 7)`.

## Algoritmo

A\* expande siempre el nodo de la frontera con menor

```
f(n) = g(n) + h(n)
```

donde `g(n)` es el costo real desde el inicio y `h(n)` es la estimación del costo restante hasta la meta.

Detalles de implementación:

- La frontera es una lista de nodos abiertos. En cada paso se elige el de menor `f`; a igualdad de `f` se prefiere el menor `h`, que acerca la búsqueda a la meta.
- Si se encuentra un camino mejor hacia un nodo ya en la frontera, se actualizan su `g` y su padre.
- La misma función `step()` implementa la búsqueda no informada poniendo `h(n) = 0`. Con costo uniforme 1, eso equivale a BFS/Dijkstra.
- Al llegar a la meta, el camino se reconstruye siguiendo los padres hasta el inicio.

## Heurística: distancia Manhattan

```
h(n) = |x − x_meta| + |y − y_meta|
```

**¿Por qué es admisible?** Con movimiento en 4 direcciones y costo 1 por paso, para ir de `n` a la meta hay que recorrer al menos `|Δx|` pasos horizontales y `|Δy|` verticales. Los muros solo pueden alargar el camino, nunca acortarlo. Por lo tanto `h(n)` nunca sobreestima el costo real, y A\* devuelve un camino óptimo.

**Consistencia:** para cualquier vecino `n'` de `n`, `h(n) ≤ 1 + h(n')`, porque un paso cambia `h` en exactamente 1. Esto implica que cada nodo se expande a lo sumo una vez con su costo `g` óptimo.

**Por qué no la distancia euclidiana:** también es admisible, pero es menos informada (euclidiana ≤ Manhattan), así que A\* expandiría más nodos. Con movimiento diagonal sí sería la elección adecuada.

## Resultados: A\* vs búsqueda no informada

Ambos algoritmos encuentran un camino de la misma longitud, que es la óptima. La diferencia está en cuántos nodos tienen que explorar: BFS se expande en círculos alrededor del inicio, mientras que A\* se dirige hacia la meta.

| Escenario | Longitud del camino | Nodos expandidos A\* | Nodos expandidos BFS | Ahorro de A\* |
|---|---|---|---|---|
| Sin muros | 19 pasos | **20** | **246** | 92 % |
| Muro en U | 29 pasos | **114** | **311** | 63 % |
| Muros aleatorios (una corrida de ejemplo) | 29 pasos | **140** | **239** | 41 % |

*Ahorro = 1 − (expandidos A\* / expandidos BFS). Los muros aleatorios se generan distintos en cada corrida, así que sus números varían; los de la tabla corresponden a la captura en `docs/muros-aleatorios.png`.*

### Análisis

- **Sin muros:** A\* expandió 20 nodos, justo los 20 de la ruta (19 pasos + el inicio), sin desperdiciar ninguno. Los nodos amarillos tienen `f = 21` (dos más que el óptimo), así que nunca se expanden. BFS explora una región enorme porque no sabe dónde está la meta.
- **Muro en U:** la distancia Manhattan es 19, pero el muro obliga a rodear y el camino real mide 29. A\* sigue siendo óptimo aunque `h` subestime. Se acerca a la meta, choca con el muro y su frontera se abre hacia arriba y abajo, porque los nodos de adelante ya no compensan. Los nodos con `f = 31` (más que la solución) nunca se expanden, por eso A\* se detiene mucho antes que BFS.
- **Muros aleatorios:** hay muchos desvíos y la ruta real (29) es bastante más larga que la distancia Manhattan (19). La heurística guía menos, así que la ventaja baja a 41 %, pero A\* nunca expande más que BFS.

**Conclusión:** la ventaja de A\* depende de qué tan bien aproxime `h` el costo real. Con una heurística admisible conserva la optimalidad y, cuanto más informada es, menos nodos explora.

## Qué se ve en el video

1. La fórmula `f = g + h` y la justificación de la heurística.
2. La frontera de A\* (con su valor `f` visible en cada celda) avanzando hacia la meta paso a paso.
3. El mismo laberinto con BFS, para ver la diferencia en nodos explorados.
4. El camino final resaltado y los contadores de nodos expandidos en los tres escenarios.
