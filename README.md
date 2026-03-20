# Simulador de Costo Laboral - Liquidaciones y DDJJ

## Descripción
Esta es una herramienta web interactiva diseñada para calcular el costo laboral de empleados en Argentina. Permite simular las liquidaciones y Declaraciones Juradas (DDJJ) detallando los aportes del trabajador y las contribuciones del empleador según la normativa laboral y previsional vigente.

## Características Principales
* **Perfil Detallado del Empleado y Empleador:** Selección exhaustiva de la situación de revista, condición, actividad (incluyendo regímenes especiales como el agrario, construcción, etc.), y modalidad de contratación.
* **Cálculo de Bases Imponibles:** Generación de las 11 remuneraciones base (Rem 1 a Rem 11) con posibilidad de edición manual.
* **Cálculos Automáticos y en Tiempo Real:** Los montos de jubilación (SIPA), obra social, INSSJyP (PAMI), Fondo Nacional de Empleo (FNE), Asignaciones Familiares, Ley de Riesgos de Trabajo (ART) y Seguro Colectivo de Vida Obligatorio (SCVO) se recalculan automáticamente al modificar los datos.
* **Regímenes Especiales:** Soporte para cálculos diferenciales, aportes adicionales y validaciones para trabajadores agrarios (RENATRE, seguro de sepelio, jubilación anticipada).
* **Opciones de Exportación:** 
  * Imprimir un reporte limpio y formateado para presentación física o PDF.
  * Exportar los datos completos del cálculo a un archivo JSON para su posterior análisis o almacenamiento.

## Estructura Técnica
El proyecto está construido como un archivo único (Single File Component) para máxima portabilidad, sin requerir frameworks ni dependencias externas:
* **HTML5:** Estructura semántica del formulario de carga de datos.
* **CSS3:** Estilos responsivos e integrados en el mismo archivo, incluyendo `@media print` para optimizar la impresión de los comprobantes.
* **JavaScript (Vanilla):** Lógica encargada de aplicar las diferentes alícuotas, deducciones y topes en el lado del cliente (frontend).

---

## Instructivo de Uso

La herramienta funciona en un único archivo HTML (`index.html`) y procesa todos los cálculos en tiempo real en tu navegador. Al modificar cualquier campo o seleccionar opciones, los totales de aportes y contribuciones se actualizarán de forma inmediata.

**Importante:** Los campos marcados con un asterisco rojo (**\***) son obligatorios para reflejar las alícuotas de forma correcta.

### 1. Datos Generales
Aquí ingresarás la información base del empleado y del empleador.
- **CUIL (\*) y Apellido y nombre:** Datos identificatorios del trabajador.
- **Cónyuge y Trabajador en CCT:** Casillas de verificación (checkboxes) para determinar características del grupo familiar y convenio aplicable.
- **SCVO (Seguro Colectivo de Vida Obligatorio):** Márcalo si el empleador debe abonar este seguro por el empleado.
- **Corresponde reducción:** Aplica para determinar si el empleado goza de alguna reducción en contribuciones patronales bajo normativas específicas.
- **Hijos:** Ingresa la cantidad de hijos (valor numérico).
- **Tipo de Empleador (\*):** Determina qué porcentaje de contribuciones patronales corresponde aplicar (según Decreto 814/01 art. 2 y/o Ley 27.541). Elegir entre Tipo 1 (Aplica 10.77% o reducidos para PYMES) o Tipo 2 (Aplica 12.35%).
- **Base diferencial LRT:** Monto extra sobre el cual calcular la cuota variable de la ART.

### 2. Perfil del Trabajador
Define el marco normativo bajo el cual el empleado está contratado. Cada opción altera los porcentajes aplicados:
- **Situación (\*):** Estado actual en el período (Activo, Licencia por Maternidad, Suspendido, etc.). Las licencias pueden anular ciertos aportes.
- **Condición (\*):** Servicios comunes, trabajador jubilado, menor o diferenciados. *Nota: Si seleccionas "Jubilado", se anulan los aportes a la Obra Social e INSSJyP*.
- **Actividad (\*):** Actividad específica. Para la actividad `097 - Trabajador Agrario`, se agregan automáticamente conceptos como el RENATRE y se ajusta el FNE.
- **Modalidad Contratación (\*):** Tiempo indeterminado, plazo fijo, temporada, pasantía, entre otros.
- **Código de siniestrado (\*):** Relevante si hubo cobertura de ART (ILT, ILPPD).
- **Localidad (\*):** Zona geográfica aplicable.

### 3. Bases Imponibles
Sección de ingreso **manual** donde debes volcar las bases de liquidación de sueldo del sistema de RRHH:
- **Remuneración Bruta:** El total devengado.
- **Remuneración 1 a Remuneración 11:** Casilleros para las bases de donde surgen distintos conceptos.
  - *Rem 1:* SIPA.
  - *Rem 2/3:* PAMI, FNE, AAFF, RENATRE.
  - *Rem 4/8:* Obra Social.
  - *Rem 9:* ART.
  - *Rem 10:* Base especial post detracción.

### 4. Datos Complementarios
Aplica para meses con múltiples situaciones (ej. 15 días activo y 15 de licencia).
- **Situaciones de revista (P1, P2 y P3):** Lista cronológica de situaciones en el mes.
- **Día de inicio:** Día calendario en que inicia P1, P2 y P3.
- **Días / Horas trabajadas:** Factores de proporcionalidad.

### 5. Seguridad Social y Obra Social
- **Seguridad Social:** Alícuotas de aportes y contribuciones adicionales y bases diferenciales.
- **Obra Social (\*):** Catálogo de Obras Sociales. *Seleccionar "000000 - NINGUNA" cancela los recargos de Obra Social para ese CUIL.* Existen campos para aportes y contribuciones extra.

### 6. Resultados y Exportación
El sistema muestra los resultados consolidados de manera tabulada en la parte inferior.
- **Tabla Aportes del Trabajador y Contribuciones del Empleador:** Todas las retenciones desglosadas con sus respectivas bases e importes.
- **Recuadro de Notas:** El sistema proveerá diagnósticos clave sobre el cálculo detectado (como advertencias por ser jubilado, trabajador agrario, u obra social en cero).
- **Herramientas de Exportación:** Usa el botón **Imprimir** para obtener una versión en PDF/Papel sin elementos de interfaz, o el botón **Exportar** para conservar el conjunto numérico como archivo JSON.

---

## Cómo Funcionan los Cálculos (Lógica JavaScript)
El proceso de cálculo se basa en una función central que toma todos los *inputs*, asigna bases imponibles a sus correspondientes tasas (soportadas o reescritas por casos especiales) y las multiplica:

1. **Recolección de Variables:** Se leen en tiempo real los valores de los selectores, checkboxes, campos de Remuneraciones, Obra Social, entre otros.
2. **Definición de Alícuotas por Empleador:** Dependiendo si es "Tipo 1" (Grupo B) o "Tipo 2" (Grupo A), se varían las retenciones patronales de base para SIPA, PAMI, AAFF y FNE.
3. **Manejo de Excepciones y Regímenes:**
   * **Jubilados:** Intercepta la condición jubilatoria forzando multiplicadores de PAMI y Obra Social a cero (`0%`).
   * **Agrarios:** Incrementa variables de Seguros de Sepelio, Jubilación Anticipada y RENATRE en función de un 1.5% o 2% fijo. También fuerza el porcentaje de cálculo del Fondo Nacional de Empleo en 0.
   * **Distribución de Obra Social:** Separa los fondos deducidos para OS en un porcentaje de redistribución (FSR, Fondo Solidario de Redistribución) dependiendo del nivel de facturación (umbral determinado de Remuneración), quedando entre 10% y 15% para FSR y el remanente neto a la OS correspondiente.
4. **Resumen de Variables:** Multiplicación de Remuneración x Alícuota y sumatoria final del "Costo Laboral" como la _Remuneración Bruta + Total Contribuciones Patronales_.

## Entorno y Requisitos
* Sistema Operativo: Multiplataforma (Windows, macOS, Linux).
* Requisitos: Cualquier navegador web moderno. No requiere conexión continua ni instalación.
