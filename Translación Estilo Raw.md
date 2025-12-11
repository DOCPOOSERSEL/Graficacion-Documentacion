---
title: Translación y Hitboxes
nav_order: 6
---
```python

import cv2 as cv

import numpy as np

  

# Se necesita guardar las posiciones de la ventana y velocidad para cambiar la direccion

# Tamaño de la ventana

W, H = 500, 500  

# Posición inicial de ambas pelotas

x, y = 50, 50  

x2, y2 = 250, 250

# Velocidad es sobre donde vamos a efectuar el cambio de direccion

# Tambien depende de si cambiamos la velocidad el angulo del rebote para evitar que repita el mismo rebote si es muy similar

# Ej x=5 y y=5 repite el mismo patron que es una linea forma \

dx, dy = 4, 5

# DAmos la velocidad para la otra pelota

dx2, dy2 = 0, 0  

  

while True:

    img = np.ones((H, W, 3), dtype=np.uint8) * 255

    cv.circle(img, (x, y), 20, (0, 234, 21), -1)

    x += dx

    y += dy

  

    # La segunda pelota deve de ser estatica por lo tanto no se mueve su velocidad

    # A menos que entre en contacto con la otra pelota

    cv.circle(img, (x2, y2), 20, (0, 234, 21), -1)

    x2 += dx2

    y2 += dy2

  

    # Rebote en los bordes, esto checa que si en 20 sale de los bordes se le resta la velocidad da 0

    # Pero sigue restando hasta que sale hacia otro lado

    if x - 20 <= 0 or x + 20 >= W:

        dx = -dx

    if y - 20 <= 0 or y + 20 >= H:

        dy = -dy

    # Los mismos rebotes pero para la segunda bola

    if x2 - 20 <= 0 or x2 + 20 >= W:

        dx2 = -dx2

    if y2 - 20 <= 0 or y2 + 20 >= H:

        dy2 = -dy2

  

    # Se encarga de revisar la distancia entre las pelotas

    dist = np.sqrt((x2 - x)**2 + (y2 - y)**2) # Esta ecuacion se encarga de medir la distancia que hay entre las dos bolas

    # Hacer que la segunda pelota se mueva en base que si la distancia de las pelotas es menor a sus radios por dos

    # La segunda bola toma la velocidad y direccion de la otra

    if dist <= 2 * 20:

        # Hacer que la segunda pelota se mueva

        dx2, dy2 = dx, dy

        # Opción: invertir la dirección de la primera también

        dx, dy = -dx, -dy

    cv.imshow("Pelota rebotando", img)

  

    k = cv.waitKey(20) & 0xFF

    if k == ord('q'):

        break

  

cv.destroyAllWindows()

```