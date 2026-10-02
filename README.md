# Project: Stealth Graffiti 3D (Survival Horror Style)

## Visión General del Proyecto
Juego de sigilo urbano en 3D en tercera persona que combina la tensión y atmósfera de un Resident Evil / Silent Hill con una temática clandestina de graffiti y evasión policial.

## Módulos y Sistemas Principales para Desarrollar

### 1. SISTEMA DE MOVIMIENTO Y CÁMARA (Third-Person Controller)
- Cámara fija o de seguimiento en tercera persona con peso y momento (estilo survival horror).
- Controles de movimiento: caminar, correr (genera ruido), agacharse (sigilo, reduce velocidad y ruido), y escalar vallas u obstáculos bajos.

### 2. SISTEMA DE ILUMINACIÓN Y SIGILO (Stealth & Light Detection)
- Sistema de visibilidad basado en la oscuridad ambiental y linternas de la policía.
- Conos de visión volumétricos para los enemigos (Raycasting o detección por ángulo/distancia).
- Medidor de ruido y luz que afecta la detección del jugador. Si el jugador usa el spray o corre en zona iluminada, es detectado instantáneamente.

### 3. MECÁNICA DE GRAFFITI (Core Loop)
- Al interactuar con una pared designada, se abre un estado de interacción/minijuego.
- El jugador debe mantener presionado un botón; esto genera un sonido gradual ("clack-clack" del spray y válvula) que aumenta el radio de alerta de los policías cercanos.
- Barra de progreso visual. Al completarse, se proyecta un decal/textura de graffiti en la pared.

### 4. INTELIGENCIA ARTIFICIAL POLICIAL (Patrulla y Persecución)
- Máquina de Estados Finitos (FSM) para la policía: Patrulla (rutas fijas por el mapa), Alerta (investigan ruidos o destellos de luz), Persecución (si ven al jugador, lo persiguen) y Búsqueda (si el jugador rompe la línea de vista, buscan en el último lugar visto).
- Sincronización con un sistema de mapa táctico.

### 5. MAPA TÁCTICO / HUD
- Una interfaz de mapa que muestra la posición estimada o en tiempo real de las rondas policiales según la progresión del jugador (zonas desbloqueadas por información o radio).
