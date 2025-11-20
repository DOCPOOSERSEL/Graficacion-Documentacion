---
title: Operadores Puntuales
nav_order: 5
---
# Cinco operadores puntuales

``` python
import cv2 as cv

# Cargar la imagen en gris
img = cv.imread('TsuruRojo.jpg', cv.IMREAD_GRAYSCALE)  

# Operador de Identidad
identidad = img.copy()

# Negativo de la imagen
negativo = 255 - img

# Umbralizacion
xd, umbral = cv.threshold(img, 127, 255, cv.THRESH_BINARY)

# Aumentar brillo
brillo = cv.add(img, 50)  # suma 50 a cada pixel
# Reducir brillo
oscura = cv.subtract(img, 50)  # resta 50 a cada píxel

# Mostrar todas las imágenes
cv.imshow('Original', img)
cv.imshow('Identidad', identidad)
cv.imshow('Negativo', negativo)
cv.imshow('Umbral', umbral)
cv.imshow('Brillo mas', brillo)
cv.imshow('Brillo menos', oscura)

cv.waitKey(0)
cv.destroyAllWindows()
```

- La imagen se transforma a escala de grises para poder aplicarle los operadores puntuales
- Se generaron cinco operadores puntuales

## Identidad
El de la identidad da el mismo valor a otra imagen, básicamente es el copiar la que ya estaba.

## Negativo
El Negativo cambia a la imagen a su versión negativa invirtiendo los colores de esta, en este caso a escala de grises.

## Umbral
Cambia la imagen a su versión binaria lo cual hace que se pase de una escala de grises a blanco y negro por completo comparándola a un valor y determinando si el pixel se vuelve o negro o blanco.

## Brillo mas/menos
El Brillo solo altera a la imagen de manera que se vuelve mas o menos brillante dependiendo del valor que se le da.