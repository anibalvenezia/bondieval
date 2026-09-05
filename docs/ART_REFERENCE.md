# Referencia técnica para arte final

Este documento existe para que quien haga el arte final (o un asistente de IA
generando modelos/texturas) tenga los datos exactos del prototipo actual sin
tener que leer `index.html` entero. Cubre: qué geometría placeholder hay que
reemplazar, con qué dimensiones/colores/pivotes, y qué comportamientos de
gameplay dependen de esa geometría (para no romper nada al reemplazarla).

No es un documento de dirección artística — para eso ver la sección 14
("Dirección visual") de `GAME_DESIGN.md`. Esto es el mapa técnico pieza por
pieza.

---

## 0. Cómo está armado hoy (y qué implica para el reemplazo)

- Todo el 3D es geometría primitiva de Three.js (cajas, esferas, cilindros,
  conos, toroides) hecha a mano en `index.html`, sin modelos externos ni
  texturas — código puro, cero assets binarios en el repo.
- **No hay loader de modelos todavía.** Si el arte final va a ser GLTF/GLB (lo
  más razonable con Three.js), hace falta sumar `GLTFLoader` desde el mismo
  CDN de `cdnjs`/`unpkg` que ya se usa para Three.js r128, y cargar los
  modelos de forma asíncrona antes de `boot()`. Esto es un cambio de código,
  no solo de arte — avisar antes de tirar los assets finales para coordinarlo.
- **El pivote/origen de cada objeto importa para el gameplay.** Las
  colisiones y el posicionamiento usan `group.position` (el pivote del
  `THREE.Group`), no el centro visual de la geometría. Un modelo final debe
  mantener el mismo punto de apoyo en el piso (y=0 en la base) que el
  placeholder que reemplaza, o hay que ajustar los offsets en el código.
- **Ningún objeto tiene textura ni UV.** Todo es color plano
  (`MeshLambertMaterial`/`MeshBasicMaterial` con `color:` sólido, algunos con
  `emissive`). Está pensado para low-poly cartoon (ver sección 14 del GDD),
  no para PBR realista.
- Todas las medidas están en "unidades de mundo" de Three.js (adimensionales,
  pero el jugador mide ~1.7 unidades de alto — usar eso como escala de
  referencia).

---

## 1. Escena, cámara y niebla

| Parámetro | Valor |
|---|---|
| Color de fondo / niebla | `#bfe0ea` (celeste día) |
| Niebla | lineal, desde 22 hasta 62 unidades de distancia a cámara |
| Cámara | perspectiva, FOV 58°, `near=0.1`, `far=200`, fija en `z = CFG.CAMERA_Z = 5.4` |
| Luz ambiental | `HemisphereLight(0xfff3d6 cielo, 0x3a4a2e suelo, intensidad 0.95)` |
| Luz direccional | `DirectionalLight(0xfff0d0, intensidad 0.9)`, posición `(4, 10, 6)` |

La niebla es parte de la mecánica visual (ver la taberna, sección 6 de este
documento) — si se cambia el color de fondo, hay que actualizar la niebla en
conjunto para que sigan matcheando (hoy son el mismo valor a propósito, así
los objetos lejanos se funden con el cielo en vez de recortarse).

---

## 2. Terreno y camino

| Elemento | Geometría | Color | Notas |
|---|---|---|---|
| Piso (pasto) | `PlaneGeometry(60, 260)` | `#3f6b3f` | plano estático bajo la cámara, no se mueve con el scroll |
| Camino | `PlaneGeometry(8, 260)` | `#9c7b4f` | franja plana sobre el piso, `y=0.01` |

El **ancho jugable real** del camino no es fijo — varía con
`pathHalfWidthAt(z)`: `clamp(2.6 + sin(z·0.018)·1.15, 1.55, 4.2)`, o sea entre
~3.1 y ~8.4 unidades de ancho total, ondulando a lo largo del recorrido. Si el
arte final agrega bordes/decoración fija al camino, tiene que tolerar ese
ancho variable (no puede ser una textura de ancho fijo pegada al piso).

---

## 3. Bondiola (jugador)

Grupo `player.group`, pivote en el piso (`y=0` = donde pisa).

| Parte | Geometría | Tamaño | Posición (y) | Color |
|---|---|---|---|---|
| Cuerpo | Cápsula (r128 no tiene `CapsuleGeometry`; fallback a cilindro) | radio 0.45, alto 0.7 (cápsula) / cilindro 0.45×0.45×1.4 | 1.0 | `#c97a1a` |
| Cabeza | Esfera | radio 0.32 | 1.75 | `#f0c090` |
| Nariz | Cono, rotado 90° en X | radio 0.08, alto 0.18 | `(0, 1.72, 0.32)` | `#d88b5a` |

Altura total aprox. **1.9 unidades** (de piso a punta de cabeza) — esta es la
escala de referencia para todo lo demás.

**Comportamientos que el modelo final debe poder reproducir** (hoy se logran
manipulando la malla `body` directamente, referenciada como `player.mesh`):

- **Squash de ataque**: al atacar, `player.mesh.scale.set(1.15, 0.9, 1.15)`
  durante 0.25s (`player.attackFlash`). Un modelo final probablemente use una
  animación de golpe en vez de esto, pero hay que reemplazar el squash por
  algo con timing similar (~0.25s).
- **Flash de daño**: al recibir una picadura, `player.mesh.material.emissive`
  se pone rojo (`#550000`) durante 0.35s (`player.hitFlash`). Con un material
  con textura, esto probablemente pase a ser un overlay/tinte rojo en vez de
  `emissive` puro.
- **Bamboleo al caminar**: `player.group.position.y` oscila con
  `sin(tiempo·0.012)·0.05` mientras juega — un placeholder de "caminata", a
  reemplazar por animación real si hay rig.
- **Inclinación lateral**: `player.group.rotation.z` se inclina proporcional
  a cuánto falta para llegar a `targetX` (efecto de "lean" al moverse).
- **Aura dorada** (zona dulce de borrachera, 30–60%): esfera semitransparente
  `#ffcf6b`, radio 0.62, centrada en `y=1.05`, opacidad pulsante 0.10–0.34.
  Es un hijo de `player.group`, independiente del modelo del cuerpo — se
  puede mantener como post-proceso/partícula sin tocar el modelo final.
- **Anillo de alcance de ataque**: elipse (`RingGeometry` escalado de forma
  no uniforme), dorado si conecta / gris si es al aire, en el piso bajo el
  jugador. Es un objeto aparte (no hijo de `player.group`), tampoco requiere
  cambios en el modelo del personaje.

---

## 4. Panales (miel)

Esfera achatada (`scale(1, 0.85, 1)`), radio 0.32, `y=0.5`.

- Color normal: `#e8a93b`. Color "expuesto" (vale más puntos,
  `CFG.HONEY_EXPOSED=3` en vez de `CFG.HONEY_BASE=1`): `#f2c23b`, un amarillo
  más saturado.
- **El arte final necesita una variante visualmente distinta para "expuesto"
  vs "normal"** — hoy es solo un cambio de tono muy sutil, sería bueno que el
  arte lo exagere (por ejemplo con un brillo/destello) para reforzar la
  decisión de riesgo/recompensa (ver sección 6 del GDD).
- Animación: floating leve (`y = 0.15 + sin(tiempo + spin)·0.06`) y rotación
  continua sobre su eje Y.

---

## 5. Abejas

Grupo con: cuerpo (esfera radio 0.22), franja negra (toro, radio 0.22, grosor
0.05), dos alas (esferas achatadas `scale(1, 0.3, 1.8)`, blancas semi-
transparentes, en `±0.18` de X). Todo centrado en `y=1.3` (vuelan).

- Color normal: `#f2d33b` (amarillo).
- **Variante "abeja de rescate"**: cuerpo verde `#5be86b` con emissive verde
  oscuro, más un halo extra (esfera translúcida `#9dffb0`, radio 0.36,
  opacidad 0.4). Solo aparece cuando el jugador tiene 1 vida — matarla de un
  ataque devuelve 1 vida. **Tiene que seguir siendo inconfundible a simple
  vista** respecto de la abeja normal (hoy es color + halo; con arte final
  puede ser un diseño de personaje totalmente distinto, lo importante es que
  el jugador la reconozca al instante, sección 7 del GDD).
- Movimiento: vuelo errático — drift lateral senoidal acotado al ancho del
  camino (`driftAmp` 0.5–1.2, `driftSpeed` 0.5–0.9, aleatorios por abeja) más
  bamboleo vertical. El arte final puede animarlo con más gracia, pero el
  *rango* de movimiento (cuánto se desplaza lateralmente) es parte de la
  dificultad — no achicarlo sin querer.

---

## 6. Enanos ladrones

Grupo con: cuerpo (cilindro, radios 0.32/0.4, alto 0.85, `y=0.55`), cabeza
(esfera radio 0.26, `y=1.05`), gorro cónico (radio 0.3, alto 0.4, `y=1.35`).

- Colores: cuerpo `#7a5230`, cabeza `#e0b088`, gorro `#a8342a` (rojo).
- Altura total aprox. 1.55 unidades — más bajo que Bondiola, a propósito
  ("enano").
- Animación: bamboleo vertical leve al caminar.
- El GDD (sección 8) pide variantes de comportamiento (caminan, bloquean,
  roban, en grupo) que hoy **no** tienen diferencia visual — si el arte final
  puede darles poses/variantes distintas según el comportamiento, sería una
  mejora sobre el prototipo, no algo que haya que preservar tal cual.

---

## 7. Jarras de hidromiel

Grupo con: cuerpo (cilindro, radios 0.22/0.28, alto 0.5, `y=0.35`), corcho
(cilindro delgado, radio 0.1, alto 0.14, `y=0.65`).

- Colores: cuerpo `#8b5a2b` (marrón), corcho `#d9b48f` (beige).
- Animación: rotación continua sobre su eje Y.

---

## 8. Árboles (decoración)

Grupo con: tronco (cilindro, radios 0.18/0.24, alto 1.6, `y=0.8`), copa (cono,
radio 1.1, alto 2.1, `y=2.2`).

- Tronco: `#5b3a26`. Copa: alterna aleatoriamente entre `#2f4b3c` y `#3a5c44`
  (dos tonos de verde bosque) — si el arte final agrega variedad de especies,
  mantener al menos 2 variantes de color/forma para que el bosque no se vea
  repetitivo (ya lo pide la sección 14 del GDD).
- Puramente decorativos, no tienen colisión ni interacción.

---

## 9. La taberna (meta del recorrido)

Grupo fijo al final del camino (no se pool-ea como el resto de los objetos).

| Parte | Geometría | Tamaño | Posición (y) | Color |
|---|---|---|---|---|
| Paredes | Caja | 2.2 × 1.6 × 1.8 | 0.8 | `#6b4423` |
| Techo | Cono de 4 caras (pirámide), rotado 45° en Y | radio 1.75, alto 1.1 | 2.15 | `#7a2a22` |
| Ventana con luz | Plano | 0.55 × 0.6 | 0.75, en la cara frontal (`z=0.91`) | `#ffdd88`, sin sombreado (`MeshBasicMaterial`) |

Altura total aprox. 2.7 unidades — más grande que Bondiola, para que se lea
como un destino, no como un objeto más del camino.

**Importante para el arte final:** esta taberna usa `fog:false` en sus
materiales a propósito — su visibilidad la controla el código a mano
(`updateTavern()`), apareciendo tenue entre la niebla ~12s antes del final y
quedando nítida ~3s antes de llegar (ver sección 12/14 de `GAME_DESIGN.md`).
Si el modelo final usa materiales distintos, **hay que mantener `fog:false`**
(o el equivalente en el material que se use) para no perder ese efecto de
"revelado" — si se le deja el fog automático de Three, la taberna se vuelve
invisible mucho más tarde de lo pensado (ver la nota técnica en `CLAUDE.md`).

---

## 10. Resumen de paleta actual

Para referencia rápida al definir una paleta de arte final (la sección 14 del
GDD ya sugiere diferenciar siluetas por tipo de objeto/enemigo):

| Elemento | Color(es) hex |
|---|---|
| Bondiola | `#c97a1a` (cuerpo), `#f0c090` (piel) |
| Panal normal / expuesto | `#e8a93b` / `#f2c23b` |
| Abeja normal / de rescate | `#f2d33b` / `#5be86b` |
| Enano (cuerpo/piel/gorro) | `#7a5230` / `#e0b088` / `#a8342a` |
| Jarra (cuerpo/corcho) | `#8b5a2b` / `#d9b48f` |
| Árbol (tronco/copa) | `#5b3a26` / `#2f4b3c` ó `#3a5c44` |
| Taberna (paredes/techo/ventana) | `#6b4423` / `#7a2a22` / `#ffdd88` |
| Piso / camino | `#3f6b3f` / `#9c7b4f` |
| Cielo / niebla | `#bfe0ea` |

Ningún color se eligió por una paleta formal — son valores puestos a mano
durante el prototipo. Libre para que el arte final los reemplace por completo,
siempre que mantenga la legibilidad instantánea entre tipos de objeto que ya
pide la sección 14 del GDD.
