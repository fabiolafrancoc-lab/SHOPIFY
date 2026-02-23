# SaludCompartida — Shopify Theme

Theme personalizado para la tienda Shopify de **SaludCompartida**.

---

## Estructura del tema

```
saludcompartida-theme/
├── assets/               ← Archivos estáticos (video, imágenes, CSS, JS)
│   └── LANDING_VIDEO SIN FIN_LIVIANO.mov
├── config/               ← Configuración del tema
│   ├── settings_schema.json
│   └── settings_data.json
├── layout/               ← Plantilla base de todas las páginas
│   └── theme.liquid
├── locales/              ← Traducciones
│   └── en.default.json
├── sections/             ← Secciones reutilizables (vacío por ahora)
├── snippets/             ← Fragmentos de código Liquid (vacío por ahora)
└── templates/            ← Plantillas de página
    ├── index.liquid            (Página de inicio)
    ├── page.contacto.liquid    (Página: Contáctanos)
    ├── page.privacidad.liquid  (Página: Aviso de Privacidad)
    └── page.terminos.liquid    (Página: Términos y Condiciones)
```

---

## Cómo subir el tema a Shopify

### Opción 1 — Subir el archivo ZIP (más fácil)

1. Descarga el archivo **`saludcompartida-theme.zip`** de este repositorio.
2. En tu panel de administración de Shopify, ve a **Tienda en línea → Temas**.
3. Haz clic en **"Agregar tema" → "Subir archivo ZIP"**.
4. Selecciona el archivo `saludcompartida-theme.zip`.
5. Shopify procesará el tema. Una vez subido, haz clic en **"Publicar"** para activarlo.

### Opción 2 — Shopify CLI (para desarrolladores)

Si tienes instalado [Shopify CLI](https://shopify.dev/docs/themes/tools/cli):

```bash
# Instalar Shopify CLI
npm install -g @shopify/cli @shopify/theme

# Conectarse a la tienda y subir el tema
shopify theme push --store tu-tienda.myshopify.com
```

---

## Páginas alternativas

Las plantillas `page.*.liquid` son **plantillas alternativas de página**. Para usarlas:

1. En el panel de Shopify ve a **Tienda en línea → Páginas**.
2. Crea o edita una página.
3. En la sección **"Plantilla"** (columna derecha), selecciona la plantilla deseada:
   - `page.contacto` → Página de Contacto
   - `page.privacidad` → Aviso de Privacidad
   - `page.terminos` → Términos y Condiciones

---

## Video de fondo

El video `LANDING_VIDEO SIN FIN_LIVIANO.mov` está en la carpeta `assets/`.  
Para usarlo en el tema, referenciarlo con el filtro `asset_url` de Liquid:

```liquid
<video autoplay muted loop playsinline>
  <source src="{{ 'LANDING_VIDEO SIN FIN_LIVIANO.mov' | asset_url }}" type="video/quicktime">
</video>
```

---

## Soporte

📧 hola@saludcompartida.com  
🌐 [saludcompartida.com](https://saludcompartida.com)
