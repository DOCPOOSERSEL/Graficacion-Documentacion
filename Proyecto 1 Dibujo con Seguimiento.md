---
title: Dibujo con seguimiento
nav_order: 7
---
# Paint con seguimiento
## Parte 1
La primera parte consta de seguimiento de color por medio de una mascara la cual detecta solo el color rojo, después de esto detecta la mancha y un pequeño espacio alrededor para evitar que el seguimiento se vuelva loco por una mancha

Después gracias a saber que tan grande es el cuadro puedo segmentar dividiendo el tamaño por 8 para poder crear los cuadros y queden mas limpios en la parte superior, ya solo es cuestión de delimitar las áreas donde se encuentran los cuadros

Ahora solo necesitamos verificar si entra en el área superior donde se encuentran los cuadros de color y checar en que lugar de el eje de las x se encuentra y dividirlo de nuevo para saber el numero de cuadro en el que se encuentra así regresándole un valor que corresponde al valor de la matriz donde tenemos los colores

Esa variable entra en la línea donde se determina la línea que se dibuja en la pantalla y en su color donde esta la matriz que le dice cual de todos tomar

``` python

import cv2

import numpy as np

  

# Captura de video

cap = cv2.VideoCapture(0)

  

# Crear un lienzo para dibujar

ret, frame = cap.read()

canvas = np.zeros_like(frame)

  
  

# Rango de color rojo en HSV

lower_red1 = np.array([0, 100, 100])

upper_red1 = np.array([10, 255, 255])

  

lower_red2 = np.array([160, 100, 100])

upper_red2 = np.array([179, 255, 255])

  

# Conocer el tamaño de mi frame para poner los cuadrados

alto, ancho, canales = frame.shape

print("Ancho:", ancho, "Alto:", alto, "Canales:", canales)

# Arreglo para los colores de los cuadros y tenerlo ordenado, ademas de poder remplazar el color de la linea por el 
# arreglo dependiendo de donde se encuentre la bola y cambiar de color

colores = [

    (0, 0, 255),    # Rojo

    (0, 255, 0),    # Verde

    (255, 0, 0),    # Azul

    (0, 255, 255),  # Amarillo

    (255, 0, 255),  # Magenta

    (255, 255, 0),  # Cian

    (128, 128, 128),# Gris

    (0, 128, 255)   # Naranja

]

color = (0,225,0)

ancho_cuadro = 640 // 8

alto_cuadro = 60

  

# Variable para guardar último punto

prev_center = None

  

# Esta funcion cambia de color usando el arreglo y checando si las coordenadas que recibe se encuentran dentro de los cuadros

# Regresando el arrglo con el numero del color

# PD: se tienen que declarar las funciones antes ya que entra en el while y ya jamas toca la funcion por ende no existe

def Cambio_de_color(Coordenadas):

    cx,cy=Coordenadas

    indice_cuadro = cx // 80  # 640/8 = 80 px por cuadro me da el entero sin decimales para ver en que No. de cuadro esta    

    if 0 <= indice_cuadro < len(colores): #Len es lenght del largo del arreglo

        return colores[indice_cuadro]

    return None

  

while True:

    ret, frame = cap.read()

    if not ret:

        break

  

    # Convertir a HSV

    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    # Crear máscara para el rojo, se combinan dos para mejorar la deteccion de este

    mask1 = cv2.inRange(hsv, lower_red1, upper_red1)

    mask2 = cv2.inRange(hsv, lower_red2, upper_red2)

    mask = cv2.bitwise_or(mask1, mask2)

  

    # Filtrar ruido

    mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, np.ones((5,5), np.uint8))

    mask = cv2.morphologyEx(mask, cv2.MORPH_DILATE, np.ones((5,5), np.uint8))

    # Buscar contornos

    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    if contours:

        # Contorno más grande

        c = max(contours, key=cv2.contourArea)

        (x, y, w, h) = cv2.boundingRect(c)

        center = (x + w//2, y + h//2)

        # X y Y del centro para determinar si se encuentra en el rango de cordenadas de los cuadros

        cx,cy = center

        # Dibujar círculo en la cámara

        cv2.circle(frame, center, 5, (0, 0, 255), -1)

        # Dibujar línea en el lienzo

        if prev_center is not None:

            # Checa si el cy para ver si se encuentra en el rango de elos cuadros

            # Si se encuentra en el rango llama a la funcion que checa en que cuadro se encuentra

            if 0<= cy <= 60:

                color = Cambio_de_color(center)

            else:

                cv2.line(canvas, prev_center, center, color , 5)

        prev_center = center

    else:

        prev_center = None

    # Combinar la cámara y el lienzo

    combined = cv2.add(frame, canvas)

    # Agregar el cuadrado/rectangulo sobre el final para que no se sobre ponga la raya al cuadro

    # La ecuacion repetirla para saber el rango donde tiene q estar la pelota para que el color cambie

    for i in range(8):

        x_inicio = i * ancho_cuadro

        x_fin = (i + 1) * ancho_cuadro

        cv2.rectangle(combined, (x_inicio, 0), (x_fin, alto_cuadro), colores[i], -1)

  

    # Mostrar resultados

    cv2.imshow("Dibujo", combined)

    cv2.imshow("Mascara", mask)

    key = cv2.waitKey(1) & 0xFF

    if key == 27:  # ESC para salir

        break

    elif key == ord('c'):  # 'c' para limpiar el lienzo

        canvas = np.zeros_like(frame)

  

cap.release()

cv2.destroyAllWindows()

```

- El código cuenta con una variable que evita que si se pierde el lugar donde esta la mancha no se inicie desde otro lugar si no desde el lugar previo de donde se vio la mancha dándole fluidez y evitando que se hagan rayas al azar
- También los cuadros se agregan al final para que se sobrepongan a la línea
- Falta agregar que pueda dibujar tanto con un cuadrado como con un circulo sustituyendo la línea que se tenia antes

## Parte 2

Esta completo de manera que ya es capaz de dibujar cuadrados y círculos además de la líneas que ya tenia antes

``` python

import cv2

import numpy as np

  

# Captura de video

cap = cv2.VideoCapture(0)

  

# Crear un lienzo para dibujar

ret, frame = cap.read()

canvas = np.zeros_like(frame)

  

# Nueva variable para determinar que tipo de figura se dibuja

tipoFigura = 0

  
  

# Rango de color rojo en HSV

lower_red1 = np.array([0, 100, 100])

upper_red1 = np.array([10, 255, 255])

  

lower_red2 = np.array([160, 100, 100])

upper_red2 = np.array([179, 255, 255])

  

# Conocer el tamaño de mi frame para poner los cuadrados

alto, ancho, canales = frame.shape

print("Ancho:", ancho, "Alto:", alto, "Canales:", canales)

# Arreglo para los colores de los cuadros y tenerlo ordenado, ademas de poder remplazar el color de la linea por el

# arreglo dependiendo de donde se encuentre la bola y cambiar de color

colores = [

    (0, 0, 255),    # Rojo

    (0, 255, 0),    # Verde

    (255, 0, 0),    # Azul

    (0, 255, 255),  # Amarillo

    (255, 0, 255),  # Magenta

    (255, 255, 0),  # Cian

    (128, 128, 128),# Gris

    (0, 128, 255)   # Naranja

]

color = (0,225,0)

ancho_cuadro = 640 // 8

alto_cuadro = 60

  

# Variable para guardar último punto

prev_center = None

  

# Esta funcion cambia de color usando el arreglo y checando si las coordenadas que recibe se encuentran dentro de los cuadros

# Regresando el arrglo con el numero del color

# PD: se tienen que declarar las funciones antes ya que entra en el while y ya jamas toca la funcion por ende no existe

def Cambio_de_color(Coordenadas):

    cx,cy=Coordenadas

    indice_cuadro = cx // 80  # 640/8 = 80 px por cuadro me da el entero sin decimales para ver en que No. de cuadro esta    

    if 0 <= indice_cuadro < len(colores): #Len es lenght del largo del arreglo

        return colores[indice_cuadro]

    return None

  

def dibujo_sobre_canvas(canvas,prev_center,center,color,tamaño,tipoFigura):

    if tipoFigura == 0:

        # Línea entre el punto anterior y el actual

        cv2.line(canvas, prev_center, center, color, tamaño)

  

    elif tipoFigura == 1:

        # Círculo en la posición actual (tamaño = radio)

        cv2.circle(canvas, center, tamaño, color, -1)

  

    elif tipoFigura == 2:

        # Cuadro entre el punto anterior y el actual

        x1, y1 = prev_center

        x2, y2 = center

        cv2.rectangle(canvas, (x1, y1), (x2, y2), color, tamaño)

  
  

while True:

    ret, frame = cap.read()

    if not ret:

        break

  

    # Convertir a HSV

    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

    # Crear máscara para el rojo, se combinan dos para mejorar la deteccion de este

    mask1 = cv2.inRange(hsv, lower_red1, upper_red1)

    mask2 = cv2.inRange(hsv, lower_red2, upper_red2)

    mask = cv2.bitwise_or(mask1, mask2)

  

    # Filtrar ruido

    mask = cv2.morphologyEx(mask, cv2.MORPH_OPEN, np.ones((5,5), np.uint8))

    mask = cv2.morphologyEx(mask, cv2.MORPH_DILATE, np.ones((5,5), np.uint8))

    # Buscar contornos

    contours, _ = cv2.findContours(mask, cv2.RETR_EXTERNAL, cv2.CHAIN_APPROX_SIMPLE)

    if contours:

        # Contorno más grande

        c = max(contours, key=cv2.contourArea)

        (x, y, w, h) = cv2.boundingRect(c)

        center = (x + w//2, y + h//2)

        # X y Y del centro para determinar si se encuentra en el rango de cordenadas de los cuadros

        cx,cy = center

        # Dibujar círculo en la cámara

        cv2.circle(frame, center, 5, (0, 0, 255), -1)

        # Dibujar línea en el lienzo

        if prev_center is not None:

            # Checa si el cy para ver si se encuentra en el rango de elos cuadros

            # Si se encuentra en el rango llama a la funcion que checa en que cuadro se encuentra

            if 0<= cy <= 60:

                color = Cambio_de_color(center)

            else:

                dibujo_sobre_canvas(canvas,prev_center,center,color,5,tipoFigura)

                # cv2.line(canvas, prev_center, center, color , 5)

        prev_center = center

    else:

        prev_center = None

    # Combinar la cámara y el lienzo

    combined = cv2.add(frame, canvas)

    # Agregar el cuadrado/rectangulo sobre el final para que no se sobre ponga la raya al cuadro

    # La ecuacion repetirla para saber el rango donde tiene q estar la pelota para que el color cambie

    for i in range(8):

        x_inicio = i * ancho_cuadro

        x_fin = (i + 1) * ancho_cuadro

        cv2.rectangle(combined, (x_inicio, 0), (x_fin, alto_cuadro), colores[i], -1)

  

    # Mostrar resultados

    cv2.imshow("Dibujo", combined)

    cv2.imshow("Mascara", mask)

    key = cv2.waitKey(1) & 0xFF

    if key == 27:  # ESC para salir

        break

    elif key == ord('c'):  # 'c' para limpiar el lienzo

        canvas = np.zeros_like(frame)

    elif key == ord('q'):

        tipoFigura = 0

    elif key == ord('a'):

        tipoFigura = 1

    elif key == ord('z'):    

        tipoFigura = 2

  
  

cap.release()

cv2.destroyAllWindows()

```

Se agrego una función llamada 'dibujo_sobre_canvas' la cual nos permite determinar en base a la variable tipo figura que vamos a dibujar a contrario del anterior que solo dibujaba líneas, además de esto, las figuras se alteran gracias a que se dibujan del punto anterior al actual

Gracias a esto mismo se pueden estirar y hacer mas grandes dependiendo de la velocidad a donde muevas el pincel

``` python

def dibujo_sobre_canvas(canvas,prev_center,center,color,tamaño,tipoFigura):

    if tipoFigura == 0:

        # Línea entre el punto anterior y el actual

        cv2.line(canvas, prev_center, center, color, tamaño)

  

    elif tipoFigura == 1:

        # Círculo en la posición actual (tamaño = radio)

        cv2.circle(canvas, center, tamaño, color, -1)

  

    elif tipoFigura == 2:

        # Cuadro entre el punto anterior y el actual

        x1, y1 = prev_center

        x2, y2 = center

        cv2.rectangle(canvas, (x1, y1), (x2, y2), color, tamaño)

```

Todo esto se controla atreves de la teclas 'Q', 'A' y 'Z' respectivamente para seleccionar con que se va a dibujar para ya no saturar el cambas, puramente como una decisión estética y por la facilidad de presionar una  tecla y cambiar el tipo de figura

``` python

key = cv2.waitKey(1) & 0xFF

    if key == 27:  # ESC para salir

        break

    elif key == ord('c'):  # 'c' para limpiar el lienzo

        canvas = np.zeros_like(frame)

    elif key == ord('q'):

        tipoFigura = 0

    elif key == ord('a'):

        tipoFigura = 1

    elif key == ord('z'):    

        tipoFigura = 2


```

- Por algún motivo se arreglo el error donde si reiniciabas el cambas con la tecla 'C' se rompía el programa pero ahora esta mas estable 