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
   
## Ejercicio 2: DTD para nota

### Preguntas de análisis
1. **¿Cuál es el elemento raíz?**

   El elemento raíz es `<nota>`, ya que contiene y engloba a todos los demás elementos del documento.

2. **¿Cuántas veces aparece `<para>`?**

   Aparece exactamente una vez. Al no llevar ningún cuantificador o símbolo adicional (`+`, `*`, `?`), la DTD establece obligatoriedad estricta y cardinalidad unitaria.

3. **¿El orden de los elementos es significativo?**

   Sí. En la declaración DTD los elementos están separados por comas `(para, de, titulo, contenido)`, lo que define una secuencia obligatoria en ese orden exacto.

4. **¿Los elementos contienen otros elementos o solamente texto?**

   `<nota>` contiene únicamente elementos hijos. Por su parte, `<para>`, `<de>`, `<titulo>` y `<contenido>` contienen exclusivamente texto analizable (`#PCDATA`).

### Tabla de elementos
| Elemento | Tipo de contenido | Declaración DTD |
|---|---|---|
| `nota` | Elementos hijos | `<!ELEMENT nota (para, de, titulo, contenido)>` |
| `para` | Texto (`#PCDATA`) | `<!ELEMENT para (#PCDATA)>` |
| `de` | Texto (`#PCDATA`) | `<!ELEMENT de (#PCDATA)>` |
| `titulo` | Texto (`#PCDATA`) | `<!ELEMENT titulo (#PCDATA)>` |
| `contenido` | Texto (`#PCDATA`) | `<!ELEMENT contenido (#PCDATA)>` |

## Ejercicio 3: DTD para matrícula universitaria

### Decisiones de diseño
1. **¿Cómo se asegura que exista al menos un domicilio?**

   Se utiliza el operador de cardinalidad `+` en la definición del contenedor: `<!ELEMENT domicilios (domicilio+)>`. Esto obliga a que exista como mínimo una ocurrencia de `<domicilio>`, permitiendo registrar más si es necesario.

2. **¿Cómo se restringen los valores posibles del atributo `tipo`?**

   Se define mediante una lista de enumeración cerrada: `tipo (familiar | habitual)`. Cualquier valor fuera de esa lista es rechazado por el analizador XML.

3. **¿Cómo se hace obligatorio el atributo `tipo`?**

   Se añade la directiva `#REQUIRED` en la lista de atributos: `<!ATTLIST domicilio tipo (familiar | habitual) #REQUIRED>`. Si el atributo se omite, el validador marca error inmediato.

4. **¿Qué diferencia existe entre la versión externa e interna implementadas?**

   - **Externa (`matricula.dtd`):** El esquema reside en un archivo independiente y se invoca con `<!DOCTYPE matricula SYSTEM "matricula.dtd">`, permitiendo que múltiples documentos XML compartan la misma definición.
   
   - **Interna (`matricula-interna.xml`):** La definición completa del DTD se incrusta en el prólogo del documento dentro de corchetes `<!DOCTYPE matricula [...]>`, logrando un documento completamente autocontenido.
   
### Tabla de elementos y atributos
| Elemento / Atributo | Tipo / Restricción | Declaración DTD |
|---|---|---|
| `matricula` | Elementos hijos | `<!ELEMENT matricula (personal, pago)>` |
| `personal` | Secuencia de hijos | `<!ELEMENT personal (dni, nombre, titulacion, curso_academico, domicilios)>` |
| `dni`, `nombre`, `titulacion`, `curso_academico` | Texto (`#PCDATA`) | `<!ELEMENT nombre (#PCDATA)>` |
| `domicilios` | 1 o más elementos | `<!ELEMENT domicilios (domicilio+)>` |
| `domicilio` | Elemento hijo | `<!ELEMENT domicilio (nombre)>` |
| `tipo` (atributo) | Enumeración obligatoria | `<!ATTLIST domicilio tipo (familiar \| habitual) #REQUIRED>` |
| `pago` | Elemento hijo | `<!ELEMENT pago (tipo_matricula)>` |
| `tipo_matricula` | Texto (`#PCDATA`) | `<!ELEMENT tipo_matricula (#PCDATA)>` |
