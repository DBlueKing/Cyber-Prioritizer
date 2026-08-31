# Cyber-Prioritizer: Algoritmo de Priorización Estocástica de Vulnerabilidades

Motor de análisis de riesgo dinámico que combina Cadenas de Markov, inteligencia de amenazas en tiempo real (NVD, EPSS, CISA KEV) y mapeo táctico (MITRE ATT&CK) para optimizar la gestión de vulnerabilidades bajo restricciones de presupuesto operativo.

**Propuesta de Valor: Cyber-Prioritizer no busca reemplazar a NVD, CVSS o EPSS, sino actuar como un complemento orquestador. Toma estas métricas técnicas aisladas y las traduce en decisiones ejecutivas, logrando una reducción directa en costos operativos y horas-hombre al evitar el parcheo innecesario de vulnerabilidades sin probabilidad real de explotación.**

## Creado por **Lukas Benjamín Ahubert Lennon**.

---

## La Metodología

La priorización tradicional basada únicamente en la severidad técnica (CVSS) es estática y asume que todas las vulnerabilidades críticas serán explotadas. Este algoritmo introduce una **metodología estocástica**:

1.  **Probabilidad Real:** Convierte el puntaje EPSS en una tasa diaria de explotación predictiva.
2.  **Cadenas de Markov:** Modela cada activo en una matriz de transición de 4 estados (Seguro, Detectado, Explotado, Mitigado) para proyectar la probabilidad de compromiso en un horizonte de 30 días.
3.  **Riesgo Dinámico:** Pondera la probabilidad matemática de explotación con la criticidad de negocio y la exposición de red del activo.
4.  **Optimización bajo Restricción:** Demuestra matemáticamente cómo asignar *K* parches disponibles para reducir la mayor cantidad de riesgo residual posible.

## Características Principales

*   **Threat Intel Automatizado:** Extracción resiliente desde NIST NVD, FIRST (EPSS) y el catálogo de explotación confirmada de CISA (KEV).
*   **Test A/B Integrado:** Dashboard comparativo que contrasta la curva de mitigación del modelo de Markov vs. el enfoque estático CVSS tradicional.
*   **Generación de Informes Estratégicos:** Exportación automática de la telemetría a reportes ejecutivos en formato Markdown, HTML y DOCX, además de grillas operativas en XLSX para equipos de SecOps.

## Uso Rápido (Demo Interactiva)

Para evaluar el modelo de forma inmediata sin configuración de entorno local:
1. Dirígete a la carpeta `/notebooks`.
2. Abre el archivo `Cyber_Prioritizer.ipynb` en Google Colab o Jupyter.
3. Ejecuta las celdas para procesar la matriz simulada o cargar el inventario CSV de tu propia organización.


## Plantilla de Inventariado

1. Dirígete a la carpeta `/Data`.
2. Abre el archivo `Formulario_Inventario.xlsx`.
3. Dentro del archivo hay parametros que mantienen homogeneidad respecto al llenado e instrucciones.

---

## Licenciamiento Dual y Uso Comercial

Este proyecto de ingeniería opera bajo un modelo de **Licenciamiento Dual**:

### 1. Uso Open Source (AGPLv3)
El código fuente está disponible gratuitamente bajo la licencia GNU AGPLv3. Eres libre de usarlo, modificarlo y distribuirlo para fines académicos, personales o proyectos de código abierto, siempre y cuando cualquier trabajo derivado o servicio de red que lo integre también se libere bajo los términos de la AGPLv3.

### 2. Licencia Comercial Corporativa
Para organizaciones corporativas que requieran integrar este motor de priorización en plataformas propietarias, servicios gestionados de seguridad, o que por normativas internas de *Compliance* no puedan cumplir con los términos de apertura de código de la AGPLv3, se requiere una Licencia Comercial. 

Para adquirir una licencia comercial exclusiva, solicitar integración corporativa o consultoría especializada en optimización de procesos y modelamiento de riesgos digitales, las consultas se gestionan a través de **IKUBERT**.

📧 *Contacto:* Contact@ikubert.com

---

# 🗺️ Roadmap y Próximas Mejoras (Versión 2.0)

El desarrollo continuo de **Cyber-Prioritizer** contempla las siguientes optimizaciones para la próxima iteración del framework:

### 1. Escalabilidad para Inventarios Masivos
*   **Objetivo:** Optimizar el pipeline de ingesta y procesamiento (`DataIngestor` y motor de Markov) para soportar y calcular eficientemente inventarios de nivel *Enterprise* superiores a los 1.000 activos simultáneos.
*   **Impacto:** Reducción de tiempos de cómputo en el Test A/B y optimización del uso de memoria al cruzar data con las APIs de inteligencia de amenazas.

### 2. Estabilidad Determinista en el Baseline (CVSS)
*   **Objetivo:** Garantizar la reproducibilidad bit a bit en los empates de severidad del modelo estático. 
*   **Detalle Técnico:** Actualmente, la métrica CVSS genera múltiples empates (ej. varios activos con 9.8 o 10.0), los cuales el método `sort_values` de Pandas resuelve de forma no determinista por defecto. Para la v2, las funciones `_curva_mitigacion`, `_hosts_parcheados` y `_ventaja_pct` implementarán el ordenamiento dual: `sort_values(["cvss_score", "riesgo_dinamico"], ascending=False)`.
*   **Impacto:** Asegura que el comparador baseline (Estrategia A) se comporte de manera 100% predecible en cada ejecución del ciclo.

### 3. Optimización de Visualización (Mapa de Riesgo)
*   **Objetivo:** Refactorizar el renderizado del Gráfico 4 (Mapa de Riesgo de Activos) para resolver la superposición de etiquetas de texto (*overplotting*) en clústeres de alta densidad.
*   **Impacto:** Mejora en la legibilidad ejecutiva del dashboard cuando se analizan infraestructuras complejas con riesgo altamente concentrado.

---
