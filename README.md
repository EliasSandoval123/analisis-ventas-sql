# Análisis de Ventas con SQL Avanzado

## Contexto de negocio
Una empresa con 5 vendedores y presencia en 5 regiones necesita entender
el rendimiento comercial por vendedor, producto y cliente. Este proyecto
aplica SQL avanzado para responder preguntas clave de negocio.

## Técnicas SQL demostradas
| Técnica | Aplicación |
|---------|-----------|
| `RANK()` | Ranking de vendedores por revenue |
| `CTE + LAG()` | Crecimiento mensual mes a mes |
| `ROW_NUMBER() + PARTITION BY` | Top 3 productos por región |
| Subconsulta con `HAVING` | Clientes sobre ticket promedio |

## Hallazgos principales
- Rodrigo Pérez lidera en revenue con $10.9M, seguido de Andrea Soto con $10.9M
- Laptop Pro 15 es el producto dominante en 4 de 5 regiones
- Septiembre tuvo el mayor crecimiento mensual (+164% vs agosto)
- Empresa S y Empresa I tienen el ticket promedio más alto ($1.7M por compra)

## Queries destacados

### Ranking de vendedores
```sql
SELECT 
    vendedor,
    ROUND(SUM(cantidad * precio_unitario * (1 - descuento)), 0) AS revenue_total,
    RANK() OVER (ORDER BY SUM(cantidad * precio_unitario * (1 - descuento)) DESC) AS ranking
FROM ventas
GROUP BY vendedor
ORDER BY ranking;
```

### Crecimiento mensual con CTE y LAG
```sql
WITH ventas_mensuales AS (
    SELECT 
        STRFTIME('%Y-%m', fecha) AS mes,
        ROUND(SUM(cantidad * precio_unitario * (1 - descuento)), 0) AS revenue
    FROM ventas
    GROUP BY mes
)
SELECT 
    mes,
    revenue,
    LAG(revenue) OVER (ORDER BY mes) AS revenue_mes_anterior,
    ROUND(
        (revenue - LAG(revenue) OVER (ORDER BY mes)) * 100.0 / 
        LAG(revenue) OVER (ORDER BY mes), 1
    ) AS crecimiento_pct
FROM ventas_mensuales
ORDER BY mes;
```

## Stack
![SQL](https://img.shields.io/badge/SQLite-003B57?logo=sqlite&style=flat-square&logoColor=white)

## Archivos
- `ventas_sample.csv` — dataset de 100 ventas (ene–oct 2024)

## Cómo usar
1. Abre DB Browser for SQLite
2. Nueva base de datos → importa `ventas_sample.csv` como tabla `ventas`
3. Ejecuta los queries en la pestaña SQL
