# Práctica XML y DTD

## Objetivo
Diseñar, estructurar y validar documentos XML a partir de información no estructurada y esquemas DTD, gestionando los artefactos de manera incremental mediante Git.

## Ejercicio 1: De texto no estructurado a XML

### Modelo propuesto
A partir del texto del pedido, se identificaron los siguientes campos y elementos para permitir búsquedas independientes:

| Información | Valor identificado | Elemento XML propuesto |
|---|---|---|
| Destinatario | Juan Delgado Martínez | `<destinatario>` (`<nombre>`, `<apellidos>`) |
| Artículo | Bicicleta Bianchi | `<articulo>` (`<descripcion>`) |
| Dirección | calle Reforma 423, interior 201 | `<direccion>` (`<calle>`, `<numero>`, `<interior>`) |
| Fecha | 19-09-2021 | `<fecha>` |

### Decisiones de diseño
1. **¿Conviene almacenar la dirección como un único texto?**

   No, porque impide realizar filtros, validaciones y búsquedas granulares sobre sus componentes individuales.

2. **¿Qué ventajas tendría separar sus componentes?**

   Permite consultas directas con XPath (por ejemplo, buscar todos los pedidos de la calle "Reforma"), simplifica la validación de tipos y facilita la integración con bases de datos.

3. **¿Cómo debería almacenarse una fecha para facilitar su procesamiento?**

   Bajo el estándar ISO 8601 (`AAAA-MM-DD`, es decir, `2021-09-19`). Esto garantiza ordenamiento cronológico y compatibilidad con parsers y motores de consulta.

4. **¿Qué información podría ser atributo y cuál elemento?**

   - **Elementos:** Los datos centrales del negocio y contenido legible (`<nombre>`, `<calle>`, `<descripcion>`).
   
   - **Atributos:** Metadatos descriptivos o identificadores que aportan contexto técnico sin ser la entidad en sí.
