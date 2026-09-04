# BONDIEVAL — Game Design Document

*Basado en el concepto y guión V1, con mejoras propuestas.*

---

## 0. Resumen ejecutivo

**Bondieval** es un juego web casual, vertical, mobile-first, pensado para jugarse en 90–105 segundos y generar el reflejo de "dale, una más" entre un grupo de amigos.

El protagonista, **Bondiola**, avanza automáticamente por un bosque medieval recolectando miel, esquivando o enfrentando abejas y enanos ladrones, mientras lucha (literalmente) contra su propia debilidad por la hidromiel. El corazón del diseño es un sistema de tres variables interdependientes — **Miel 🍯 / Enemigos ⚔️ / Borrachera 🍺** — cuya interacción no se explica, sino que se descubre jugando.

---

## 1. Concepto general

- Juego web casual, **modo vertical**, para celular.
- Público objetivo: grupo de amigos + personas que normalmente no juegan (no-gamers).
- Premisa: Bondiola atraviesa el bosque, junta toda la miel que puede y llega a su taberna, esquivando abejas y enanos ladrones, mientras lidia con su afición a la hidromiel.
- La dificultad no está en los controles, sino en **descubrir la interacción** entre miel, enemigos y borrachera para lograr el mejor puntaje.

**Mejora sugerida:** definir una frase de "elevator pitch" fija para usar en cualquier material de difusión (grupo de WhatsApp, landing, etc.):
> *"Corré por el bosque, juntá miel, no te dejes robar... y ojo con la hidromiel."*

---

## 2. Duración

- Partida fija de **1:30 a 1:45 min**.
- Avance automático; el jugador controla posición, no velocidad.
- Duración pensada para generar rejugabilidad inmediata.

**Mejora sugerida:** considerar un **modo "Endless" opcional** desbloqueable después de X partidas, solo para los jugadores más metidos — sin tocar el modo principal, que debe seguir siendo corto y fijo.

---

## 3. Jugabilidad

- Cámara detrás y ligeramente elevada, mirando hacia adelante (cámara tipo "endless runner" en 3ª persona).
- El personaje avanza; el escenario se desplaza para dar sensación de movimiento.
- Dos ejes de control: **lateral** (izquierda/derecha) y **profundidad** (adelante/atrás dentro de una superficie, no carriles fijos).
- Los objetos (panales, abejas, enanos, jarras) se distribuyen en esa superficie 2D de juego, obligando a priorizar posicionamiento.

**Mejora sugerida — curva de ancho de camino:** que el ancho jugable de la superficie varíe a lo largo del recorrido (se angosta en tramos de tensión, se abre en tramos de recolección) para reforzar el ritmo sin cambiar reglas.

---

## 4. Controles

- **Arrastrar el dedo** → mueve a Bondiola (lateral y profundidad).
- **Tocar sobre Bondiola** → ataque.
- Sin joystick virtual en la v1.
- Solo dos verbos: **moverse + atacar**.

**Mejoras sugeridas:**
- Agregar un **swipe rápido = esquiva/dash corto** (mismo gesto de arrastrar pero con velocidad, sin agregar un botón nuevo) para dar más expresividad sin sumar complejidad de UI.
- Zona muerta (dead zone) alrededor del dedo para evitar micro-temblores al recolectar objetos pegados.
- Considerar **auto-ataque leve** al pasar muy cerca de un enemigo débil, para que jugadores torpes no se frustren (activable/desactivable en dificultad).

---

## 5. Interfaz

Variables visibles en pantalla durante la partida:

| Variable | Símbolo | Rol |
|---|---|---|
| Miel | 🍯 | Puntaje |
| Enemigos derrotados | ⚔️ | Puntaje |
| Borrachera | 🍺 | Puntaje + mecánica de riesgo |
| Vida | ❤️ | No forma parte del puntaje directamente |

Interfaz grande, clara, legible en celular.

**Mejora sugerida:** la barra de Borrachera debería tener **feedback visual progresivo** (color, vibración del ícono, leve distorsión de cámara) para que el jugador *sienta* el estado sin leer el número — reforzando el descubrimiento orgánico en vez de depender del HUD.

---

## 6. Miel

- Panales a lo largo del camino → **+1 miel** al recolectar.
- Componente principal del puntaje.
- Ubicación estratégica: algunos panales están en zonas peligrosas (cerca de abejas, en profundidad).

**Mejora sugerida:** variar el "peso" de los panales (algunos dan +1, otros +3 pero están más expuestos) para intensificar la decisión riesgo/recompensa sin agregar una mecánica nueva.

---

## 7. Abejas

- Protegen los panales.
- Se pueden esquivar o enfrentar.
- Picadura: **−1 vida, −20% borrachera** (el segundo efecto no se explica).
- Esto las convierte en una herramienta estratégica (bajar la borrachera a propósito).

**Mejora sugerida:** enjambres pequeños (2-3 abejas en formación) en tramos avanzados, para que el jugador decida entre "una picadura controlada" vs. "arriesgarse a varias".

---

## 8. Enanos ladrones

- Enemigo principal. Variantes de comportamiento: caminan, bloquean, roban panales, llevan tarros, aparecen en grupo, distintas profundidades.
- Atacables. Derrotarlos → **+1 enemigo**, algunos sueltan miel.
- Si Bondiola llega muy borracho a la taberna, los enanos le roban la miel (parte de la narrativa del final "borracho").

**Mejoras sugeridas:**
- Un enano "carterista" que roba miel ya recolectada por el jugador si se le acerca demasiado sin atacar primero — refuerza que ignorarlos también tiene costo.
- Telegrafiar el ataque de los enanos con una animación clara (0.3–0.5s) para que el combate se sienta justo en celular, donde la precisión táctil es limitada.

---

## 9. Hidromiel y borrachera

- Jarras de hidromiel → **+20% borrachera**.
- No es un simple bonus: tiene efectos positivos y negativos según el nivel.
  - Baja: funcionamiento normal.
  - Media: beneficios.
  - Alta: pérdida de control, comportamiento impredecible.
  - 80%+: consecuencias importantes (afecta el final).

**Mejoras sugeridas (para que la curva "más no siempre es mejor" se sienta bien):**
- **Borrachera media (30–60%):** multiplicador leve de puntaje por miel, o ataque más fuerte — el "sweet spot" que el jugador debe aprender a buscar.
- **Borrachera alta (60–80%):** cámara y controles empiezan a "resbalar" (input con inercia/drift), mezclando riesgo con comedia — refuerza la mecánica sin texto explicativo.
- **80%+:** además de afectar el final, reducir visión periférica (viñeta) para que cueste más esquivar, cerrando el loop de riesgo/recompensa de forma justa.
- Dejar que la borrachera **decaiga lentamente con el tiempo** (no solo con picaduras de abeja), para que el jugador tenga otra palanca de control sutil.

---

## 10. La estrategia oculta

Curva de descubrimiento esperada a través de partidas sucesivas: de "juntar toda la miel" → "la borrachera me arruinó" → "un poco de borrachera ayuda" → "las abejas sirven para bajarla" → dominio del sistema.

**Mejora sugerida:** para reforzar esto sin romper el "no explicar nada", usar **mensajes post-partida sutiles y no didácticos** (ej. "Llegaste con 92% de borrachera..." con tono de comentario, no de tutorial) que inviten a inferir sin spoilear la fórmula.

---

## 11. Puntaje

- Basado en Miel, Enemigos y Borrachera.
- Fórmula exacta oculta a propósito, para incentivar la experimentación.
- Pantalla final tipo: `🍯 57 | ⚔️ 18 | 🍺 73% → 🏆 9.842 PUNTOS`

**Mejoras sugeridas:**
- **Compartir resultado como imagen** (captura estilizada tipo "Wordle") pensada para mandar directo al grupo de amigos — encaja perfecto con el objetivo social del juego.
- Guardar **mejor puntaje local** (sin necesidad de cuentas ni backend en v1) para dar sensación de progreso entre partidas.
- Mostrar, además del puntaje, un **"apodo" cómico del resultado** (ej. "Recolector Sobrio", "Héroe Ebrio", "Presa Fácil") calculado según la combinación de las tres variables — refuerza la identidad graciosa del juego sin agregar sistemas.

---

## 12. Guión de una partida (timeline)

| Tiempo | Beat |
|---|---|
| 0:00–0:15 | Introducción al movimiento, primeros panales |
| 0:15–0:30 | Primeras abejas, aprendizaje de esquiva |
| 0:30–0:50 | Primer enano ladrón, combate y recompensa |
| 0:50–1:10 | Primeras jarras de hidromiel, cambios visuales de borrachera |
| 1:10–1:25 | Caos y combinaciones de decisiones |
| 1:25–1:45 | Tramo final, mayor intensidad, llegada a la taberna |

**Mejora sugerida:** en el tramo final (1:25–1:45), agregar un **pequeño "sprint" visual** (aceleración de cámara/escenario) en los últimos 5-8 segundos antes de la taberna, para darle un cierre de tensión física además de la narrativa.

---

## 13. Finales

**Final normal:** borrachera controlada, Bondiola guarda la miel. *"Un día productivo."*

**Final borracho (≥80% borrachera aprox.):** Bondiola se desploma, los enanos intentan robar, aparecen osos que espantan a los enanos pero se llevan la miel igual. *"La próxima vez, menos hidromiel."*

**Mejoras sugeridas:**
- Un **tercer final "heroico"** para combinaciones extremas (ej. muchísimos enanos derrotados + borrachera muy baja): Bondiola llega sobrio, con la ropa destrozada, y los parroquianos lo aclaman — recompensa visual para el estilo de juego "combativo".
- Un **final "fantasma"** de muerte (si se queda sin vidas antes de llegar): tono cómico, no punitivo — mantiene el juego "divertido aunque se juegue mal".

---

## 14. Dirección visual

- 3D caricaturesca, medieval, humorística — no realista.
- Bosque con árboles, vegetación, piedras, troncos, animaciones ambientales, camino con profundidad real.
- La perspectiva es parte de la mecánica, no solo estética.

**Mejoras sugeridas:**
- Paleta de color diferenciada por tramo (mañana → atardecer, por ejemplo) para reforzar la sensación de progreso en un recorrido corto.
- Silueta y siluetas de color claramente distinguibles para cada tipo de objeto/enemigo (legibilidad instantánea en pantalla chica es crítico para un juego táctil rápido).
- Reservar presupuesto de detalle visual para **Bondiola mismo**: su animación de caminata/ataque cambiando con el nivel de borrachera es probablemente el mayor generador de risas y de video para compartir.

---

## 15. Filosofía del juego

Fácil de aprender, difícil de dominar, rápido, divertido aunque se juegue mal, rejugable, gracioso, accesible para no-gamers, ideal para compartir resultados. Sistema simple (Miel/Enemigos/Borrachera) sin estadísticas ni reglas complejas.

**Mejora sugerida:** agregar un séptimo criterio explícito: **"gracioso incluso mirando a otro jugar"** — pensando en el contexto real de uso (grupo de amigos pasándose el celular), la animación de borrachera y los finales deberían dar tanto placer a quien mira como a quien juega.

---

## 16. Alcance de la primera versión

**Incluido en v1:** pantalla vertical, un bosque, Bondiola, movimiento lateral + profundidad, avance automático, ataque, panales, abejas, enanos, jarras de hidromiel, vidas, miel, contador de enemigos, borrachera, partida de 1:30–1:45, taberna, al menos dos finales, pantalla de resultados.

**Fuera de v1:** múltiples niveles, inventario, mejoras permanentes, multijugador, cuentas de usuario, ranking online, gran variedad de enemigos, historia extensa.

**Mejoras sugeridas de alcance (bajo costo, alto impacto social):**
- ✅ Compartir resultado como imagen (ver sección 11) — bajo costo técnico, encaja perfecto con "grupo de amigos".
- ✅ Mejor puntaje local (localStorage) — trivial de implementar, buen enganche.
- ⏸️ Dejar explícitamente fuera del v1 (aunque tentador): sonido con voces originales/doblaje — consume mucho tiempo de producción para una v1 de validación. Priorizar SFX simples + música de ambiente.

---

## 17. Consideración técnica: ¿Three.js es la elección correcta?

**Sí, Three.js es una buena elección** para este proyecto, y la recomiendo mantener, por estas razones:

- El diseño depende de **profundidad real como mecánica** (sección 3 y 14), no solo estética — esto es exactamente el caso de uso donde un motor 3D real gana claramente contra un enfoque 2D con trucos de perspectiva (parallax, sprites escalados).
- Three.js corre bien en WebGL sobre navegadores móviles modernos, y el estilo "caricaturesco, no realista" que se busca (sección 14) permite usar geometría de baja densidad de polígonos y texturas simples — esto ayuda muchísimo al rendimiento en celulares de gama media, que probablemente sea el hardware real del grupo de amigos.
- Es gratuito, tiene comunidad enorme, y es directamente compatible con el flujo de trabajo de "juego web casual" sin necesidad de instalar nada (share por link).

**Alternativas y cuándo tendrían sentido (para que quede documentado el criterio, no porque las recomiende sobre Three.js):**

| Alternativa | Cuándo tendría sentido en vez de Three.js |
|---|---|
| **Babylon.js** | Si se quisiera un motor más "todo incluido" (físicas, editor visual) a costa de un bundle más pesado — no imprescindible para el alcance de v1. |
| **PlayCanvas** | Si se quisiera un editor visual colaborativo tipo Unity-en-el-browser — útil si sumás gente sin experiencia en código, pero agrega dependencia de su plataforma. |
| **Unity WebGL / Godot HTML5** | Si el equipo ya tuviera experiencia previa en esos motores — pero el build de salida para web suele ser más pesado y lento de cargar que Three.js puro, lo cual choca con el objetivo de "juego casual, compartible por link, para no-gamers". |
| **2D puro (Phaser, Pixi.js)** | Solo si se decidiera sacrificar la mecánica de profundidad real y volver a "carriles". No recomendado, porque la sección 3 del documento original es explícita en que la profundidad **es** la mecánica, no un adorno. |

**Recomendación técnica adicional:** dado que es "para un grupo de amigos", conviene optimizar desde el día uno para **tiempo de carga bajo** (assets comprimidos, lazy load de texturas, `draco` compression en los modelos) — nada mata más rápido un juego casual compartido por WhatsApp que una carga de 15 segundos antes de la primera partida.

---

## 18. Próximos pasos sugeridos

1. Prototipo de movimiento (sección 3 y 4) sin arte final, para validar que "atravesar el bosque 90-105 segundos" ya sea divertido — tal como indica el documento original en su cierre.
2. Prototipar el sistema de borrachera de forma aislada (sin enanos ni abejas) para calibrar los umbrales (30/60/80%) antes de integrarlo con el resto.
3. Testear con 2-3 personas del grupo de amigos que **no sean gamers**, ya que es el público que más valida (o invalida) la simplicidad de controles buscada.
4. Definir el asset de "compartir resultado" (sección 11) temprano, porque condiciona parte del diseño de UI de la pantalla de resultados.
