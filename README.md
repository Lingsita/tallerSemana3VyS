# Diccionario de Datos – Ask A Manager Salary Survey 2021

## Variables originales

| Variable (original) | Tipo | Descripción |
|---------------------|------|-------------|
| Timestamp | Fecha/Hora (texto) | Momento en que la persona envió la respuesta al formulario. |
| How old are you? | Categórica (texto) | Rango de edad autodeclarado de la persona encuestada. |
| Industry | Categórica (texto) | Industria en la que trabaja la persona. |
| Job title | Texto libre | Cargo o puesto desempeñado por la persona. |
| Additional context on job title | Texto libre | Información adicional o aclaraciones sobre el cargo. |
| Annual salary | Numérica (texto) | Salario anual reportado, usualmente con separadores. |
| Other monetary comp | Numérica (texto) | Compensación monetaria adicional anual. |
| Currency | Categórica | Moneda principal del salario reportado. |
| Currency - other | Texto | Moneda especificada manualmente cuando no está en la lista. |
| Additional context on income | Texto libre | Aclaraciones adicionales sobre el ingreso. |
| Country | Categórica | País donde trabaja la persona. |
| State | Categórica | Estado, provincia o región. |
| City | Texto | Ciudad reportada por la persona. |
| Overall years of professional experience | Categórica | Rango de experiencia profesional total. |
| Years of experience in field | Categórica | Rango de experiencia en el campo específico. |
| Highest level of education completed | Categórica | Máximo nivel educativo alcanzado. |
| Gender | Categórica | Género autodeclarado. |
| Race | Categórica | Raza o etnicidad autodeclarada. |

## Variables modeladas

| Variable (modelada) | Tipo | Descripción |
|--------------------|------|-------------|
| fecha_respuesta | datetime | Fecha y hora de respuesta parseada correctamente. |
| grupo_edad | categórica | Rango de edad estandarizado. |
| industria | categórica | Industria normalizada. |
| titulo_cargo | texto | Título del cargo limpio. |
| titulo_cargo_contexto | texto | Contexto adicional del cargo. |
| salario_anual | numérica | Salario anual convertido a número. |
| compensacion_extra_anual | numérica | Compensación adicional anual. |
| moneda | categórica | Moneda consolidada y estandarizada. |
| ingreso_contexto | texto | Contexto adicional del ingreso. |
| pais | categórica | País estandarizado. |
| estado_region | categórica | Estado o región normalizada. |
| ciudad | categórica | Ciudad limpia y normalizada. |
| exp_total_rango | categórica | Rango de experiencia total. |
| exp_total_min | numérica | Mínimo de experiencia total. |
| exp_total_max | numérica | Máximo de experiencia total. |
| exp_total_mid | numérica | Punto medio del rango de experiencia total. |
| exp_campo_rango | categórica | Rango de experiencia en el campo. |
| exp_campo_min | numérica | Mínimo de experiencia en el campo. |
| exp_campo_max | numérica | Máximo de experiencia en el campo. |
| exp_campo_mid | numérica | Punto medio del rango de experiencia en el campo. |
| educacion_nivel | categórica | Nivel educativo alcanzado. |
| educacion_ordinal | numérica | Codificación ordinal del nivel educativo. |
| genero | categórica | Género estandarizado. |
| raza_etnicidad | categórica | Raza o etnicidad estandarizada. |
| comp_total_anual | numérica | Compensación total anual. |



## Paso a paso para actualizar datos y aplicar el modelado 

### A. Obtención y versionado del dataset

Descargar el CSV desde la fuente que use el equipo (por ejemplo, Kaggle o el link público asociado a la encuesta 2021). Guardarlo como:

data/raw/ask_a_manager_salary_survey_2021.csv

No sobrescribir el archivo anterior: crear una carpeta por fecha de carga, por ejemplo:

data/raw/2026-02-08/ask_a_manager_salary_survey_2021.csv

Registrar en un CHANGELOG.md:

fecha de descarga, fuente, tamaño (filas/columnas), y hash (md5/sha1) para trazabilidad.

### B. Carga y estandarización inicial (staging)

Leer el CSV asegurando encoding (UTF-8) y que no se infieran mal los tipos:

Cargar todo como texto inicialmente (excepto que lo controlen explícitamente).

Renombrar columnas desde los nombres originales a los nombres modelados en español (diccionario fijo).

Ejemplo: Timestamp → fecha_respuesta, Annual salary → salario_anual_raw, etc.

Mantener por un tiempo los “_raw” para auditoría.

### C. Limpieza de texto (todas las variables string)

Para cada campo texto/categórico:

trim() (quitar espacios al inicio/fin),

colapsar espacios múltiples,

normalizar comillas raras,

estandarizar nulos: "", "NA", "N/A" → NULL.

### D. Parseo de fecha

Convertir fecha_respuesta a datetime:

si viene en formato tipo 4/27/2021 11:02, parsear con formato mes/día/año.

Validar:

% de fechas nulas (debería ser ~0),

rango de fechas esperado (2021 principalmente).

### E. Normalización de moneda

Crear moneda como:

si Currency está en lista estándar (USD, GBP, EUR, etc.), usarla;

si Currency indica “Other”, usar Currency - other;

aplicar upper() y trimming.

(Opcional) Validar contra catálogo:

monedas desconocidas quedan como moneda = 'UNKNOWN' y se documentan en un reporte de calidad.

### F. Parseo numérico de salario y compensación extra

Crear salario_anual desde salario_anual_raw:

remover comas ,,

remover símbolos ($, £, etc.) si existen,

convertir a número.

Crear compensacion_extra_anual desde compensacion_extra_anual_raw con la misma lógica.

Reglas de nulos:

si extra viene vacío: setear compensacion_extra_anual = 0 solo si salario_anual existe (esto evita inflar nulos).

Chequeos de calidad:

contar salarios <= 0,

percentiles (p1, p50, p99) para detectar outliers/errores de digitación.

### G. Estandarización geográfica

pais: aplicar una tabla de mapeo (US, USA, United States → US, etc.).

estado_region y ciudad:

limpieza básica (trim),

opcional: diccionario de corrección de typos para ciudades (si lo necesitan para análisis por ciudad).

### H. Transformación de experiencia (rangos → numérico)

Para exp_total_rango y exp_campo_rango:

normalizar strings (ej. "8 - 10 years" → "8-10 years"),

extraer min y max:

"5-7 years" → min=5, max=7, mid=6

"2 - 4 years" → min=2, max=4, mid=3

"21+ years" → min=21, max=NULL (o 21), mid=NULL (o 21) según regla del equipo.

Documentar claramente la regla para “+” (importante para replicabilidad).

### I. Educación (categórica + ordinal opcional)

educacion_nivel: limpieza básica.

educacion_ordinal: definir un mapeo fijo en un archivo de configuración (ej. YAML/JSON) para que no quede “hardcodeado” sin control.

### J. Género y raza/etnicidad

genero:

mapear variantes a un set controlado,

conservar un genero_raw si necesitan trazabilidad.

raza_etnicidad:

mismo enfoque (map + raw),

recomendar reportar agregado para evitar categorías con muy pocos registros (privacidad / robustez estadística).

### K. Variables derivadas finales

Calcular comp_total_anual = salario_anual + compensacion_extra_anual.

(Opcional internacional) Si convierten a USD:

usar una tabla fx_rates versionada (fecha, moneda, tasa_a_usd) y unir por moneda;

calcular salario_usd, comp_total_usd.

guardar en metadatos la fecha de la tasa (para reproducibilidad).

### L. Salida final (analytics-ready)

Generar un reporte de calidad:

nulos por columna,

top valores por categóricas,

conteo de monedas,

outliers de salario.
