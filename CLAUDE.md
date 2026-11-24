# Recepción (ART)

Esta es la app de **Recepción** (~4900 líneas en `index.html`). La más grande: alimenta a las otras dos.

## Las 3 apps de ART Apartments (Barcelona)

Andrés las va editando con Claude. Habla en español rioplatense: respondé igual, simple y sin tecnicismos.

| App | Repo | Quién la usa | URL publicada (probable, sin verificar) |
|---|---|---|---|
| Recepción | Artbcn/recepcion | Recepción: carga listados (llegadas/salidas/in house), arma y **publica** el parte de limpieza y el de Diego, preventivas por períodos | https://artbcn.github.io/recepcion/ |
| Limpieza | Artbcn/Limpieza | Eli (gobernanta) y camareras: reparto, cierre del día, fondo | https://artbcn.github.io/Limpieza/ |
| Mantenimiento | Artbcn/Mantenimiento | Diego (mantenimiento): ve su parte y marca tareas hechas | https://artbcn.github.io/Mantenimiento/ |

Otros repos relacionados: `reparto` (se embebe en un iframe dentro de Limpieza, mismo origen artbcn.github.io), `bravo`, `mapa`, `claves`.

### Cómo están hechas
- Cada app es **un solo `index.html`** (HTML + CSS + JS en el mismo archivo, sin build, sin dependencias locales). Supabase JS se carga por CDN.
- Se publican con **GitHub Pages desde `main`**: hacer commit y push a `main` = publicar. No hay otras ramas.
- Recepción y Limpieza tienen aviso/recarga automática cuando hay versión nueva (hacen `fetch(location.pathname)` y comparan).

### Datos compartidos (un mismo proyecto Supabase)
- `limp_kv` (k, v jsonb, src, updated_at): almacén clave-valor compartido. Ej.: `art-bases` (listados por día), `art-period-estado` (preventivas/períodos), `art-fondosel-*`. Recepción sincroniza entre PCs con esto y evita pisar datos de otra pestaña.
- `limp_parte` (fecha, data): parte de limpieza que publica Recepción → lo lee Limpieza.
- `limp_cierre`: lo que marca Limpieza al cerrar el día (se guarda campo por campo para no pisar notas). Recepción lo lee.
- `mant_parte` (fecha, data): parte de Diego que publica Recepción → lo lee Mantenimiento.
- `mant_cierre`: lo que Diego marca como hecho. Recepción lo lee.
- `dist_empleadas`, `dist_asig`, `dist_tareas_diarias`, `dist_extra_tareas`: reparto de camareras (Limpieza / app reparto).

**Si cambiás la forma de un dato en una app, revisá las otras que lo leen** (ej. un campo nuevo en `mant_parte` → Recepción lo escribe y Mantenimiento lo muestra).

### Forma de trabajar
- Cambios chicos y probados; mensajes de commit en español describiendo qué ve el usuario.
- Probar el HTML con Playwright/Chromium antes de publicar cuando se pueda (ojo: escribe en el Supabase real; no publicar datos de prueba).
- Push directo a `main` salvo que Andrés pida otra cosa.
- Nunca borrar ni pisar datos de Supabase sin confirmar con Andrés.
