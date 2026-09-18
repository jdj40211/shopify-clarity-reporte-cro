# Reporte semanal de conversión con Claude: Shopify + Microsoft Clarity

Una rutina de **Claude Code** que cada lunes lee los datos de tu tienda Shopify y de Microsoft Clarity, analiza el embudo de conversión y te manda un reporte por correo con recomendaciones que apuntan a archivos reales de tu tema.

## 👉 [Abrir el prompt (PROMPT.md)](PROMPT.md)

Cópialo, reemplaza los valores entre `{{ }}` con los datos de tu tienda y pégalo en tu rutina de Claude Code. Abajo está el paso a paso.

## 📬 Así llega el correo

👉 **[Ver un ejemplo completo del reporte](docs/ejemplo-reporte.md)**

| Métrica | Esta semana | Semana anterior | Variación |
|---|--:|--:|--:|
| Sesiones | 1.520 | 1.406 | 🟢 ▲ +8,1 % |
| Tasa de conversión | 0,94 % | 0,58 % | 🟢 ▲ +61,2 % |
| Pedidos | 21 | 44 | 🔴 ▼ -52,3 % |
| Ventas (COP) | $5.880.000 | $9.480.000 | 🔴 ▼ -38,0 % |

> **Mayor fuga:** checkout → compra (21,4 %). El tráfico de búsqueda convirtió 0 %.
> **Recomendación #1:** en `src/components/SearchOverlay.jsx`, mostrar productos más vendidos cuando la búsqueda no encuentra nada.

*Cifras ilustrativas.*

## Qué trae el reporte

1. Resumen ejecutivo de la semana
2. KPIs contra la semana anterior: sesiones, tasa de conversión, pedidos, ventas y ticket promedio
3. Embudo: sesiones → carrito → checkout → compra, con la mayor fuga marcada
4. Diferencias por dispositivo y por fuente de tráfico
5. Top 5 de productos
6. Fricción detectada en Clarity: rage clicks, dead clicks, scroll y errores de JS
7. De 3 a 5 recomendaciones priorizadas, cada una ligada a una sección o archivo del tema

## Cómo funciona

```
Rutina de Claude Code (cron: lunes 6:00 a. m. hora Bogotá)
   │
   ├─ clona el repo de tu tema Shopify (para citar archivos reales)
   ├─ Shopify Admin GraphQL API → ShopifyQL (sesiones, ventas, embudo)
   ├─ Microsoft Clarity Data Export API → comportamiento (últimos 3 días)
   └─ Gmail connector → te envía el reporte en HTML
```

La rutina es de **solo lectura**: no hace commits, no escribe en Shopify y nunca imprime tokens.

## Requisitos

- Claude Code con acceso a rutinas (claude.ai/code)
- Una tienda Shopify y el código de tu tema en un repo de GitHub
- Un proyecto en [Microsoft Clarity](https://clarity.microsoft.com) instalado en la tienda
- El conector de Gmail activado en Claude

## Paso a paso

### 1. Instala Clarity en el tema

Crea `snippets/clarity.liquid` con el script de tu proyecto de Clarity y llámalo en `layout/theme.liquid` justo después de `{{ content_for_header }}`:

```liquid
{% render 'clarity' %}
```

Recomendación: envuelve el script en `{% unless request.design_mode %}` para no grabar las sesiones del editor de temas.

> Los bloqueadores de anuncios bloquean `clarity.ms`. Para comprobar que carga, usa una ventana de incógnito.

### 2. Consigue el token de Clarity

En Clarity, ve a **Settings → Data Export → Generate new API token**. La API solo entrega los últimos 1–3 días y permite 10 llamadas diarias por proyecto.

### 3. Consigue un token de Shopify (Admin API)

Crea una app en el [Dev Dashboard de Shopify](https://dev.shopify.com) con los scopes `read_orders`, `read_products` y `read_reports`.

- **Si la app y la tienda están en la misma organización**, puedes usar el grant de *client credentials*.
- **Si están en organizaciones distintas**, ese grant responde `400 shop_not_permitted`. En ese caso:
  1. Configura la app con distribución personalizada e instálala en la tienda.
  2. Agrega un `redirect_uri` a la versión de la app (por ejemplo `https://example.com/callback`).
  3. Haz una sola vez el flujo de *authorization code grant*. Así obtienes un **token offline** (`shpca_…`) que no expira.

### 4. Crea el entorno de nube

En claude.ai/code, crea un entorno para la rutina:

- **Variables de entorno**: las de [`.env.example`](.env.example)
- **Red personalizada**: permite `TU-TIENDA.myshopify.com`, `www.clarity.ms` y `shopify.dev`

### 5. Crea la rutina

- **Repo**: el de tu tema Shopify
- **Entorno**: el que creaste en el paso 4
- **Herramientas**: `Bash`, `Read`, `Glob`, `Grep` y el conector de Gmail
- **Cron**: `0 11 * * 1` (lunes 11:00 UTC = 6:00 a. m. en Bogotá)
- **Prompt**: el contenido de [`PROMPT.md`](PROMPT.md) con tus datos en los `{{ }}`

Ejecútala una vez a mano para comprobar que el correo te llega.

## Cosas que aprendimos

- La consulta ShopifyQL del embudo está probada con la API `2026-07`. Las columnas de comparación llegan como `comparison_<métrica>__previous_period`.
- `conversion_rate` llega como fracción: `0.0091` equivale a 0,91 %.
- En la API `2026-07`, el modelo `sessions` de ShopifyQL **no tiene columna de dispositivo**: probamos `device_type`, `device` y `device_category` y ninguna existe. Para el desglose por dispositivo, usa Clarity (`dimension1=Device`).
- En la primera corrida, Clarity respondió **403**. Revisa que el `CLARITY_TOKEN` del entorno de nube sea el mismo que probaste en local.
- Clarity solo tiene datos desde el día en que lo instalaste y su API solo devuelve los últimos 3 días. Shopify cubre la semana completa y Clarity aporta el contexto de comportamiento.

## Seguridad

- No subas tokens al repo. `.env` ya está en el `.gitignore`.
- Los tokens van solo en las variables del entorno de nube.
- Si un token se filtra, revócalo: en Shopify, desinstala la app; en Clarity, borra el token.

## Licencia

MIT
