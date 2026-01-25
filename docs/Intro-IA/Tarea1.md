# Practica de pseudocódigo

## Problema
Se dispone de un tablero de 5×5 que representa un laberinto, en el cual existe una posición inicial, una posición objetivo o meta y varias casillas que funcionan como obstáculos o trampas. Un robot debe desplazarse dentro de este tablero avanzando una sola casilla por movimiento, pudiendo moverse únicamente en las direcciones arriba, abajo, izquierda o derecha, con el propósito de alcanzar la meta.

## Laberinto
![Diagrama del sistema](recursos/laberinto.png){ width="40%"}

## pseudocódigo
```
Crear mapa 5x5
Inicia en coordenada (0,0)
Meta = (4,4)
Trampas = (1,1), (2,2), (2,4)

Si se mueve a la derecha: X = X + 1
Si se mueve a la izquierda: X = X - 1
Si se mueve abajo: Y = Y + 1
Si se mueve arriba: Y = Y - 1

Si X = 0 no ir a la izquierda
Si Y = 0 no ir arriba
Si X = 4 no ir a la derecha
Si Y = 4 no ir abajo

Si sensor derecho Y sensor izquierdo libres ENTONCES ir derecha
Si hay trampa ENTONCES ir derecha
Si sensor arriba Y sensores abajo libres ENTONCES ir abajo
Si posición = (1,0) no ir abajo

Si coordenadas = meta
    return 0
```
