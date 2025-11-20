---
title: Dibujo Primitivo
nav_order: 12
---
# Dibujo simple en opencv
## Casa

``` python

import cv2 as cv

import numpy as np

  

img = np.ones((500,500,3),np.uint8)*150
# Casa en general
cv.rectangle(img, (150, 250), (350, 450), (180, 100, 70), -1) 

# Puerta
cv.rectangle(img, (230, 350), (270, 450), (100, 60, 30), -1)

# Ventana
cv.rectangle(img, (170, 280), (210, 320), (255, 255, 255), -1)
# Borde negro
cv.rectangle(img, (170, 280), (210, 320), (0, 0, 0), 2)

# Techo
puntos_triangulo = np.array([[150, 250],[350, 250], [250, 150]], np.int32)
cv.fillPoly(img, [puntos_triangulo], (150, 50, 50)) # Relleno del triangulo

  

cv.imshow('img', img)

cv.waitKey(0)

cv.destroyAllWindows()

```

Un dibujo simple de una casa  se usan varios cuadrados para crear la puertas y ventanas, se usa el menos para llenar el cuadro y luego otro para hacer una línea sobre la ventana y darle forma, el principal es lo que forma la casa

### Techo de la casa
Se usa la función 'fillPoly' para llenar los puntos que se le dio en el array a cambio de usar el cuadrado que es directamente una función que requiere 4 espacios.
Esta función nos deja crear cualquier polígono y llenarlo en base a las coordenadas dadas por el array que marcarían sus vértices
``` python 
# Techo
ptsT = np.array([[150, 250],[350, 250],[250, 150]], np.int32)
cv.fillPoly(img, [ptsT], (150, 50, 50)) # Relleno del triangulo
```