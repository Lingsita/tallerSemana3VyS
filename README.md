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
