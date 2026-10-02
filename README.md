# 🛠️ GDD & TECHNICAL SPECIFICATION: "STEALTH GRAFFITI 3D" (VERTICAL SLICE DEMO)

---

## 1. VISIÓN GENERAL Y DIRECCIÓN ARTÍSTICA

- **Título del Proyecto (Provisorio):** *Stealth Graffiti 3D*
- **Género:** Survival Horror / Sigilo Urbano / Acción Táctica en Tercera Persona.
- **Inspiraciones Visuales y Atmósfera:** *Resident Evil* (remakes/clásicos por la cámara y el peso del personaje), *Silent Hill* (niebla densa, opresión, suciedad urbana) y *Alan Wake* (contraste de oscuridad profunda con haces de luz volumétrica).
- **Entorno y Tiempo:** Un callejón industrial/urbano en ruinas a las **3:00 AM**. Lluvia tenue/piso húmedo reflectante, niebla volumétrica, farolas parpadeantes y una autopista elevada al fondo con tráfico distante.
- **Identidad del Protagonista:** 
  - **Atuendo:** Suéter negro con capucha holgada, pantalones tácticos oscuros, calzado urbano silencioso y máscara/pasamontañas oscuro.
  - **Equipamiento Visible:** Mochila de lona desgastada en la espalda.
  - **Física de Sonido Dinámico (Mochila):** La mochila contiene botes de spray sueltos. Al caminar a paso rápido o correr, los botes chocan físicamente entre sí (`SprayCanRattle`), generando un micro-sonido metálico (`clack-clack`) que incrementa el radio de ruido del jugador.

---

## 2. INTERFAZ DE USUARIO Y HUD (INMERSIÓN MÍNIMA)

1. **Mapa Táctico (Esquina Inferior Derecha):**
   - Un plano de papel arrugado / interfaz de radio interceptada en formato cibernético/callejero.
   - Muestra la topografía básica del callejón y la autopista.
   - Revela conos de visión en tiempo real o pulsos de ondas de las patrullas policiales detectadas por frecuencia de radio.

2. **Indicador de Inventario de Spray:**
   - Ubicado en un lateral de la pantalla de forma discreta.
   - Muestra un máximo estricto de **3 latas de spray** (Lata 1: Trazo/Borde, Lata 2: Relleno, Lata 3: Detalle/Luz).

3. **Sin Barra de Vida Tradicional:**
   - La salud/estrés se mide por el ritmo cardíaco (audio) y la viñeta de la pantalla que se oscurece o se llena de grano de película cuando la policía está cerca o persiguiéndote.

---

## 3. ARQUITECTURA DE SISTEMAS Y MECÁNICAS DE JUEGO

### A. Controlador del Jugador en Tercera Persona (`PlayerController.cs` / `player.gd`)
- **Cámara:** En tercera persona, fija sobre el hombro (*Over-The-Shoulder*) con desaceleración suave (*Damping*). Ángulo estrecho para generar claustrofobia.
- **Estados de Movimiento (State Machine):**
  - `IDLE`: Parado. Generación de ruido: **0%**.
  - `CROUCH_WALK` (Sigilo): Movimiento agachado detrás de coberturas. Velocidad reducida un 60%. Generación de ruido: **0%**. Físicas de la mochila silenciadas.
  - `WALK`: Caminar estándar. Velocidad media. Generación de ruido: **20%**. Pequeña probabilidad de tinte sonoro de los botes de spray.
  - `RUN` (Sprint): Correr desesperado. Consume estamina. Generación de ruido: **100%**. Activa sonido fuerte de botes chocando en la mochila (`SprayCanRattle`), alertando a policías en un radio de 15 metros.
  - `CLIMB`: Estado de interacción vertical encadenado a superficies escalables (escaleras de gato).
  - `PAINTING`: Estado congelado/interactivo durante la ejecución del grafiti.

### B. Sistema de Iluminación y Sigilo (`StealthSystem.cs` / `stealth_system.gd`)
- **Detección por Sombras:** El jugador posee un medidor interno de invisibilidad basado en la luz que incide sobre su modelo 3D.
- **Conos de Luz Policial (Light Volumetrics & Raycasting):**
  - Cada linterna o faro de vehículo emite un cono de luz físico (*Spotlight*).
  - Se lanzan Raycasts desde el origen de la luz hacia la posición del jugador.
  - Si el Raycast impacta al jugador y no hay obstáculo (cajas, paredes) interfiriendo:
    - `Unaware` (Normal) ➡️ `Suspicious` (Investiga la última posición vista) ➡️ `Detected` (Alerta general y persecución).

### C. Sistema de Escalada Vertical (`LadderInteraction.cs`)
- La valla publicitaria mide **20 metros de altura** y su soporte central es una columna cilíndrica de acero con una escalera de gato (*Cat Ladder*) adosada.
- **Trigger de Escala:** Al acercarse a la base de la columna, aparece un prompt sutil ("Presiona [E] para subir").
- **Animación:** Movimiento pausado, pesado, peldaño a peldaño. Si el jugador acelera en la escalera, genera ruido metálico.

### D. Sistema de Pintura y Grafiti (`GraffitiMinigame.cs`)
- Al llegar a la pasarela superior de la valla publicitaria, el jugador se posiciona frente al panel frontal/posterior.
- Se activa la cámara de interacción en primera persona / hombro fijo.
- **Minijuego del Spray:**
  - El jugador mantiene presionado el botón primario para pintar.
  - Mecánica de trazado: Dibujar dos letras gigantes con estilo "Bomba / Bubble Letter": **K** y **O**.
  - **Efectos de Sonido Dinámicos:**
    1. Agitar la lata (*Clack-clack-clack* de la balín interno).
    2. Sonido de presión de la válvula de aire/pintura (*Psssshhhh*).
    3. Emisión de partículas de pintura 3D proyectadas en la textura del panel mediante un *Decal System*.
  - **Progreso:** Barra de completado (0% a 100%). Al llegar al 100%, el decal completo del grafiti **"K O"** se fija permanentemente en la valla.

---

## 4. SECUENCIA CINEMÁTICA Y SCRIPTED EVENTS (PASO A PASO DEL DEMO)

INICIO: 03:00 AM]
│
▼
[FASE 1: CALLEJÓN OSCURO]
 Spawn en el fondo del callejón sin salida.
 Iluminación: Sombras densas, charcos con reflejos, cajas de madera apiladas.
 Objetivo: Caminar en sigilo hacia la base de la valla publicitaria.
│
▼
[FASE 2: ASCENSO A LA VALLA]
 Interacción con la escalera del cilindro metálico (20 metros).
 Ascenso vertical pesado hasta la plataforma metálica.
 Vista panorámica: Al fondo se observa una autopista elevada con farolas y tráfico distante.
│
▼
[FASE 3: EJECUCIÓN DEL GRAFITI]
 Iniciar minijuego de pintura.
 Dibujar letras "K O" (Estilo bomba) usando las latas de spray.
 Sonidos de la válvula resonando en el silencio de la noche.
 Evento trigger: Al llegar al 100% de pintado...
│
▼
[FASE 4: LA EMBOSCADA POLICIAL (CINEMÁTICA EN TIEMPO REAL)]
 Audio: Sirena de policía estridente rompe el silencio nocturno.
 Visual: En la autopista elevada al fondo, un patrullero con luces policiales rojas y azules frena de golpe.
 El patrullero derrapa, da la vuelta y baja por la rampa de acceso, bloqueando la entrada del callejón.
 Dos oficiales de policía bajan del coche equipados con linternas de alta potencia.
│
▼
[FASE 5: DESCENSO, SIGILO Y ESCAPE]
 Los policías entran al callejón barriendo las zonas con las linternas.
 El jugador debe bajar rápidamente por la escalera de la valla sin ser detectado por los conos de luz.
 Una vez abajo, debe agacharse (⁠CROUCH_WALK⁠), esconderse detrás de las cajas de madera apiladas.
 El mapa táctico muestra los patrones de patrulla en tiempo real.
 Objetivo final: Evitar la línea de visión de los policías, desplazarse entre las sombras del lateral del callejón y escapar por un conducto o brecha en la cerca trasera.
 
## 5. LÓGICA DE LA INTELIGENCIA ARTIFICIAL POLICIAL (`PoliceAI.cs` / `police_ai.gd`)

La IA de los oficiales de policía funciona mediante una **Máquina de Estados Finitos (FSM - Finite State Machine)** impulsada por dos subsistemas sensoriales: **Vista (Raycasting + Cono de Luz Volumétrico)** y **Oído (Procesador de Ruido Por Radio/Nivel)**.

### A. Subsistemas Sensoriales de la IA
1. **Sistema de Visión Volumétrica (`VisionCone`):**
   - **Cono Primario (Visión Directa):** Ángulo de 45°, alcance de 12 metros. Si el jugador entra en este cono y no hay oclusión (cubierto por cajas o sombras densas), la detección sube inmediatamente al **100%** (Estado: `PURSUIT`).
   - **Cono Secundario (Visión Periférica/Linterna):** Ángulo de 80°, alcance de 18 metros. Aumenta la barra de alerta de la IA progresivamente según la cantidad de luz que reciba el jugador.
   - **Oclusión:** Raycasts lanzados desde los ojos/linterna del oficial hacia la cabeza, torso y pies del jugador. Si los tres Raycasts colisionan con geometría (`Environment` / `CoverBox`), la línea de visión se considera **rompida**.

2. **Sistema de Audición y Ruido (`NoisePerception`):**
   - Escucha los eventos de ruido dentro de un radio determinado:
     - `Crouch-walk`: 0 metros (Inaudible).
     - `Walk`: 3 metros.
     - `Run` + `SprayCanRattle` (sonido de la mochila): 15 metros.
     - `SprayValve` (sonido al pintar la valla): 8 metros.
   - Al detectar un sonido, la IA almacena un vector `NoiseLocation` y pasa automáticamente a investigar.

3. **Sistema de Comunicación por Radio (`RadioSystem`):**
   - Si un oficial entra en estado `PURSUIT`, emite una señal de radio que pone al segundo oficial automáticamente en estado `PURSUIT` o `FLANK` (interceptación táctica).

---

### B. Transiciones de la Máquina de Estados Finitos (FSM)

STATE_PATROL]
│
(Escucha ruido /
brillo sospechoso)
│
▼
[STATE_ALERT] ────────(Ve al jugador claramente)───────► [STATE_PURSUIT]
│                                                         │
(Investiga zona y                                         (Pierde línea de visión
no encuentra nada)                                        por más de 5 segundos)
│                                                         │
▼                                                         ▼
[STATE_PATROL] ◄───────(Búsqueda fallida / 10s)───────── [STATE_SEARCH]
#### Detalle Técnico de Estados de la FSM Policial:

1. **`STATE_PATROL` (Patrulla de Rutina):**
   - **Velocidad de Movimiento:** `1.8 m/s` (Caminar pausado sobre ruta de Waypoints).
   - **Comportamiento:** 
     - La IA recorre un array de puntos `Waypoint[]` predefinidos en el callejón con una pausa de `2.0` segundos en cada nodo.
     - Aplica rotación suavizada a la cabeza y linterna (`Mathf.SmoothDampAngle`) realizando un barrido angular de `-45°` a `+45°` cada `3.5` segundos.
   - **Umbrales de Salida:**
     - Si recibe un evento de sonido `OnNoiseEmitted` con intensidad `> 0.3` dentro del radio de audición ➡️ Transición a `STATE_ALERT`.
     - Si la acumulación de luz en el medidor de visibilidad del jugador supera el `25%` en el cono periférico ➡️ Transición a `STATE_ALERT`.
     - Si la visibilidad del jugador alcanza el `100%` en el cono primario de visión ➡️ Transición directa a `STATE_PURSUIT`.

2. **`STATE_ALERT` (Investigación / Sospecha):**
   - **Velocidad de Movimiento:** `2.5 m/s` (Caminar cauteloso con arma desenfundada o linterna fija).
   - **Comportamiento:**
     - La IA se detiene en seco durante `1.2` segundos, reproduce un sonido de voz (*"¿Hay alguien ahí?"* / *"Revisa esa esquina"*).
     - Fija el objetivo de rotación de la linterna y cabeza hacia las coordenadas vectoriales `NoiseLocation` o `LastSpottedPosition`.
     - Camina en línea recta evaluando la zona sospechosa.
   - **Temporizador y Umbrales de Salida:**
     - **Timer de Sospecha (`SuspicionTimer`):** Duración de `8.0` segundos. Si el temporizador llega a `0` sin volver a escuchar ruidos ni detectar sombras en movimiento, realiza un suspiro/línea de voz de calma (*"Falsa alarma"*) y regresa a `STATE_PATROL`.
     - Si durante la investigación detecta al jugador con acumulación de visibilidad `> 75%` ➡️ Transición inmediata a `STATE_PURSUIT`.

3. **`STATE_PURSUIT` (Persecución / Caza Total):**
   - **Velocidad de Movimiento:** `4.2 m/s` (Sprint policial acelerado).
   - **Comportamiento:**
     - Dispara el evento global `OnPlayerDetected`: La música cambia a una pista industrial agobiante y la interfaz enciende la viñeta de estrés.
     - El vehículo patrullero activa sirenas y luces de emergencia. La linterna del oficial pasa a modo *Strobe* (parpadeo de alta intensidad).
     - La IA recalcula constantemente el camino más corto hacia la posición actual del jugador utilizando el sistema de navegación 3D (`NavMeshAgent`).
     - Emite señal de radio sincrónica a los oficiales secundarios para realizar un movimiento de flanqueo.
   - **Umbrales de Salida:**
     - Si el jugador dobla una esquina o se oculta tras cajas break/coberturas y los tres Raycasts de comprobación de visibilidad colisionan con la geometría del escenario durante más de `5.0` segundos consecutivos ➡️ Transición a `STATE_SEARCH`.

4. **`STATE_SEARCH` (Búsqueda Registrada en Coberturas):**
   - **Velocidad de Movimiento:** `3.0 m/s` (Desplazamiento táctico).
   - **Comportamiento:**
     - La IA corre hacia el último punto exacto donde vio o escuchó al jugador (`LastKnownPosition`).
     - Al llegar a la posición, genera un radio de búsqueda circular de `6.0` metros y selecciona aleatoriamente 3 puntos cercanos de cobertura (`CoverBox` / contenedores de basura).
     - Inspecciona cada cobertura iluminando directamente el interior con la linterna durante `2.5` segundos por punto.
   - **Temporizador y Umbrales de Salida:**
     - **Timer de Búsqueda (`SearchTimer`):** Duración total de `12.0` segundos. Si no logra restablecer la línea de visión con el jugador, la IA baja el estado de alerta a `STATE_ALERT` (investigando con dudas) durante `4.0` segundos adicionales antes de retornar finalmente a `STATE_PATROL`.

---

## 6. ESTRUCTURA COMPLETA DE ARCHIVOS Y MÓDULOS DEL PROYECTO

```text
/Assets (o /Scripts)
│
├── /Core
│   ├── GameManager.cs            # Controlador de estados de flujo del juego (Menu, Gameplay, Pause, GameOver, Victory).
│   ├── AudioManager.cs           # Sistema central de mezcla de sonido, audio dinámico, pasos, latas y sirenas.
│   ├── EventManager.cs           # Bus de eventos desacoplados (Action/Signals) para evitar dependencias directas.
│   └── StealthManager.cs         # Administrador de luz ambiental global y cálculo de visibilidad del jugador.
│
├── /Player
│   ├── PlayerController.cs       # Controlador de movimiento FSM (Idle, CrouchWalk, Walk, Run, Climb, Paint).
│   ├── BackpackAudio.cs          # Cálculo de velocidad física y emisor del micro-sonido metálico 'SprayCanRattle'.
│   ├── PlayerHealthStamina.cs    # Control de la energía al sprintar y nivel de estrés/pánico del jugador.
│   └── PlayerAnimation.cs        # Controlador del Grafo de Animaciones 3D, mezclas (blends) e IK para manos/pies.
│
├── /Environment
│   ├── BillboardLadder.cs        # Punto de interacción, escalada vertical de 20 metros e IK de manos a peldaños.
│   ├── GraffitiSurface.cs        # Detector de superficie de valla, proyección de Decals 3D y minijuego "K O".
│   ├── CoverBox.cs               # Objetos de cobertura física, cajas y contenedores que bloquean el Raycast de la IA.
│   └── TriggerPoliceArrival.cs   # Disparador cinemático que spawnea la patrulla tras completar el grafiti a las 3:00 AM.
│
├── /AI
│   ├── PoliceFSM.cs              # Gestor principal de la Máquina de Estados Finitos (Patrol, Alert, Pursuit, Search).
│   ├── VisionCone.cs             # Generador de cono de luz volumétrico, cálculos angulares y Raycasting de visibilidad.
│   ├── NoisePerception.cs        # Receptor de eventos de ruido en rango (pasos, mochilas, válvulas de spray).
│   └── PatrolPath.cs             # Waypoints, nodos de patrulla y tiempo de espera entre recorridos.
│
└── /UI
    ├── TacticalMap.cs            # Interfaz de mapa táctico arrugado/frecuencia de radio en esquina inferior derecha.
    ├── InventoryHUD.cs           # Renderizado de las 3 latas de spray y estado de selección
7. INSTRUCCIONES DIRECTAS PARA EL AGENTE DE CÓDIGO (PROMPT DE IMPLEMENTACIÓN EXTREMO)
Actúa con el rigor de un Desarrollador Principal de Motores de Videojuegos 3D. Genera el código en arquitectura totalmente modular respetando las siguientes directrices y reglas de oro:
A. Pipeline Prioritario de Construcción (Implementar en este orden estricto):
1. Paso 1 - Controlador de Jugador (⁠PlayerController.cs⁠ + ⁠BackpackAudio.cs⁠):
 Implementar control en 3ª persona sobre el hombro (Over-The-Shoulder).
 Crear las 5 mecánicas de estado (⁠IDLE⁠, ⁠CROUCH_WALK⁠, ⁠WALK⁠, ⁠RUN⁠, ⁠CLIMB⁠).
 Calcular la velocidad del jugador en ⁠BackpackAudio.cs⁠: Si el jugador pasa de ⁠WALK⁠ a ⁠RUN⁠, emitir el evento ⁠OnNoiseEmitted⁠ con radio de ⁠15.0m⁠ reproduciendo el clip sonoro de latas metálicas chocando (⁠SprayCanRattle⁠).
2. Paso 2 - IA Policial (⁠PoliceFSM.cs⁠ + ⁠VisionCone.cs⁠ + ⁠NoisePerception.cs⁠):
 Programar la FSM con los 4 estados descritos (⁠STATE_PATROL⁠, ⁠STATE_ALERT⁠, ⁠STATE_PURSUIT⁠, ⁠STATE_SEARCH⁠).
 Implementar ⁠VisionCone.cs⁠: Proyectar Raycasts hacia las partes clave del jugador (⁠head⁠, ⁠torso⁠, ⁠feet⁠). Si hay una caja (⁠CoverBox⁠) entre el origen y el jugador, ignorar la detección.
3. Paso 3 - Escalera de 20m y Pintura (⁠BillboardLadder.cs⁠ + ⁠GraffitiSurface.cs⁠):
 Crear el trigger de interacción para la escalera del cilindro de la valla. Bloquear la cámara y subir al jugador peldaño a peldaño.
 Crear el minijuego de grafiti: Al mantener presionado el botón primario de acción, dibujar sobre la textura de la valla las letras "K O" estilo bomba mientras se emite el sonido de la válvula y partículas 3D de pintura.
4. Paso 4 - Evento Cinemático de Emboscada (⁠TriggerPoliceArrival.cs⁠):
 Cuando el progreso del grafiti en ⁠GraffitiSurface.cs⁠ alcance el ⁠100%⁠, disparar el evento ⁠OnGraffitiCompleted⁠.
 Reproducir sonido de sirena estridente, instanciar el modelo del patrullero en la autopista elevada al fondo y bajar a 2 oficiales en el callejón iniciando el patrón de patrulla/búsqueda.
5. Paso 5 - Interfaz Táctica (⁠TacticalMap.cs⁠ + ⁠StealthHUD.cs⁠):
 Dibujar la posición de la IA en tiempo real dentro del mapa táctico ubicado en la esquina inferior derecha.
 Conectar la intensidad del cono de luz sobre el jugador con la viñeta de estrés en pantalla.
B. Normas de Calidad de Código y Arquitectura:
 Eventos Desacoplados: Utilizar únicamente ⁠System.Action⁠ / ⁠Delegates⁠ (en C#) o ⁠signals⁠ (en GDScript) para conectar los módulos (ejemplo: ⁠OnNoiseEmitted⁠, ⁠OnPlayerDetected⁠, ⁠OnGraffitiCompleted⁠).
 Cero Métodos Basura en Update: Queda strictly prohibido usar ⁠FindObjectOfType⁠, ⁠GameObject.Find⁠ o ⁠GetComponent⁠ dentro de loops repetitivos como ⁠Update()⁠ o ⁠_process()⁠. Guardar todas las referencias en caché durante el ⁠Awake()⁠ / ⁠Start()⁠.
 Documentación Inline: Comentar cada función principal en español explicando la matemática detrás de los Raycasts, los ángulos de visión y el flujo de estados de la FSM.
