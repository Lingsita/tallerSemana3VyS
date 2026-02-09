---
title: EDA Ask A Manager Salary Survey Documentation
description: fuente que nos habla de los sueldos que tienen profesionales en diferentes disciplinas (principalmente en Estados Unidos) y estos datos son recolectados por “Ask a Manager”, los datos quedan alojados en un Google Sheets público que se actualiza constantemente. 
---

# Diccionario de Datos

## Variables originales

| Variable (original) | Tipo | Descripción |
|---------------------|------|-------------|
| How old are you? | Categórica (texto) | Rango de edad autodeclarado de la persona encuestada. |
| What industry do you work in? | Categórica (texto) | Industria en la que trabaja la persona. |
| Job title | Texto libre | Cargo o puesto desempeñado por la persona. |
| If your job title needs additional context, please clarify here: | Texto libre | Información adicional o aclaraciones sobre el cargo. |
| What is your annual salary? (You'll indicate the currency in a later question. If you are part-time or hourly, please enter an annualized equivalent -- what you would earn if you worked the job 40 hours a week, 52 weeks a year.) | Numérica (texto) | Salario anual reportado, usualmente con separadores. |
| How much additional monetary compensation do you get, if any (for example, bonuses or overtime in an average year)? Please only include monetary compensation here, not the value of benefits. | Numérica (texto) | Compensación monetaria adicional anual. |
| Please indicate the currency | Categórica | Moneda principal del salario reportado. |
| If "Other," please indicate the currency here: | Texto | Moneda especificada manualmente cuando no está en la lista. |
| If your income needs additional context, please provide it here: | Texto libre | Aclaraciones adicionales sobre el ingreso. |
| What country do you work in? | Categórica | País donde trabaja la persona. |
| If you're in the U.S., what state do you work in? | Categórica | Estado, provincia o región. |
| What city do you work in? | Texto | Ciudad reportada por la persona. |
| How many years of professional work experience do you have overall? | Categórica | Rango de experiencia profesional total. |
| How many years of professional work experience do you have in your field? | Categórica | Rango de experiencia en el campo específico. |
| What is your highest level of education completed? | Categórica | Máximo nivel educativo alcanzado. |
| What is your gender? | Categórica | Género autodeclarado. |
| What is your race? (Choose all that apply.) | Categórica | Raza o etnicidad autodeclarada. |

## Variables modeladas

| Variable (modelada) | Tipo | Descripción |
|--------------------|------|-------------|
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

Descargar el CSV desde la fuente, se puede revisar el formulario en [Formulario Encuesta](https://www.askamanager.org/2021/04/how-much-money-do-you-make-4.html)  y los datos quedan alojados en un [Google Sheets](https://docs.google.com/spreadsheets/d/1IPS5dBSGtwYVbjsfbaMCYIWnOuRmJcbequohNxCyGVw/edit?resourcekey#gid=1625408792) público que se actualiza constantemente. Hacer una copia del mismo, en Google drive se versionan automaticamente los cambios y tiene integracion directa a Looker Studio.

Registrar:

- Fecha de descarga
- Fuente
- Tamaño (filas/columnas) para trazabilidad.

### B. Carga y estandarización inicial

Cargar todo como texto inicialmente.

Renombrar columnas desde los nombres originales a los nombres modelados en español.

Ejemplo: Timestamp → fecha_respuesta, Annual salary → salario_anual_raw, etc.

Mantener por un tiempo los “_raw” para auditoría.

### C. Limpieza de texto

Para cada campo texto/categórico:

- Quitar espacios al inicio/fin con trim()

- Colapsar espacios múltiples

- Normalizar comillas raras

- Estandarizar nulos: "", "NA", "N/A" → NULL.

### D. Parseo de fecha

### E. Normalización de moneda

Crear moneda como:
si Currency está en lista estándar (USD, GBP, EUR, etc.), usarla;
si Currency indica “Other”, usar Currency - other;
aplicar upper() y trimming.
Si no se encuentra la moneda aplica el valor tal como viene de la fuente.

### F. Parseo numérico de salario y compensación extra

Crear salario_anual desde salario_anual_raw:

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
- Cantidad de Registros/Encuestas
- Ubicación Geográfica
- Promedio Salarios por Industria
- Outliers de salario

