---
title: Calculadora con manos
nav_order: 8
---
# Calculadora seguimiento de manos
El propósito del código es crear una calculadora simple con cv2, se reutilizo mucho código del [Proyecto 1 Dibujo con seguimiento](Proyecto 1 Dibujo con Seguimiento.md) para crear la interfaz
- Se vaciaron los cuadros que se crean calculando el tamaño de la pantalla para llenarlos con la función de texto de cv2 y colocar los números
- Se reutiliza la detección por zonas pero omitiendo que las coordenadas que siguen ahora son las del índice de la mano derecha
``` python

import cv2

import mediapipe as mp

  

mp_hands = mp.solutions.hands

hands = mp_hands.Hands(static_image_mode=False, max_num_hands=2, min_detection_confidence=0.5)

  

ancho_cuadro = 640 // 10

ancho_cuadro_op = 640 // 4

alto_cuadro = 60

cap = cv2.VideoCapture(0)

  

operacion = ""

ultima_zona = None

  

while True:

    ret, frame = cap.read()

    if not ret:

        break

    frame = cv2.flip(frame, 1)

    frame_rgb = cv2.cvtColor(frame, cv2.COLOR_BGR2RGB)

    results = hands.process(frame_rgb)

  

    h, w, _ = frame.shape

    dedo = None

  

    # Detección del indice derecho

    if results.multi_hand_landmarks and results.multi_handedness:

        for hand_landmarks, handedness in zip(results.multi_hand_landmarks, results.multi_handedness):

            if handedness.classification[0].label == 'Right':

                x = int(hand_landmarks.landmark[8].x * w)

                y = int(hand_landmarks.landmark[8].y * h)

                dedo = (x, y)

                cv2.circle(frame, dedo, 8, (0, 0, 255), -1)

  

    # Dibujar los cuadros de numeros sacado del proyecto 1 casi todo menos la deteccion

    for i in range(10):

        x_inicio = i * ancho_cuadro

        x_fin = (i + 1) * ancho_cuadro

        cv2.rectangle(frame, (x_inicio, 0), (x_fin, alto_cuadro), (0, 0, 255), 1)

        cv2.putText(frame, str(i), (x_inicio + 20, 40), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 255), 2)

  

    # Dibujar los cuadros de operaciones

    for i, op in 4:

        x_inicio = i * ancho_cuadro_op

        x_fin = (i + 1) * ancho_cuadro_op

        cv2.rectangle(frame, (x_inicio, 400), (x_fin, 400 + alto_cuadro), (0, 0, 255), 1)

        cv2.putText(frame, op, (x_inicio + 60, 440), cv2.FONT_HERSHEY_SIMPLEX, 1.5, (255, 255, 255), 3)

  

    # Detectar si el dedo toca un cuadro

    ops = ["+", "-", "*", "="] # Indice creado para facilitar la deteccion dependiendo del cuadro si es el 1 al 4 correspondiendo a la operacion del inidce

    if dedo:

        x, y = dedo

        zona = None

  

        if y < alto_cuadro:

            zona = str(x // ancho_cuadro)

        elif 400 <= y <= 400 + alto_cuadro: # Detecta si se encuentra en la sona de las operaciones

            index = x // ancho_cuadro_op

            zona = ops[index] if index < len(ops) else None

  

        # Si hay zona nueva para evitar que cada frame se genere un numero y que espere a que entre en otra guardando donde esta y donde va a estar como el codigo de las esferaz

        # Que enrealidad es el tipo de la operacion +,-,*,=

        if zona and zona != ultima_zona:

            if zona == "=":

                try:

                    resultado = eval(operacion) # Para que evalue la operacion

                    print(f"{operacion} = {resultado}")

                    operacion = str(resultado)

                except:

                    print("Error en la operación")

            else:

                operacion += zona

                print("Operación actual:", operacion)

            ultima_zona = zona

        elif not zona:

            ultima_zona = None

  

    cv2.putText(frame, f"Operacion: {operacion}", (10, 380), cv2.FONT_HERSHEY_SIMPLEX, 1, (255, 255, 0), 2)

  

    cv2.imshow("Line", frame)

  

    if cv2.waitKey(1) & 0xFF == 27:

        break

  

cap.release()

cv2.destroyAllWindows()

```