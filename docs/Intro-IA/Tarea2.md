# Codigo de laberinto

## Problema
Se dispone de un tablero de 5×5 que representa un laberinto, en el cual existe una posición inicial, una posición objetivo o meta y varias casillas que funcionan como obstáculos o trampas. Un robot debe desplazarse dentro de este tablero avanzando una sola casilla por movimiento, pudiendo moverse únicamente en las direcciones arriba, abajo, izquierda o derecha, con el propósito de alcanzar la meta.

## Laberinto
![Diagrama del sistema](recursos/laberinto.png){ width="40%"}

## Codigo
```C++
robot = [0,0]
inicio = (0,0)
fin = (4,4)
n=5

laberinto = [
    [1, 0, 0, 0, 0],
    [0, -1, 0, 0, 0],
    [0, 0, -1, 0, 0],
    [0, 0, 0, 0, 0],
    [0, 0, -1, 0, 0]
]

def mostrar():
    for i in range(n):
        print (laberinto[i])

def trampa(casilla):
    if casilla == -1:
        return True
    else:
        return False

def mover_derecha(pos):
    laberinto[pos[0]][robot[1]] = 1
    for robot[1] in range(pos[1], n):
        if trampa(laberinto[pos[0]][robot[1]]):
            print("¡Trampa! El robot no puede avanzar a la derecha.")
            robot[1] -= 1
            return
        laberinto[pos[0]][robot[1]] = 1
        mostrar()
        print()
    robot[1] = n - 1
    return
    
def mover_abajo(pos):
    for robot[0] in range(pos[0], n):
        if trampa(laberinto[robot[0]][pos[1]]):
            print("¡Trampa! El robot no puede avanzar hacia abajo.")
            robot[0] -= 1
            return
        laberinto[robot[0]][pos[1]] = 1
        mostrar()
        print()
    robot[0] = n - 1
    return

def mover_izquierda(pos):
    for robot[1] in range(pos[1], -1, -1):
        if trampa(laberinto[pos[0]][robot[1]]):
            print("¡Trampa! El robot no puede avanzar a la izquierda.")
            robot[1] += 1
            return
        laberinto[pos[0]][robot[1]] = 1
        mostrar()
        print()
    robot[1] = 0
    return

def mover_arriba(pos):
    for robot[0] in range(pos[0], -1, -1):
        if trampa(laberinto[robot[0]][pos[1]]):
            print("¡Trampa! El robot no puede avanzar hacia arriba.")
            robot[0] += 1
            return
        laberinto[robot[0]][pos[1]] = 1
        mostrar()
        print()
    robot[0] = 0
    return

print("Posición inicial del robot:", robot)
mover_derecha(inicio)
mover_abajo((robot[0], robot[1]))
mover_derecha((robot[0], robot[1]))
mover_abajo((robot[0], robot[1]))
print("Posición final del robot:", robot)
```