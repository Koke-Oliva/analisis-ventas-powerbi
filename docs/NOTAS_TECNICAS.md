# Notas técnicas y reproducibilidad

## Dos paquetes disponibles

### 1. Demo reproducible

Archivo: `AnalisisVentas-PBIP-Demo.zip`

Esta es la opción recomendada para quien quiera **abrir, actualizar e interactuar con el proyecto sin disponer de los archivos académicos originales**.

La demo conserva:

- las tres páginas del informe;
- el modelo semántico;
- las relaciones;
- las medidas DAX;
- los filtros y segmentadores;
- la estructura PBIP / PBIR / TMDL.

Las tablas `Clientes`, `Canales`, `Vendedores`, `Facturas` y `Cobranzas` usan datos sintéticos generados localmente en Power Query. No requieren rutas externas ni credenciales.

Los valores de esta demo son demostrativos y **no deben compararse con las cifras ni capturas del proyecto original**.

### 2. Proyecto original

Archivo: `AnalisisVentas-PBIP.zip`

Conserva las consultas del proyecto académico/refactorizado y permite revisar la estructura del informe y del modelo.

Las fuentes originales no se redistribuyen. Las consultas mantienen rutas genéricas como:

```text
C:\Data\AnalisisVentas\Ejer PBI.xlsx
C:\Data\AnalisisVentas\Clientes.csv
```

Por ello, el paquete original **no es reproducible de extremo a extremo por un tercero**: para actualizar el modelo se necesitan los archivos fuente correspondientes y deben reconfigurarse sus rutas en Power BI Desktop.

## Apertura

Para cualquiera de los paquetes:

1. Descarga el archivo ZIP.
2. Descomprímelo en una carpeta local.
3. Abre `AnalisisVentas.pbip` con una versión reciente de Power BI Desktop.
4. En la demo reproducible, si Power BI solicita aplicar cambios o actualizar el modelo, acepta la actualización.

El paquete excluye la caché local `.pbi/`, que no forma parte del código fuente versionable.

## Verificación estructural realizada

Se validó el paquete PBIP publicado a nivel de estructura:

- `AnalisisVentas.pbip` referencia correctamente a `AnalisisVentas.Report/`;
- `definition.pbir` referencia correctamente a `../AnalisisVentas.SemanticModel`;
- el informe contiene **3 páginas**;
- el paquete contiene **26 visualizaciones**;
- los archivos JSON del informe son sintácticamente válidos;
- el modelo incluye las tablas `Clientes`, `Canales`, `Cobranzas`, `Facturas`, `Vendedores`, `Calendario` y `Medidas`;
- las relaciones principales están definidas en TMDL;
- el filtro de los Top 6 se conserva como selección categórica fija del período completo, evitando la definición Top N que generaba incompatibilidades en versiones previas del PBIR;
- en la demo reproducible no permanecen rutas `C:\Data\AnalisisVentas\...`.

Esta comprobación es una **validación estructural del artefacto PBIP**. La apertura final depende de la versión de Power BI Desktop instalada en el equipo del usuario.

## Decisiones de modelado

- `Calendario` funciona como dimensión temporal común para `Facturas` y `Cobranzas`.
- `Saldo Pendiente` conserva el signo de `[Total Ventas] - [Total Cobros]`, evitando ocultar un eventual sobrecobro mediante `ABS()`.
- `Clientes con venta` se calcula sobre `Facturas` para responder al contexto de filtros temporales.
- Algunas medidas originales de la evaluación se conservan ocultas como trazabilidad; el informe utiliza las medidas refactorizadas.

## Alcance analítico

- Los datos originales de 2016 son parciales hasta julio.
- `Tasa Cobranza` es una razón agregada entre cobros y ventas dentro del contexto de filtro; no representa recuperación por cohorte de factura.
- Los Top 6 de vendedores y canales se definieron por ventas acumuladas del período completo. Los filtros modifican sus valores, pero no recalculan dinámicamente el conjunto de seis categorías.
- La demo sintética replica el comportamiento funcional del modelo, no los resultados numéricos originales.

## Revisión técnica rápida

- `src/semantic-model/Medidas.tmdl`: medidas DAX.
- `src/semantic-model/Calendario.tmdl`: dimensión temporal.
- `src/semantic-model/relationships.tmdl`: relaciones principales.
- `src/report/`: metadatos PBIR del informe.
