---
title: Capa de Invisibilidad
nav_order: 4
---
# Capa de Invisibilidad
``` python
import cv2

import numpy as np

  

# Captura de video desde la cámara

cap = cv2.VideoCapture(0)

# Permitir que la cámara se estabilice

cv2.waitKey(2000)

# Capturar el fondo durante unos segundos

ret, background = cap.read()

if not ret:

    print("Error al capturar el fondo.")

    cap.release()

    exit()

  

while cap.isOpened():

    ret, frame = cap.read()

    if not ret:

        break

    # Convertir el cuadro a espacio de color HSV

    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    # Definir el rango de color de la tela (verde, en este caso) en HSV

    # Con la adicion de otro rango de color dentro del verde mejora un poco la deteccion

    lower_green = np.array([30, 60, 60])

    upper_green = np.array([80, 255, 255])

    lower_green1 = np.array([40, 60, 60])

    upper_green1 = np.array([90, 255, 255])

    # Crear dos máscara que detecta el área verde y sumarlas

    mask1 = cv2.inRange(hsv, lower_green, upper_green)

    mask2 = cv2.inRange(hsv, lower_green1, upper_green1)

    mask = mask1+mask2

    # Refinar la máscara (puedes ajustar los parámetros para mejorar la detección)

    # Invertir la máscara para obtener las áreas que no son verdes

    mask_inv = cv2.bitwise_not(mask)

    # Aplicar la máscara a la imagen original para mostrar solo las partes no verdes

    res1 = cv2.bitwise_and(frame, frame, mask=mask_inv)

  

    # Aplicar la máscara al fondo para cubrir las partes verdes

    res2 = cv2.bitwise_and(background, background, mask=mask)

  

    # Combinar ambas imágenes

    final_output = cv2.addWeighted(res1, 1, res2, 1, 0)

  

    # Mostrar el resultado final

    cv2.imshow("Capa de Invisibilidad", final_output)

    cv2.imshow('mask', mask)

  

    # Presionar 'q' para salir

    if cv2.waitKey(1) & 0xFF == ord('q'):

        break

  

# Liberar los recursos

cap.release()

cv2.destroyAllWindows()

```

Toma el fotograma inicial de la cámara para establecerlo como el fondo o la imagen, sobre esta se pone el video que se ve en la cámara