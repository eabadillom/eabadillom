# Guía de Versionado Semántico y Registro de Cambios

Los proyectos en este perfil siguen el estándar de **Versionado Semántico (SemVer)** bajo el formato **`X.Y.Z`** (`MAYOR.MENOR.PARCHE`) y documenta sus actualizaciones siguiendo las convenciones de [Keep a Changelog](https://keepachangelog.com).

> **💡 Sobre esta guía y mis proyectos anteriores:**  
> Este documento nace de mi propia experiencia trabajando en los proyectos que ves en este perfil y de la necesidad de poner orden conforme fueron creciendo. Por eso, notarás que varios de los repositorios más antiguos no siguen estas reglas al pie de la letra, ya que fueron creados antes de definir este estándar.  
>  
> A partir de ahora, estas convenciones son mi base de trabajo para todos los desarrollos nuevos y las estoy adoptando poco a poco en el mantenimiento de mis sistemas existentes, mirando hacia adelante sin reescribir su historial.

---

## ¿Cuándo incrementar cada número? (`X.Y.Z`)

Dado un número de versión `MAYOR.MENOR.PARCHE`, se debe incrementar de acuerdo con el impacto de los cambios realizados:

### 1. Versión MAYOR (`X.0.0`) — *Breaking Changes*
Se incrementa cuando se realizan **cambios incompatibles con versiones anteriores**. Si un usuario o sistema actualiza a esta versión, su implementación actual dejará de funcionar si no realiza ajustes.
* **Reinicio:** Al subir `X`, los números `Y` y `Z` vuelven a `0` (ej. de `1.4.2` pasa a `2.0.0`).
* **Ejemplos prácticos:**
  * Eliminar o renombrar métodos, clases, endpoints o parámetros públicos.
  * Cambiar el tipo de dato que retorna una función o la estructura de respuesta de una API.
  * Cambiar la versión mínima requerida del lenguaje o entorno (ej. migrar un proyecto de Java 11 a Java 17).
  * Modificar el comportamiento de una funcionalidad de manera que rompa la lógica esperada por quienes ya la usaban.

### 2. Versión MENOR (`0.Y.0`) — *Nuevas funcionalidades compatibles*
Se incrementa cuando se añade **nueva funcionalidad** manteniendo la compatibilidad con versiones anteriores (retrocompatibilidad).
* **Reinicio:** Al subir `Y`, el número `Z` vuelve a `0` (ej. de `1.4.2` pasa a `1.5.0`).
* **Ejemplos prácticos:**
  * Agregar nuevos métodos, clases, endpoints o parámetros opcionales.
  * Marcar una funcionalidad existente como obsoleta (ej. `@Deprecated` en Java) para avisar que se eliminará en una futura versión mayor.
  * Mejoras internas importantes de rendimiento que no alteran la forma en que se interactúa con el código.

### 3. Versión PARCHE (`0.0.Z`) — *Corrección de errores*
Se incrementa cuando se hacen **correcciones de errores (bug fixes)** o ajustes internos que mantienen la compatibilidad con versiones anteriores.
* **Ejemplos prácticos:**
  * Solucionar excepciones (como un `NullPointerException`) o fallos lógicos sin cambiar la firma de los métodos públicos.
  * Corregir vulnerabilidades de seguridad.
  * Actualizar dependencias internas que no afecten la API pública.
  * Correcciones en la documentación o en mensajes de error.

> **Nota sobre la etapa inicial (`0.Y.Z`):** Mientras el proyecto esté en desarrollo inicial (versión `0.x.x`), la API pública no se considera estable y pueden ocurrir cambios incompatibles al subir el número `Y`. La versión **`1.0.0`** define la primera API pública estable.

---

## ¿Cómo documentar los cambios? (`CHANGELOG.md`)

Cada vez que realices una contribución en un *Pull Request*, debes anotar tus cambios en el archivo `CHANGELOG.md` dentro de la sección **`[Unreleased]`** (Sin lanzar). 

Agrupa tus notas usando exclusivamente estas categorías (en español o inglés, según el idioma del repositorio):

* **`Added` (Añadido):** Para funcionalidades nuevas. *(Sube versión MENOR)*
* **`Changed` (Cambiado):** Para cambios en funcionalidades existentes. *(Sube versión MENOR o MAYOR si rompe compatibilidad)*
* **`Deprecated` (Obsoleto):** Para funciones que se eliminarán en próximas versiones. *(Sube versión MENOR)*
* **`Removed` (Eliminado):** Para funciones obsoletas que ya fueron borradas. *(Sube versión MAYOR)*
* **`Fixed` (Corregido):** Para corrección de errores. *(Sube versión PARCHE)*
* **`Security` (Seguridad):** En caso de parches de vulnerabilidades. *(Sube versión PARCHE)*

### Ejemplo de estructura en `CHANGELOG.md`:

```markdown
## [Unreleased]
### Added
- Nuevo endpoint `/api/v1/usuarios/exportar` para descargar reportes en CSV.

### Fixed
- Corregido error al validar fechas nulas en el servicio de autenticación.

## - 2026-11-20
### Added
- Soporte para autenticación mediante tokens JWT.
```

---

## Flujo de trabajo en Git / GitHub para colaboradores

1. **Durante el desarrollo (Colaboradores):** 
   * No modifiques el número de versión en los archivos de configuración del proyecto (como `pom.xml`, `build.gradle`, `package.json`, etc.) ni crees *tags* manualmente.
   * Agrega únicamente la descripción de tus cambios en el archivo `CHANGELOG.md` bajo la sección `[Unreleased]`.
2. **Al lanzar una versión (Mantenedores):**
   * Se evalúan los cambios acumulados en `[Unreleased]` para determinar si corresponde incrementar `X`, `Y` o `Z` (prevalece siempre el cambio de mayor impacto).
   * Se actualiza el número de versión en los archivos de configuración del proyecto.
   * Se mueve el contenido de `[Unreleased]` a un nuevo encabezado con el número de versión y la fecha en formato ISO (`YYYY-MM-DD`), por ejemplo: `## - 2026-11-20`.
   * Se crea el *tag* correspondiente en Git (`git tag -a v1.3.0 -m "Release v1.3.0"`) y se publica en GitHub.
