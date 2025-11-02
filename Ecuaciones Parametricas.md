---
title: Ecuaciones Parametricas
nav_order: 11
---
# Ecuaciones Paramétricas
## ¿ Qué son las EP ?

Las **ecuaciones paramétricas** son una forma de representar curvas, trayectorias o movimientos utilizando una **variable independiente llamada parámetro**, normalmente denotada como ( t ).  
En lugar de expresar una variable directamente en función de otra (como en las ecuaciones tradicionales ( y = f(x) )), las ecuaciones paramétricas definen cada coordenada por separado en función del mismo parámetro:  

$$
x = f(t), \quad y = g(t) 
$$

Este método permite describir con mayor precisión figuras o trayectorias que no pueden representarse fácilmente mediante una sola ecuación. Por ejemplo, pueden usarse para representar líneas, curvas cerradas, espirales o trayectorias en el espacio tridimensional.

El parámetro ( t ) suele interpretarse como el **tiempo** o como una **variable que controla el recorrido** a lo largo de la curva. A medida que ( t ) cambia, los valores de ( x ) y ( y ) varían simultáneamente, generando los puntos que conforman la figura.

En resumen, las ecuaciones paramétricas permiten expresar de manera más general y versátil la posición de un punto en el plano o en el espacio, facilitando el análisis y la representación de movimientos o curvas que las ecuaciones tradicionales no pueden describir fácilmente.

## 10 Ecuaciones Paramétricas simples

``` python

import cv2

import numpy as np



  

# Tamaño de la imagen

alto, ancho = 600, 600

  

# Parámetro t puede ser modificado para alterar las cuervas

t = np.linspace(0, 2*np.pi, 500)

  
def ecuaciones(t):

    curvas = []

    # Cada curva se tiene que ajustar si no se me sale del cuadro

    curvas.append( (np.cos(t)*100 + 300, np.sin(t)*100 + 300) )   # 1. Círculo

    curvas.append( (2*np.cos(t)*50 + 300, np.sin(t)*100 + 300) )  # 2. Elipse

    curvas.append( (np.cos(t)/(1+np.sin(t)**2)*150 + 300, np.cos(t)*np.sin(t)/(1+np.sin(t)**2)*150 + 300) ) # 3. Lemniscata

    curvas.append( (np.sin(2*t)*100 + 300, np.sin(3*t)*100 + 300) ) # 4. Hipocicloide simple

    curvas.append( (np.sin(3*t)*50 + 300, np.sin(3*t)*50 + 300) )   # 5. Pétalo

    curvas.append( (np.sin(t)*100 + 300, np.sin(2*t)*100 + 300) )   # 6. Variante 1

    curvas.append( (np.cos(t)*100 + 300, np.cos(3*t)*100 + 300) )   # 7. Variante 2

    curvas.append( (np.sin(t)*100 + 300, np.sin(2*t)*50 + 300) )    # 8. Variante 3

    curvas.append( (np.cos(2*t)*50 + 300, np.sin(3*t)*100 + 300) )  # 9. Variante 4

    curvas.append( (np.sin(3*t)*100 + 300, np.cos(2*t)*50 + 300) )  # 10. Variante 5

    return curvas

  

curvas = ecuaciones(t)

  

# Dibujar cada curva en su propia imagen

for idx, (x_vals, y_vals) in enumerate(curvas):

    img = np.ones((alto, ancho, 3), dtype=np.uint8) * 255  # Fondo blanco

    x_pts = np.clip(np.array(x_vals, dtype=np.int32), 0, ancho-1)   # Asegura que queden dentro

    y_pts = np.clip(np.array(y_vals, dtype=np.int32), 0, alto-1)

  

    for i in range(len(x_pts)-1):

        cv2.line(img, (x_pts[i], y_pts[i]), (x_pts[i+1], y_pts[i+1]), (0,0,0), 1)

  

    cv2.imshow(str(idx+1), img)

  

cv2.waitKey(0)

cv2.destroyAllWindows()

```