# Tabajo 1

## Calibración de la camara

Para la calibracion de la camara se siguio la guia de GeeksforGeeks (s.f.), con el cual se obtuvo los siguientes resultados. En la figura 1 se muestran cuatro de las imagenes utilizadas con las esquinas detectadas, esta deteccion se hace porque son puntos de referencia precisos que se utilizan como la entrada esencial para el algoritmo de calibracion de la cámara.

<table>
  <caption>
    <strong>Figura 1:</strong> Tableros de ajedrez con las esquinas detectadas.
  </caption>
  <tr>
    <td style="text-align: center;">
      <img src="../Imagenes_calibracion/detected_imagen1.jpeg" width="90%" alt="Imagen 1">
      <p><strong>Figura 1.a</strong></p>
    </td>
    <td style="text-align: center;">
      <img src="../Imagenes_calibracion/detected_imagen3.jpeg" width="90%" alt="Imagen 2">
      <p><strong>Figura 1.b</strong> </p>
    </td>
  </tr>
  <tr>
    <td style="text-align: center;">
      <img src="../Imagenes_calibracion/detected_imagen6.jpeg" width="90%" alt="Imagen 3">
      <p><strong>Figura 1.c</strong></p>
    </td>
    <td style="text-align: center;">
      <img src="../Imagenes_calibracion/detected_imagen4.jpeg" width="90%" alt="Imagen 4">
      <p><strong>Figura 1.d</strong> </p>
    </td>
  </tr>
</table>

El objetivo de calibrar camara es poder obtener las estimaciones de los parametros que convierte una coordenasa en el mundo real (3D) en una coordenasa de un pixel en la imagen (2D). Para esto se hayo la matriz de la camara y los coeficientes de distorción.

**Tabla 1:** Matriz Intrínseca (K) de la Cámara Calibrada.

$$
\mathbf{K} = \begin{pmatrix}
1540.2 & 0 & 960.5 \\
0 & 1538.9 & 540.3 \\
0 & 0 & 1
\end{pmatrix}
$$

$$
\mathbf{D} = \begin{pmatrix}
-0.125 & 0.04 & 0.001 & 0.0005 & 0
\end{pmatrix}
$$

<p align="center">
    <strong>Ecuación 1:</strong> Coeficientes de Distorsión Radial ($k_1, k_2, k_3$) y Tangencial ($p_1, p_2$).
</p>


## Referencias

GeeksforGeeks. (s.f.). *Camera Calibration with Python OpenCV*. https://www.geeksforgeeks.org/python/camera-calibration-with-python-opencv/