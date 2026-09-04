# Bondieval

Juego web casual, vertical y mobile-first: Bondiola atraviesa un bosque medieval recolectando miel, esquivando abejas y enanos ladrones, mientras lidia con su afición a la hidromiel. Partida de ~100 segundos, controlada por un sistema de tres variables interdependientes — **Miel 🍯 / Enemigos ⚔️ / Borrachera 🍺** — que el jugador descubre jugando, sin tutorial explícito.

## Estado actual

Prototipo jugable (`index.html`), autocontenido, hecho con [Three.js](https://threejs.org/) vía CDN — sin build ni instalación. Cubre:

- Movimiento por arrastre (lateral + profundidad) y ataque por toque.
- Ancho de camino variable a lo largo del recorrido.
- Sistema completo de Miel / Enemigos / Borrachera / Vida, con decaimiento pasivo y efectos visuales por tramo de borrachera (bonus de puntaje, drift de cámara, viñeta).
- Panales, abejas, enanos ladrones y jarras de hidromiel, con pacing por tiempo siguiendo el guión de partida del documento de diseño.
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

El documento de diseño completo, con las mejoras propuestas sobre el concepto original, está en [`docs/GAME_DESIGN.md`](docs/GAME_DESIGN.md).

## Próximos pasos

Ver la sección "Próximos pasos sugeridos" del documento de diseño: testeo con no-gamers, arte final (reemplazar geometría gris), SFX/música, y feature de compartir resultado como imagen.
