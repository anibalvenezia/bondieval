# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

# Bondieval

Prototipo jugable de un juego web casual, vertical y mobile-first (ver `docs/GAME_DESIGN.md` para el diseño completo).

@docs/GAME_DESIGN.md

## Arquitectura actual

- **Un solo archivo**: `index.html` contiene todo (HTML + CSS + JS inline). No hay build step, ni bundler, ni `package.json`. Se abre directo en el navegador.
- **Motor**: Three.js r128, cargado desde `cdnjs.cloudflare.com` vía `<script>` clásico (variable global `THREE`, sin módulos ES).
- **Sin dependencias de npm.** No agregar un bundler, framework o build system a menos que se pida explícitamente — el objetivo del proyecto es "compartible por link, carga rápida, sin instalar nada".
- **Deploy**: GitHub Pages sirve directo desde la rama `main`, carpeta raíz. `index.html` en la raíz del repo *es* el sitio publicado en https://anibalvenezia.github.io/bondieval/. Cualquier cambio en `main` se ve reflejado ahí (con el delay normal de Pages).

## Estructura del JS dentro de `index.html`

Todo vive en un único IIFE. Las secciones, en orden:

1. `CFG` — objeto con todas las constantes de balance (duración, velocidad, sensibilidad de arrastre, valores de miel/borrachera/vida). **Cambios de balance van acá, no hardcodeados en el resto del código.**
2. `pathHalfWidthAt(z)` — función que define el ancho del camino en cada punto del recorrido.
3. Estado global: `player`, `miel`/`enemigos`/`borrachera`/`vidas`, `objects` (pools de honeycombs/bees/dwarves/jars/trees).
4. `initThree()` / `resize()` — setup de escena, cámara, luces, piso.
5. Fábricas de objetos (`makeTree`, `makeHoney`, `makeBee`, `makeDwarf`, `makeJar`, `makeTavern`) — geometría low-poly hecha a mano, sin modelos externos. `makeBee(x, z, healer)` acepta un flag opcional para la abeja de rescate (ver nota abajo); `makeTavern()` crea el landmark fijo del final del recorrido (posición `worldZ = TOTAL_DISTANCE`, materiales con `fog:false` porque su visibilidad la controla `updateTavern()` a mano, no el fog automático de Three).
6. `phaseFlags(t)` y `spawnSegment(z)` — el pacing del guión de partida (qué aparece y cuándo, en base al tiempo transcurrido, siguiendo la sección 12 del documento de diseño; los umbrales están escalados a `CFG.DURATION`, no a los tiempos originales del GDD).
7. `speedAt(t)` — velocidad de avance por escalones (sube cada `CFG.SPEED_STEP_SEC` desde `BASE_SPEED` hasta `TOP_SPEED`). `TOTAL_DISTANCE` (constante derivada, integral de `speedAt` sobre `CFG.DURATION`) y `kmRemainingAt(worldZ)` traducen esa distancia a los "km narrativos" (`CFG.TOTAL_TRIP_KM`) que se muestran en el HUD y en el resumen — es un flavor stat, no física real.
8. Input táctil (`onPointerDown/Move/Up`) — arrastre = movimiento lateral+profundidad, tap corto sin arrastre = ataque. El signo de profundidad y el clamp lateral están atados al sistema de coordenadas de cámara (`CFG.CAMERA_Z`) — ver nota de colisión abajo.
9. Lógica de gameplay (`doAttack`, `checkPassiveCollisions`, `scoredHoney`) — acá vive la interacción entre las tres variables. En la zona dulce de borrachera (30–60%) `doAttack` tiene más alcance y `scoredHoney` aplica el multiplicador de puntaje; `updateDrunkFX` refleja ese mismo rango con el aura dorada de `player.aura` y `toast()` acepta un tercer parámetro `big` para el feedback reforzado de miel. La abeja de rescate (`o.userData.healer`) solo se puede spawnear con `vidas === 1` (control en `spawnSegment`, cooldown vía `lastHealerSpawnZ`) y devuelve 1 vida si se la mata en `doAttack`; si no, el sting normal de `checkPassiveCollisions` aplica igual.
10. `loop()` — el game loop principal (requestAnimationFrame), con estado `'start' | 'playing' | 'paused' | 'end'`; toda la actualización de gameplay está gateada por `state === 'playing'`, así que pausar (`pauseGame`/`resumeGame`) no requiere lógica extra de congelamiento. `updateTavern(elapsed)` se llama ahí mismo, junto al resto de updates de gameplay.
11. Flujo de partida (`resetGame`, `startGame`, `pauseGame`, `resumeGame`, `endGame`, `nickname`, `endMessage`, `handleBestScore`).

**Nota sobre colisiones y profundidad:** la posición de colisión del jugador es `pz = player.worldZ - player.depth` (no `+`). El render de los objetos (`renderZ` en `updatePositions`) no depende de `player.depth`, así que ese signo es el que hace que la colisión coincida con dónde se ve al jugador en pantalla — si se vuelve a tocar el control de profundidad, mantener ese acople.

## Convenciones

- **Idioma**: todo el texto de UI, comentarios de gameplay y mensajes de commit van en español (así está el resto del repo y el documento de diseño).
- **La fórmula de puntaje y los umbrales de borrachera son intencionalmente opacos para el jugador** — no agregar texto en la UI que se los explique (ver sección 10 y 15 del documento de diseño: "no explicar nada", descubrimiento orgánico).
- Mantené el HUD legible en pantalla chica (mobile-first) — cualquier elemento nuevo de UI se prueba primero en un viewport angosto (~380px).
- No usar `localStorage`/`sessionStorage` si esto se sigue trabajando como artifact de Claude — para el juego standalone en GitHub Pages sí es válido usar `localStorage` para el mejor puntaje; la versión actual usa `window.storage` (API de artifacts) para ese propósito, así que si el archivo se sigue editando *fuera* de un artifact de Claude, migrar `handleBestScore()` a `localStorage`.

## Cómo probar cambios

No hay servidor de dev. Para levantar el juego localmente:

```bash
npx serve .
# o
python3 -m http.server 8000
```

Abrir en `http://localhost:8000` (o el puerto que corresponda), idealmente probando también en un celular real en la misma red, dado que el control es 100% táctil.

No hay tests automatizados ni linter configurado todavía. Verificación manual: abrir en navegador, jugar una partida completa (~60s) y confirmar que no hay errores en consola.

## Próximos pasos del proyecto

Con las últimas rondas de ajustes (abeja de rescate, feedback visual de la zona dulce, distancia en km, taberna emergiendo entre la niebla) se dio por cerrada la etapa de feedback/pulido del prototipo. Ver la sección "Próximos pasos sugeridos" al final de `docs/GAME_DESIGN.md`: arte final (reemplazar la geometría gris de las fábricas de objetos, incluida la taberna), SFX/música, testeo con no-gamers, y feature de compartir resultado como imagen.
