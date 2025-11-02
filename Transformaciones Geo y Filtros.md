---
title: Transformaciones Geométricas / Filtro Kernel
nav_order: 10
---
``` python
import cv2 as cv

import numpy as np

  

# Para imagen 1: escalar factor 2, aplicar filtro, rotar 40 , rotar 45 aplicar filtro

# Img 2: Escalar 2, rotar 45, aplicar filtro

# Img 2: Transladar imagen al centro, rotar 90 grados, escalar 2, aplicar filtro

  

#Crear condicion y ciclo para que decida cual filtro usar por pixel, recive la imagen y la regresa filtrada

def filtroBilineal (img):

    # Crear imagen copia para poder dejar que funcione la imagen si no da error como no definida

    img_filtrada = np.zeros_like(img)

    h, w = img.shape

    # Se crea la matriz del filtro de convolucion para tomar en cruz los pixeles para que se realce mejor la imagen y la de mas q ya estaba

    kernel_mas = np.array([[0, -1, 0],

                        [-1, 5, -1],

                        [0, -1, 0]])

    kernel_x = np.array([[-1,0,-1],

                        [0,5,0],

                        [-1,0,-1]])

    #Crear condicion y ciclo para que decida cual filtro usar por pixel

    for y in range(h):

        for x in range(w):

            # Toma los pixeles q estan al rededor de el pixel "Vecindad" 3*3

            vecindad = img[y:y+3, x:x+3]

  

            # Condición para seleccionar kernel checando que tan oscura es la region determinando cual usar sumandolos y dividiendo entre ellos

            if np.mean(vecindad) > 127:

                kernel = kernel_mas

            else:

                kernel = kernel_x

            # Ajustar kernel al tamaño de vecindad para evitar un error en bordes o q los tome en cuenta valla

            # Dandole el valor de el tamaño de la vecindad para que vaya de 0 al valor de esta

            ky, kx = vecindad.shape

            kernel_ajustada = kernel[0:ky, 0:kx]

  

            # Convolución manual

            valor = np.sum(vecindad * kernel_ajustada)

            valor = np.clip(valor, 0, 255)

            img_filtrada[y, x] = valor

  

    return img_filtrada            

  

# Transformaciones chiquitas para ahorrar espacio

def rotar(img, angulo):

    h, w = img.shape

    M = cv.getRotationMatrix2D((w//2, h//2), angulo, 1)

    return cv.warpAffine(img, M, (w, h))

  

def escalar(img, fx=1, fy=1):

    return cv.resize(img, None, fx=fx, fy=fy)

  

def trasladar(img, dx, dy):

    h, w = img.shape

    M = np.float32([[1, 0, dx], [0, 1, dy]])

    return cv.warpAffine(img, M, (w, h))

  
  

# Cargar la imagen en escala de grises

img = cv.imread('Todos\TsuruRojo.jpg', 0)

# Cordenadas para mover la img

h, w = img.shape

  

# Trans 1

img1 = escalar(img, 2, 2)

img1 = rotar(img1, 40)

img1 = rotar(img1, 45)

img1_filtrada = filtroBilineal(img1)

  

# Trans 2

img2 = escalar(img, 2, 2)

img2 = rotar(img2, 45)

img2_filtrada = filtroBilineal(img2)

  

# Trans 3

dx, dy = w//2, h//2  # trasladar al centro

img3 = trasladar(img, dx, dy)

img3 = rotar(img3, 90)

img3 = escalar(img3, 2, 2)

img3_filtrada = filtroBilineal(img3)

  

# Mostrar resultados

cv.imshow('Imagen 1 Filtrada', img1_filtrada)

cv.imshow('Imagen 2 Filtrada', img2_filtrada)

cv.imshow('Imagen 3 Filtrada', img3_filtrada)

cv.waitKey(0)

cv.destroyAllWindows()

  

#Ya va amarrando :)

# Ya amarroooooooooo siuuuuuu
```

Se utiliza el filtro kernel para mejorar la calidad de la imagen, se crea una vecindad para mejor decidir que filtro se va a utilizar por pixel para tener un mejor resultado

Después se usan las transformaciones para alterar la imagen para causar una perdida de pixeles para poder probar el filtro

Se