
# Requisitos y Lógica del Juego de Plataformas 2D en AMOS

## 1. Configuración de Pantallas

### Pantalla Principal (Grande)
- **Tamaño**: 640x400 píxeles, 32 colores, modo Lowres.
- **Propósito**: Representa el "mundo completo" del juego, contiene todo el mapa.
- **Nota**: Es más grande que la pantalla visible para permitir el scroll.

### Pantalla Visible
- **Tamaño**: 320x200 píxeles (mitad del ancho y alto de la pantalla principal).
- **Propósito**: Ventana que el jugador ve en pantalla, muestra una porción del mapa grande.
- **Método**: Se desplaza dentro de la pantalla principal usando `Screen Copy`.

## 2. Recursos Gráficos

### Tiles
- **Tamaño**: 16x16 píxeles.
- **Carga**: Desde el archivo `"tiles.abk"`.
- **Propósito**: Representan el terreno (suelo, paredes, árboles, agua, etc.).

### Bob (Jugador)
- **Tamaño**: 16x16 píxeles (coincide con los tiles).
- **Carga**: Desde el archivo `"bob.abk"`.
- **Propósito**: Representa al jugador que se mueve en el mapa.

## 3. Mapa

### Tamaño
- **Propuesta**: 50x25 tiles (800x400 píxeles).
  - Ancho: 50 tiles * 16 = 800 píxeles (mayor que 640 para scroll en X).
  - Alto: 25 tiles * 16 = 400 píxeles (igual a la pantalla principal, ajustable para más scroll en Y).
- **Nota**: Más grande que la pantalla principal para forzar el scroll.

### Estructura
- **Almacenamiento**: Matriz `MAPA(X,Y)` con valores enteros.
  - Ejemplo: 1 = suelo, 2 = pared, 3 = borde.
- **Tamaño visible en tiles**:
  - Ancho: 320/16 = 20 tiles.
  - Alto: 200/16 = 12.5 tiles (redondeado a 13 para cubrir la pantalla).

## 4. Jugador

### Posición Inicial
- **Coordenadas**: (50, 170) píxeles en el mapa.
- **Ubicación**: Cerca del lado izquierdo, altura intermedia.

### Movimiento
- **Tipo**: Plataforma 2D.
- **Dirección principal**: Avanza hacia la derecha.
- **Acciones adicionales**: Subir (árboles, escaleras), bajar (cuevas), caer (agua, pozos).
- **Velocidad**: 8 píxeles por frame (ajustable según pruebas).

### Límites
- **X**: 0 a 784 (800 - 16, tamaño del Bob).
- **Y**: 0 a 384 (400 - 16).

## 5. Cámara y Scroll

### Comportamiento del Scroll
- **Zona sin scroll**:
  - El jugador se mueve libremente sin desplazar la cámara si está en:
    - **X**: Entre 90 y 200 píxeles (rango de 110, centrado aprox. en 145).
    - **Y**: Entre 50 y 140 píxeles (rango de 90, centrado aprox. en 95).
- **Activación del scroll**:
  - **En X**:
    - Si `JUGADORX < 90`, la cámara se mueve a la izquierda.
    - Si `JUGADORX > 200`, la cámara se mueve a la derecha.
  - **En Y**:
    - Si `JUGADORY < 50`, la cámara sube.
    - Si `JUGADORY > 140`, la cámara baja.
- **Velocidad del scroll**: Igual a la del jugador (8 píxeles por frame).

### Límites del Scroll
- **En X**:
  - Mínimo: 0 (inicio del mapa).
  - Máximo: 480 (800 - 320), pero se detiene cuando `JUGADORX - SC = 260` (60 píxeles desde el borde derecho).
- **En Y**:
  - Mínimo: 0 (inicio del mapa).
  - Máximo: 200 (400 - 200), pero se detiene cuando `JUGADORY - SX = 140` (60 píxeles desde el borde inferior).

### Método de Scroll
- **`Screen Display`**: Define la pantalla grande y visible.
- **`Screen Copy`**: Copia una porción de 320x200 desde `(SC, SX)` de la pantalla grande a la pantalla visible.

## 6. Variables Clave

- **`JUGADORX`**:
  - Tipo: Entero.
  - Rango: 0 a 784.
  - Propósito: Posición X del jugador en el mapa (píxeles).
- **`JUGADORY`**:
  - Tipo: Entero.
  - Rango: 0 a 384.
  - Propósito: Posición Y del jugador en el mapa (píxeles).
- **`SC`**:
  - Tipo: Entero.
  - Rango: 0 a 480.
  - Propósito: Posición X de la cámara en el mapa (desplazamiento horizontal).
- **`SX`**:
  - Tipo: Entero.
  - Rango: 0 a 200.
  - Propósito: Posición Y de la cámara en el mapa (desplazamiento vertical).
- **`MAPA(X,Y)`**:
  - Tipo: Matriz de enteros (50x25).
  - Propósito: Almacena los tipos de tiles del mapa.
- **`VELOCIDAD`**:
  - Tipo: Constante (ejemplo: 8).
  - Propósito: Píxeles por frame que se mueve el jugador y la cámara.

## 7. Lógica del Juego

### 1. Inicialización
- Configurar pantalla grande: 800x400, 32 colores, Lowres.
- Configurar pantalla visible: 320x200 dentro de la grande.
- Cargar `"tiles.abk"` y `"bob.abk"`.
- Llenar `MAPA(50,25)` con datos iniciales.
- Establecer `JUGADORX = 50`, `JUGADORY = 170`.
- Establecer `SC = 0`, `SX = 0`.

### 2. Bucle Principal
1. **Entrada del jugador**:
   - Si derecha: `JUGADORX = JUGADORX + VELOCIDAD` (máximo 784).
   - Si izquierda: `JUGADORX = JUGADORX - VELOCIDAD` (mínimo 0).
   - Si arriba: `JUGADORY = JUGADORY - VELOCIDAD` (mínimo 0).
   - Si abajo: `JUGADORY = JUGADORY + VELOCIDAD` (máximo 384).
2. **Actualizar cámara**:
   - **En X**:
     - Si `JUGADORX < 90` y `SC > 0`: `SC = SC - VELOCIDAD`.
     - Si `JUGADORX > 200` y `SC < 480`: `SC = SC + VELOCIDAD`, pero si `JUGADORX - SC > 260`, fijar `SC = JUGADORX - 260`.
   - **En Y**:
     - Si `JUGADORY < 50` y `SX > 0`: `SX = SX - VELOCIDAD`.
     - Si `JUGADORY > 140` y `SX < 200`: `SX = SX + VELOCIDAD`, pero si `JUGADORY - SX > 140`, fijar `SX = JUGADORY - 140`.
3. **Dibujar**:
   - Recorrer `MAPA(X,Y)` y dibujar cada tile en la pantalla grande en `(X*16, Y*16)`.
   - Dibujar al jugador en `(JUGADORX, JUGADORY)` en la pantalla grande.
   - Copiar 320x200 desde `(SC, SX)` de la pantalla grande a `(0, 0)` de la pantalla visible.
4. **Actualizar pantalla**:
   - Usar `Bob Update` y `Wait Vbl` para refrescar sin parpadeos.

### 3. Condiciones Especiales
- **Final en X**: Si `JUGADORX = 740`, `SC = 480`, dejando 60 píxeles visibles (`JUGADORX - SC = 260`).
- **Final en Y**: Si `JUGADORY = 340`, `SX = 200`, dejando 60 píxeles visibles (`JUGADORY - SX = 140`).

---

### Instrucciones para PDF
Si quieres convertir esto a PDF:
1. Copia el texto de arriba en un archivo con extensión `.md` (ejemplo: `logica_juego.md`).
2. Abre el archivo en un editor como Visual Studio Code con la extensión Markdown instalada, o usa una herramienta online como [StackEdit](https://stackedit.io/).
3. Exporta a PDF desde el editor (en VS Code: "Markdown: Export to PDF").

Si prefieres que te lo entregue ya como PDF, no tengo la capacidad de generar archivos directamente, pero el Markdown es súper portable y fácil de convertir. Dime si necesitas ayuda con eso.

¿Qué te parece? ¿Seguimos con el pseudocódigo después de que revises esto con tu dibujo? ¡Estoy listo para el siguiente paso cuando tú lo estés!
