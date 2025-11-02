---
title: Calculadora con manos
nav_order: 8
---
# Calculadora seguimiento de manos

El propósito del código es crear una calculadora simple con cv2, se reutilizo mucho código del [Dibujo con seguimiento](Proyecto 1 Dibujo con Seguimiento.md) para crear la interfaz
- Se vaciaron los cuadros que se crean calculando el tamaño de la pantalla para llenarlos con la función de texto de cv2 y colocar los números
- Se reutiliza la detección por zonas pero omitiendo que las coordenadas que siguen ahora son las del índice de la mano derecha

``` python

import cv2

import mediapipe as mp

  

mp_hands = mp.solutions.hands

mp_drawing = mp.solutions.drawing_utils

hands = mp_hands.Hands(static_image_mode=False, max_num_hands=2, min_detection_confidence=0.5)

  

# Tamaños de los cuadros

ancho_cuadro = 640 // 10

ancho_cuadro_op = 640 // 4

alto_cuadro = 60

  

# Captura de video

cap = cv2.VideoCapture(0)

  

operacion = ""  # guarda los simbolos presionados

ultima_zona = None  # evita multiples detecciones por frame

  

def detectar_zona(x, y): # Recibe las cordenadas para regresar en donde esta

    # Zona de numeros (arriba)

    if y < alto_cuadro:

        index = x // ancho_cuadro # Se reusa la divicion para determinar en que cuadro esta

        if 0 <= index <= 9:

            return str(index)

    # Zona de operaciones (abajo)

    if 400 <= y <= 400 + alto_cuadro + 19:

        index = x // ancho_cuadro_op

        if index == 0:

            return "+"

        elif index == 1:

            return "-"

        elif index == 2:

            return "*"

        elif index == 3:

            return "="

    return None

  

while True:

    ret, frame = cap.read()

    if not ret:

        break

  

    frame = cv2.flip(frame, 1)

    h, w, _ = frame.shape

  

    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    results = hands.process(frame_rgb)

  

    # Detectar dedo índice derecho

    right_index = None

    if results.multi_hand_landmarks and results.multi_handedness:

        for hand_landmarks, handedness in zip(results.multi_hand_landmarks, results.multi_handedness):

            if handedness.classification[0].label == 'Right':

                index_tip = hand_landmarks.landmark[8]

                x, y = int(index_tip.x * w), int(index_tip.y * h)

                right_index = (x, y)

                cv2.circle(frame, (x, y), 8, (0, 0, 255), -1)

  

    # Dibujar cuadros de números

    for i in range(10):

        x_inicio = i * ancho_cuadro

        x_fin = (i + 1) * ancho_cuadro

        cv2.rectangle(frame, (x_inicio, 0), (x_fin, alto_cuadro), (0, 0, 255), 1)

        cv2.putText(frame, str(i), (x_inicio + 20, 40), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2)

  

    # Dibujar cuadros de operaciones y guardalos para poner el texto en los cuadros

    operaciones = ["+", "-", "*", "="]

    for i, op in enumerate(operaciones):

        x_inicio = i * ancho_cuadro_op

        x_fin = (i + 1) * ancho_cuadro_op

        y_inicio = 400

        y_fin = alto_cuadro + 419

        cv2.rectangle(frame, (x_inicio, y_inicio), (x_fin, y_fin), (0, 0, 255), 1)

        cv2.putText(frame, op, (x_inicio + 60, y_inicio + 50), cv2.FONT_HERSHEY_SIMPLEX, 1.5, (255, 255, 255), 3)

  

    # Detectar si el índice toca alguna zona que enrealidad comprueba si esta en el mismo valor, ademas de checar por algun error y re iniciar la operacion en pantalla

    if right_index:

        x, y = right_index

        zona = detectar_zona(x, y)

        if zona and zona != ultima_zona: # Checa que se toco y evita que se repita en los frames

            if zona == "=":

                try:

                    resultado = eval(operacion)

                    print(f"{operacion} = {resultado}")

                    operacion = str(resultado)

                except: # Reinicia en caso de salir mal

                    print("Error en la operación")

                    operacion = ""

            else:

                operacion += zona # Si no se toca el igual se va a sumar el numero/ecuacion o 'Zona' a la operacion

            ultima_zona = zona

        elif not zona:

            ultima_zona = None  # reinicia si el dedo sale de zona

  

    cv2.putText(frame, f"Operacion: {operacion}", (10, 380), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 0), 2)

  

    cv2.imshow("Calculadora con Manos", frame)

  

    if cv2.waitKey(1) & 0xFF == 27:

        break

  

cap.release()

cv2.destroyAllWindows()

```

Ya nada mas al final checa que operación tiene y la procesa checando que este presente en el frame el dedo índice, además de revisando si se encuentra en la misma zona usando el concepto de 'hitboxes' para determinar cuando entra y que espere a su salida