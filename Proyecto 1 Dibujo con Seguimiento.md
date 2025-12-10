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
print("ancho:", ancho, "altp:", alto, "canal", canales)
# Arreglo para los colores de los cuadros y tenerlo ordenado, ademas de poder remplazar el color de la linea por el 
# arreglo dependiendo de donde se encuentre la bola y cambiar de color
colores = [
	(0, 0, 255),# Rojo
	(0, 255, 0),# Verde
	(255, 0, 0),# Azul
	(0, 255, 255),# Amarillo
	(255, 0, 255),# Magenta
	(255, 255, 0),# Cian
	(128, 128, 128),# `Gris`
	(0, 128, 255)# Naranja
]

# El del inicio xd
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

## Parte 3 Final

### Detección de manos

``` python

import cv2

import mediapipe as mp

import numpy as np

  

# Inicializar MediaPipe Hands

mp_hands = mp.solutions.hands

mp_drawing = mp.solutions.drawing_utils

hands = mp_hands.Hands(min_detection_confidence=0.7, min_tracking_confidence=0.7)

  

# Función para determinar la letra según la posición de los dedos

def reconocer_letra(hand_landmarks, frame):

    h, w, _ = frame.shape  # Tamaño de la imagen

    # Obtener coordenadas de los puntos clave en píxeles

    dedos = [(int(hand_landmarks.landmark[i].x * w), int(hand_landmarks.landmark[i].y * h)) for i in range(21)]

    # Obtener posiciones clave (puntas de los dedos)

    pulgar, indice, medio, anular, meñique = dedos[4], dedos[8], dedos[12], dedos[16], dedos[20]

  

    # Mostrar los números de los landmarks en la imagen

    for i, (x, y) in enumerate(dedos):

        cv2.circle(frame, (x, y), 5, (0, 234, 0), -1)  # Puntos verdes

        cv2.putText(frame, str(i), (x, y - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 255, 255), 1)

  

    # Dibujar coordenadas del pulgar

    cv2.putText(frame, f'({int(pulgar[0])}, {int(pulgar[1])})', (pulgar[0], pulgar[1] - 15),

                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (245, 0, 0), 2, cv2.LINE_AA)

  

    cv2.line(frame, (int(pulgar[0]), int(pulgar[1])), (int(indice[0]), int(indice[1])), (244,34,12), 2)

    # Calcular distancias en píxeles

    distancia_pulgar_indice = np.linalg.norm(np.array(pulgar) - np.array(indice))

    distancia_indice_medio = np.linalg.norm(np.array(indice) - np.array(medio))

  

    cv2.putText(frame, f'({distancia_pulgar_indice})', (pulgar[0]-40, pulgar[1] - 45),

                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255, 255, 255), 2, cv2.LINE_AA)

  
  

    #cv2.circle(frame, (pulgar[0]+20,pulgar[1]+20), int(distancia_indice_medio), (34,234,65), -1 )

    # Lógica para reconocer algunas letras

    if distancia_pulgar_indice < 30 and distancia_indice_medio > 50:

        return "A"  # Seña de la letra A (puño cerrado con pulgar al lado)

    elif indice[1] < medio[1] and medio[1] < anular[1] and anular[1] < meñique[1]:

        return "B"  # Seña de la letra B (todos los dedos estirados, pulgar en la palma)

    elif distancia_pulgar_indice > 50 and distancia_indice_medio > 50:

        return "C"  # Seña de la letra C (mano en forma de "C")

  

    return "Desconocido"

  

# Captura de video en tiempo real

cap = cv2.VideoCapture(0)

  

while cap.isOpened():

    ret, frame = cap.read()

    if not ret:

        break

  

    # Convertir a RGB

    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

  

    # Procesar la imagen con MediaPipe

    results = hands.process(frame_rgb)

  

    # Dibujar puntos de la mano y reconocer letras

    if results.multi_hand_landmarks:

        for hand_landmarks in results.multi_hand_landmarks:

            mp_drawing.draw_landmarks(frame, hand_landmarks, mp_hands.HAND_CONNECTIONS)

            # Identificar la letra

            letra_detectada = reconocer_letra(hand_landmarks, frame)

  

            # Mostrar la letra en pantalla

            cv2.putText(frame, f"Letra: {letra_detectada}", (50, 50),

                        cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 0), 2, cv2.LINE_AA)

  

    # Mostrar el video

    cv2.imshow("Reconocimiento de Letras", frame)

  

    # Salir con la tecla 'q'

    if cv2.waitKey(1) & 0xFF == ord('q'):

        break

  

# Liberar recursos

cap.release()

cv2.destroyAllWindows()

```

Se usa el siguiente código como la base de detección entre las manos atreves de mediapipe, para poder poner los landmaks de cada parte del dedo

``` python
import cv2

import mediapipe as mp

import numpy as np

  

# Inicializar MediaPipe Hands

mp_hands = mp.solutions.hands

mp_drawing = mp.solutions.drawing_utils

hands = mp_hands.Hands(min_detection_confidence=0.7, min_tracking_confidence=0.7)

  

# Función para determinar la letra según la posición de los dedos

def reconocer_letra(hand_landmarks, frame):

    h, w, _ = frame.shape  # Tamaño de la imagen

    # Obtener coordenadas de los puntos clave en píxeles

    dedos = [(int(hand_landmarks.landmark[i].x * w), int(hand_landmarks.landmark[i].y * h)) for i in range(21)]

    # Obtener posiciones clave (puntas de los dedos)

    pulgar, indice, medio, anular, meñique = dedos[4], dedos[8], dedos[12], dedos[16], dedos[20]

  

    # Mostrar los números de los landmarks en la imagen

    for i, (x, y) in enumerate(dedos):

        cv2.circle(frame, (x, y), 5, (0, 234, 0), -1)  # Puntos verdes

        cv2.putText(frame, str(i), (x, y - 10), cv2.FONT_HERSHEY_SIMPLEX, 0.5, (0, 255, 255), 1)

  

    # Dibujar coordenadas del pulgar

    cv2.putText(frame, f'({int(pulgar[0])}, {int(pulgar[1])})', (pulgar[0], pulgar[1] - 15),

                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (245, 0, 0), 2, cv2.LINE_AA)

  

    # El if detecta si hay dos manos

    if results.multi_hand_landmarks and len(results.multi_hand_landmarks) == 2:

        #Agarra el set de cordenadas de cada mano y la asigna a la variable mano

        mano1 = results.multi_hand_landmarks[0]

        mano2 = results.multi_hand_landmarks[1]

  

        # Coordenadas de los índices de ambas manos para unir los indices

        indice1 = (int(mano1.landmark[8].x * w), int(mano1.landmark[8].y * h))

        indice2 = (int(mano2.landmark[8].x * w), int(mano2.landmark[8].y * h))

  

        # Dibujar línea entre índices por el momento en lo que me sale el cuadrado

        cv2.line(frame, indice1, indice2, (244,34,12), 2)

  

    # Calcular distancias en píxeles

    distancia_pulgar_indice = np.linalg.norm(np.array(pulgar) - np.array(indice))

    distancia_indice_medio = np.linalg.norm(np.array(indice) - np.array(medio))

  

    cv2.putText(frame, f'({distancia_pulgar_indice})', (pulgar[0]-40, pulgar[1] - 45),

                cv2.FONT_HERSHEY_SIMPLEX, 0.6, (255, 255, 255), 2, cv2.LINE_AA)

  
  

    #cv2.circle(frame, (pulgar[0]+20,pulgar[1]+20), int(distancia_indice_medio), (34,234,65), -1 )

    # Lógica para reconocer algunas letras

    if distancia_pulgar_indice < 30 and distancia_indice_medio > 50:

        return "A"  # Seña de la letra A (puño cerrado con pulgar al lado)

    elif indice[1] < medio[1] and medio[1] < anular[1] and anular[1] < meñique[1]:

        return "B"  # Seña de la letra B (todos los dedos estirados, pulgar en la palma)

    elif distancia_pulgar_indice > 50 and distancia_indice_medio > 50:

        return "C"  # Seña de la letra C (mano en forma de "C")

  

    return "Desconocido"

  

# Captura de video en tiempo real

cap = cv2.VideoCapture(0)

  

while cap.isOpened():

    ret, frame = cap.read()

    if not ret:

        break

  

    # Convertir a RGB

    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

  

    # Procesar la imagen con MediaPipe

    results = hands.process(frame_rgb)

  

    # Dibujar puntos de la mano y reconocer letras

    if results.multi_hand_landmarks:

        for hand_landmarks in results.multi_hand_landmarks:

            mp_drawing.draw_landmarks(frame, hand_landmarks, mp_hands.HAND_CONNECTIONS)

            # Identificar la letra

            letra_detectada = reconocer_letra(hand_landmarks, frame)

  

            # Mostrar la letra en pantalla

            cv2.putText(frame, f"Letra: {letra_detectada}", (50, 50),

                        cv2.FONT_HERSHEY_SIMPLEX, 1, (0, 255, 0), 2, cv2.LINE_AA)

  

    # Mostrar el video

    cv2.imshow("Reconocimiento de Letras", frame)

  

    # Salir con la tecla 'q'

    if cv2.waitKey(1) & 0xFF == ord('q'):

        break

  

# Liberar recursos

cap.release()

cv2.destroyAllWindows()
```

En este segundo código nos permite detectar si ambas manos se encuentran en el recuadro para realizar la acción o permitirnos en general detectar de la mano izquierda a la derecha para que cada mano controle un aspecto del pincel en la pizarra

- Mano uno: Res escalado del pincel
- Mano dos: Que gire el pincel

Se le asignara al escaneo de las manos su propio recuadro para no interferir con la detección de la pizarra ( porque pues no podía borrar la lineal que quedaba entre los dedos sin borrar la pizarra )
### Código de la Pizarra final

``` python
import cv2

import numpy as np

import mediapipe as mp

  

mp_hands = mp.solutions.hands

mp_drawing = mp.solutions.drawing_utils

  

# Inicializamos el detector de manos

hands = mp_hands.Hands(min_detection_confidence=0.7, min_tracking_confidence=0.7)

  
  

# Captura de video

cap = cv2.VideoCapture(0)

  

# Crear un lienzo para dibujar

ret, frame = cap.read()

canvas = np.zeros_like(frame)

# Nueva variable para determinar que tipo de figura se dibuja

tipoFigura = 0

  

# Esta variable controla si la mano derecha modifica el tamaño del trazo

cambiar_tamaño_activado = False

# Esta variable controla si se aplica rotación acumulada al canvas cuando hay mano izquierda

rotacion_activada = False

  

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

  

tamaño_personal = 5     # tamaño controlado por mano derecha

rotacion_personal = 0   # rotación en grados controlada por mano izquierda

  
  

def Cambio_de_color(Coordenadas):

    # Esta funcion cambia de color usando el arreglo y checando si las coordenadas

    # que recibe se encuentran dentro de los cuadros. Regresa el color correspondiente.

    cx,cy=Coordenadas

    indice_cuadro = cx // 80  # 640/8 = 80 px por cuadro me da el entero sin decimales

    if 0 <= indice_cuadro < len(colores):

        return colores[indice_cuadro]

    return None

  
  

def rotar_canvas(canvas_local, angulo):

    # Esta funcion rota el canvas alrededor de su centro.

    # retorna una nueva imagen rotada

    h, w = canvas_local.shape[:2]

    centro = (w // 2, h // 2)

    M = cv2.getRotationMatrix2D(centro, angulo, 1.0)

    rotado = cv2.warpAffine(canvas_local, M, (w, h))

    return rotado

  

def dibujo_sobre_canvas(canvas_local, prev_center_local, center_local, color_local, tamaño_local, tipoFigura_local, rotacion_local):

    if tipoFigura_local == 0:

        # Línea entre el punto anterior y el actual

        cv2.line(canvas_local, prev_center_local, center_local, color_local, tamaño_local)

  

    elif tipoFigura_local == 1:

        # Círculo en la posición actual (tamaño = radio)

        cv2.circle(canvas_local, center_local, tamaño_local, color_local, -1)

  

    elif tipoFigura_local == 2:

        # Cuadro entre el punto anterior y el actual

        x1, y1 = prev_center_local

        x2, y2 = center_local

        cv2.rectangle(canvas_local, (x1, y1), (x2, y2), color_local, tamaño_local)

  

# Ventana para las landmarks de las manos

ventana_manos = np.zeros((480, 640, 3), dtype=np.uint8)

  
  

# Variables auxiliares para detectar presencia de manos

mano_izquierda_detectada = False

mano_derecha_detectada = False

  

while True:

    ret, frame = cap.read()

    if not ret:

        break

  

    # Convertir a RGB para MediaPipe

    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    results = hands.process(frame_rgb)

    # Esto la borra para que no detecte el chingo de landmarks JAJAJAJAJA

    # me pelee comicamente con esto una hora

    ventana_manos[:] = (0, 0, 0)

  

    # Ponemos el false en los indicadores de presencia de manos en cada frame

    mano_izquierda_detectada = False

    mano_derecha_detectada = False

  

    # Usamos results.multi_hand_landmarks junto con results.multi_handedness para
    # saber si la mano es izquierda o derecha pero me las da alrevez aveces

    if results.multi_hand_landmarks and results.multi_handedness:

        for hand_landmarks, handedness in zip(results.multi_hand_landmarks, results.multi_handedness):

            # Dibujar landmarks en la ventana de manos

            mp_drawing.draw_landmarks(ventana_manos, hand_landmarks, mp_hands.HAND_CONNECTIONS)

  

            # Determinar cual es

            etiqueta = handedness.classification[0].label

  

            h, w, _ = frame.shape

            # Coordenadas de pulgar al indice y su distancia

            pulgar = np.array([hand_landmarks.landmark[4].x * w, hand_landmarks.landmark[4].y * h])

            indice = np.array([hand_landmarks.landmark[8].x * w, hand_landmarks.landmark[8].y * h])

            distancia = np.linalg.norm(pulgar - indice)

  

            # Mano derecha controla tamaño

            if etiqueta == 'Right':

                mano_derecha_detectada = True

                if cambiar_tamaño_activado:

                    # Normalizamos la distancia de los indices para que me regresen entre 1 a 40

                    tamaño_personal = int(np.interp(distancia, [10, 200], [1, 40]))

            # Mano izquierda rotacion

            elif etiqueta == 'Left':

                mano_izquierda_detectada = True

                # Se normaliza la distancia de nuevo pero en este caso entre 0 180 para los grados

                rotacion_personal = int(np.interp(distancia, [10, 200], [0, 180]))

    # Esto evita que el canvas retenga la rotacion si la mano se sale de escena para que no se ponga loco

    if not mano_izquierda_detectada:

        rotacion_personal = 0

    # Convertimos el frame a HSV, creamos máscara para rojo

    hsv = cv2.cvtColor(frame, cv2.COLOR_BGR2HSV)

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

        cx, cy = center

  

        # Dibujar círculo en la cámara en la posición detectada (pequeño punto)

        cv2.circle(frame, center, 5, (0, 0, 255), -1)

  

        # Dibujar la forma en el lienzo si hay prev_center

        if prev_center is not None:

            # Si la pelota está en la franja superior (cuadros), cambiamos color

            if 0 <= cy <= 60:

                color = Cambio_de_color(center)

            else:

                # el tamaño usado es tamano_personal que despues e recibe como tamaño local para cambiar el grosor de

                dibujo_sobre_canvas(canvas, prev_center, center, color, tamaño_personal, tipoFigura, rotacion_personal)

  

        # Actualizamos prev_center

        prev_center = center

    else:

        prev_center = None

  

    # Checa si rota o no

    if rotacion_activada and mano_izquierda_detectada:

        canvas = rotar_canvas(canvas, rotacion_personal)

        canvas_rotado = canvas.copy()

        # Se tiene que poner en none si no al rotar se desalinea

        prev_center = None

    else:

        # Si no rotamos pues se pone nada mas el canvas viejo

        canvas_rotado = canvas.copy()

  

    # Combinar la cámara y el lienzo

    combined = cv2.add(frame, canvas_rotado)

  

    # Agregar el cuadrado/rectangulo sobre el final para que no se sobre ponga la raya al cuadro

    # La ecuacion repetirla para saber el rango donde tiene q estar la pelota para que el color cambie

    for i in range(8):

        x_inicio = i * ancho_cuadro

        x_fin = (i + 1) * ancho_cuadro

        cv2.rectangle(combined, (x_inicio, 0), (x_fin, alto_cuadro), colores[i], -1)

  

    # Mostrar resultados

    cv2.imshow("Dibujo", combined)

    cv2.imshow("Mascara", mask)

    cv2.imshow("Manos", ventana_manos)

  

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

    # Tecla t activa/desactiva el modo donde la mano derecha cambia el tamaño del trazo

    elif key == ord('t'):

        cambiar_tamano_activado = not cambiar_tamano_activado

    # Tecla r activa/desactiva la rotacio acumulada del canvas con la mano izquierda

    elif key == ord('r'):

        rotacion_activada = not rotacion_activada

  
  

cap.release()

cv2.destroyAllWindows()
```

Esta es la versión final del código su mayor cambio es:

``` python
results = hands.process(frame_rgb)

    # Esto la borra para que no detecte el chingo de landmarks JAJAJAJAJA

    # me pelee comicamente con esto una hora

    ventana_manos[:] = (0, 0, 0)

  

    # Ponemos el false en los indicadores de presencia de manos en cada frame

    mano_izquierda_detectada = False

    mano_derecha_detectada = False

  

    # Usamos results.multi_hand_landmarks junto con results.multi_handedness para

    # saber si la mano es izquierda o derecha pero me las da alrevez aveces

    if results.multi_hand_landmarks and results.multi_handedness:

        for hand_landmarks, handedness in zip(results.multi_hand_landmarks, results.multi_handedness):

            # Dibujar landmarks en la ventana de manos

            mp_drawing.draw_landmarks(ventana_manos, hand_landmarks, mp_hands.HAND_CONNECTIONS)

  

            # Determinar cual es

            etiqueta = handedness.classification[0].label

  

            h, w, _ = frame.shape

            # Coordenadas de pulgar al indice y su distancia

            pulgar = np.array([hand_landmarks.landmark[4].x * w, hand_landmarks.landmark[4].y * h])

            indice = np.array([hand_landmarks.landmark[8].x * w, hand_landmarks.landmark[8].y * h])

            distancia = np.linalg.norm(pulgar - indice)

  

            # Mano derecha controla tamaño

            if etiqueta == 'Right':

                mano_derecha_detectada = True

                if cambiar_tamaño_activado:

                    # Normalizamos la distancia de los indices para que me regresen entre 1 a 40

                    tamaño_personal = int(np.interp(distancia, [10, 200], [1, 40]))

            # Mano izquierda rotacion

            elif etiqueta == 'Left':

                mano_izquierda_detectada = True

                # Se normaliza la distancia de nuevo pero en este caso entre 0 180 para los grados

                rotacion_personal = int(np.interp(distancia, [10, 200], [0, 180]))
```

El cual nos permite usar los landmarks de las manos atreves de mediapipe para poder rotar el cambas o agrandar el pincel.

La rotación la maneja esta función la cual nos da el tamaño del frame el cual después obtenemos el alto y el ancho de este para sacar el centro de este y hacer una matriz para rotarlo desde este, para después rotarlo usando warpedAffine

``` python
def rotar_canvas(canvas_local, angulo):

    # Esta funcion rota el canvas alrededor de su centro.

    # retorna una nueva imagen rotada

    h, w = canvas_local.shape[:2]

    centro = (w // 2, h // 2)

    M = cv2.getRotationMatrix2D(centro, angulo, 1.0)

    rotado = cv2.warpAffine(canvas_local, M, (w, h))

    return rotado
```

Lo que hace warpedAffine es darnos la rotación de nuestro canvas  al aplicar a cada pixel la matriz de rotación que generamos en la matriz M a base de el ángulo de rotación que queremos darle y el centro de esta

Lo malo es que debido a la rotación que hay el color se desvanece un poco o se pone borroso debido a como funciona que al darnos coordenadas no tan exactas como (10.5,10.5) se toma el color cercano haciendo que se degrade la imagen