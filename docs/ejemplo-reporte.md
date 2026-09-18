# Ejemplo del correo que llega

> Este es el formato real que genera la rutina. **Las cifras, productos y nombres son ilustrativos**: no corresponden a una tienda real.
> En Gmail llega como HTML con tablas; aquí está en Markdown para que se lea en GitHub.

---

**Asunto:** `Mi Tienda · Conversión 07 sep – 13 sep 2026`

## Mi Tienda · Reporte semanal de conversión
Periodo: 07 sep – 13 sep 2026 (comparado con 31 ago – 06 sep 2026) · Zona horaria: America/Bogota

### 1. Resumen ejecutivo
Las sesiones crecieron 8,1 % y la tasa de conversión subió a 0,94 % (+61 % frente a la semana anterior), con más agregados al carrito y más sesiones que llegaron a checkout. Aun así, **las ventas cayeron 38 %** porque hubo menos pedidos, aunque el ticket promedio subió 29 %. La mayor fuga sigue estando entre "llegó a checkout" y "completó la compra", y el tráfico de búsqueda no generó ninguna compra esta semana.

### 2. KPIs

| Métrica | Esta semana | Semana anterior | Variación |
|---|--:|--:|--:|
| Sesiones | 1.520 | 1.406 | 🟢 ▲ +8,1 % |
| Tasa de conversión | 0,94 % | 0,58 % | 🟢 ▲ +61,2 % |
| Pedidos | 21 | 44 | 🔴 ▼ -52,3 % |
| Ventas (COP) | $5.880.000 | $9.480.000 | 🔴 ▼ -38,0 % |
| Ticket promedio | $280.000 | $215.455 | 🟢 ▲ +29,9 % |

### 3. Embudo de conversión

| Etapa | Sesiones | % del paso anterior |
|---|--:|--:|
| Sesiones totales | 1.520 | — |
| Agregaron al carrito | 95 | 6,3 % |
| Llegaron a checkout | 70 | 73,7 % |
| Completaron la compra | 15 | **21,4 %** 🔴 |

**Mayor fuga:** casi 8 de cada 10 sesiones que llegan a checkout no terminan la compra.

### 4. Dispositivo y fuentes de tráfico

| Fuente | Sesiones | % del total | Conversión |
|---|--:|--:|--:|
| Social | 790 | 52,0 % | 0,76 % |
| Directo | 410 | 27,0 % | 1,71 % |
| Búsqueda | 315 | 20,7 % | **0,00 %** |
| Desconocido | 5 | 0,3 % | 0,00 % |

Social trae más de la mitad del tráfico, pero convierte menos de la mitad que directo.

### 5. Productos

| Producto | Ventas | Pedidos |
|---|--:|--:|
| Chaqueta Técnica Negra | $450.000 | 1 |
| Camiseta Reflectiva 02 | $340.000 | 2 |
| Licra Ciclista | $310.000 | 2 |
| Short Trail | $280.000 | 2 |
| Camiseta Reflectiva 01 | $260.000 | 3 |

Los 5 productos más vendidos suman solo el 28 % de las ventas: la demanda está muy repartida y conviene reforzar los que más venden.

### 6. Comportamiento (Microsoft Clarity, últimos 3 días)
412 sesiones (18 de bots), 37 rage clicks y 64 dead clicks, concentrados en la galería de la página de producto y en el selector de tallas. Scroll promedio: 48 %. Hubo 3 errores de script en `/cart`.

### 7. Recomendaciones

1. **[Alto impacto / bajo esfuerzo] Recuperar las búsquedas sin resultados.** En `src/components/SearchOverlay.jsx`, cuando la búsqueda no encuentra nada solo aparece un texto. Mostrar ahí los productos más vendidos y enlaces a colecciones.
2. **[Alto impacto / esfuerzo medio] Bajar el abandono en checkout.** En `src/components/CartDrawer.jsx`, mostrar los métodos de pago y las garantías antes del botón de checkout.
3. **[Impacto medio / bajo esfuerzo] Destacar los más vendidos.** Agregar una etiqueta de "Más vendido" en `snippets/product-card.liquid`.
4. **[Impacto medio / esfuerzo medio] Revisar el selector de tallas.** Clarity marca dead clicks ahí. Revisar el componente de variantes en `sections/main-product.liquid`.

---
*Fuentes: Shopify Admin GraphQL API (ShopifyQL, 2026-07) y Microsoft Clarity Data Export API. Dato no disponible: desglose por dispositivo (esa columna no existe en ShopifyQL).*
