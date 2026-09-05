# Bondieval

Juego web casual, vertical y mobile-first: Bondiola atraviesa un bosque medieval recolectando miel, esquivando abejas y enanos ladrones, mientras lidia con su afición a la hidromiel. Partida de ~60 segundos, con la velocidad de avance subiendo en escalones, controlada por un sistema de tres variables interdependientes — **Miel 🍯 / Enemigos ⚔️ / Borrachera 🍺** — que el jugador descubre jugando, sin tutorial explícito.

## Estado actual

Prototipo jugable (`index.html`), autocontenido, hecho con [Three.js](https://threejs.org/) vía CDN — sin build ni instalación. Cubre:

- Movimiento por arrastre (lateral + profundidad) y ataque por toque, con botón de pausa.
- Ancho de camino variable a lo largo del recorrido, y velocidad de avance que sube por escalones cada 12s.
- Sistema completo de Miel / Enemigos / Borrachera / Vida, con decaimiento pasivo y efectos visuales por tramo de borrachera: aura dorada pulsante y toast reforzado en la "zona dulce" (30–60%, bonus de puntaje y alcance de ataque), drift de cámara y viñeta en borrachera alta.
- Panales, abejas (con vuelo errático), enanos ladrones y jarras de hidromiel, con pacing por tiempo siguiendo el guión de partida del documento de diseño.
- Abeja de rescate: aparece diferenciada cuando queda 1 sola vida — matarla de un ataque devuelve una vida, fallar y que pique es una picadura normal.
- Barra de progreso hacia la taberna con distancia restante en km, además de las stats de Miel/Enemigos/Vida y borrachera; la misma distancia se muestra en el resumen si la partida termina antes de llegar.
- La taberna se insinúa entre la niebla en el tramo final y se va acercando hasta el final de la partida.
- Anillo de alcance al atacar (dorado si conecta, gris si es al aire) y toast "💨 al aire" cuando falla — feedback agregado tras testeo con no-gamers, que notaron que no quedaba claro el timing del ataque contra abejas/enanos.
- Cuatro finales (normal, borracho, fantasma, heroico) con apodo cómico calculado.
- Mejor puntaje persistente y pantalla de resultados.

## Cómo probarlo

Abrí `index.html` directamente en un navegador (idealmente en celular), o serví la carpeta con cualquier servidor estático:

```bash
npx serve .
# o
python3 -m http.server 8000
```

## Documentación

El documento de diseño completo, con las mejoras propuestas sobre el concepto original, está en [`docs/GAME_DESIGN.md`](docs/GAME_DESIGN.md). Para reemplazar la geometría placeholder por arte final, ver la referencia técnica pieza por pieza en [`docs/ART_REFERENCE.md`](docs/ART_REFERENCE.md).

## Próximos pasos

Con el testeo real con no-gamers ya hecho y el ajuste de legibilidad del ataque, se da por cerrada la etapa de feedback y pulido del prototipo. Ver la sección "Próximos pasos sugeridos" del documento de diseño: arte final (reemplazar geometría gris), SFX/música, y feature de compartir resultado como imagen.
