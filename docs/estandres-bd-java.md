# Guía de Convenciones: Base de Datos y Desarrollo Java

Este documento establece los estándares de nomenclatura para el diseño de bases de datos relacionales y el desarrollo de código en Java (JDK 8+), buscando un equilibrio entre la **consistencia estructural** en SQL y la **claridad y concisión** en el código (Clean Code / DDD).

---

## 1. Nomenclatura de Base de Datos

### 1.1 Reglas Generales
* **Formato:** `snake_case` en minúsculas, sin acentos ni caracteres especiales (evitar la letra `ñ`).
* **Idioma:** Español. No mezclar palabras en inglés con español (evitar *Spanglish*).
* **Singular:** Los nombres de tablas y columnas deben escribirse en singular (ej. `empleado`, `detalle_pedido`).

### 1.2 Nomenclatura de Tablas y Adaptación a Esquemas Existentes
Debido a que este repositorio alberga proyectos creados en distintos momentos, existen dos estilos válidos para nombrar las tablas:

#### Estilo A: Con prefijo de clasificación (`ppp_nnnn`)
Utilizado en bases de datos que separan explícitamente la naturaleza de sus entidades:
* **`cat_nnnn` (Catálogos):** Tablas maestras o de referencia cuyos valores cambian con poca frecuencia (ej. `cat_sucursal`, `cat_tipo_movimiento`).
* **`det_nnnn` (Detalle / Transaccionales):** Tablas que almacenan información operativa o transaccional y que dependen de otras tablas o catálogos (ej. `det_empleado`, `det_pedido`).
* **`nnnn`:** Nombre de la entidad en singular, siguiendo las mismas reglas de abreviación que las columnas.

#### Estilo B: Sin prefijo (`nnnn`)
Utilizado en bases de datos donde el nombre de la tabla expresa directamente la entidad en singular sin clasificadores previos (ej. `sucursal`, `empleado`, `pedido`).

> **Regla de Continuidad:**  
> *Antes de crear una nueva tabla en una base de datos existente, verifica la convención predominante en ese esquema. **Respeta y continúa utilizando el mismo estilo** (con prefijos `cat_`/`det_` o sin prefijos) para mantener la homogeneidad interna de cada proyecto.*

### 1.3 Estructura de Columnas (`pp_mmm`)
Toda columna debe seguir la estructura **`pp_mmm`**, donde:
* **`pp`**: Prefijo de 2 letras que indica el dominio o tipo de dato.
* **`mmm`**: Mnemónico descriptivo de la columna.

#### Catálogo Oficial de Prefijos (`pp`)

| Prefijo | Significado | Uso y Reglas de Desempate | Ejemplo |
| :--- | :--- | :--- | :--- |
| **`id`** | Identificador | Llaves primarias (PK) y foráneas (FK) internas (autoincrementales o UUID). | `id_empleado`, `id_sucursal` |
| **`cd`** | Código | Claves de negocio, catálogos o identificadores externos. | `cd_rfc`, `cd_moneda`, `cd_postal` |
| **`st`** | Estatus | Estados de un ciclo de vida o flujo. | `st_pedido`, `st_factura` |
| **`in`** | Indicador | Banderas booleanas (`1`/`0`, `true`/`false`). | `in_activo`, `in_borrado` |
| **`tp`** | Tipo | Clasificaciones o categorías. | `tp_cliente`, `tp_movimiento` |
| **`nb`** | Nombre | Nombres propios, títulos o descripciones cortas indexables. | `nb_cliente`, `nb_articulo` |
| **`tx`** | Texto | Descripciones largas, observaciones o comentarios. | `tx_observacion`, `tx_direccion` |
| **`ct`** | Cantidad | Conteos, inventarios o magnitudes operables matemáticamente. | `ct_articulo`, `ct_dia` |
| **`nu`** | Número | Números que funcionan como etiquetas, folios o secuencias. | `nu_folio`, `nu_telefono`, `nu_exterior` |
| **`im`** | Importe | Valores monetarios (unitarios, parciales o totales). | `im_subtotal`, `im_iva`, `im_total` |
| **`pc`** | Porcentaje | Tasas o proporciones porcentuales. | `pc_descuento`, `pc_comision` |
| **`fa`** | Factor | Multiplicadores, tipos de cambio o coeficientes. | `fa_conversion`, `fa_impuesto` |
| **`fh`** | Fecha | Fechas sin componente de hora (`DATE`). | `fh_alta`, `fh_nacimiento` |
| **`hm`** | Hora | Horas sin componente de fecha (`TIME`). | `hm_entrada`, `hm_salida` |
| **`tm`** | Timestamp | Fecha y hora exacta (`DATETIME` / `TIMESTAMP`). | `tm_creacion`, `tm_ult_acceso` |
| **`du`** | Duración | Intervalos o lapsos de tiempo medibles. | `du_sesion`, `du_garantia` |

### 1.4 Reglas para el Mnemónico (`mmm`) y Abreviaturas
* **Longitud:** Procurar nombres cortos y directos. Si el concepto requiere más de una palabra, separarlas con guion bajo (`_`), limitando a un máximo de 3 palabras.
* **Uso de Abreviaturas:** Si una palabra compuesta tiene una abreviatura estándar que mantiene claro el contexto, **se debe favorecer su uso** para evitar nombres extensos.
  * ✅ `fh_ult_acceso` (en lugar de `fh_ultimo_acceso`)
  * ✅ `im_desc_aplicado` (en lugar de `im_descuento_aplicado`)
  * ✅ `cd_suc_origen` (en lugar de `cd_sucursal_origen`)

#### Diccionario de Abreviaturas Permitidas
*(Mantener esta lista actualizada según las necesidades del dominio)*

| Palabra | Abreviatura | Ejemplo |
| :--- | :--- | :--- |
| Actualización | `act` | `tm_ult_act` |
| Anterior | `ant` | `im_saldo_ant` |
| Artículo | `art` | `id_art` |
| Autorización | `aut` | `cd_aut` |
| Cancelación | `canc` | `fh_canc` |
| Configuración | `conf` | `tx_conf` |
| Departamento | `depto` | `nb_depto` |
| Descuento | `desc` | `im_desc` |
| Detalle | `det` | `det_pedido` |
| Documento | `doc` | `nu_doc` |
| Empleado | `emp` | `id_emp` |
| Máximo | `max` | `ct_max` |
| Mínimo | `min` | `ct_min` |
| Movimiento | `mov` | `tp_mov` |
| Referencia | `ref` | `cd_ref` |
| Registro | `reg` | `fh_reg` |
| Sucursal | `suc` | `id_suc` |
| Teléfono | `tel` | `nu_tel` |
| Último / Última | `ult` | `fh_ult_acceso` |
| Usuario | `usr` | `id_usr` |

### 1.5 Nomenclatura de Llaves e Índices

Todos los objetos de restricción (*constraints*) e índices deben nombrarse explícitamente siguiendo estas reglas (evitando nombres autogenerados por el motor de base de datos):

| Objeto | Estructura | Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| **Llave Primaria** | `pk_nnnn` | **`pk`**: Prefijo fijo.<br>**`nnnn`**: Nombre de la tabla (en singular). | `pk_empleado`<br>`pk_detalle_pedido` |
| **Llave Foránea** | `fk_mmmm__nnnn` | **`fk`**: Prefijo fijo.<br>**`mmmm`**: Tabla origen (donde se define la FK).<br>**`nnnn`**: Tabla destino (tabla referenciada).<br>*(Nota: Se usa doble guion bajo `__` como separador de tablas).* | `fk_empleado__sucursal`<br>`fk_detalle_pedido__articulo` |
| **Índice Único** | `uk_nnnn__cccc` | **`uk`**: Prefijo fijo (*Unique Key*).<br>**`nnnn`**: Nombre de la tabla.<br>**`cccc`**: Columna(s) que componen la unicidad. | `uk_empleado__cd_rfc`<br>`uk_usuario__nb_correo` |
| **Índice Regular** | `idx_nnnn__cccc` | **`idx`**: Prefijo fijo.<br>**`nnnn`**: Nombre de la tabla.<br>**`cccc`**: Columna(s) indexadas (sin prefijo `pp_` si son varias, para acortar). | `idx_pedido__fh_alta`<br>`idx_empleado__suc_estatus` |

#### Reglas de Excepción en Llaves e Índices
* **Múltiples llaves foráneas hacia la misma tabla:** Si una tabla tiene dos o más relaciones hacia la misma tabla destino, agregar un sufijo de rol al final separado por doble guion bajo (`fk_mmmm__nnnn__rol`).
  * *Ejemplo:* `fk_traslado__sucursal__origen` y `fk_traslado__sucursal__destino`.
* **Límite de longitud del motor:** Si el nombre resultante excede el límite de caracteres del motor de base de datos (ej. 30 caracteres en versiones antiguas de Oracle o 64 en MySQL/PostgreSQL), aplicar las abreviaturas oficiales definidas en la sección 1.4.
  * *Ejemplo:* `fk_det_pedido__art` en lugar de `fk_detalle_pedido__articulo`.

### 1.6 Otros Objetos de Base de Datos

Para el resto de los objetos programables o virtuales dentro del esquema, utilizar los siguientes prefijos:

| Objeto | Estructura | Descripción | Ejemplo |
| :--- | :--- | :--- | :--- |
| **Vista** | `vw_nnnn` | **`vw`**: Prefijo fijo (*View*).<br>**`nnnn`**: Nombre descriptivo de la consulta o entidad virtual. | `vw_empleado_activo`<br>`vw_resumen_ventas` |
| **Secuencia** | `seq_nnnn` | **`seq`**: Prefijo fijo (*Sequence*).<br>**`nnnn`**: Nombre de la tabla o concepto que alimenta. | `seq_empleado`<br>`seq_nu_folio_factura` |
| **Trigger** | `trg_nnnn__ev` | **`trg`**: Prefijo fijo (*Trigger*).<br>**`nnnn`**: Nombre de la tabla asociada.<br>**`ev`**: Momento y evento (`bi`, `ai`, `bu`, `au`, `bd`, `ad`). | `trg_pedido__ai` *(After Insert)*<br>`trg_empleado__bu` *(Before Update)* |
| **Procedimiento Almacenado** | `sp_nnnn` | **`sp`**: Prefijo fijo (*Stored Procedure*).<br>**`nnnn`**: Verbo en infinitivo + sustantivo que describe la acción. | `sp_calcular_nomina`<br>`sp_depurar_log_acceso` |

---

## 2. Mapeo de Base de Datos a Modelos Java (JDK 8)

En Java, el **tipo de dato** y el **contexto de la clase** ya aportan la información semántica que en SQL se resuelve con los prefijos. Por lo tanto:
* **Nunca** trasladar los prefijos de tabla (`cat_`, `det_`) al nombre de las clases modelo (ej. `cat_sucursal` → `Sucursal`, `det_empleado` → `Empleado`).
* **Nunca** trasladar los prefijos de columna (`fh_`, `im_`, `nb_`, etc.) a los atributos Java.

El mapeo entre la tabla/columna y la clase/atributo se delega a la capa de persistencia (ej. `@Table(name = "det_empleado")` y `@Column(name = "fh_alta")`).

### Tabla de Equivalencias

| Prefijo BD | Tipo Java 8 | Criterio de Nombrado en Java | Ejemplo BD → Java |
| :--- | :--- | :--- | :--- |
| `id_` | `Long` / `Integer` | `id` para la PK propia; `id<Entidad>` o relación de objeto para FK. | `id_empleado` → `id` |
| `cd_` | `String` | Sustantivo directo del código. | `cd_rfc` → `rfc` |
| `st_` | `Enum` / `String` | `estatus` o el nombre del estado específico. | `st_pedido` → `estatus` |
| `in_` | `Boolean` | Adjetivo o participio. **No usar** el prefijo `is` en el atributo. | `in_activo` → `activo` |
| `nb_` / `tx_` | `String` | Sustantivo directo sin prefijo. | `nb_puesto` → `puesto`<br>`tx_comentario` → `comentario` |
| `ct_` | `Integer` / `Long` | Sustantivo en plural si representa conteo de elementos. | `ct_articulo` → `articulos`<br>`ct_dia` → `dias` |
| `nu_` | `Integer` / `String` | Sustantivo en singular. | `nu_folio` → `folio` |
| `im_` | `BigDecimal` | Concepto monetario directo. | `im_subtotal` → `subtotal`<br>`im_total` → `total` |
| `pc_` / `fa_` | `BigDecimal` | Concepto directo. | `pc_descuento` → `porcentajeDescuento`<br>`fa_impuesto` → `factorImpuesto` |
| `fh_` | `LocalDate` | Omitir la palabra "fecha" si el contexto de la clase es claro. | `fh_alta` → `alta` (o `fechaAlta`)<br>`fh_nacimiento` → `nacimiento` |
| `hm_` | `LocalTime` | Sustantivo del evento. | `hm_entrada` → `entrada` |
| `tm_` | `LocalDateTime` / `Instant` | Sustantivo del evento ocurrido. | `tm_creacion` → `creacion`<br>`tm_ult_acceso` → `ultimoAcceso` |

---

## 3. Estándares de Nombrado en Código (Clean Code & DDD)

> **Principio de Concisión Contextual:**  
> *"Un nombre debe ser tan largo como sea necesario para evitar ambigüedad, y tan corto como sea posible para facilitar su lectura. Nunca repitas en un atributo o método información que ya esté explícita en el nombre de la clase, en el tipo de retorno o en la firma de sus parámetros."*

### 3.1 Evitar Redundancia con el Contexto de la Clase
Si un método o atributo pertenece a la clase `Empleado` o `EmpleadoBL`, no debe repetir la palabra `Empleado` en su nombre.

* ❌ `empleado.getNombreEmpleado()`
* ✅ `empleado.getNombre()`
* ❌ `EmpleadoBL.calcularSueldoEmpleado(empleado)`
* ✅ `EmpleadoBL.calcularSueldo(empleado)`

### 3.2 Los Parámetros Definen el "Cómo", el Método Define el "Qué"
Evitar incluir en el nombre del método el tipo de dato de retorno o el nombre de los parámetros que recibe. Si se requiere un comportamiento por defecto, utilizar **sobrecarga de métodos**.

* ❌ `DateTool.getMananaDatePorZonaHoraria(ZoneId zona)`
* ✅ `DateTool.getManana(ZoneId zona)`
* ✅ `DateTool.getManana()` *(Sobrecarga que invoca internamente a `getManana(ZONA_DEFAULT)`)*

### 3.3 Verbos Precisos del Dominio (Lenguaje Ubicuo)
En el diseño guiado por el dominio (DDD), los métodos deben expresar la intención real del negocio de forma directa, evitando describir la mecánica interna de la implementación.

* ❌ `EmpleadoBL.asignarHorarioDeEmpleadoARegistro(empleado, horaInicio, horaFin)`
* ✅ `EmpleadoBL.registrarAsistencia(empleado, horaInicio, horaFin)`

### 3.4 Sufijos Estándar de Arquitectura
Para evitar variaciones como `BusinessLogic` vs `BL`, utilizar únicamente los sufijos aprobados para componentes arquitectónicos:

* **Entidades / Modelos:** `<Entidad>` (ej. `Empleado`, `Factura`)
* **Lógica de Negocio:** `<Entidad>BL` o `<Entidad>Service` (ej. `EmpleadoBL`)
* **Acceso a Datos:** `<Entidad>DAO` o `<Entidad>Repository` (ej. `EmpleadoDAO`)
* **Objetos de Transferencia:** `<Entidad>DTO` (ej. `EmpleadoDTO`)
* **Utilerías:** `<Concepto>Tool` o `<Concepto>Utils` (ej. `DateTool`)

---

## 4. Ejemplo Práctico Integral

A continuación se muestra cómo aplicar todas las reglas de esta guía en un caso real de negocio (gestión de empleados por sucursal), desde la definición en SQL hasta su implementación en Java 8.

### 4.1 Definición en Base de Datos (SQL)
Siguiendo un esquema con prefijos `cat_` y `det_`:

```sql
-- Tabla de Catálogo (cat_)
CREATE TABLE cat_sucursal (
    id_sucursal     BIGINT          NOT NULL,
    cd_sucursal     VARCHAR(10)     NOT NULL,
    nb_sucursal     VARCHAR(100)    NOT NULL,
    in_activo       SMALLINT        NOT NULL DEFAULT 1,
    CONSTRAINT pk_cat_sucursal PRIMARY KEY (id_sucursal),
    CONSTRAINT uk_cat_sucursal__cd_sucursal UNIQUE (cd_sucursal)
);

-- Tabla Transaccional / Detalle (det_)
CREATE TABLE det_empleado (
    id_empleado     BIGINT          NOT NULL,
    id_sucursal     BIGINT          NOT NULL,
    cd_rfc          VARCHAR(13)     NOT NULL,
    nb_empleado     VARCHAR(120)    NOT NULL,
    im_sueldo_base  DECIMAL(12,2)   NOT NULL,
    fh_alta         DATE            NOT NULL,
    tm_ult_acceso   TIMESTAMP       NULL,
    in_activo       SMALLINT        NOT NULL DEFAULT 1,
    CONSTRAINT pk_det_empleado PRIMARY KEY (id_empleado),
    CONSTRAINT fk_det_empleado__cat_sucursal FOREIGN KEY (id_sucursal) 
        REFERENCES cat_sucursal (id_sucursal),
    CONSTRAINT uk_det_empleado__cd_rfc UNIQUE (cd_rfc)
);

-- Índice regular para búsquedas frecuentes por sucursal y estatus
CREATE INDEX idx_det_empleado__suc_activo 
    ON det_empleado (id_sucursal, in_activo);
```

### 4.2 Clases Modelo en Java (JDK 8)
Los prefijos de tabla (`cat_`, `det_`) y de columna (`cd_`, `nb_`, `im_`, `fh_`, `in_`) desaparecen, dejando atributos limpios y expresivos:

```java
@Entity
@Table(name = "cat_sucursal")
public class Sucursal {

    @Id
    @Column(name = "id_sucursal")
    private Long id;

    @Column(name = "cd_sucursal")
    private String codigo;

    @Column(name = "nb_sucursal")
    private String nombre;

    @Column(name = "in_activo")
    private Boolean activo;

    // Getters y Setters omitidos por brevedad
}
```

```java
@Entity
@Table(name = "det_empleado")
public class Empleado {

    @Id
    @Column(name = "id_empleado")
    private Long id;

    @ManyToOne(fetch = FetchType.LAZY)
    @JoinColumn(name = "id_sucursal")
    private Sucursal sucursal;

    @Column(name = "cd_rfc")
    private String rfc;

    @Column(name = "nb_empleado")
    private String nombre;

    @Column(name = "im_sueldo_base")
    private BigDecimal sueldoBase;

    @Column(name = "fh_alta")
    private LocalDate alta;

    @Column(name = "tm_ult_acceso")
    private LocalDateTime ultimoAcceso;

    @Column(name = "in_activo")
    private Boolean activo;

    // Getters y Setters omitidos por brevedad
}
```

### 4.3 Lógica de Negocio (`EmpleadoBL`)
Aplicando el principio de concisión contextual y sobrecarga de métodos:

```java
public class EmpleadoBL {

    private static final ZoneId ZONA_DEFAULT = ZoneId.of("America/Mexico_City");

    // ✅ Conciso: No repite "Empleado" ni los tipos de parámetros en el nombre
    public void registrarAsistencia(Empleado empleado, LocalTime entrada, LocalTime salida) {
        // Lógica de negocio...
    }

    // ✅ Sobrecarga para usar zona horaria por defecto
    public void actualizarAcceso(Empleado empleado) {
        actualizarAcceso(empleado, ZONA_DEFAULT);
    }

    public void actualizarAcceso(Empleado empleado, ZoneId zona) {
        empleado.setUltimoAcceso(LocalDateTime.now(zona));
    }
}
```
