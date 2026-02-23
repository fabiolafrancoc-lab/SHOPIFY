# SaludCompartida — Shopify Theme

Theme personalizado para la tienda Shopify de **SaludCompartida**.

---

## Estructura del tema

```
saludcompartida-theme/
├── assets/               ← Archivos estáticos (video, imágenes, foto registro)
├── config/               ← Configuración del tema
│   ├── settings_schema.json
│   └── settings_data.json
├── layout/               ← Plantilla base de todas las páginas
│   └── theme.liquid
├── locales/              ← Traducciones
│   └── en.default.json
├── sections/             ← Secciones de la página de inicio
│   ├── hero.liquid            (Hero principal)
│   ├── benefits.liquid        (Beneficios)
│   ├── companions.liquid      (Lupita & Fernanda)
│   ├── trust.liquid           (Confianza / prueba social)
│   ├── social-proof.liquid    (Testimonios)
│   ├── final-cta.liquid       (CTA final)
│   ├── header.liquid          (Encabezado)
│   └── footer-note.liquid     (Pie de página legal)
├── snippets/             ← Fragmentos reutilizables
└── templates/            ← Plantillas de página
    ├── index.liquid                (Página de inicio)
    ├── page.registro-jan.liquid    (Registro completo — UN SOLO PASO)
    ├── page.registro.liquid        (Registro Paso 1 — datos del migrante)
    ├── page.familia.liquid         (Registro Paso 2 — datos familia México)
    ├── page.contacto.liquid        (Contáctanos)
    ├── page.privacidad.liquid      (Aviso de Privacidad)
    └── page.terminos.liquid        (Términos y Condiciones)
```

---

## Flujo de pago (registro → Shopify Checkout)

```
[page.registro-jan] → guarda en Supabase → agrega producto al carrito → /checkout
```

El formulario `page.registro-jan.liquid` sigue este flujo automáticamente:
1. Usuario llena sus datos y los de un familiar
2. Los datos se guardan en Supabase (policy_holders + beneficiaries)
3. Se agrega la suscripción al carrito de Shopify (`/cart/add.js`)
4. Se redirige al checkout nativo de Shopify (`/checkout`) con el email pre-cargado

### ⚠️ Configuración requerida después de subir el tema

Debes configurar dos valores en el panel de Shopify para que el pago funcione:

#### 1. Configura los IDs de variante del producto de suscripción

1. En Shopify Admin → **Productos** → abre tu producto de suscripción (el "Plan Básico")
2. Haz clic en la variante → copia el número al final de la URL, ej: `.../variants/`**`40123456789012`**
3. Ve a **Tienda en línea → Temas → Personalizar**
4. En el panel izquierdo busca **"Suscripción — Pago"**
5. Pega el ID en **"ID variante — Plan Básico ($12/mes)"**
6. Repite para el Plan Premium si aplica

#### 2. Configura las credenciales de Supabase

En el archivo `templates/page.registro-jan.liquid` reemplaza:
- `SUPABASE_URL_PLACEHOLDER` → tu URL de Supabase (ej: `https://xxxx.supabase.co`)
- `SUPABASE_ANON_KEY_PLACEHOLDER` → tu anon key de Supabase

**⚠️ Importante:** nunca subas estas credenciales a un repositorio público. Configúralas directamente en el editor de código de Shopify Admin.

---

## Cómo subir el tema a Shopify

### Opción 1 — Subir el archivo ZIP (más fácil)

1. Descarga el archivo **`saludcompartida-theme.zip`** de este repositorio.
2. En tu panel de administración de Shopify, ve a **Tienda en línea → Temas**.
3. Haz clic en **"Agregar tema" → "Subir archivo ZIP"**.
4. Selecciona el archivo `saludcompartida-theme.zip`.
5. Shopify procesará el tema. Una vez subido, haz clic en **"Personalizar"** para configurar los IDs de variante (ver arriba), luego **"Publicar"**.

### Opción 2 — Shopify CLI (para desarrolladores)

```bash
npm install -g @shopify/cli @shopify/theme
shopify theme push --store tu-tienda.myshopify.com
```

---

## Páginas alternativas

Las plantillas `page.*.liquid` son **plantillas alternativas de página**. Para usarlas:

1. En el panel de Shopify ve a **Tienda en línea → Páginas**.
2. Crea o edita una página.
3. En la sección **"Plantilla"** (columna derecha), selecciona la plantilla deseada:
   - `page.registro-jan` → Registro completo (un solo paso)
   - `page.registro` → Registro Paso 1
   - `page.familia` → Registro Paso 2
   - `page.contacto` → Página de Contacto
   - `page.privacidad` → Aviso de Privacidad
   - `page.terminos` → Términos y Condiciones

---

## Soporte

📧 hola@saludcompartida.com  
🌐 [saludcompartida.com](https://saludcompartida.com)
