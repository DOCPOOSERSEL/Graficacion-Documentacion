---
title: Dibujo Primitivo
nav_order: 12
---
``` python

import cv2 as cv

import numpy as np

  

img = np.ones((500,500,3),np.uint8)*150

cv.rectangle(img, (150, 250), (350, 450), (180, 100, 70), -1)   # Casa en general

  

# Puerta

cv.rectangle(img, (230, 350), (270, 450), (100, 60, 30), -1)    # puerta

  

# Ventana

cv.rectangle(img, (170, 280), (210, 320), (255, 255, 255), -1)  # Ventana blanca

cv.rectangle(img, (170, 280), (210, 320), (0, 0, 0), 2)         # Borde negro

  

# Techo

ptsT = np.array([[150, 250], 
				[350, 250], 
				[250, 150]], 
				np.int32)  # puntos del triángulo

cv.fillPoly(img, [ptsT], (150, 50, 50)) # Relleno del triangulo

  

cv.imshow('img', img)

cv.waitKey(0)

cv.destroyAllWindows()

```

Un dibujo simple de una casa  se usan varios cuadrados para crear la puertas y ventanas, se usa el menos para llenar el cuadro y luego otro para hacer una línea sobre la ventana y darle forma, el principal es lo que forma la casa

A falta de un triangulo le damos los puntos de donde estarían los puntos del triangulo y luego se usa la función fillPoly para llenar el triangulo con los puntos que le dimos a la matriz