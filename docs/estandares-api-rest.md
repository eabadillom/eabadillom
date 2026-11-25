# Guía de Nomenclatura y Diseño para Endpoints REST

El diseño de una API RESTful se basa en **recursos** (sustantivos) manipulados a través de **métodos HTTP** (verbos). Un buen diseño hace que la API sea predecible, intuitiva y fácil de mantener.

> **💡 Sobre esta guía y mis proyectos anteriores:**  
> Este documento nace de mi propia experiencia trabajando en los proyectos que ves en este perfil y de la necesidad de poner orden conforme fueron creciendo. Por eso, notarás que varios de los repositorios más antiguos no siguen estas reglas al pie de la letra, ya que fueron creados antes de definir este estándar.  
>  
> A partir de ahora, estas convenciones son mi base de trabajo para todos los desarrollos nuevos y las estoy adoptando poco a poco en el mantenimiento de mis sistemas existentes, mirando hacia adelante sin reescribir su historial.


---

## 1. Usa sustantivos en plural, nunca verbos

La URL debe identificar *qué* recurso estás manipulando, mientras que el método HTTP indica *qué acción* estás realizando.

* ❌ **Incorrecto:** `GET /empresas/obtenerEmpresas`
* ❌ **Incorrecto:** `POST /empresas/crearEmpresa`
* ✅ **Correcto:** `GET /empresas`
* ✅ **Correcto:** `POST /empresas`

> **Nota:** Usa siempre el plural (`/empresas` en lugar de `/empresa`) para mantener consistencia, tanto si buscas toda la colección como un elemento específico.

---

## 2. Aprovecha los métodos HTTP (CRUD estándar)

Utiliza la misma ruta base (`/empresas`) y cambia únicamente el método HTTP según la operación:

| Operación | Método HTTP | Endpoint | Descripción |
| :--- | :--- | :--- | :--- |
| **Listar** | `GET` | `/empresas` | Obtiene una lista de empresas. |
| **Consultar detalle** | `GET` | `/empresas/{id}` | Obtiene una empresa específica por su ID. |
| **Crear** | `POST` | `/empresas` | Crea una nueva empresa. |
| **Reemplazar** | `PUT` | `/empresas/{id}` | Actualiza todos los datos de una empresa. |
| **Actualizar parcial** | `PATCH` | `/empresas/{id}` | Modifica solo algunos campos de la empresa. |
| **Eliminar** | `DELETE` | `/empresas/{id}` | Elimina una empresa. |

---

## 3. Usa Query Parameters para filtrar, ordenar y paginar

Cuando necesites devolver una lista de empresas "de acuerdo a ciertos criterios", no crees rutas nuevas. Pasa los criterios como parámetros de consulta (`?clave=valor`) sobre el endpoint principal.

* **Filtrado:**
  `GET /empresas?sector=tecnologia&pais=mexico`
* **Ordenamiento:**
  `GET /empresas?sort=nombre&order=asc`
* **Paginación:**
  `GET /empresas?page=2&limit=20`
* **Combinado:**
  `GET /empresas?activa=true&sector=finanzas&page=1&limit=10`

---

## 4. Jerarquía para recursos anidados

Si una empresa tiene sub-recursos que dependen directamente de ella (como empleados o sucursales), refléjalo en la ruta manteniendo el patrón `recurso/{id}/sub-recurso`:

* **Obtener empleados de una empresa:**
  `GET /empresas/{empresa-id}/empleados`
* **Obtener un empleado específico de esa empresa:**
  `GET /empresas/{empresa-id}/empleados/{empleado-id}`

> **Buena práctica:** Evita anidar más de 2 niveles de profundidad (ej. `/empresas/{empresa-id}/sucursales/{sucursal-id}/empleados`). Si necesitas llegar tan profundo, suele ser mejor crear un endpoint independiente como `GET /empleados?sucursal-id=123`.

---

## 5. Formato y estilo de las URLs

Para evitar errores de compatibilidad entre servidores y mantener un estándar limpio:

* **Usa minúsculas:** Las URLs son sensibles a mayúsculas en muchos servidores.
  * ✅ `/empresas` | ❌ `/Empresas`
* **Usa `kebab-case` en rutas, variables de ruta y parámetros:**
  * ✅ `/empresas/tipos-contrato`
  * ✅ `/empresas/{empresa-id}/empleados/{empleado-id}`
  * ❌ `/empresas/tiposContrato`
  * ❌ `/empresas/{empresaId}/empleados/{empleadoId}`
* **No termines con barra inclinada (`/`):**
  * ✅ `/empresas` | ❌ `/empresas/`

---

## 6. Convenciones de Nomenclatura: URLs vs. JSON

Para mantener consistencia entre el estándar web (HTTP) y nuestro stack tecnológico en el backend (**Java / Spring Boot**), aplicamos estilos distintos según la capa:

| Elemento | Convención | Ejemplo | Justificación |
| :--- | :--- | :--- | :--- |
| **Rutas y Path Variables** | `kebab-case` | `/empresas/{empresa-id}/tipos-contrato` | Estándar web legible, insensible a problemas de capitalización en proxies/servidores. |
| **Query Params** | `kebab-case` | `?fecha-inicio=2026-11-01&empresa-id=10` | Mantiene uniformidad estética con el resto de la URL. |
| **Propiedades JSON** | `camelCase` | `"fechaInicio": "2026-11-01"` | Mapeo nativo y automático con DTOs de Java (Jackson) y objetos en frontend. |
| **Valores de Enums** | `SCREAMING_SNAKE_CASE` | `"estatus": "EN_TIEMPO"` | Mapeo directo con `enum` de Java sin conversiones manuales. |
| **Fechas y Horas** | ISO 8601 | `"2026-11-10"` / `"08:58:00"` | Estándar internacional compatible con `LocalDate` y `LocalTime` de Java. |

### Reglas adicionales para propiedades JSON:
* **Booleanos:** Usa prefijos claros como `es` o `tiene` (ej. `esActiva`, `tieneRetardo`) o adjetivos directos (`activa`).
* **Listas/Colecciones:** Usa siempre nombres en plural (ej. `"asistencias": [...]`, `"details": [...]`).
* **Valores nulos:** Si un dato aún no ocurre (por ejemplo, la hora de salida de un empleado que sigue en turno), devuelve explícitamente `"horaSalida": null` en lugar de omitir la llave, para mantener un contrato de datos predecible.

---

## 7. Excepciones: ¿Cuándo sí usar un verbo?

Existen acciones de negocio que no encajan en un CRUD tradicional (conocidas como *controladores*). En estos casos específicos, se permite usar un verbo al final de la ruta usando `POST`:

* `POST /empresas/{empresa-id}/activar`
* `POST /empresas/{empresa-id}/suspender`
* `POST /empresas/{empresa-id}/exportar-reporte`

---

## 8. Versionado de la API

El versionado permite introducir cambios que rompen la compatibilidad (*breaking changes*) sin afectar a los clientes que ya consumen la API.

* **Estándar recomendado (en la URL):** Incluye la versión mayor al inicio de la ruta usando el prefijo `v{numero}`.
  * ✅ `GET /v1/empresas`
  * ✅ `GET /v2/empresas`
* **Cuándo incrementar la versión:**
  * **Sube versión (`v1` -> `v2`):** Si eliminas campos, cambias el tipo de dato de una propiedad o modificas la estructura de la respuesta.
  * **Mantén la versión:** Si agregas nuevos endpoints o nuevos campos opcionales en la respuesta (son cambios retrocompatibles).

> **Nota:** Evita usar versiones menores en la URL como `/v1.2/empresas`. Mantén solo el número entero (`v1`, `v2`).

---

## 9. Manejo de Errores y Códigos de Estado

Nunca devuelvas un código `200 OK` con un mensaje de error dentro del JSON. Utiliza los códigos de estado HTTP estándar y acompaña la respuesta con un cuerpo JSON consistente.

### Códigos HTTP más comunes

| Código | Significado | Cuándo usarlo (Ejemplo con `/empresas`) |
| :--- | :--- | :--- |
| **`200 OK`** | Éxito general | Al listar (`GET /empresas`) o actualizar (`PUT`/`PATCH`). |
| **`201 Created`** | Recurso creado | Al registrar una nueva empresa con `POST /empresas`. |
| **`204 No Content`** | Éxito sin cuerpo | Al eliminar una empresa con `DELETE /empresas/{empresa-id}`. |
| **`400 Bad Request`** | Petición inválida | El cliente envió filtros mal formados o JSON inválido. |
| **`401 Unauthorized`** | No autenticado | Falta el token de acceso o expiró. |
| **`403 Forbidden`** | Sin permisos | El usuario está autenticado, pero no tiene rol para ver empresas. |
| **`404 Not Found`** | No encontrado | La empresa con el `{empresa-id}` solicitado no existe. |
| **`422 Unprocessable`** | Error de validación | El JSON es válido, pero falló una regla (ej. RFC/NIT duplicado). |
| **`500 Server Error`** | Error interno | Fallo inesperado en el servidor o base de datos. |

### Estructura estándar para respuestas de error

Define un formato único para todos los errores de tu API (inspirado en el estándar *RFC 9457 / Problem Details*):

```json
{
  "status": 422,
  "code": "EMPRESA_DUPLICADA",
  "message": "Los datos enviados no son válidos.",
  "details": [
    {
      "field": "rfc",
      "issue": "Ya existe una empresa registrada con este identificador fiscal."
    }
  ],
  "timestamp": "2026-11-25T13:30:00Z",
  "path": "/v1/empresas"
}
```

---

## 10. Caso Práctico Integral: Consulta de Asistencias

Este ejemplo aplica todas las reglas anteriores: `kebab-case` en la URL/parámetros y `camelCase` en el JSON de respuesta.

### El escenario
Se requiere consultar las asistencias de los empleados. El rango de fechas (periodo) es **obligatorio**, mientras que la empresa y la sucursal son **opcionales**.

### Diseño del Endpoint

Como la empresa y la sucursal son opcionales, **no** se deben anidar en la ruta. En su lugar, `/asistencias` actúa como recurso principal y todos los criterios viajan como *query parameters* en `kebab-case`.

* ❌ **Incorrecto:** `GET /v1/asistencias/consultarAsistencias`
* ✅ **Correcto:** `GET /v1/asistencias`

### Parámetros de consulta (Query Parameters)

| Parámetro (`kebab-case`) | Tipo | Requerido | Formato / Ejemplo | Descripción |
| :--- | :--- | :--- | :--- | :--- |
| `fecha-inicio` | `string` | **Sí** | `2026-11-01` (`LocalDate`) | Fecha inicial del periodo a consultar. |
| `fecha-fin` | `string` | **Sí** | `2026-11-15` (`LocalDate`) | Fecha final del periodo a consultar. |
| `empresa-id` | `integer` | No | `10` (`Long`) | Filtra las asistencias por una empresa específica. |
| `sucursal-id` | `integer` | No | `45` (`Long`) | Filtra las asistencias por una sucursal específica. |

### Ejemplos de peticiones válidas

* **Consulta general (solo parámetros obligatorios):**
  `GET /v1/asistencias?fecha-inicio=2026-11-01&fecha-fin=2026-11-15`
* **Consulta filtrada por empresa:**
  `GET /v1/asistencias?fecha-inicio=2026-11-01&fecha-fin=2026-11-15&empresa-id=10`
* **Consulta filtrada por empresa y sucursal:**
  `GET /v1/asistencias?fecha-inicio=2026-11-01&fecha-fin=2026-11-15&empresa-id=10&sucursal-id=45`

### Ejemplo de respuesta exitosa (`200 OK`)

Propiedades en `camelCase` mapeadas directamente desde los DTOs de Spring Boot:

```json
{
  "data": [
    {
      "fechaAsistencia": "2026-11-10",
      "horaEntrada": "08:58:00",
      "horaSalida": "18:05:00",
      "estatus": "EN_TIEMPO",
      "descripcionAsistencia": "En tiempo",
      "empleado": {
        "id": 1024,
        "nombreCompleto": "Ana Sofía Martínez"
      },
      "empresa": {
        "id": 10,
        "nombre": "Tecnologías del Centro S.A."
      },
      "sucursal": {
        "id": 45,
        "nombre": "Sucursal Reforma"
      }
    },
    {
      "fechaAsistencia": "2026-11-11",
      "horaEntrada": "09:22:00",
      "horaSalida": null,
      "estatus": "RETARDO",
      "descripcionAsistencia": "Retardo",
      "empleado": {
        "id": 1024,
        "nombreCompleto": "Ana Sofía Martínez"
      },
      "empresa": {
        "id": 10,
        "nombre": "Tecnologías del Centro S.A."
      },
      "sucursal": {
        "id": 45,
        "nombre": "Sucursal Reforma"
      }
    }
  ],
  "meta": {
    "totalRegistros": 2,
    "periodo": {
      "fechaInicio": "2026-11-01",
      "fechaFin": "2026-11-15"
    }
  }
}
```

### Ejemplo de error por falta de periodo (`400 Bad Request`)

```json
{
  "status": 400,
  "code": "PARAMETROS_INVALIDOS",
  "message": "El periodo de consulta es obligatorio y debe ser válido.",
  "details": [
    {
      "field": "fecha-inicio",
      "issue": "El parámetro 'fecha-inicio' es requerido en formato YYYY-MM-DD."
    }
  ],
  "timestamp": "2026-11-25T13:45:00Z",
  "path": "/v1/asistencias"
}
```

---

## 11. Ejemplos de Implementación en Java (Spring Boot)

A continuación se muestra cómo implementar este contrato híbrido (`kebab-case` en parámetros/rutas y `camelCase` en JSON) tanto en proyectos legacy con **JDK 8** como en aplicaciones modernas con **JDK 17**.

> **Nota importante sobre `kebab-case` en Spring:** Como Java no permite guiones medios (`-`) en los nombres de las variables, es obligatorio indicar explícitamente el nombre del parámetro dentro de `@RequestParam("fecha-inicio")` y `@PathVariable("empresa-id")`.

---

### Opción A: Implementación compatible con JDK 8 (Spring Boot 2.x)

En **JDK 8**, se utilizan clases tradicionales (POJOs) con getters/setters (o anotaciones de Lombok como `@Data` / `@Builder`) y el paquete `javax.validation`.

#### 1. Controlador REST (`AsistenciaController.java`)

```java
package com.empresa.api.controller;

import com.empresa.api.dto.AsistenciaItemDTO;
import com.empresa.api.dto.AsistenciaResponseDTO;
import com.empresa.api.service.AsistenciaService;
import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDate;
import java.util.List;

@RestController
@RequestMapping("/v1/asistencias")
public class AsistenciaController {

    private final AsistenciaService asistenciaService;

    public AsistenciaController(AsistenciaService asistenciaService) {
        this.asistenciaService = asistenciaService;
    }

    @GetMapping
    public ResponseEntity<AsistenciaResponseDTO> consultarAsistencias(
            @RequestParam("fecha-inicio") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate fechaInicio,
            @RequestParam("fecha-fin") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate fechaFin,
            @RequestParam(value = "empresa-id", required = false) Long empresaId,
            @RequestParam(value = "sucursal-id", required = false) Long sucursalId) {

        if (fechaFin.isBefore(fechaInicio)) {
            throw new IllegalArgumentException("La fecha-fin no puede ser anterior a fecha-inicio.");
        }

        List<AsistenciaItemDTO> registros = asistenciaService.buscar(fechaInicio, fechaFin, empresaId, sucursalId);

        AsistenciaResponseDTO.PeriodoDTO periodo = new AsistenciaResponseDTO.PeriodoDTO(fechaInicio, fechaFin);
        AsistenciaResponseDTO.MetaDTO meta = new AsistenciaResponseDTO.MetaDTO(registros.size(), periodo);

        return ResponseEntity.ok(new AsistenciaResponseDTO(registros, meta));
    }

    // Ejemplo de PathVariable en kebab-case para rutas anidadas
    @GetMapping("/empresas/{empresa-id}/empleados/{empleado-id}")
    public ResponseEntity<Void> ejemploRutaAnidada(
            @PathVariable("empresa-id") Long empresaId,
            @PathVariable("empleado-id") Long empleadoId) {
        return ResponseEntity.ok().build();
    }
}
```

#### 2. Enum y DTOs (`AsistenciaItemDTO.java` y `AsistenciaResponseDTO.java`)

Jackson serializará automáticamente los nombres de los atributos en `camelCase`.

```java
package com.empresa.api.dto;

import java.time.LocalDate;
import java.time.LocalTime;
import java.util.List;

public class AsistenciaResponseDTO {
    private List<AsistenciaItemDTO> data;
    private MetaDTO meta;

    public AsistenciaResponseDTO(List<AsistenciaItemDTO> data, MetaDTO meta) {
        this.data = data;
        this.meta = meta;
    }

    public List<AsistenciaItemDTO> getData() { return data; }
    public MetaDTO getMeta() { return meta; }

    public static class MetaDTO {
        private int totalRegistros;
        private PeriodoDTO periodo;

        public MetaDTO(int totalRegistros, PeriodoDTO periodo) {
            this.totalRegistros = totalRegistros;
            this.periodo = periodo;
        }

        public int getTotalRegistros() { return totalRegistros; }
        public PeriodoDTO getPeriodo() { return periodo; }
    }

    public static class PeriodoDTO {
        private LocalDate fechaInicio;
        private LocalDate fechaFin;

        public PeriodoDTO(LocalDate fechaInicio, LocalDate fechaFin) {
            this.fechaInicio = fechaInicio;
            this.fechaFin = fechaFin;
        }

        public LocalDate getFechaInicio() { return fechaInicio; }
        public LocalDate getFechaFin() { return fechaFin; }
    }
}
```

```java
package com.empresa.api.dto;

import java.time.LocalDate;
import java.time.LocalTime;

public class AsistenciaItemDTO {

    public enum EstatusAsistencia {
        EN_TIEMPO, RETARDO, VACACIONES, DESCANSO, INCAPACIDAD
    }

    private LocalDate fechaAsistencia;
    private LocalTime horaEntrada;
    private LocalTime horaSalida; // Jackson lo serializa como null si no tiene valor
    private EstatusAsistencia estatus;
    private String descripcionAsistencia;
    private EmpleadoResumenDTO empleado;
    private CatalogoResumenDTO empresa;
    private CatalogoResumenDTO sucursal;

    // Constructores, Getters y Setters estándar (o @Data con Lombok)
    public LocalDate getFechaAsistencia() { return fechaAsistencia; }
    public void setFechaAsistencia(LocalDate fechaAsistencia) { this.fechaAsistencia = fechaAsistencia; }

    public LocalTime getHoraEntrada() { return horaEntrada; }
    public void setHoraEntrada(LocalTime horaEntrada) { this.horaEntrada = horaEntrada; }

    public LocalTime getHoraSalida() { return horaSalida; }
    public void setHoraSalida(LocalTime horaSalida) { this.horaSalida = horaSalida; }

    public EstatusAsistencia getEstatus() { return estatus; }
    public void setEstatus(EstatusAsistencia estatus) { this.estatus = estatus; }

    public String getDescripcionAsistencia() { return descripcionAsistencia; }
    public void setDescripcionAsistencia(String descripcionAsistencia) { this.descripcionAsistencia = descripcionAsistencia; }

    public EmpleadoResumenDTO getEmpleado() { return empleado; }
    public void setEmpleado(EmpleadoResumenDTO empleado) { this.empleado = empleado; }

    public CatalogoResumenDTO getEmpresa() { return empresa; }
    public void setEmpresa(CatalogoResumenDTO empresa) { this.empresa = empresa; }

    public CatalogoResumenDTO getSucursal() { return sucursal; }
    public void setSucursal(CatalogoResumenDTO sucursal) { this.sucursal = sucursal; }

    public static class EmpleadoResumenDTO {
        private Long id;
        private String nombreCompleto;

        public EmpleadoResumenDTO(Long id, String nombreCompleto) {
            this.id = id;
            this.nombreCompleto = nombreCompleto;
        }
        public Long getId() { return id; }
        public String getNombreCompleto() { return nombreCompleto; }
    }

    public static class CatalogoResumenDTO {
        private Long id;
        private String nombre;

        public CatalogoResumenDTO(Long id, String nombre) {
            this.id = id;
            this.nombre = nombre;
        }
        public Long getId() { return id; }
        public String getNombre() { return nombre; }
    }
}
```

---

### Opción B: Implementación moderna con JDK 17 (Spring Boot 3.x)

Al migrar a **JDK 17** y **Spring Boot 3**, puedes reemplazar todos los DTOs verbosos por **`record`** inmutables (que Jackson soporta de forma nativa), usar `jakarta.validation` y aprovechar la inferencia de tipos con `var`.

#### 1. DTOs inmutables con Java `record` (`AsistenciaDtos.java`)

```java
package com.empresa.api.dto;

import java.time.LocalDate;
import java.time.LocalTime;
import java.util.List;

public final class AsistenciaDtos {

    public enum EstatusAsistencia {
        EN_TIEMPO, RETARDO, VACACIONES, DESCANSO, INCAPACIDAD
    }

    public record AsistenciaResponse(
            List<AsistenciaItem> data,
            Meta meta
    ) {}

    public record Meta(
            int totalRegistros,
            Periodo periodo
    ) {}

    public record Periodo(
            LocalDate fechaInicio,
            LocalDate fechaFin
    ) {}

    public record AsistenciaItem(
            LocalDate fechaAsistencia,
            LocalTime horaEntrada,
            LocalTime horaSalida,
            EstatusAsistencia estatus,
            String descripcionAsistencia,
            EmpleadoResumen empleado,
            CatalogoResumen empresa,
            CatalogoResumen sucursal
    ) {}

    public record EmpleadoResumen(Long id, String nombreCompleto) {}

    public record CatalogoResumen(Long id, String nombre) {}
}
```

#### 2. Controlador REST en JDK 17 (`AsistenciaController.java`)

```java
package com.empresa.api.controller;

import com.empresa.api.dto.AsistenciaDtos.*;
import com.empresa.api.service.AsistenciaService;
import org.springframework.format.annotation.DateTimeFormat;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.*;

import java.time.LocalDate;

@RestController
@RequestMapping("/v1/asistencias")
public class AsistenciaController {

    private final AsistenciaService asistenciaService;

    public AsistenciaController(AsistenciaService asistenciaService) {
        this.asistenciaService = asistenciaService;
    }

    @GetMapping
    public ResponseEntity<AsistenciaResponse> consultarAsistencias(
            @RequestParam("fecha-inicio") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate fechaInicio,
            @RequestParam("fecha-fin") @DateTimeFormat(iso = DateTimeFormat.ISO.DATE) LocalDate fechaFin,
            @RequestParam(name = "empresa-id", required = false) Long empresaId,
            @RequestParam(name = "sucursal-id", required = false) Long sucursalId) {

        if (fechaFin.isBefore(fechaInicio)) {
            throw new IllegalArgumentException("La fecha-fin no puede ser anterior a fecha-inicio.");
        }

        var registros = asistenciaService.buscar(fechaInicio, fechaFin, empresaId, sucursalId);
        var meta = new Meta(registros.size(), new Periodo(fechaInicio, fechaFin));

        return ResponseEntity.ok(new AsistenciaResponse(registros, meta));
    }
}
```

#### 3. Manejador Global de Errores (`GlobalExceptionHandler.java` - Compatible con JDK 8 y 17)

Para cumplir con el formato estandarizado de error de la **Sección 9**, captura las excepciones de parámetros faltantes o inválidos de forma centralizada:

```java
package com.empresa.api.exception;

import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.MissingServletRequestParameterException;
import org.springframework.web.bind.annotation.ExceptionHandler;
import org.springframework.web.bind.annotation.RestControllerAdvice;

import javax.servlet.http.HttpServletRequest; // En Spring Boot 3 (JDK 17) cambiar a jakarta.servlet.http.HttpServletRequest
import java.time.Instant;
import java.util.*;

@RestControllerAdvice
public class GlobalExceptionHandler {

    @ExceptionHandler(MissingServletRequestParameterException.class)
    public ResponseEntity<Map<String, Object>> handleMissingParams(
            MissingServletRequestParameterException ex,
            HttpServletRequest request) {

        Map<String, String> detail = new LinkedHashMap<>();
        detail.put("field", ex.getParameterName()); // Devuelve "fecha-inicio" o "fecha-fin" en kebab-case
        detail.put("issue", "El parámetro '" + ex.getParameterName() + "' es requerido.");

        Map<String, Object> body = new LinkedHashMap<>();
        body.put("status", HttpStatus.BAD_REQUEST.value());
        body.put("code", "PARAMETROS_INVALIDOS");
        body.put("message", "Faltan parámetros obligatorios en la consulta.");
        body.put("details", Collections.singletonList(detail));
        body.put("timestamp", Instant.now().toString());
        body.put("path", request.getRequestURI());

        return ResponseEntity.badRequest().body(body);
    }
}
```
