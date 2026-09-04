# BONDIEVAL — Game Design Document

*Basado en el concepto y guión V1, con mejoras propuestas.*

---

## 0. Resumen ejecutivo

**Bondieval** es un juego web casual, vertical, mobile-first, pensado para jugarse en unos 60 segundos y generar el reflejo de "dale, una más" entre un grupo de amigos.

> **Nota (prototipo):** el concepto original proponía 90–105s (ver sección 2). En el prototipo jugable esa duración se acortó a 60s tras probarlo — con 90-105s el ritmo se sentía lento — y la velocidad de avance ahora sube en escalones en vez de mantenerse fija. El resto de esta sección y del documento describe el diseño original; donde el prototipo se desvía, queda anotado en la sección correspondiente.

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

**✅ Implementado en el prototipo (con cambios sobre el original):**
- Duración fija bajada a **60 segundos** (`CFG.DURATION`) — en playtesting, 90-105s se sentía lento.
- La velocidad de avance ya no es fija: sube en **escalones cada 12 segundos** (`CFG.SPEED_STEP_SEC`), de `BASE_SPEED` a `TOP_SPEED`, en vez de mantenerse constante con un único sprint final. Esto también reemplaza la mejora sugerida de "sprint visual en el tramo final" de la sección 12.

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

**✅ Implementado en el prototipo:**
- Botón de **pausa** (⏸️) en el HUD, con overlay para reanudar — no estaba en el alcance original de v1 (sección 16).
- La sensibilidad del arrastre lateral se ajustó a la baja tras playtesting (se sentía demasiado rápida); el eje de profundidad (adelante/atrás) también se corrigió porque el sentido estaba invertido respecto de lo intuitivo (swipe hacia arriba = avanzar).

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

**✅ Implementado en el prototipo:**
- Feedback de color progresivo en la barra de Borrachera según umbral, además del drift de cámara y viñeta (secciones 9 y 14).
- **Barra de progreso hacia la taberna** (🌲 → 🏠) separada de la barra de tiempo genérica — no estaba especificada en el documento original, se agregó para que el jugador tenga una noción clara de "cuánto falta" sin depender de contar segundos mentalmente.
- Esa barra ahora también muestra la **distancia restante en km** (un flavor stat, no física real — ver sección 9 técnica en CLAUDE.md), y el mismo número aparece en el resumen final ("Distancia a la taberna: X.Xkm") si la partida termina antes de llegar.

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

**✅ Implementado en el prototipo:** las abejas ya no quedan fijas flotando en el aire — tienen vuelo errático (drift lateral senoidal, acotado al ancho del camino) para que esquivarlas/atraparlas sea una decisión activa y no solo de timing en profundidad.

**✅ Abeja de rescate (agregada tras feedback):** cuando el jugador queda con **1 sola vida**, puede aparecer una abeja bien diferenciada (verde brillante, con halo), como máximo una a la vez. Matarla de un ataque devuelve 1 vida; si el ataque falla y la abeja llega a picar, es una picadura normal — con 1 vida, eso termina la partida. Genera una decisión real en el momento más tenso de la run, en vez de que la última vida sea solo cuenta regresiva.

---

## 8. Enanos ladrones

- Enemigo principal. Variantes de comportamiento: caminan, bloquean, roban panales, llevan tarros, aparecen en grupo, distintas profundidades.
- Atacables. Derrotarlos → **+1 enemigo**, algunos sueltan miel.
- Si Bondiola llega muy borracho a la taberna, los enanos le roban la miel (parte de la narrativa del final "borracho").

**Mejoras sugeridas:**
- Un enano "carterista" que roba miel ya recolectada por el jugador si se le acerca demasiado sin atacar primero — refuerza que ignorarlos también tiene costo.
- Telegrafiar el ataque de los enanos con una animación clara (0.3–0.5s) para que el combate se sienta justo en celular, donde la precisión táctil es limitada.

**✅ Implementado en el prototipo (variante: telegrafiar el ataque de Bondiola, no el de los enanos):** en testeo con no-gamers, el problema resultó ser el lado opuesto — no quedaba claro *cuándo el propio ataque del jugador* conecta contra abejas o enanos, sobre todo sin arte final que le dé silueta clara a los enemigos. Se agregó un anillo de alcance elíptico que aparece con cada ataque (dorado si conectó, gris si fue al aire) más un toast "💨 al aire" en los fallos, para que el jugador calibre el timing por prueba y error sin necesitar arte definitivo. Telegrafiar también el ataque de los enanos sigue pendiente como mejora sugerida.

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

**✅ Implementado en el prototipo:**
- Multiplicador de puntaje por miel en la zona dulce (30–60%) — `scoredHoney()`.
- **Ataque con más alcance** en la zona dulce (30–60%) — el segundo incentivo que sugería esta sección, no implementado hasta ahora.
- Decaimiento pasivo de borrachera con el tiempo (`DRUNK_DECAY_PER_SEC`).
- Cámara/viñeta progresiva en 60–80% y 80%+ (sección visual ya cubierta).
- Las jarras aparecen **antes** (desde ~t=15/60 en vez de cerca del final) y con **mayor probabilidad** por segmento — en playtesting había muy pocas ocasiones de subir la borrachera a propósito.
- **Feedback visual de la zona dulce**: Bondiola tiene un aura dorada pulsante mientras la borrachera está en 30–60%, y el toast de "+N 🍯" se agranda y cambia de color cuando el pickup cae dentro del bonus de puntaje. Ningún texto explica el porqué — es puro refuerzo visual atado a los mismos umbrales, para que el jugador lo asocie jugando (ver "no explicar nada" en la sección 15).

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

*(Tabla original, pensada para partidas de 90–105s — ver más abajo el guión real del prototipo con `CFG.DURATION = 60`.)*

| Tiempo | Beat |
|---|---|
| 0:00–0:15 | Introducción al movimiento, primeros panales |
| 0:15–0:30 | Primeras abejas, aprendizaje de esquiva |
| 0:30–0:50 | Primer enano ladrón, combate y recompensa |
| 0:50–1:10 | Primeras jarras de hidromiel, cambios visuales de borrachera |
| 1:10–1:25 | Caos y combinaciones de decisiones |
| 1:25–1:45 | Tramo final, mayor intensidad, llegada a la taberna |

**Mejora sugerida:** en el tramo final, agregar un **pequeño "sprint" visual** (aceleración de cámara/escenario) en los últimos 5-8 segundos antes de la taberna, para darle un cierre de tensión física además de la narrativa.

**✅ Guión real del prototipo (60s, reescalado proporcionalmente en `phaseFlags(t)`):**

| Tiempo | Beat |
|---|---|
| 0:00–0:08 | Introducción al movimiento, primeros panales |
| 0:08–0:15 | Primeras abejas, aprendizaje de esquiva |
| 0:15–0:17 | Primeras jarras de hidromiel |
| 0:17–0:41 | Primer enano ladrón, combate y recompensa |
| 0:41–0:50 | Caos y combinaciones de decisiones |
| 0:48–1:00 | Tramo final: mayor intensidad, la taberna se insinúa entre la niebla (nítida desde 0:57) hasta la llegada |

El "sprint" del tramo final ya no es un evento único: la velocidad sube en escalones cada 12s durante toda la partida (sección 2), así que la sensación de aceleración progresiva está presente desde antes, no solo al final.

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

**✅ Implementado en el prototipo:** la taberna (geometría gris placeholder, igual que el resto de las fábricas de objetos) es ahora un landmark fijo al final del recorrido que se insinúa entre la niebla ~12s antes de terminar la partida y queda nítida en los últimos ~3s — la "zanahoria motivadora" del tramo final que antes no existía visualmente, solo como barra de progreso.

---

## 15. Filosofía del juego

Fácil de aprender, difícil de dominar, rápido, divertido aunque se juegue mal, rejugable, gracioso, accesible para no-gamers, ideal para compartir resultados. Sistema simple (Miel/Enemigos/Borrachera) sin estadísticas ni reglas complejas.

**Mejora sugerida:** agregar un séptimo criterio explícito: **"gracioso incluso mirando a otro jugar"** — pensando en el contexto real de uso (grupo de amigos pasándose el celular), la animación de borrachera y los finales deberían dar tanto placer a quien mira como a quien juega.

---

## 16. Alcance de la primera versión

**Incluido en v1:** pantalla vertical, un bosque, Bondiola, movimiento lateral + profundidad, avance automático, ataque, panales, abejas, enanos, jarras de hidromiel, vidas, miel, contador de enemigos, borrachera, partida de 1:30–1:45, taberna, al menos dos finales, pantalla de resultados.

*(En el prototipo, la duración fija quedó en 60s y se sumó un botón de pausa — ver secciones 2 y 4.)*

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

**✅ Etapa de feedback y pulido del prototipo cerrada (2026-09-04).** Los puntos 1 y 3 de esta lista quedan cubiertos: el prototipo de movimiento pasó por varias rondas de ajuste (controles, ritmo, abejas, incentivos de borrachera, abeja de rescate, distancia/taberna, anillo de alcance de ataque), y ya se testeó con 2 personas del grupo de amigos que no son gamers. Su feedback: la distancia en km a la taberna les gustó, y confirmaron que no quedaba claro el timing del ataque contra abejas/enanos — atendido con el anillo de alcance y el feedback de "al aire" (sección 8). Queda pendiente el punto 4 (definir el asset de compartir resultado) antes de pasar a arte final.

1. Prototipo de movimiento (sección 3 y 4) sin arte final, para validar que "atravesar el bosque 90-105 segundos" ya sea divertido — tal como indica el documento original en su cierre.
2. Prototipar el sistema de borrachera de forma aislada (sin enanos ni abejas) para calibrar los umbrales (30/60/80%) antes de integrarlo con el resto.
3. Testear con 2-3 personas del grupo de amigos que **no sean gamers**, ya que es el público que más valida (o invalida) la simplicidad de controles buscada.
4. Definir el asset de "compartir resultado" (sección 11) temprano, porque condiciona parte del diseño de UI de la pantalla de resultados.
