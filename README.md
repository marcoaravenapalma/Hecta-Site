# Hecta — Sitio web

Plataforma de terrenos verificados en la Región del Maule. Este repositorio contiene el sitio público y un panel de administración (Decap CMS) para publicar terrenos sin tocar código.

---

## 1. Qué hay en este repo

```
hecta-site/
├── index.html              ← Home (copia de hecta.html, para que sea la página por defecto)
├── hecta.html              ← Misma home (los enlaces antiguos siguen funcionando)
├── maule.html              ← Listado de terrenos (lee data/terrenos.json)
├── terreno.html            ← Ficha individual (lee según ?id=xxx en la URL)
├── subir-terreno.html      ← Formulario para vendedores (envía a Netlify Forms)
├── como-funciona.html      ← Página de servicio
│
├── data/
│   └── terrenos.json       ← Base de datos de terrenos (la edita el CMS)
│
├── uploads/                ← Imágenes que sube el CMS
│
├── admin/
│   ├── index.html          ← Panel del CMS (URL: /admin)
│   └── config.yml          ← Schema de campos del CMS
│
├── netlify.toml            ← Configuración de Netlify
├── .gitignore
└── README.md               ← Este archivo
```

---

## 2. Pasos para poner el sitio online

Vas a necesitar dos cuentas gratuitas: **GitHub** y **Netlify**. Toma unos 20-30 minutos la primera vez.

### Paso 1 — Crear el repositorio en GitHub

1. Entra a [github.com](https://github.com) y crea una cuenta si no tienes.
2. Arriba a la derecha, click en `+` → **New repository**.
3. Nombre sugerido: `hecta-site`. Déjalo en **Private** o **Public** (da igual para Netlify).
4. **No** marques "Add a README file". Click en **Create repository**.
5. En la pantalla siguiente verás los comandos para subir archivos. Lo más fácil:
   - Click en el link **"uploading an existing file"** (parte central de la pantalla).
   - Arrastra TODOS los archivos y carpetas de esta carpeta `hecta-site/` al recuadro.
   - Asegúrate de subir las carpetas `admin/`, `data/` y `uploads/` con sus archivos adentro.
   - Abajo, escribe un mensaje como "Versión inicial" y click en **Commit changes**.

Si sabes usar Git por terminal, también sirve:
```bash
cd hecta-site
git init
git add .
git commit -m "Versión inicial"
git branch -M main
git remote add origin https://github.com/TU-USUARIO/hecta-site.git
git push -u origin main
```

### Paso 2 — Conectar el repo a Netlify

1. Entra a [netlify.com](https://netlify.com) y crea cuenta (puedes usar tu cuenta de GitHub).
2. Click en **Add new site** → **Import an existing project**.
3. Elige **GitHub** y autoriza el acceso.
4. Selecciona el repo `hecta-site`.
5. En la configuración del deploy:
   - Branch: `main`
   - Build command: (déjalo vacío)
   - Publish directory: `.` (un punto, o vacío)
6. Click en **Deploy site**.

En 30 segundos tendrás una URL tipo `magnificent-puddle-1234.netlify.app`. Cámbiale el nombre en **Site settings → Change site name** a algo como `hecta-maule`. Tu URL queda `hecta-maule.netlify.app`.

**Pruébalo:** abre tu URL y debería cargar el home. Anda a `/maule.html` y verifica que aparezcan los 12 terrenos iniciales.

### Paso 3 — Activar el panel del CMS (Netlify Identity + Git Gateway)

El panel `/admin` necesita autenticación. Netlify la provee gratis.

1. En tu sitio en Netlify, anda a **Site configuration → Identity** (o **Integrations → Identity** en algunas versiones).
2. Click en **Enable Identity**.
3. Una vez activado, en la misma página:
   - **Registration preferences**: cambia a **Invite only** (importantísimo, si no cualquiera podría registrarse).
   - **Git Gateway**: anda a la sección, click en **Enable Git Gateway**.
4. Vuelve a la pestaña **Identity** (al costado del menú) y click en **Invite users**.
5. Escribe tu email y envía la invitación. Te llegará un correo con un link.
6. Abre el link, define tu contraseña.
7. Anda a `tu-sitio.netlify.app/admin` → ingresa con ese email y contraseña.

Ya estás dentro del panel.

### Paso 4 — Configurar el formulario de "Sube tu terreno"

Netlify Forms se activa solo al detectar el `data-netlify="true"` que ya está en `subir-terreno.html`. Pero hay que decirle dónde mandarte las notificaciones:

1. En tu sitio en Netlify: **Forms** (menú principal).
2. Vas a ver el form `subir-terreno` listado. Click en él.
3. Anda a **Settings → Form notifications → Add notification → Email notification**.
4. Pon tu email (o `contacto@hecta.cl`). Cada envío te llegará por correo.

Pruébalo: anda a `tu-sitio.netlify.app/subir-terreno.html`, llena el formulario, envía, y revisa tu correo + el panel **Forms** en Netlify.

---

## 3. Cómo publicar un terreno desde el CMS

1. Anda a `tu-sitio.netlify.app/admin` y entra con tu email.
2. Click en **Terrenos → Listado de terrenos**.
3. Verás la lista actual. Click en **Add Terrenos** abajo de todo para crear uno nuevo, o click en uno existente para editarlo.
4. Llena los campos:
   - **Estado**: déjalo en `borrador` mientras trabajas. Cámbialo a `publicado` cuando esté listo.
   - **ID interno**: identificador único (ej: `mar-013`). **No lo cambies después de publicar**, porque la URL del terreno se construye con eso (`/terreno.html?id=mar-013`).
   - **Título**, **sector**, **comuna**, **m²**, **precio UF**, **UF/m²**: lo básico de la card.
   - **Tags**: las etiquetas que aparecen en la card del listado.
   - **Servicios**: marca los que correspondan.
   - **Posición X/Y en mapa**: dónde se muestra el círculo en el mapa pequeño del listado de Maule. Es 0–100 (porcentaje).
   - **Fotos**: arrastra imágenes, se suben a `/uploads`.
5. Click en **Publish → Publish now** (arriba a la derecha).

En 30-60 segundos Netlify rebuildeará el sitio automáticamente y tu terreno aparecerá en `/maule.html`.

### Borradores y publicados

- Solo los terrenos con `estado: publicado` aparecen en el sitio público.
- Los borradores quedan guardados en el JSON pero no se muestran. Útil para preparar fichas mientras esperan validación.

---

## 4. Cómo funciona técnicamente

- **Sitio estático**: HTML + CSS + JS en el cliente. Sin backend, sin base de datos.
- **Datos**: `data/terrenos.json` es la única fuente de verdad. Lo edita el CMS, lo lee el sitio.
- **Renderizado**:
  - `maule.html` hace `fetch('data/terrenos.json')` al cargar y renderiza las cards con React (in-browser, vía Babel standalone — ya estaba así).
  - `terreno.html` lee `?id=xxx` de la URL, hace el mismo fetch, busca el terreno y rellena los IDs marcados (`#lot-title`, `#lot-stat-precio`, etc.). Si no se pasa `?id=`, queda como demo estática.
- **Imágenes**: el CMS las sube a `/uploads/` y guarda la ruta en el JSON.
- **Formulario de "Sube tu terreno"**: usa **Netlify Forms** — no hay servidor propio. Cada envío queda registrado en el panel de Netlify y te llega por email.
- **CMS**: Decap CMS (open source). Hace commits a GitHub usando el Git Gateway de Netlify. Cada vez que publicas, queda registrado en el historial del repo.

---

## 5. Dominio propio (cuando esté listo)

1. Compra el dominio (`hecta.cl`, por ejemplo) en NIC Chile.
2. En Netlify: **Domain management → Add custom domain**.
3. Sigue las instrucciones para apuntar los DNS a Netlify (cambias los nameservers o agregas un registro CNAME, según tu caso).
4. SSL/HTTPS se activa automáticamente y gratis (Let's Encrypt).

---

## 6. Costos

Todo lo de arriba es **gratis** en los planes free de Netlify y GitHub mientras estés bajo estos límites (más que suficiente para empezar):

- Netlify Free: 100 GB de ancho de banda al mes, 300 minutos de build, 100 envíos de form al mes.
- Netlify Identity Free: 1.000 usuarios activos al mes (tú eres uno).
- GitHub: ilimitado para repos privados pequeños.

---

## 7. Próximos pasos sugeridos

Una vez que esto esté funcionando, hay mejoras naturales:

- **Mapa real con coordenadas**: integrar Leaflet o Mapbox en `terreno.html` usando `lat`/`lng` (los campos ya están en el JSON, solo falta el componente).
- **Galería real con las fotos del CMS**: hoy `terreno.html` muestra ilustraciones SVG topográficas; reemplazarlas por las imágenes que subió el CMS al campo `fotos`.
- **Más campos en la ficha** (análisis del equipo, comparables, riesgos): agregar al schema del CMS y al script de renderizado de `terreno.html`.
- **Búsqueda por mapa**: ya tienes coordenadas y los terrenos en JSON.

---

## Soporte

Si algo se rompe:

1. Revisa el panel **Deploys** de Netlify — ahí ves el log de cada build.
2. Si el CMS no carga, verifica que **Identity** y **Git Gateway** estén ambos activados.
3. Si los terrenos no aparecen, abre `tu-sitio.netlify.app/data/terrenos.json` directo en el navegador para ver si el JSON es válido.
