# Cepillos

App personal para iPhone que lleva el desgaste de dos cepillos de dientes: el de casa y el de la oficina. Las cerdas de cada cepillo pasan del azul intenso al blanco amarillento y se abren a medida que suman lavados, y la app avisa cuando se acerca la fecha de cambiarlos.

## Qué hace

- **Dos cepillos**: vistos desde arriba, con sus cerdas en una base de vidrio. El color de cada mechón se degrada con el uso (los del borde se gastan antes) y, pasado el 40 % de desgaste, empiezan a abrirse.
- **Desgaste de cada uno**: porcentaje, lavados acumulados de 180, días que faltan, fecha del cambio y lavados de hoy. Se actualiza solo, cada día y a lo largo del día.
- **Desgaste por día**: lo que suma cada cepillo en cada día de la semana. El de casa gasta 1,7 % los días normales y 1,1 % lunes, martes y jueves; el de la oficina, 0,6 % esos tres días y nada el resto.
- **Aviso**: faltando 14 días o menos aparece un aviso ámbar; con 7 días o menos, o vencido, uno rojo. Cada cepillo tiene el suyo.
- **Registrar cepillo nuevo**: se elige casa u oficina y la fecha del cambio (por defecto hoy); el cepillo vuelve a empezar desde cero. La hoja muestra el cambio estimado antes de guardar.
- **Historial**: cepillos usados, con los días y los lavados de cada uno. Cada registro se edita o elimina.
- **Tocar un cepillo**: sale espuma y el cepillo se balancea. Con «reducir movimiento» queda estático.
- **Panel Casa**: publica la fecha de cada cepillo y la del cambio para verlos en el panel (ver más abajo).
- **Respaldo**: copia todos los registros como texto y restáuralos desde ahí (los datos viven solo en el dispositivo).

## Cómo se calcula

Nadie anota cada lavado: el desgaste se cuenta por calendario desde la fecha en que se puso cada cepillo.

- 3 lavados al día (a las 7, 13 y 21 h). Cada cepillo se cambia a los 180 lavados o al cumplir su máximo de días de uso, lo que ocurra primero: 70 días el de casa y 1 año el de la oficina.
- Lunes, martes y jueves el lavado del mediodía es en la oficina, con el otro cepillo. Esos días el de casa suma 2 lavados y el de la oficina, 1; los demás días, el de casa suma 3.
- Por semana: casa 18 lavados (≈ 10 semanas de vida, unos 70 días) y oficina 3 (≈ 60 semanas, más de un año). El de casa llega a los 180 lavados justo a los 70 días; el de la oficina se cambia por antigüedad, al año.
- El porcentaje de desgaste es el mayor entre lavados (de 180) y días de uso (de 70 o de 365).
- El día en que se pone el cepillo cuenta como día completo; el día de hoy cuenta solo los lavados cuya hora ya pasó.

Los supuestos están al inicio del script, en `WASH_HOURS`, `OFFICE_WASH`, `OFFICE_DAYS` y `BRUSHES` (cupo de lavados y máximo de días de cada cepillo). Si cambia la rutina, se cambian ahí.

## Instalar en el iPhone

1. En GitHub, ve a **Settings → Pages** y publica la rama `main` desde la raíz (`/`). La app queda en `https://pablocarvallo.github.io/cepillos/`.
2. Abre esa dirección en **Safari** en el iPhone.
3. Toca **Compartir → Añadir a pantalla de inicio**.

Se abre como app independiente, con su icono, y funciona sin conexión. El zoom con los dedos y con doble toque está bloqueado.

## Conectar con Panel Casa

En Panel Casa, abre Conexiones y toca «Copiar código para las apps». En esta app, baja hasta la tarjeta «Panel Casa» y toca «Pegar código». Desde ahí, cada cambio se publica en el buzón privado, en un hito llamado «Cepillos · panel» con este contenido:

```
{ app: "cepillos", v: 1, at, n, casa: { date, due } | null, oficina: { date, due } | null }
```

`date` es el día en que se puso el cepillo y `due`, el día del cambio (el primero entre los 180 lavados y el máximo de días de uso).

## Archivos

| Archivo | Uso |
| --- | --- |
| `index.html` | La app completa (HTML, CSS y JavaScript sin dependencias de compilación) |
| `manifest.webmanifest` | Nombre, colores e iconos de la app instalada |
| `sw.js` | Service worker para uso sin conexión |
| `icons/icon-full.svg` | Icono a pantalla completa (fuente de los PNG) |
| `icons/icon.svg` | Icono con esquinas redondeadas (favicon) |
| `icons/apple-touch-icon.png` | Icono de 180 px para la pantalla de inicio del iPhone |
| `icons/icon-192.png`, `icons/icon-512.png`, `icons/icon-1024.png` | Iconos del manifiesto y archivo de alta resolución |

## Diseño

Sigue el sistema de diseño común de las apps domésticas (repositorio `casa`, carpeta `base/`). El `index.html` lleva en línea los bloques `/*DS:TOKENS*/`, `/*DS:VARS*/`, `/*DS:BASE*/`, `/*DS:COMP*/` y `/*DS:APP*/`; los tres primeros y `DS:COMP` se actualizan con `herramientas/aplicar-base.py` y no se editan a mano. Lo propio de la app (cepillos, tarjeta principal, desgaste por día, aviso) está en `DS:APP`. El módulo de Panel Casa va entre `/*PANEL:START*/` y `/*PANEL:END*/` y se copia con `herramientas/conectar-apps.py`.

## Datos

Los registros se guardan en `localStorage` bajo la clave `cepillos-registros`: una lista de objetos `{ id, b, date }` con el cepillo (`casa` u `oficina`) y la fecha (`AAAA-MM-DD`) en que se puso.
