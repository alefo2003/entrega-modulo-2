 Modelado de Datos y Cálculo de Medidas DAX

Este repositorio contiene la implementación del modelo relacional y la capa de análisis mediante fórmulas DAX en Power BI para el sistema de gestión de ventas y stock.

---

## 1. Verificación del Modelo de Datos

Se estructuró el modelo bajo un esquema en estrella (**Star Schema**) conectando cuatro tablas de dimensiones a la tabla central de hechos, garantizando la integridad referencial y optimizando el rendimiento de las consultas.

### Estructura de Relaciones
- **`Dim_Cliente[ID_Cliente]`** $\rightarrow$ `Hechos_Ventas[ID_Cliente]` *(1:* | Dirección: Única)*
- **`Dim_Producto[ID_Producto]`** $\rightarrow$ `Hechos_Ventas[ID_Producto]` *(1:* | Dirección: Única)*
- **`Dim_Sucursal[ID_Sucursal]`** $\rightarrow$ `Hechos_Ventas[ID_Sucursal]` *(1:* | Dirección: Única)*
- **`Dim_Calendario[Date]`** $\rightarrow$ `Hechos_Ventas[Fecha]` *(1:* | Dirección: Única)*

> **Nota:** La tabla continua `Dim_Calendario` fue generada dinámicamente mediante DAX, ordenada por el campo `Mes Nro` y configurada formalmente como **Tabla de Fechas**.

---

## 2. Documentación de Medidas DAX

Todas las métricas fueron creadas en una tabla dedicada de medidas (`_Medidas`) organizada en tres categorías principales:

### Medidas Base
* **Total Ventas:** Suma del importe total de las transacciones.
  ```dax
  Total Ventas = SUM(Hechos_Ventas[Importe_Venta])
  Total Costos = SUM(Hechos_Ventas[Costo_Total])
  Total Transacciones = COUNTROWS(Hechos_Ventas)
  Clientes Únicos = DISTINCTCOUNT(Hechos_Ventas[ID_Cliente])
  Utilidad Bruta = [Total Ventas] - [Total Costos]
  Margen Bruto % = DIVIDE([Utilidad Bruta], [Total Ventas], 0)
  % Ventas sobre Total = 
DIVIDE(
    [Total Ventas],
    CALCULATE([Total Ventas], ALL(Dim_Producto)),
    0
)
Ventas Año Anterior = 
CALCULATE(
    [Total Ventas],
    SAMEPERIODLASTYEAR(Dim_Calendario[Date])
)
Crecimiento Ventas % = 
VAR VentasLY = [Ventas Año Anterior]
VAR Diferencia = [Total Ventas] - VentasLY
RETURN
    DIVIDE(Diferencia, VentasLY, 0)
  <img width="2560" height="1440" alt="image" src="https://github.com/user-attachments/assets/1fb102dc-4651-426d-b147-dfde9c8cfafc" />
 
