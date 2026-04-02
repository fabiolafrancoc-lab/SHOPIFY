# SaludCompartida — Shopify Theme

Theme personalizado para la tienda Shopify de **SaludCompartida**.

---

## Flujo completo del usuario

```
Landing (index)
  └─ Botones magenta ───────────────────────────────► /pages/registro
                                                              │
                                        Llena datos (migrante)
                                               │
                                               ▼
                                      /pages/familia
                                    (agrega familia en México)
                                               │
                              Cart API: variante + selling_plan
                              (suscripción digital, sin envío)
                                               │
                                               ▼
                                     Shopify Checkout
                               (30 días gratis → $12/mes)
                               (Sin dirección de envío)
                                               │
                                  Pago completado → redirect
                                               │
                                               ▼
                               /pages/suscripcion-exitosa
                           (Confetti + instrucciones + CTA app)
                                               │
                     ┌─────────────────────────┴─────────────────────────┐
                     ▼                                                   ▼
               Email al migrante                              WhatsApp a la familia
            (código personal + instrucciones)            (código de acceso en México)
```

---

## Estructura del tema

```
layout/
  └── theme.liquid                           ← Envoltorio HTML base
templates/
  ├── index.liquid                           ← Landing (CTAs → /pages/registro)
  ├── page.registro.liquid                   ← Step 1: datos del migrante
  ├── page.familia.liquid                    ← Step 2: familia + checkout con selling plan
  ├── page.suscripcion-exitosa.liquid        ← Página post-suscripción exitosa  ← NUEVA
  ├── page.gracias.liquid                    ← Página post-pago genérica
  ├── page.contacto.liquid
  ├── page.privacidad.liquid
  └── page.terminos.liquid
config/
  ├── settings_schema.json                   ← Campos del personalizador (variant + selling plan IDs)
  └── settings_data.json
sections/
  ├── hero.liquid, benefits.liquid, companions.liquid
  ├── trust.liquid, social-proof.liquid
  ├── final-cta.liquid, header.liquid, footer-note.liquid
snippets/
  ├── sc-icon-check.liquid
  └── sc-checkout-subscription.liquid        ← Botón "Continuar con el Pago" reutilizable  ← NUEVO
source/                                      ← HTML de referencia original (no se sube a Shopify)
assets/
```

---

## Implementación del checkout con suscripción

### IDs de producto/suscripción

| Recurso | GID | Numeric ID |
|---|---|---|
| Product (Plan Familiar I) | `gid://shopify/Product/7759345188941` | `7759345188941` |
| ProductVariant (Plan Familiar I) | `gid://shopify/ProductVariant/42835468845133` | `42835468845133` |
| SellingPlan (Plan Familiar I) | `gid://shopify/SellingPlan/7685865549` | `7685865549` |
| SellingPlanGroup | `gid://shopify/SellingPlanGroup/5804097613` | `5804097613` |

### Cómo funciona el flujo técnico

El botón "Continuar al pago" en `/pages/familia` llama al **Shopify Cart API** con:

```js
{
  items: [{
    id: 42835468845133,        // numeric variant ID
    quantity: 1,
    selling_plan: 7685865549,  // numeric selling plan ID ← suscripción
    properties: { /* datos del migrante + familia */ }
  }]
}
```

Esto crea un carrito con suscripción activa y redirige al checkout nativo de Shopify (`/checkout`).

### Configurar en Theme Settings

Ve a **Tienda en línea → Temas → Personalizar → Suscripción — Pago**:

| Campo | Valor |
|---|---|
| ID variante — Plan Familiar I | `42835468845133` |
| ID variante — Plan Premium | *(ID del variant premium cuando esté disponible)* |
| Selling Plan ID — Plan Familiar I | `7685865549` |
| Selling Plan ID — Plan Premium | *(ID del selling plan premium cuando esté disponible)* |

---

## ⚠️ Configuración para producto digital (sin envío)

Para que Shopify **no pida dirección de envío** en el checkout:

1. **Shopify Admin → Catálogo → Productos → [Plan Familiar I]**
2. Desplázate a la sección **Envío**
3. **Desactiva** la casilla **"Este es un producto físico"**
4. Guarda los cambios

> ✅ Con esto, el checkout nativo de Shopify omite automáticamente la sección de dirección de envío.
> No se requiere ningún cambio en código Liquid.

---

## Configurar redirect post-pago

Para redirigir a `/pages/suscripcion-exitosa` después del checkout:

**Shopify Admin → Configuración → Checkout → Página de estado del pedido → Scripts adicionales**

```liquid
{% if first_time_accessed %}
<script>
  window.location.href =
    '/pages/suscripcion-exitosa?nombre={{ checkout.billing_address.first_name | url_encode }}';
</script>
{% endif %}
```

---

## Snippet reutilizable: `sc-checkout-subscription`

Si necesitas un botón de pago en cualquier otra página o sección:

```liquid
{% render 'sc-checkout-subscription' %}
```

Con overrides opcionales:

```liquid
{% render 'sc-checkout-subscription',
   variant_id: 42835468845133,
   selling_plan_id: 7685865549,
   btn_label: 'Continuar con el Pago'
%}
```

---

## Páginas que crear en Shopify

Ve a **Tienda en línea → Páginas** y crea cada una asignando su plantilla:

| URL (`/pages/...`) | Título sugerido | Plantilla |
|---|---|---|
| `registro` | Registro | `page.registro` |
| `familia` | Tu familia | `page.familia` |
| `suscripcion-exitosa` | ¡Suscripción Activada! | `page.suscripcion-exitosa` |
| `gracias` | ¡Bienvenido! | `page.gracias` |
| `contacto` | Contáctanos | `page.contacto` |
| `privacidad` | Aviso de Privacidad | `page.privacidad` |
| `terminos` | Términos y Condiciones | `page.terminos` |

---

## TODO — Página de consultas

> **Pendiente:** El HTML de la página de consultas no ha sido entregado aún.
> Una vez recibido, crear `templates/page.consultas.liquid` y agregar
> la página en Shopify Admin con handle `consultas`.

---

## Webhook de suscripciones (placeholder)

Cuando esté disponible un backend, registrar el webhook en:

**Shopify Admin → Configuración → Notificaciones → Webhooks**

| Evento | Topic | URL destino |
|---|---|---|
| Suscripción creada | `subscription_contracts/create` | `https://tu-backend.com/webhooks/subscription-created` |
| Suscripción cancelada | `subscription_contracts/cancel` | `https://tu-backend.com/webhooks/subscription-cancelled` |

El payload incluye `customer`, `selling_plan`, y `line_items`. Usar para:
- Activar acceso en la plataforma
- Enviar códigos por email/WhatsApp (via Resend)
- Registrar en Supabase

---

## Cómo subir el tema a Shopify

### Opción 0 — GitHub (recomendado)

1. Shopify Admin → **Tienda en línea → Temas**
2. **Agregar tema** → **Connect from GitHub**
3. Autoriza GitHub si lo solicita
4. Selecciona el repo `SHOPIFY` y la rama `main`
5. Shopify crea un **nuevo theme** desde GitHub
6. Pulsa **Publicar** en ese theme

> Nota: Shopify no "sincroniza" un theme ya publicado si no esta conectado.
> Si cambia GitHub, vuelve a conectar o crea un theme nuevo desde GitHub.

### Opción A — ZIP

1. Descarga **`saludcompartida-theme.zip`** de este repositorio
2. Shopify Admin → **Tienda en línea → Temas → Agregar tema → Subir archivo ZIP**

### Opción B — Shopify CLI

```bash
npm install -g @shopify/cli @shopify/theme
shopify theme push --store tu-tienda.myshopify.com
```

---

## Soporte

📧 contact@saludcompartida.com  
🌐 [saludcompartida.com](https://saludcompartida.com)  
📱 [saludcompartida.app](https://saludcompartida.app)
