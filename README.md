# Visión por Computador: Práctica 2. Funciones básicas de OpenCV

## Introducción
El objetivo de la práctica es proporcionar una comprensión sólida y aplicada de las técnicas de procesamiento de imágenes utilizando OpenCV, enfocándose en la detección de características relevantes en imágenes capturadas en tiempo real.

## Paquetes necesarios

Esta práctica está centrada mayormente en el tratamiento de imágenes, por lo que se necesitan los paquetes:
- opencv-python
- matplotlib

Para instalarlos, usar los comandos:

```pip install opencv-python```
```pip install matplotlib```

## Tareas

### Tarea 1: Contar píxeles blancos por filas en la imagen resultado de Canny

En esta tarea, realizaremos el conteo de los píxeles blancos (con valor 255) por filas en la imagen obtenida mediante el operador Canny. Esta operación nos permitirá identificar las filas que contienen una cantidad significativa de píxeles blancos, lo cual es útil para analizar la salida del detector de bordes.

Utilizamos la función cv2.reduce para contar el número de píxeles blancos por cada fila. La suma de los valores de los píxeles por fila se realiza de la siguiente manera:

```row_counts = cv2.reduce(canny, 1, cv2.REDUCE_SUM, dtype=cv2.CV_32SC1)```

Luego, normalizamos el conteo en base al número de columnas de la imagen (que se obtiene del segundo valor devuelto por shape) y al valor máximo de un píxel (255). Esto nos permitirá obtener la proporción de píxeles blancos por fila:

```rows = row_counts[:, 0] / (255 * canny.shape[1])```
```maxFil = max(rows)```

Con el valor máximo de píxeles blancos calculado (maxFil), determinamos cuáles filas tienen un número de píxeles blancos mayor o igual que el 95% de este máximo. Para ello, inicializamos una lista vacía llamada rowsFound y realizamos la comparación, las contamos e imprimimos el resultado.
Para marcar las filas encontradas en la imagen de Canny, utilizamos la función cv2.circle para dibujar un círculo en las posiciones correspondientes.

![{E4F42862-E7D2-4E5C-8874-B48A66CC6794}](https://github.com/user-attachments/assets/36f45d0f-082e-48ed-82b6-ba8627a5d723)

### Tarea 2: Aplicar umbralizado a la imagen resultante de Sobel y realizar conteo de píxeles no nulos

Para llevar a cabo esta tarea, comenzaremos aplicando un umbral a la imagen obtenida a partir del operador Sobel. Este proceso es crucial ya que el umbralizado nos permitirá segmentar los píxeles relevantes, facilitando el conteo posterior. Utilizaremos la función threshold de la librería OpenCV para realizar esta operación.

Primero, definimos el valor del umbral, que en este caso será:

```valorUmbral = 130```

Con este valor, aplicamos el umbralizado a la imagen de Sobel convertida a 8 bits:

```res, imagenUmbralizada = cv2.threshold(sobel8_v1, valorUmbral, 255, cv2.THRESH_BINARY)```

Una vez que hemos obtenido la imagen umbralizada, procederemos a contar los píxeles no nulos por filas y columnas. Utilizaremos la función cv2.reduce para calcular la suma de píxeles en cada fila y columna. El conteo de píxeles no nulos por filas se logra de la siguiente manera:

```row_counts = cv2.reduce(imagenUmbralizada, 1, cv2.REDUCE_SUM, dtype=cv2.CV_32SC1)```
```rows = row_counts[:, 0] / (255 * imagenUmbralizada.shape[1])```
```maxFil = max(rows)```

De forma similar, realizamos el conteo por columnas:

```col_counts = cv2.reduce(imagenUmbralizada, 0, cv2.REDUCE_SUM, dtype=cv2.CV_32SC1)```
```cols = col_counts[0] / (255 * imagenUmbralizada.shape[0])```
```maxCols = max(cols)```

A continuación, identificamos las filas y columnas que superan el 95% del valor máximo de conteo. Para ello, inicializamos dos listas vacías, rowsFound y colsFound, donde almacenaremos las posiciones que cumplen con esta condición:

```rowsFound = []```
```for i in range(len(rows)):```
    ```if rows[i] > 0.95 * maxFil:```
        ```rowsFound.append(i)```

```colsFound = []```
```for i in range(len(cols)):```
    ```if cols[i] > 0.95 * maxCols:```
        ```colsFound.append(i)```

Una vez hecho esto, se imprimiran los resultados:

![{98859FD5-034C-4B99-9357-4475EB21B413}](https://github.com/user-attachments/assets/90676337-679a-4720-aa2c-40d2c83c2914)

### Tarea 3: Demostrador de imágenes de la cámara con funciones de OpenCV
Para esta tarea, se requiere desarrollar un demostrador que capture imágenes de la cámara web y muestre diversas aplicaciones de procesamiento de imágenes utilizando funciones de OpenCV. Este demostrador permitirá exhibir lo aprendido en las prácticas anteriores a personas que no cursen la asignatura.

El procesamiento variará dependiendo del modo activo. Se pueden definir tres modos de la siguiente manera:

Modo 1: Mostrar la imagen original de la cámara sin modificaciones.

Modo 2: Aplicar un mapa de color a la imagen y detectar el píxel más oscuro en cada fila.

```fraem = cv2.applyColorMap(frame, cv2.COLORMAP_JET)```
```gray_frame = cv2.cvtColor(frame, cv2.COLOR_BGR2GRAY)```
```for i in range(0, height, salto_filas):```
    ```min_brightness = np.min(gray_frame[i, :])```
    ```x_min = np.argmin(gray_frame[i, :])```
    ```cv2.circle(fraem, (x_min, i), 1, (255, 255, 255), 1) ```

Modo 3: Filtro al estilo Minecraft

 ```for y in range(0, height, block_size):```
     ```for x in range(0, width, block_size):```
         ```y_end = min(y + block_size, height)```
         ```x_end = min(x + block_size, width)```
         ```block = frame[y:y_end, x:x_end]```
         ```color = block.mean(axis=(0, 1), dtype=int)```
        ```fraem[y:y_end, x:x_end] = color```

Interacción del usuario: Para permitir que el usuario cambie entre modos de visualización, se utiliza cv2.waitKey(20) para capturar las teclas presionadas. Por ejemplo:

- Presionar ESC para salir del bucle.
- Presionar 1, 2 o 3 para cambiar al modo correspondiente.

![{47FB2116-7B67-4554-B7EC-D45C764772B4}](https://github.com/user-attachments/assets/b96cccef-15de-4b2a-902e-940cfc02237a)
![{4CC26566-DEA8-4BB3-BE54-95DAB5C2E0B7}](https://github.com/user-attachments/assets/e589b4fd-7723-4cee-98fd-0ea82cf65aeb)

### Tarea 4: Demostrador de Procesamiento de Imagen

Esta tatrea tomará como base el concepto de interacción visual mediante la detección de objetos en movimiento, similar a las instalaciones mostradas en los vídeos mencionados.

Configuración del eliminador de fondo: Se utilizará el método de sustracción de fondo MOG2 para detectar movimientos:

```eliminadorFondo = cv2.createBackgroundSubtractorMOG2(history=100, varThreshold=50, detectShadows=True)```

Se establecerán varios colores para resaltar los objetos en movimiento:

```colores = [(255, 0, 0), (0, 255, 0), (0, 0, 255), (255, 255, 0), (255, 0, 255), (0, 255, 255)]```

Dentro de un bucle while, se capturarán los fotogramas y se aplicará la sustracción de fondo para identificar las áreas en movimiento. Se procesará de la siguiente manera:

- Captura y volteo del fotograma.
- Aplicación del eliminador de fondo para obtener una imagen binaria que represente el movimiento.
- Detección de contornos de los objetos en movimiento.
- Dibujo de círculos y rectángulos alrededor de los contornos detectados.

Se mostrarán dos ventanas: una con los contornos detectados y otra con los objetos resaltados en el fotograma original:

![{BDACA1C0-FBB5-4270-AF12-7E9C28141D75}](https://github.com/user-attachments/assets/e82356a1-e056-45d5-84ce-bb67398ff7d8)

### Autores

- [@Santiago Santana Martínez](https://github.com/Tiago1615)
- [@Mauro Gómez Guillén](https://github.com/MGGdesigns)

### Referencias Bibliográficas

- [Guión de la práctica](https://github.com/otsedom/otsedom.github.io/blob/main/VC/P2/README.md)
- [My little piece of privacy](https://www.niklasroy.com/project/88/my-little-piece-of-privacy)
- [Messa di voce](https://www.youtube.com/watch?v=GfoqiyB1ndE)
- [Virtual air guitar](https://www.youtube.com/watch?v=FIAmyoEpV5c)
