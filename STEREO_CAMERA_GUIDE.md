# 🎥 Guía de Cámara Estéreo - Celeris WebGPU

## 📋 Resumen
Este proyecto ha sido modificado para mostrar **dos vistas simultáneas** de la simulación desde ángulos ópticos diferentes, ideal para experimentación con visión estéreo.

---

## 🖥️ Interfaz de Usuario

### Ubicación de los Controles
Los controles de cámara estéreo se encuentran en la sección **"Modify Visualization"** del panel lateral izquierdo, cerca del final de esa sección.

### Controles Disponibles

#### **Camera 1 (Vista Izquierda)**
- **Camera 1 Yaw - Left View (degrees):** Rotación horizontal (ángulo en el plano XY)
  - Rango típico: -180° a 180°
  - **Default: -30°** (mirando hacia la izquierda - GROUND LEVEL)
  
- **Camera 1 Pitch - Vertical angle (degrees):** Rotación vertical (ángulo en el plano XZ)
  - Rango típico: -90° a 90°
  - **Default: 5°** (ligeramente hacia arriba desde el horizonte)

#### **Camera 2 (Vista Derecha)**
- **Camera 2 Yaw - Right View (degrees):** Rotación horizontal de la segunda cámara
  - Rango típico: -180° a 180°
  - **Default: 30°** (mirando hacia la derecha - efecto estéreo amplio)
  
- **Camera 2 Pitch - Vertical angle (degrees):** Rotación vertical de la segunda cámara
  - Rango típico: -90° a 90°
  - **Default: 5°** (ligeramente hacia arriba desde el horizonte)

#### **Separación Estéreo**
- **Horizontal offset for Camera 2 (meters):** Desplazamiento horizontal entre cámaras
  - Baseline separation (separación entre las "lentes")
  - **Default: 2.0 metros** (exagerado para efecto dramático)
  - Valores típicos para estéreo humano: 0.065m
  - Valores típicos para análisis: 0.5 a 5.0 metros

#### **Altura de Cámara**
- **renderEyeHeight:** Altura de la cámara sobre el suelo
  - **Default: 1.5 metros** (altura de ojos humanos al nivel del suelo)

---

## 🔧 Ubicaciones Técnicas en el Código

### 1. **Variables de Configuración** 
📁 **Archivo:** `js/constants_load_calc.js`  
📍 **Líneas:** ~262-268

```javascript
// ★★★ STEREO CAMERA PARAMETERS ★★★
// GROUND-LEVEL CAMERA CONFIGURATION (like security camera or pedestrian view)
camera1_yaw: -30.0,    // Camera 1 yaw angle - looking left
camera1_pitch: 5.0,    // Camera 1 pitch angle - slightly up from horizon
camera2_yaw: 30.0,     // Camera 2 yaw angle - looking right
camera2_pitch: 5.0,    // Camera 2 pitch angle - slightly up from horizon
stereo_baseline: 2.0,  // Horizontal separation (2m = wide stereo effect)
renderEyeHeight: 1.5,  // Camera height above ground (1.5m = human eye level)
```

**Para cambiar valores por defecto:** Edita estos valores directamente en el archivo.

---

### 2. **Renderizado de Cámara 1**
📁 **Archivo:** `js/main.js`  
📍 **Líneas:** ~1948-1976

```javascript
// ★ Use Camera 1 angles for first render
const activeYaw = calc_constants.camera1_yaw * Math.PI / 180;
const activePitch = calc_constants.camera1_pitch * Math.PI / 180;
```

**Uso:** Estos valores controlan la orientación de la primera cámara en el modo 3D (viewType == 2).

---

### 3. **Renderizado de Cámara 2**
📁 **Archivo:** `js/main.js`  
📍 **Líneas:** ~2230-2280

```javascript
// ★ Use Camera 2 angles for second render
const camera2Yaw = calc_constants.camera2_yaw * Math.PI / 180;
const camera2Pitch = calc_constants.camera2_pitch * Math.PI / 180;

// Calculate Camera 2 position with stereo baseline offset
const eye2 = vec3.clone(camState.position);
// ★ Apply horizontal offset (stereo baseline) along the right vector
vec3.scaleAndAdd(eye2, eye2, right2, calc_constants.stereo_baseline);
```

**Uso:** Controla la segunda vista con su propio ángulo y posición offset.

---

### 4. **Event Listeners**
📁 **Archivo:** `js/main.js`  
📍 **Líneas:** ~3083-3088

```javascript
// ★ Stereo camera controls
{ id: 'camera1_yaw-button', input: 'camera1_yaw-input', property: 'camera1_yaw' },
{ id: 'camera1_pitch-button', input: 'camera1_pitch-input', property: 'camera1_pitch' },
{ id: 'camera2_yaw-button', input: 'camera2_yaw-input', property: 'camera2_yaw' },
{ id: 'camera2_pitch-button', input: 'camera2_pitch-input', property: 'camera2_pitch' },
{ id: 'stereo_baseline-button', input: 'stereo_baseline-input', property: 'stereo_baseline' },
```

**Uso:** Conecta los controles HTML con las variables internas.

---

### 5. **Controles HTML**
📁 **Archivo:** `index.html`  
📍 **Líneas:** ~489-540

```html
<!-- Camera 1 Angles -->
<input type="number" id="camera1_yaw-input" name="camera1_yawValue" value="0">
<input type="number" id="camera1_pitch-input" name="camera1_pitchValue" value="0">

<!-- Camera 2 Angles -->
<input type="number" id="camera2_yaw-input" name="camera2_yawValue" value="0">
<input type="number" id="camera2_pitch-input" name="camera2_pitchValue" value="0">

<!-- Stereo Baseline -->
<input type="number" id="stereo_baseline-input" name="stereo_baselineValue" value="0">
```

**Uso:** Interfaz visual para ajustar parámetros en tiempo real.

---

## 🎮 Cómo Usar

### Modo Básico (Ángulos Diferentes)
1. Carga una simulación (ejemplo o custom)
2. Cambia a **3D Explorer Mode** (viewType = 2)
3. Ajusta **Camera 1 Yaw** (ej: 0°)
4. Ajusta **Camera 2 Yaw** (ej: 10°)
5. Observa las dos vistas con ángulos diferentes

### Modo Estéreo (Posiciones Diferentes)
1. Mantén ambas cámaras con el mismo Yaw y Pitch
2. Ajusta **Horizontal offset** (ej: 1.0 metros)
3. Las cámaras están separadas horizontalmente como ojos humanos
4. Útil para análisis de profundidad

### Ejemplos de Configuraciones

#### **Ground-Level Stereo (DEFAULT)** - Vista a nivel del suelo
```
Camera 1: Yaw = -30°, Pitch = 5°, Offset = 0m
Camera 2: Yaw = 30°, Pitch = 5°, Offset = 2.0m
Height: 1.5m
USO: Simula dos cámaras de seguridad mirando hacia lados opuestos
```

#### **Wide Angle Security** - Vigilancia de área amplia
```
Camera 1: Yaw = -45°, Pitch = 0°, Offset = 0m
Camera 2: Yaw = 45°, Pitch = 0°, Offset = 0m
Height: 2.5m
USO: Cobertura de 90° de área
```

#### **Forward Stereo** - Vista frontal estéreo (como visión humana)
```
Camera 1: Yaw = -5°, Pitch = 10°, Offset = 0m
Camera 2: Yaw = 5°, Pitch = 10°, Offset = 0.065m
Height: 1.7m
USO: Emula visión binocular humana
```

#### **Satellite vs Ground** - Comparación aérea/terrestre
```
Camera 1: Yaw = 0°, Pitch = -85°, Offset = 0m (vista cenital)
Camera 2: Yaw = 0°, Pitch = 10°, Offset = 0m (vista terrestre)
Height: Variable
USO: Comparar perspectivas aérea y terrestre simultáneamente
```

---

## 🐛 Notas Importantes

1. **Las dos vistas solo aparecen en modo 3D (Explorer Mode)**, no en el modo 2D de diseño.

2. **Ángulos y coordenadas:**
   - **Yaw (XY):** 0° = mirando al Este, 90° = Norte, -90° = Sur, 180° = Oeste
   - **Pitch (XZ):** 0° = horizontal, 90° = mirando arriba, -90° = mirando abajo

3. **Baseline:** 
   - Valores pequeños (0.1-0.5m): sutiles diferencias
   - Valores medianos (0.5-2.0m): efecto estéreo humano
   - Valores grandes (>2.0m): exagerado, útil para análisis

4. **Rendimiento:** El renderizado dual consume aproximadamente el doble de GPU. Para simulaciones grandes, puede reducir el framerate.

---

## 🔬 Para Experimentación Avanzada

### Modificar directamente en JavaScript
Para experimentos rápidos sin usar la UI:

```javascript
// En la consola del navegador:
calc_constants.camera1_yaw = 0;
calc_constants.camera1_pitch = 15;
calc_constants.camera2_yaw = 10;
calc_constants.camera2_pitch = 15;
calc_constants.stereo_baseline = 1.0;
```

### Animaciones de cámara
Para crear animaciones que cambien los ángulos automáticamente, modifica la función `frame()` en `main.js` alrededor de la línea 1320.

---

## 📊 Sistema de Coordenadas

```
      Z (arriba)
      |
      |
      |_______ Y (Norte)
     /
    /
   X (Este)
```

- **Yaw = 0°:** Cámara apunta hacia +X (Este)
- **Yaw = 90°:** Cámara apunta hacia +Y (Norte)
- **Pitch = 0°:** Cámara mira horizontalmente
- **Pitch = 90°:** Cámara mira hacia arriba (+Z)

---

## 📝 Log de Cambios

### Archivos Modificados:
1. `index.html` - Agregado segundo canvas y controles UI
2. `js/main.js` - Implementado renderizado dual con cámaras independientes
3. `js/constants_load_calc.js` - Agregadas variables de configuración estéreo

### Marcadores en el Código:
Busca `★` en los archivos para encontrar rápidamente todas las modificaciones relacionadas con estéreo.

---

¡Disfruta experimentando con la visualización estéreo! 🎉

