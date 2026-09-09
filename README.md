# VÉRTICE

Minijuegos hechos con Three.js. Cada juego es un único archivo HTML: no hay build,
ni dependencias instaladas, ni servidor. Three.js se carga desde CDN con un importmap.

```
├── index.html              ← el hub (portada 3D + lista de juegos)
├── games/
│   ├── cube-runner.html          ← propios de VÉRTICE
│   ├── tower-stack.html
│   ├── cruce-loco.html
│   ├── fruta-loca.html
│   ├── sumo-party.html
│   ├── canasta-loca.html
│   ├── olimpiadas-granja.html
│   ├── porrazo-party.html
│   ├── aura-battle.html
│   ├── turbo-kart-rally.html
│   ├── anguera-superstars.html
│   ├── futbol-retro.html
│   ├── kings-league.html
│   ├── neon-coast.html
│   └── turbo-stumblers.html
├── .nojekyll
└── README.md
```

## Publicar en GitHub Pages

1. Crea un repo y sube estos archivos a la raíz de `main`.
2. En el repo: **Settings → Pages**.
3. En *Source* elige **Deploy from a branch**, rama `main`, carpeta `/ (root)`.
4. Guarda. En un minuto tendrás `https://<usuario>.github.io/<repo>/`.

Cada `git push` a `main` republica el sitio. No hay nada que compilar.

Si quieres que viva en `https://<usuario>.github.io/` sin subcarpeta, el repo tiene
que llamarse exactamente `<usuario>.github.io`.

## Probarlo en local

Los módulos ES no funcionan con doble clic (`file://` bloquea el import). Necesitas
un servidor, y cualquiera vale:

```bash
python3 -m http.server 8000
# abre http://localhost:8000
```

## Añadir un juego

Dos pasos:

1. Copia `games/cube-runner.html` como `games/tu-juego.html` y reescribe la lógica.
   La cabecera ya trae el sistema visual montado: paleta, sombreado toon, contornos,
   HUD y panel de inicio.
2. Añade una entrada al array `GAMES` en `index.html`:

```js
{
  slug:  'tu-juego',                    // = nombre del archivo en /games, sin .html
  title: 'Tu Juego',
  desc:  'Qué se hace, en una frase.',
  keys:  'Cómo se juega',
  shape: 'runner',                      // runner tower farm fight dance kart ball crown
                                        // cross fruit sumo hoop road flag orb
  color: 'var(--lime)'                  // color de la cabecera de la tarjeta
}
```

Si quieres un icono nuevo, añádelo al objeto `SHAPES`: es un SVG de 64×64 con
`stroke="#2B1A55"` y `stroke-width="5"`, para que case con el resto.

## El sistema visual, por si lo tocas

- **Paleta**: tinta `#2B1A55` en todos los bordes y contornos, crema `#FFF6E5` para
  superficies, y seis colores caramelo para los objetos. La tinta oscura en *todos*
  los bordes es lo que mantiene unida la imagen; si la cambias, cámbiala en todas partes.
- **Contorno de dibujo animado**: cada objeto 3D lleva una copia de sí mismo, inflada
  y con las caras invertidas (`side: THREE.BackSide`). No hay post-procesado, así que
  va bien hasta en móviles viejos.
- **Sombreado plano**: `MeshToonMaterial` con una rampa de tres escalones y filtrado
  `NearestFilter`. Sin el filtro nearest el degradado se suaviza y se pierde el efecto.
- **Botones**: el relieve es un `box-shadow` sólido sin desenfoque, y al pulsar el botón
  baja lo mismo que mide esa sombra. Es lo que da la sensación de tecla física.
- **Tipografía**: Fredoka. Los titulares llevan el contorno grueso en un `::before` con
  `-webkit-text-stroke`, que necesita `isolation:isolate` en el titular para no quedar
  tapado por el fondo del contenedor.

## Detalles que quizá quieras revisar

- **Versión de Three.js**: fijada a `0.164.0` en cada archivo, a propósito, para que una
  actualización no rompa un juego que ya funcionaba. Si actualizas, hazlo de uno en uno.
- **Récords**: en `localStorage`, o sea por navegador y dispositivo. Una tabla compartida
  necesitaría backend, y ahí GitHub Pages ya no llega.
- **Estilo**: la dirección visual es de arcade chunky, pero los personajes y las formas
  son originales. No uses assets ni el logo de juegos comerciales si publicas esto.

## Traer un juego que ya tenías hecho

`olimpiadas-granja.html` entró así. Si el juego es un único HTML, se copia a `/games/`
y se añade su entrada a `GAMES`. Lo único que hay que tocar del juego en sí:

- un enlace de vuelta (`<a href="../index.html">`) en su pantalla de inicio;
- la fuente y los colores, si quieres que no desentone;
- guardar la partida en `localStorage`, si no lo hacía ya.

Si el juego trae archivos aparte (imágenes, sonidos, js suelto), dale carpeta propia
—`/games/mi-juego/index.html`— y en su entrada usa `href` en vez de `slug`. Para eso,
en `index.html` cambia el enlace de la tarjeta por:

```js
<a class="card" href="${g.href || `games/${g.slug}.html`}">
```

Tres cosas que rompen al publicar y no en local:

- **Rutas absolutas.** `/assets/x.png` apunta fuera del sitio si vives en una subcarpeta.
  Usa rutas relativas: `assets/x.png`.
- **Mayúsculas.** GitHub Pages distingue `Nave.png` de `nave.png`. Tu ordenador no.
- **Versiones distintas de Three.js.** No pasa nada: cada juego carga la suya y no se
  pisan, porque son páginas independientes. Olimpiadas usa la r128 y los otros dos la
  0.164.

## Estado de los juegos importados

Los seis importados conservan su propio estilo: cada uno tiene identidad hecha, y
uniformarlos habría sido romper algo que ya funcionaba para ganar poco. Lo único
que comparten con el hub es el botón de vuelta.

| Juego | Guarda progreso | Notas |
|---|---|---|
| Olimpiadas de la Granja | sí (añadido) | 10 pruebas, 4 personajes |
| Anguera Superstars | sí (ya lo traía) | Three.js va incrustado: 780 KB, funciona sin CDN |
| Porrazo Party | no | ya usaba Fredoka, es el que mejor pega con el hub |
| Aura Battle | no | pide horizontal |
| Turbo Kart Rally | no | la mejor vuelta se pierde al recargar |
| Fútbol Retro 7v7 | no | entra directo al partido, sin menú |
| Neon Coast | no | conducción infinita, cinco paisajes |
| Turbo Stumblers | no | ya usaba Fredoka |
| Kings League Arcade | sí (ya lo traía) | 945 KB con Three.js dentro; usa marca y nombres reales |

Añadir persistencia a los que no la tienen es el mismo patrón que en Olimpiadas:
leer de `localStorage` al arrancar y guardar cuando cambie el récord.

## Ojo con los nombres de archivo

Un juego llamado `index.html` en la raíz del repo sustituye a la portada del sitio.
Kings League llegó así y por eso está guardado como `games/kings-league.html`.
Regla simple: en `/games/` el nombre del archivo es el `slug`, y ninguno se llama `index`.

## Los seis juegos propios

`cube-runner`, `tower-stack`, `cruce-loco`, `fruta-loca`, `sumo-party` y
`canasta-loca` comparten la misma base: sombreado toon con rampa de tres escalones,
contorno por copia inflada, HUD de pastillas, panel con rebote, récord en
`localStorage` y confeti. Los cuatro últimos se generan con un script a partir de
una plantilla común, así que un cambio en el sistema visual se aplica a todos:

```
/home/claude/build_games.py     plantilla + Cruce Loco + Sumo Party
/home/claude/build_games2.py    Canasta Loca + Fruta Loca
```

Ese script no viaja con el sitio: los HTML generados son autónomos y se editan
directamente si solo quieres tocar uno.
