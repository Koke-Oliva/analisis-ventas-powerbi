# Notas técnicas y reproducibilidad

## Apertura
1. Descarga `AnalisisVentas-PBIP.zip`.
2. Descomprime el archivo.
3. Abre `AnalisisVentas.pbip` con Power BI Desktop.

El paquete incluye `AnalisisVentas.Report/` y `AnalisisVentas.SemanticModel/` y excluye la caché local `.pbi/`.

## Fuentes de datos
Los archivos Excel y CSV originales pertenecen al material académico del curso y no se redistribuyen. Para evitar exponer rutas personales, las consultas usan rutas genéricas:

```text
C:\Data\AnalisisVentas\Ejer PBI.xlsx
C:\Data\AnalisisVentas\Clientes.csv
```

Para actualizar el modelo es necesario apuntar esas consultas a las fuentes correspondientes en Power BI Desktop.

## Decisiones de modelado
- `Calendario` funciona como dimensión temporal común para `Facturas` y `Cobranzas`.
- `Saldo Pendiente` conserva el signo de `[Total Ventas] - [Total Cobros]`, evitando ocultar un eventual sobrecobro mediante `ABS()`.
- `Clientes con venta` se calcula sobre `Facturas` para responder al contexto de filtros temporales.
- Algunas medidas originales de la evaluación se conservan ocultas como trazabilidad; el informe utiliza las medidas refactorizadas.

## Alcance analítico
- Los datos de 2016 son parciales hasta julio.
- `Tasa Cobranza` es una razón agregada entre cobros y ventas dentro del contexto de filtro; no representa recuperación por cohorte de factura.
- Los Top 6 de vendedores y canales se definieron por ventas acumuladas del período completo. Los filtros modifican sus valores, pero no recalculan dinámicamente el conjunto de seis categorías.

## Revisión técnica rápida
- `src/semantic-model/Medidas.tmdl`: medidas DAX.
- `src/semantic-model/Calendario.tmdl`: dimensión temporal.
- `src/semantic-model/relationships.tmdl`: relaciones principales.
- `src/report/`: metadatos PBIR del informe.
