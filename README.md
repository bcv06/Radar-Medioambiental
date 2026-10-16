# Radar Medio Ambiental · SONAMI

Monitor regulatorio ambiental de la minería en Chile. Cada hallazgo enlaza a su **fuente directa**: el documento o la página del organismo que lo originó (MMA, SEA, SMA, SBAP, Congreso, Contraloría, Consejo de Monumentos, DGA, Diario Oficial).

La base inicial reúne los 61 hallazgos de los reportes quincenales de monitoreo regulatorio minero-ambiental N° 12 a 15 (16-ago al 15-oct-2026), cada uno rastreado hasta su fuente oficial, más 7 hallazgos nuevos leídos directamente en esas fuentes.

## Estructura

```
index.html                 Página lista para GitHub Pages (se genera con el build)
data/hallazgos.json        Hallazgos (open research + nuevos de fuente directa)
data/fuentes.json          Catálogo de fuentes directas verificadas (alimentan el pipeline)
src/template.html          Diseño, estilos y lógica del radar (placeholders {{LOGOS}} y {{DATA}})
src/logos.html             Logos SONAMI (versión clara y oscura, embebidos)
scripts/build.py           Arma index.html y dist/artifact.html desde src/ y data/
docs/runbook-pipeline.md   Procedimiento de la barrida automática desde las fuentes
.nojekyll                  Evita que GitHub Pages procese el sitio con Jekyll
```

## Publicar en GitHub Pages

1. Crea el repositorio (por ejemplo `Radar-Medio-Ambiental`) y sube todo el contenido de esta carpeta a la rama `main`.
2. En el repositorio: **Settings → Pages → Build and deployment → Deploy from a branch → `main` / `(root)`**.
3. El sitio queda en `https://<usuario>.github.io/Radar-Medio-Ambiental/`.

Desde la terminal:

```bash
git init -b main
git add .
git commit -m "Radar Medio Ambiental · versión inicial"
git remote add origin https://github.com/<usuario>/Radar-Medio-Ambiental.git
git push -u origin main
```

## Actualizar los datos

1. Edita o agrega hallazgos en `data/hallazgos.json` (esquema en `docs/runbook-pipeline.md`, paso 3).
2. Regenera la página: `python3 scripts/build.py` (opcional: fecha de corte, `python3 scripts/build.py 2026-10-20T09:00`).
3. Haz commit y push de `index.html` y `data/`.

`scripts/build.py` también genera `dist/artifact.html`, el mismo radar sin envoltura HTML, para republicarlo como Artifact en Claude.

## Esquema de un hallazgo

| Campo | Descripción |
|---|---|
| `id` | sha1(fuente\|url\|título en minúscula)[:12] |
| `origen` | `reporte` (vino en un reporte quincenal) o `fuente_directa` (leído en la fuente) |
| `reporte` | N° del reporte quincenal, o `null` |
| `dia`, `fechaFuente` | Fecha del hallazgo y fecha del documento (YYYY-MM-DD) |
| `fuente` | `MMA` `SEA` `SMA` `SBAP` `LEG` `CGR` `JUD` `CMN` `AGUA` `MIN` `DO` `OTRO` |
| `criticidad` | `critica` `alta` `media` `info` |
| `plazo`, `estado` | Plazo vigente (consulta, PAC, sesión) y `cerrada` cuando vence |
| `url`, `urlTipo` | Enlace principal y su tipo: `primaria`, `listado`, `secundaria`, `no_encontrada` |
| `verif` | `abierta` (documento abierto y coincide), `listado` (figura en el listado oficial), `buscador`, `no_abrible`, `no` |
| `otrasUrls`, `notas` | Fuentes complementarias y observaciones de verificación |

## Criticidad

- **Crítica**: medida directa sobre un proyecto o faena minera (paralización, sanción, RCA reclamada o revocada, contingencia) o hito legislativo con votación o plazo inminente que afecta a la minería.
- **Alta**: normativa, dictamen, consulta pública abierta, pronunciamiento sobre un proyecto minero en el SEIA.
- **Media**: seguimiento sectorial.
- **Info**: contexto.

## Notas técnicas

- Página estática, sin servidor ni base de datos. La única dependencia externa es SheetJS 0.18.5 (cdnjs) para el botón «Descargar Excel».
- Tema claro y oscuro automático según el sistema.
- Contraloría (`contraloria.cl`) bloquea la lectura automática: esos hallazgos se confirman por buscador y prensa, y quedan marcados en el radar.
- La versión en Claude vive en https://claude.ai/artifact/GM1gykDFb9n6th7DEFuTf1 (privada, compartible desde su menú).
