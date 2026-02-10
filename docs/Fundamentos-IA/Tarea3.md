# Ge

```c++
import random

n = random.randint(1, 5) 
matriz1 = []
matriz2 = []


for i in range(n):
    fila1 = []
    fila2 = []
    for j in range(n):
        fila1.append(random.randint(0, 5)) 
        fila2.append(random.randint(0, 5))
    matriz1.append(fila1)
    matriz2.append(fila2)


print("Matriz 1:")
for f in matriz1:
    print(f)

print("\nMatriz 2:")
for f in matriz2:
    print(f) 
print("-" * 20)


matriz_mult = []
for i in range(len(matriz1)):
    fila_nueva = []
    for j in range(len(matriz2[0])):
        suma = 0
        for k in range(len(matriz1[0])):
            
            suma += matriz1[i][k] * matriz2[k][j]
        fila_nueva.append(suma)
    matriz_mult.append(fila_nueva)

print("Resultado final:")
for f in matriz_mult:
    print(f)
```
