# SaludCompartida — Shopify Theme

Theme personalizado para la tienda Shopify de **SaludCompartida**.

---

## Flujo completo del usuario

```
Landing (index)
  └─ 6 botones magenta ──────────────────────────────► /pages/registro-jan
                                                              │
                                        Llena datos (migrante + familiar)
                                        Guarda en Supabase (policy_holders + beneficiaries)
                                        Agrega suscripción al carrito de Shopify
                                              │
                                              ▼
                                    Shopify Checkout
                               (30 días gratis → $12/mes)
                               (Sin impuestos — servicio en México)
                                              │
                                 Pago completado → redirect
                                              │
                                              ▼
                                   /pages/gracias
                              (Confetti + firmas + instrucciones)
                                              │
                    ┌─────────────────────────┴─────────────────────────┐
                    ▼                                                   ▼
              Email al migrante                              WhatsApp a la familia
           (código personal + instrucciones)            (código de acceso en México)
                    │                                         [vía Resend — automático]
                    └─────────────────────────┬─────────────────────────┘
                                              ▼
                                   saludcompartida.app
                              (usuario entra su código → dashboard)
```

> Shopify interviene **solo** en los pasos: Landing → registro-jan → Checkout → /pages/gracias.
> A partir de /pages/gracias, todo es automático via Supabase + Resend.

---

## Estructura del tema

```
layout/
  └── theme.liquid                     ← Envoltorio HTML base
templates/
  ├── index.liquid                     ← Landing (6 CTAs → /pages/registro-jan)
  ├── page.registro-jan.liquid         ← Formulario de registro completo
  ├── page.gracias.liquid              ← Página post-pago (payment success)
  ├── page.contacto.liquid
  ├── page.privacidad.liquid
  └── page.terminos.liquid
config/
  ├── settings_schema.json             ← Define campos del personalizador
  └── settings_data.json
locales/
  └── en.default.json
assets/
  ├── registro-photo.jpg               ← Foto izquierda del formulario de registro
  ├── LANDING_VIDEO SIN FIN_LIVIANO.mov
  └── saludcompartida-transp dark-bg-no-tagline copy 2.jpg
sections/
  ├── hero.liquid, benefits.liquid, companions.liquid
  ├── trust.liquid, social-proof.liquid
  ├── final-cta.liquid, header.liquid, footer-note.liquid
snippets/
source/                                ← HTML de referencia original (no se sube a Shopify)
```

---

## Cómo subir el tema a Shopify

### Opción A — ZIP (más rápido)

1. Descarga **`saludcompartida-theme.zip`** de este repositorio
2. Shopify Admin → **Tienda en línea → Temas → Agregar tema → Subir archivo ZIP**
3. Selecciona el ZIP → Shopify lo procesa automáticamente
4. Haz clic en **"Personalizar"** para configurar los valores requeridos (ver abajo)
5. Haz clic en **"Publicar"**

### Opción B — Shopify CLI

```bash
npm install -g @shopify/cli @shopify/theme
shopify theme push --store tu-tienda.myshopify.com
```

---

## Configuración requerida después de subir

Ve a **Tienda en línea → Temas → Personalizar** y configura estos 4 campos:

| Sección | Campo | Valor |
|---|---|---|
| **Suscripción — Pago** | ID variante — Plan Básico | Admin → Productos → Plan Básico → edita la variante → número al final de la URL |
| **Contacto** | WhatsApp Business URL | Tu link de WhatsApp, ej: `https://wa.me/15551234567` |
| **Supabase — Integración** | Supabase URL | Supabase Dashboard → Settings → API → Project URL |
| **Supabase — Integración** | Supabase Anon Key | Supabase Dashboard → Settings → API → anon / public key |

---

## Configurar redirect post-pago (IMPORTANTE)

Después del checkout, Shopify muestra su propia página de confirmación de pedido.
Para redirigir automáticamente a `/pages/gracias`, agrega este script en:

**Shopify Admin → Configuración → Checkout → Página de estado del pedido → Scripts adicionales**

```liquid
{% if first_time_accessed %}
<script>
  window.location.href =
    '/pages/gracias?nombre={{ checkout.billing_address.first_name | url_encode }}';
</script>
{% endif %}
```

Esto:
- Solo ejecuta el redirect la **primera vez** que el cliente ve la página de confirmación
- Pasa el nombre del cliente como parámetro para personalizar el mensaje de bienvenida en `/pages/gracias`

---

## Páginas que crear en Shopify

Ve a **Tienda en línea → Páginas** y crea cada una asignando su plantilla:

| URL (`/pages/...`) | Título sugerido | Plantilla | Linked desde |
|---|---|---|---|
| `registro-jan` | Registro | `page.registro-jan` | Los 6 botones magenta de la landing |
| `gracias` | ¡Bienvenido! | `page.gracias` | Shopify Additional Scripts (post-checkout) |
| `contacto` | Contáctanos | `page.contacto` | Footer de la landing |
| `privacidad` | Aviso de Privacidad | `page.privacidad` | Footer de la landing |
| `terminos` | Términos y Condiciones | `page.terminos` | Footer de la landing |

---

## Suscripción Shopify

El producto de suscripción ya está configurado en Shopify:
- **30 días gratis** → el primer cargo es $0.00
- **$12 USD/mes** a partir del día 31, cobro automático
- **Sin impuestos** (el servicio se presta en México, fuera de EE.UU.)

---

## Soporte

📧 contact@saludcompartida.com  
🌐 [saludcompartida.com](https://saludcompartida.com)  
📱 [saludcompartida.app](https://saludcompartida.app) — app del usuario final
