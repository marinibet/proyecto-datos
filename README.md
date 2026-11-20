# Análisis de las categorías de mora

Equipo E__ · Análisis de Datos Intermedio · Estud-IA (Capa 2)

## Brief v1

| Campo | Respuesta |
|---|---|
| Usuario y decisión | |
| Pregunta | ¿Qué entidad financiera presenta la mayor cantidad de clientes en cada categoría de calificación de riesgo (A a E) y cómo se comporta esta distribución frente a las demás entidades? |
| Fuente con API | https://www.datos.gov.co/resource/rvii-eis8.json |
| Qué representa una fila | El saldo mensual de cartera de un producto de crédito en una entidad financiera, con su altura de mora y su distribución por calificación de riesgo (número de clientes y saldo en A, B, C, D y E). |
| Campo que la identifica | `fecha_corte` + `tipo_entidad` + `codigo_entidad` + `unicap` + `renglon` |
| Cada cuánto se actualiza | Mensualmente |
| Dato a confirmar | Transformar los datos a su tipo correspondiente (la API entrega los números como texto). Confirmar que la combinación de campos que identifica la fila no se repite, y si la columna `renglon` incluye filas de subtotal. |
| Tres indicadores | 1. Cantidad de clientes por calificación de riesgo (A a E) en cada entidad. · 2. Participación de cada calificación dentro del total de clientes calificados de la entidad (%). · 3. Saldo de cartera por calificación de riesgo y participación de la cartera riesgosa (B a E) en el saldo total. |
| Terminó bien si… | Hay una tabla con el número y el porcentaje de clientes en cada calificación (A a E) para cada entidad y mes, y los totales cuadran con la fuente (sin filas duplicadas). |
| No vamos a construir | No vamos a identificar qué factores influyen en la calificación de riesgo de los bancos. |

## Contrato de datos

- Una fila es: el saldo de cartera de un producto de crédito (`unicap` + `renglon`) en una entidad financiera (`tipo_entidad` + `codigo_entidad`) en un mes (`fecha_corte`), con su altura de mora, el número de clientes con mora mayor a 30 días y el número de clientes y el saldo en cada calificación de riesgo (A a E).
- La identifica: `fecha_corte` + `tipo_entidad` + `codigo_entidad` + `unicap` + `renglon`. Se incluye `tipo_entidad` porque el mismo código de entidad puede repetirse entre tipos de entidad (por ejemplo, un banco y una cooperativa financiera).
- Si llega una fila repetida: se conserva una sola (la última cargada) y se anota en la bitácora cuántas se descartaron.
- Si falta un campo: si falta un campo de identificación, la fila se descarta y se anota en la bitácora. Si falta un valor numérico, se deja vacío (no se reemplaza por 0) y la fila se excluye solo del indicador que use ese valor.

## Bitácora de decisiones

| Fecha | Decisión | Por qué |
|---|---|---|
| 2026-11-17 | Usar el dataset "Distribución de cartera por producto" de la Superintendencia Financiera (rvii-eis8). | Trae el número de clientes y el saldo en cada calificación de riesgo (A a E), la mora por altura y el número de clientes con mora mayor a 30 días, por entidad y producto, mensual desde 2015. |
| 2026-11-17 | Enfocar el proyecto en comparar la distribución de clientes por calificación de riesgo entre entidades, no en predecir ni en explicar las causas de la mora. | Los datos son agregados por entidad y producto; no hay datos de personas para estudiar perfiles ni causas. |
| 2026-11-17 | Usar el endpoint sin `?$limit=5`. | Con ese parámetro la API devuelve solo 5 filas. |
| 2026-11-17 | Medir los clientes por calificación (A a E), no "clientes morosos por calificación". | El dataset trae el número de clientes en cada calificación y, aparte, el total de clientes con mora mayor a 30 días; no cruza ambos datos. |

## Reglas del repositorio

- Es público: nada de datos personales, contraseñas, tokens ni llaves.
- Las credenciales van en un archivo `.env`, que Git ignora (ver `.gitignore`). `.env.example` muestra qué variables hacen falta, sin valores.
- Cada commit explica en su mensaje qué se decidió y por qué.
