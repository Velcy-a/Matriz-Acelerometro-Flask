# Visualización de sensor X-Y en matriz 4x4 (Flask)

Aplicación web en **Python (Flask)** que lee en tiempo real los ejes X e Y del **acelerómetro de un celular**, enviados por una aplicación Android (APK) a través de un **socket TCP**, y muestra la inclinación del teléfono sobre una **matriz de 4x4** y en vistas individuales horizontal (eje X) y vertical (eje Y).

Proyecto desarrollado por **Jaime Fuentes** para la asignatura *Desarrollo de Software para Hardware* (DCSH01, Evaluación 4.2) — INACAP, 2026.

![Vista de la matriz 4x4](capturas/Matriz.png)

## Funcionamiento

```
Celular (acelerómetro + APK)
        │  TCP, puerto 12345: mensaje "X:<valor>,Y:<valor>"
        ▼
Servidor Flask (app.py): interpreta los valores y renderiza la vista
        │  HTTP, puerto 5000
        ▼
Navegador: la página se actualiza cada 2 segundos
```

1. Cada vez que se carga una página, `app.py` abre una conexión TCP con el celular y recibe un mensaje con el formato `X:<valor>,Y:<valor>`.
2. Los valores se convierten a números y se envían a la plantilla HTML correspondiente.
3. La plantilla (Jinja2) decide qué celda se pinta de verde según el rango en que cae cada eje.
4. La página se recarga automáticamente cada 2 segundos, por lo que al inclinar el celular la celda activa se desplaza siguiendo el movimiento.
5. Si la conexión falla, el error se muestra en consola y la vista usa X = 0, Y = 0 para no detenerse.

## Vistas

| Ruta | Vista | Descripción |
|------|-------|-------------|
| `/` | Matriz 4x4 | Combina ambos ejes y marca la celda donde se encuentra el sensor. |
| `/ejex` | Eje X (horizontal) | Fila de 4 segmentos que muestra solo la inclinación en el eje X. |
| `/ejey` | Eje Y (vertical) | Columna de 4 segmentos que muestra solo la inclinación en el eje Y. |

**Eje X**

![Vista del eje X](capturas/Eje%20X.png)

**Eje Y**

![Vista del eje Y](capturas/Eje%20Y.png)

## Rangos

Cada eje se divide en 4 rangos de 4.5 unidades, lo que forma las 16 celdas de la matriz:

| Segmento | Rango del eje |
|----------|---------------|
| 1 | valor ≥ 4.5 |
| 2 | 0 ≤ valor < 4.5 |
| 3 | −4.5 ≤ valor < 0 |
| 4 | valor < −4.5 |

## Trabajo realizado sobre el ejemplo base

El profesor entregó la APK y una página base que mostraba la posición en **4 cuadrantes (2x2)**. A partir de ella:

- Se amplió la representación a una **matriz de 4x4** con rangos de 4.5 unidades por eje.
- Se crearon las **vistas horizontal (eje X) y vertical (eje Y)**, con navegación entre las tres páginas.
- Se **corrigió un error del código base** en `app.py`: las lecturas de los ejes X e Y llegaban invertidas a las vistas. El problema se encontró revisando el código y se corrigió reasignando los valores antes de enviarlos a las plantillas.
- Por iniciativa propia, se **mejoró el diseño de la página** (colores, descripción de cada vista y botones de navegación).

<details>
<summary>Ver ejemplo base</summary>

![Ejemplo base con 4 cuadrantes](capturas/vista-base.png)

</details>

## Estructura

```
├── app.py              # Servidor Flask: conexión TCP con el sensor y rutas web
├── templates/
│   ├── index.html      # Vista de la matriz 4x4
│   ├── ejex.html       # Vista individual del eje X
│   └── ejey.html       # Vista individual del eje Y
└── capturas/           # Imágenes usadas en este README
```

## Cómo ejecutarlo

**Requisitos:** Python 3, Flask y un celular con la APK del curso (envía los datos del acelerómetro), conectado a la misma red que el computador.

1. Instalar Flask:
   ```bash
   pip install flask
   ```
2. En `app.py`, cambiar `HOST` por la IP del celular (el puerto por defecto es `12345`):
   ```python
   HOST = "192.168.101.4"
   PORT = 12345
   ```
3. Iniciar el servidor:
   ```bash
   python app.py
   ```
4. Abrir en el navegador `http://localhost:5000`, o `http://<IP-del-computador>:5000` desde otro dispositivo de la red.

## Tecnologías

Python · Flask · Jinja2 · Sockets TCP · HTML/CSS · Acelerómetro (Android)
