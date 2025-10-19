# Tabajo 1

## Calibración de la camara

Para la calibración de la cámara se siguió la guia de GeeksforGeeks (s.f.), con el cual se obtuvieron los siguientes resultados.

La función cv2.drawChessboardCorners() desempeña un papel crucial al visualizar y verificar el éxito de la detección de esquinas en la imagen, asegurando la calidad de los puntos de referencia. Se empleó la siguiente línea de código para la visualización:

```python
# Dibujar y mostrar las esquinas en la imagen
image = cv2.drawChessboardCorners(image, 
                                  CHECKERBOARD, 
                                  corners2, ret)
```
En la Figura 1 se muestran cuatro de las imágenes utilizadas con las esquinas detectadas. Esta detección es esencial, ya que estos puntos proporcionan las coordenadas de referencia precisas necesarias para el algoritmo de calibración de la cámara.

<table>
  <caption>
    <strong>Figura 1:</strong> Tableros de ajedrez con las esquinas detectadas.
  </caption>
  <tr>
    <td style="text-align: center;">
      <img src="..\Taller 1\Puntos_detectados\detected_imagen5.jpeg" width="90%" alt="Imagen 1">
      <p><strong>Figura 1.a</strong></p>
    </td>
    <td style="text-align: center;">
      <img src="../Taller 1\Puntos_detectados\detected_imagen3.jpeg" width="90%" alt="Imagen 2">
      <p><strong>Figura 1.b</strong> </p>
    </td>
  </tr>
  <tr>
    <td style="text-align: center;">
      <img src="../Taller 1\Puntos_detectados\detected_imagen7.jpeg" width="90%" alt="Imagen 3">
      <p><strong>Figura 1.c</strong></p>
    </td>
    <td style="text-align: center;">
      <img src="../Taller 1\Puntos_detectados\detected_imagen6.jpeg" width="90%" alt="Imagen 4">
      <p><strong>Figura 1.d</strong> </p>
    </td>
  </tr>
</table>

El objetivo principal de la calibración es obtener la estimación de los parámetros que transforman una coordenada del mundo real (3D) en una coordenada de píxel en la imagen (2D). Para ello, se determinaron la matriz intrínseca de la cámara y los coeficientes de distorsión.

**Ecuación 1:** Matriz Intrínseca de la Cámara ($\mathbf{K}$)

$$
\mathbf{K} = \begin{bmatrix}
1.344 \times 10^{3} & 0.0 & 801.86 \\
0.0 & 1.351 \times 10^{3} & 613.08 \\
0.0 & 0.0 & 1.0
\end{bmatrix}
$$

De la Ecuación 1 se obtine la  observar la Matriz Intrínseca de la cámara que esta compuesta por las longitudes focales $f_x$ y $f_y$, y las cordenadas $c_x$ y $c_y$ del centro óptico.  
Se observar que $f_x \approx 1344.23 \text{ píxeles}$ y $f_y \approx 1350.77 \text{ píxeles}$, la diferencia  aproximadamente 6.5 píxeles entre los ejes focales representa menos del $1\%$ del valor total, lo que confirma que la camara utiliza píxeles cuadrados, si esta diferencia resulta ser significativamente mayor ya estariamos hablando de píxeles rectangulares o que hay un problema, ya sea de aliasing (píxeles de un sensor es demasiado grande en comparación con los detalles finos de la escena) o escalado en la lectura del sensor. Respecto al centro óptico, los puntos $c_x = 801.86 \text{ píxeles}$ y $c_y = 613.08 \text{ píxeles}$ se encuentran muy próximos al centro geométrico teórico de la imagen $(800.0, 600.0)$. El desplazamiento es mínimo, siendo de $1.86 \text{ píxeles}$ en $x$ y $13.08 \text{ píxeles}$ en $y$.

$$\text{Error RMS Total} = \frac{1}{N} \sum_{i=1}^{N} \left( \frac{1}{M_{i}} \sum_{j=1}^{M_{i}} \left\| \mathbf{m}_{i, j} - \mathbf{\hat{m}}_{i, j} \right\| \right)$$

Caption 1. Fórmula utilizada para calcular el Error RMS Total de Reproyección. $N$ es el número total de imágenes; $M_i$ es el número de puntos detectados en la imagen $i$; $\mathbf{m}_{i, j}$ es la coordenada 2D real (observada) del punto; y $\mathbf{\hat{m}}_{i, j}$ es la coordenada 2D predicha (reproyectada) por el modelo de la cámara.

El RMS obtenido fue de $0.495 \text{ píxeles}$, que es menor al umbral de $0.5 \text{ píxeles}$. Esto quiere decir que la ubicación real de las esquinas en la imagen y la ubicación predicha por el modelo matemático de la cámara difieren en menos de medio píxel.

**Ecuación 2:** Vector de Coeficientes de Distorsión ($\mathbf{D}$)

$$
\mathbf{D} = \begin{pmatrix}
2.771 \times 10^{-1} & -1.895 & -7.036 \times 10^{-5} & 5.678 \times 10^{-4} & 4.138
\end{pmatrix}
$$

En la Ecuación 2 se obtienen los coheficinetes de distorción $k_1 = 0.277$ de primer orden, $k_2 = -1.895 $, $p_1 = -7.036 \times 10^{-5 } $ de segundo orden y $k_3 = 4.138$ de tercer orden. Cuando $k_1$ es negativo predomina la distorcion barril (las lineas se curvan hacia afuera), pero cuando $k_1$ es positivo se espera tener una distorsion cojin (las lineas se curvan hacia adentro). Como el coeficiente $k_1 > 0$ se espera que las imagenes muestren una distorsion cojin. 

Respecto a los coeficientes de distorsión, se observa lo siguiente:

- Coeficiente $k_2$: Al ser un valor negativo, $k_2$ está actuando como un compensador o corrector para mitigar el efecto de una potencial subcorrección (o sobrecorrección) generada por $k_1$.

- Coeficiente $k_3$: El valor de $k_3$ es significativamente grande. Esto indica que la distorsión de la lente es compleja o fuerte, lo que sugiere que el modelo de calibración no logró un ajuste preciso de la distorsión utilizando únicamente los coeficientes $k_1$ y $k_2$.

Los parámetros $p_1$ y $p_2$ miden la distorsión tangencial, la cual provoca que un punto en el mundo real se proyecte en la imagen con un pequeño desplazamiento asimétrico a lo largo de un vector que no es puramente radial.

Los valores obtenidos son: $p_1 = -7.036 \times 10^{-5}$ y $p_2 = 5.678 \times 10^{-4}$.

Al ser valores tan pequeños, esto demuestra una alineación física casi perfecta entre el eje óptico de la lente y el sensor de la cámara, un factor ideal para la visión artificial de precisión.

<h4>Figura 2: Comparación de Imágenes (Original vs. Corregida)</h4>
<div style="display: grid; grid-template-columns: 50% 50%; gap: 10px;">
    
<!-- PAR 1 -->
<div style="text-align: center;">
    <img src="../Taller 1/Imagenes_calibracion/imagen2.jpeg" width="100%" alt="Imagen Original 1">
    <p><strong>(a.1) Original</strong></p>
</div>
<div style="text-align: center;">
    <img src="../Taller 1/Imagenes_corregidas/undistorted_imagen2.jpeg" width="100%" alt="Imagen Corregida 1">
    <p><strong>(b.1) Corregida</strong></p>
</div>

<!-- PAR 2 -->
<div style="text-align: center;">
    <img src="../Taller 1/Imagenes_calibracion/imagen3.jpeg" width="100%" alt="Imagen Original 2">
    <p><strong>(a.2) Original</strong></p>
</div>
<div style="text-align: center;">
    <img src="../Taller 1/Imagenes_corregidas/undistorted_imagen3.jpeg" width="100%" alt="Imagen Corregida 2">
    <p><strong>(b.2) Corregida</strong></p>
</div>

<!-- PAR 3 -->
<div style="text-align: center;">
    <img src="../Taller 1/Imagenes_calibracion/imagen7.jpeg" width="100%" alt="Imagen Original 3">
    <p><strong>(a.3) Original</strong></p>
</div>
<div style="text-align: center;">
    <img src="../Taller 1/Imagenes_corregidas/undistorted_imagen7.jpeg" width="100%" alt="Imagen Corregida 3">
    <p><strong>(b.3) Corregida</strong></p>
</div>
</div>

La corrección de la distorsión radial se evaluó con especial atención en las esquinas, ya que esta distorsión aumenta proporcionalmente con la distancia al centro óptico $(𝑟)$, lo que hace que dicha zona requiera el mayor desplazamiento de píxeles. Visualmente, la Figura 2 confirma el éxito, mostrando que las líneas del tablero, que en la imagen original se curvaban hacia el interior (distorsión de tipo cojín), ahora son segmentos perfectamente rectos y paralelos, incluso en los bordes extremos. Esta corrección robusta está respaldada por el bajo error RMS de reproyección $(0.495 px)$, lo que indica que la predicción de la posición de cada punto, incluidos los de las esquinas, se logró con una precisión subpíxel.


## Referencias
GeeksforGeeks. (s.f.). *Camera Calibration with Python OpenCV*. https://www.geeksforgeeks.org/python/camera-calibration-with-python-opencv/

Reolink. (s.f.). Barrel Distortion: What It Is, and How to Fix It. Reolink Blog. https://reolink.com/blog/barrel-distortion/

OpenCV Development Team. (s.f.). Camera Calibration and 3D Reconstruction. Obtenido de la documentación oficial de OpenCV, módulo calib3d.

Szeliski, R. (2021). Computer Vision: Algorithms and Applications (2.ª ed.). Springer.