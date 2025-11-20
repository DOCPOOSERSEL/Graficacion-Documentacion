---
title: Cuadrados y Triángulos GLUT
nav_order: 5
---
# Uso de GL para triángulos y cuadrados

## Cuadrados y básicos de GL
Se usa 'GL'  para generar un espacio donde se pueden crear varios modelos 3D desde el código

En este caso solo es un cuadro en 2D en un espacio 3D por motivos de la muestra

``` python

import sys

from OpenGL.GL import *

from OpenGL.GLUT import *

from OpenGL.GLU import *

  

def init():

    glClearColor(0.0, 0.0, 0.0, 1.0)  # Establecer color de fondo

    glMatrixMode(GL_PROJECTION)

    glLoadIdentity()

    gluPerspective(45, 1.0, 0.1, 50.0)  # Configuración de perspectiva

    glMatrixMode(GL_MODELVIEW)

  

def display():

    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)  # Limpiar buffers

    glLoadIdentity()

    glTranslatef(0.0, 0.0, -50)  # Mover la cámara hacia atrás

  

    # Dibujar un Cuadrado

    glBegin(GL_QUADS)

    glColor3f(1.0, 0.0, 0.0)  # Rojo

    glVertex3f(-1.0, -1.0, 0.0)

    glColor3f(0.0, 1.0, 0.0)  # Verde

    glVertex3f(1.0, -1.0, 0.0)

    glColor3f(0.0, 0.0, 1.0)  # Azul

    glVertex3f(1.0, 1.0, 0.0)

    glColor3f(1.0, 1.0 , 0.0)

    glVertex3f(-1.0,1.0,0.0)

    glEnd()

  

    glutSwapBuffers()  # Intercambiar buffers

  

def main():

    # Inicializar GLUT

    glutInit(sys.argv)

    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH)

    glutInitWindowSize(800, 600)

    glutCreateWindow(b'Triangulo con GLUT y Python')

    # En caso de usar el glut para crear la ventanna se deve de formatear el string para que use codificacion en binario o algo asi dice la documentacion para esto se pone 'b'

  

    init()

    glutDisplayFunc(display)

    glutMainLoop()

  

if __name__ == "__main__":

    main()

```

Aun que simple demuestra lo principal de el uso de los vértices para crear un objeto en el espacio 3D
- Siguen una línea de el primero al segundo y del ultimo al primero para cerrarlo
- Si no se sigue este orden pueden terminar en otras figuras

## Triangulo y Cuadrado

``` python

import sys

from OpenGL.GL import *

from OpenGL.GLUT import *

from OpenGL.GLU import *

  

def init():

    glClearColor(0.0, 0.0, 0.0, 1.0)  # Establecer color de fondo

    glMatrixMode(GL_PROJECTION)

    glLoadIdentity()

    gluPerspective(45, 1.0, 0.1, 50.0)  # Configuración de perspectiva

    glMatrixMode(GL_MODELVIEW)

  

def display():

    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)  # Limpiar buffers

    glLoadIdentity()

    glTranslatef(0.0, 0.0, -30)  # Mover la cámara hacia atrás

  

    # Dibujar un Cuadrado

    glBegin(GL_QUADS)

    glColor3f(1.0, 0.0, 0.0)  # Rojo

    glVertex3f(-1.0, -1.0, 0.0)

    glColor3f(0.0, 1.0, 0.0)  # Verde

    glVertex3f(-0.5, -1.0, 0.0)

    glColor3f(0.0, 0.0, 1.0)  # Azul

    glVertex3f(-0.5, 1.0, 0.0)

    glColor3f(1.0, 1.0 , 0.0)

    glVertex3f(-1.0,1.0,0.0)

    glEnd()

  

    # Dibujar un triangulo y aparte esta alrevez para no hacer dos cosas

    glBegin(GL_TRIANGLES)

    glColor3f(1.0, 0.0, 0.0)  # Rojo

    glVertex3f(0.0, 1.0, 0.0)

    glColor3f(0.0, 1.0, 0.0)  # Verde

    glVertex3f(0.5, 1.0, 0.0)

    glColor3f(0.0, 0.0, 1.0)  # Azul

    glVertex3f(0.25, -1.0, 0.0)

    glEnd()

  

    glutSwapBuffers()  # Intercambiar buffers

  

def main():

    # Inicializar GLUT

    glutInit(sys.argv)

    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH)

    glutInitWindowSize(800, 600)

    glutCreateWindow(b'Triangulo con GLUT y Python')

    # En caso de usar el glut para crear la ventanna se deve de formatear el string para que use codificacion en binario o algo asi dice la documentacion para esto se pone una 'b'

  

    init()

    glutDisplayFunc(display)

    glutMainLoop()

  

if __name__ == "__main__":

    main()

```

En este ejemplo simple se crean dos figuras simples con el fin de mostrar que puede haber mas de un tipo en la misma escena

Estos dos requieren un ajuste de la cámara para salir los dos o en el caso acomodarlos en el espacio donde existen

## Pirámide
Para terminar lo básico se colocan 4 triángulos en el espacio con un cuadrado abajo para que parezca una pirámide 

``` python

import sys

from OpenGL.GL import *

from OpenGL.GLUT import *

from OpenGL.GLU import *

  

def init():

    glClearColor(0.0, 0.0, 0.0, 1.0)  # Establecer color de fondo

    glMatrixMode(GL_PROJECTION)

    glLoadIdentity()

    gluPerspective(45, 1.0, 0.1, 50.0)  # Configuración de perspectiva

    glMatrixMode(GL_MODELVIEW)

  

def display():

    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT)

    glLoadIdentity()

    glTranslatef(0.0, 0.0, -10)  # Mover camara

  

    # Empiezan los triangulos

    glBegin(GL_TRIANGLES)

    # Cara frontal que no es frontal (rojo)

    glColor3f(1.0, 0.0, 0.0)

    glVertex3f(0.0, 1.0, 0.0)   # Punta

    glVertex3f(-1.0, -1.0, 1.0)

    glVertex3f(1.0, -1.0, 1.0)

  

    # Cara derecha (verde)

    glColor3f(0.0, 1.0, 0.0)

    glVertex3f(0.0, 1.0, 0.0)

    glVertex3f(1.0, -1.0, 1.0)

    glVertex3f(1.0, -1.0, -1.0)

  

    # Cara trasera que es la frontal (azul)

    glColor3f(0.0, 0.0, 1.0)

    glVertex3f(0.0, 1.0, 0.0)

    glVertex3f(1.0, -1.0, -1.0)

    glVertex3f(-1.0, -1.0, -1.0)

  

    # Cara izquierda (amarillo)

    glColor3f(1.0, 1.0, 0.0)

    glVertex3f(0.0, 1.0, 0.0)

    glVertex3f(-1.0, -1.0, -1.0)

    glVertex3f(-1.0, -1.0, 1.0)

  

    glEnd()

  

    # Base cuadrada

    glBegin(GL_QUADS)

    glColor3f(0.6, 0.3, 0.0)  # Marron

    glVertex3f(-1.0, -1.0, 1.0)

    glVertex3f(1.0, -1.0, 1.0)

    glVertex3f(1.0, -1.0, -1.0)

    glVertex3f(-1.0, -1.0, -1.0)

    glEnd()

  

    glutSwapBuffers()

  

def main():

    # Inicializar GLUT

    glutInit(sys.argv)

    glutInitDisplayMode(GLUT_DOUBLE | GLUT_RGB | GLUT_DEPTH)

    glutInitWindowSize(800, 600)

    glutCreateWindow(b'Triangulo con GLUT y Python')

    # En caso de usar el glut para crear la ventanna se deve de formatear el string para que use codificacion en binario o algo asi dice la documentacion para esto se pone una 'b'

  

    init()

    glutDisplayFunc(display)

    glutMainLoop()

  

if __name__ == "__main__":

    main()

```

Ya se genera cada uno de los objetos dando forma a lo que es una pirámide 