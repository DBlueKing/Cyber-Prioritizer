# Informe Estratégico de Mitigación de Riesgos Tecnológicos

| | |
|---|---|
| **Organización** | Empresa Simulada S.A. |
| **Fecha de emisión** | 2026-08-31 |
| **Clasificación** | TLP:AMBER — Uso interno |
| **Horizonte de análisis** | 30 días |
| **Marcos de referencia** | NIST CSF 2.0 · MITRE ATT&CK · CVSS v3.x · EPSS (FIRST.org) · KEV (CISA) |
| **Metodología** | Priorización estocástica mediante Cadenas de Markov |

---

## 1. Resumen Ejecutivo

La superficie de ataque evaluada comprende **68 activos** con
vulnerabilidades confirmadas, bajo una restricción presupuestaria de
**10 intervenciones de parcheo** en el ciclo actual.

La priorización tradicional por severidad técnica (CVSS) asigna recursos a las
vulnerabilidades más *graves en teoría*; el modelo estocástico propuesto los asigna
a las más *probables de ser explotadas contra el negocio*. Con el mismo presupuesto:

| Indicador | CVSS (tradicional) | Markov (propuesta) |
|---|---|---|
| Riesgo dinámico inicial | 17.6676 | 17.6676 |
| Riesgo residual tras 10 parches | 13.1248 | 12.2990 |
| Reducción de riesgo lograda | 25.7% | **30.4%** |

**Conclusión ejecutiva:** la estrategia propuesta evita un **6.3%
más de riesgo residual** que el método tradicional, sin inversión adicional. En
términos de eficiencia de capital, cada parche del plan propuesto rinde más unidades
de reducción de riesgo por peso invertido — el mismo principio de optimización bajo
restricción que gobierna cualquier cartera de inversiones.

## 2. Alcance y Metodología

El análisis integra cuatro fuentes de evidencia por cada vulnerabilidad: la severidad
técnica intrínseca (CVSS v3.x, NVD/NIST), la probabilidad de explotación observada
en el ecosistema real (EPSS, FIRST.org, actualizado diariamente), la confirmación de
explotación activa (catálogo KEV de CISA: los CVE listados reciben un piso de
probabilidad, pues la explotación deja de ser predicción) y la criticidad de
negocio del activo definida por la organización, ponderada por su exposición de red.
Sobre esta base, cada activo se
modela como una cadena de Markov de cuatro estados (Seguro, Detectado, Explotado,
Mitigado) cuya matriz de transición incorpora la tasa diaria de explotación derivada
del EPSS. La proyección a 30 días entrega la probabilidad de compromiso, que
ponderada por la criticidad de negocio produce el **Riesgo Dinámico** — la métrica
única de priorización de este informe.

## 3. Plan de Priorización de Parcheo (Top 10)

| prioridad   | host         | cve_id         | criticidad   |   cvss_score |   epss_score | kev   |   riesgo_dinamico | sla           |
|:------------|:-------------|:---------------|:-------------|-------------:|-------------:|:------|------------------:|:--------------|
| P1          | VPN-PA-01    | CVE-2024-3400  | Alta         |         10   |      0.99999 | SÍ ⚠  |          0.586104 | P1 — 72 horas |
| P2          | ADC-CTX-01   | CVE-2023-4966  | Alta         |          9.4 |      0.99999 | SÍ ⚠  |          0.586104 | P1 — 72 horas |
| P3          | VPN-PULSE-01 | CVE-2019-11510 | Alta         |         10   |      0.99999 | SÍ ⚠  |          0.586104 | P2 — 7 días   |
| P4          | LB-F5-01     | CVE-2022-1388  | Alta         |          9.8 |      0.99958 | SÍ ⚠  |          0.563119 | P2 — 7 días   |
| P5          | FW-EDGE-01   | CVE-2023-20198 | Alta         |         10   |      0.99571 | SÍ ⚠  |          0.535759 | P2 — 7 días   |
| P6          | SRV-VPN-01   | CVE-2018-13379 | Alta         |          9.1 |      0.99999 | SÍ ⚠  |          0.514182 | P3 — 30 días  |
| P7          | SRV-WEB-03   | CVE-2017-5638  | Alta         |          9.8 |      0.99999 | SÍ ⚠  |          0.514182 | P3 — 30 días  |
| P8          | SRV-MAIL-01  | CVE-2021-26855 | Alta         |          9.1 |      0.99996 | SÍ ⚠  |          0.503428 | P3 — 30 días  |
| P9          | SRV-WEB-01   | CVE-2021-41773 | Alta         |          9.8 |      0.99992 | SÍ ⚠  |          0.497076 | P3 — 30 días  |
| P10         | SRV-MAIL-02  | CVE-2022-41082 | Alta         |          8   |      0.9997  | SÍ ⚠  |          0.482568 | P3 — 30 días  |

Los SLA se asignan por posición en el ranking de riesgo: P1 (72 horas) para los dos
primeros activos, P2 (7 días) hasta la posición 5, P3 (30 días) para el resto.

## 4. Fichas de Acción por Activo

Como CISO con formación en Ingeniería Industrial, entiendo la ciberseguridad como la **optimización de la función de pérdida bajo restricciones severas** (tiempo, recursos de ingeniería y continuidad de negocio). El modelo de Markov ha priorizado estos 10 activos por su alta probabilidad de transición a un estado comprometido y su criticidad. 

A continuación, presento la ficha técnica de optimización y mitigación para cada vector de riesgo crítico expuesto en el perímetro.

---

### 1. VPN-PA-01 — CVE-2024-3400
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Aplicar inmediatamente la actualización de hotfix de PAN-OS proporcionada por Palo Alto Networks para la versión afectada.
   * *Mitigación temporal (si el parche no es inmediato):* Deshabilitar temporalmente la característica GlobalProtect o la telemetría de dispositivos (Device Telemetry) mediante la configuración del sistema, ya que el vector de inyección depende de esta función.
   * Verificar en los logs del firewall la ausencia de creación de archivos arbitrarios en `/var/appweb/sslvpn/dynamic/` o directorios similares.
3. **Impacto al Negocio si es Explotado**: Un atacante obtendría control total (nivel root) del perímetro de red, permitiéndole interceptar todo el tráfico corporativo, moverse lateralmente hacia la red interna sin resistencia y comprometer la confidencialidad de las comunicaciones y credenciales de toda la organización.

---

### 2. ADC-CTX-01 — CVE-2023-4966
1. **MITRE ATT&CK**: TA0004 (Escalación de Privilegios) — T1068 (Explotación para Escalación de Privilegios).
2. **Plan de Mitigación Técnico**:
   * Actualizar el firmware de NetScaler ADC y NetScaler Gateway a las versiones corregidas (13.1-49.13, 12.1-65.25, etc.).
   * *Mitigación temporal:* Invalidar todas las sesiones activas existentes (`kill icr_sessions` y reiniciar el servicio AAA) y revocar las cookies de sesión emitidas antes del parche, ya que la vulnerabilidad permite el robo de memoria de sesión (Citrix Bleed).
3. **Impacto al Negocio si es Explotado**: Exposición masiva de sesiones de usuario activas. Los atacantes pueden secuestrar identidades corporativas sin necesidad de credenciales, accediendo a aplicaciones publicadas y datos sensibles de clientes y empleados con la misma autoridad que el usuario legítimo.

---

### 3. VPN-PULSE-01 — CVE-2019-11510
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Aplicar el parche oficial de Pulse Secure (versiones 8.2R12.1, 8.3R7.1, 9.0R3.4 o superiores).
   * Forzar el reseteo de todas las contraseñas de usuarios y administradores que hayan transitado por esta VPN, así como la invalidación de certificados de sesión.
   * *Mitigación temporal:* Restringir el acceso por IP de origen (whitelisting estricto) en el firewall perimetral hacia la interfaz de administración y portal de usuarios de la VPN.
3. **Impacto al Negocio si es Explotado**: Compromiso histórico de credenciales y lectura de archivos del sistema (incluyendo archivos de configuración con contraseñas en texto plano). Pérdida total de la confianza en el perímetro de acceso remoto, obligando a una auditoría forense y potencial notificación regulatoria por brecha de datos.

---

### 4. LB-F5-01 — CVE-2022-1388
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Actualizar BIG-IP a las versiones 16.1.2.2, 15.1.5.1, 14.1.4.6, 13.1.5 o posteriores.
   * *Mitigación temporal:* Bloquear el acceso a la interfaz de administración de iControl REST desde redes externas/no confiables mediante reglas de control de acceso (Port Lockdown o packet filters en el propio BIG-IP). Asegurar que la gestión solo sea accesible desde una VLAN de administración aislada.
3. **Impacto al Negocio si es Explotado**: Bypass completo de la autenticación de gestión. Un atacante puede ejecutar comandos arbitrarios en el balanceador, alterando el enrutamiento del tráfico, interceptando transacciones financieras o interrumpiendo totalmente la disponibilidad de los servicios críticos expuestos al público.

---

### 5. FW-EDGE-01 — CVE-2023-20198
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Actualizar Cisco IOS XE a una versión que contenga la corrección definitiva para CVE-2023-20198 y CVE-2023-20273.
   * *Mitigación temporal:* Deshabilitar las características de servidor HTTP/HTTPS (Web UI) en los equipos IOS XE expuestos a Internet (`no ip http server` / `no ip http secure-server`).
   * Auditar el sistema en busca de usuarios locales no autorizados o implantes en el sistema de archivos (`show users`, verificar modificaciones en flash).
3. **Impacto al Negocio si es Explotado**: Control absoluto del dispositivo perimetral con privilegios de root. El atacante puede desplegar persistencia a nivel de firmware, desviar tráfico corporativo (Man-in-the-Middle) y colapsar las operaciones de red de toda la compañía.

---

### 6. SRV-VPN-01 — CVE-2018-13379
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Actualizar FortiOS a las versiones 6.0.5, 5.6.8, 5.4.13 o superiores. 
   * *Mitigación temporal:* Deshabilitar el portal web de SSL VPN (`config vpn ssl settings` -> `status disable`) y migrar temporalmente el acceso remoto al cliente Tunel VPN si el servicio web no es estrictamente necesario, o restringir accesos por geolocalización/IP en el firewall upstream.
3. **Impacto al Negocio si es Explotado**: Descarga no autenticada de archivos de sistema, incluyendo el archivo con las credenciales cifradas de los usuarios de la VPN. Permite la suplantación masiva de identidad de empleados y contratistas, comprometiendo la red interna desde las terminales de confianza.

---

### 7. SRV-WEB-03 — CVE-2017-5638
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Actualizar Apache Struts a la versión 2.3.32 o 2.5.10.1 (o versiones soportadas actuales).
   * *Mitigación temporal:* Cambiar el analizador Multipart utilizado (por ejemplo, implementando un wrapper personalizado) o desplegar una regla específica en el WAF (Web Application Firewall) que inspeccione la cabecera `Content-Type` en busca de patrones OGNL maliciosos (`#cmd=`, `#context`, etc.).
3. **Impacto al Negocio si es Explotado**: Ejecución remota de comandos en el servidor del portal de clientes. Si el servidor carece de segmentación adecuada, un atacante puede pivotar hacia bases de datos de clientes, provocando la exfiltración masiva de PII (Información de Identificación Personal), multas regulatorias severas y daño reputacional irreversible.

---

### 8. SRV-MAIL-01 — CVE-2021-26855
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública / SSRF).
2. **Plan de Mitigación Técnico**:
   * Aplicar los parches de actualización acumulativos de Microsoft Exchange (marzo de 2021 o posteriores).
   * *Mitigación temporal:* Utilizar el script de mitigación de Microsoft (EOMT.ps1) o configurar reglas de bloqueo en el firewall de aplicaciones/IIS para restringir las peticiones HTTP sospechosas que explotan el proxy SSRF (`/ecp/` y cabeceras `Cookie: X-BEResource`).
3. **Impacto al Negocio si es Explotado**: Acceso no autorizado al servidor de correo corporativo. Permite el robo masivo de comunicaciones confidenciales, secretos comerciales y la utilización de cuentas legítimas para realizar campañas de suplantación de identidad (Phishing) dirigidas a clientes y proveedores.

---

### 9. SRV-WEB-01 — CVE-2021-41773
1. **MITRE ATT&CK**: TA0001 (Acceso Inicial) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Actualizar Apache HTTP Server a la versión 2.4.51 o superior (la versión 2.4.49 introdujo la falla y 2.4.50 tuvo un parche incompleto).
   * *Mitigación temporal:* Revisar la configuración de Apache para asegurar que todas las directrices `Require` estén configuradas explícitamente en `denied` para directorios fuera de la raíz web, y deshabilitar explícitamente los scripts CGI si no son necesarios (`Options -ExecCGI`).
3. **Impacto al Negocio si es Explotado**: Exposición de archivos del sistema operativo fuera del directorio web y posible ejecución remota de código si CGI está habilitado. Permite al atacante comprometer la integridad del portal público de clientes y utilizarlo como cabeza de playa para escalar privilegios en la red de servidores de plataforma.

---

### 10. SRV-MAIL-02 — CVE-2022-41082
1. **MITRE ATT&CK**: TA0002 (Ejecución) — T1190 (Explotar Aplicación Pública).
2. **Plan de Mitigación Técnico**:
   * Aplicar la actualización de seguridad de Microsoft correspondiente a noviembre de 2022 o posterior para Exchange Server.
   * *Mitigación temporal:* Implementar las reglas de reescritura de URL en IIS recomendadas por Microsoft para bloquear solicitudes maliciosas dirigidas a PowerShell backend que aprovechan la vulnerabilidad de deserialización (vulnerabilidad gemela de ProxyNotShell, CVE-2022-41040).
3. **Impacto al Negocio si es Explotado**: Ejecución remota de código a través de PowerShell con los privilegios del servicio de Exchange. Permite a actores maliciosos instalar puertas trasera persistentes (webshells), exfiltrar libretas de direcciones, leer correos electrónicos y comprometer la continuidad de las operaciones administrativas y de colaboración de la empresa.

## 5. Riesgo Aceptado Temporalmente (58 activos diferidos)

Los siguientes activos quedan fuera del presupuesto del ciclo actual. Esta es una
decisión de **aceptación temporal de riesgo**, no una omisión:

| host          | cve_id         | criticidad   |   riesgo_dinamico |
|:--------------|:---------------|:-------------|------------------:|
| SRV-MFT-01    | CVE-2023-34362 | Alta         |        0.471978   |
| CAM-EXT-01    | CVE-2021-36260 | Media        |        0.467704   |
| SRV-APP-01    | CVE-2021-44228 | Alta         |        0.437055   |
| SRV-APP-02    | CVE-2021-44228 | Alta         |        0.437055   |
| NAS-QNAP-01   | CVE-2021-28799 | Alta         |        0.43023    |
| SRV-ERP-01    | CVE-2020-14882 | Alta         |        0.429979   |
| SRV-VC-01     | CVE-2021-21972 | Alta         |        0.391603   |
| SRV-RDP-01    | CVE-2019-0708  | Alta         |        0.359927   |
| SRV-IAM-01    | CVE-2021-40539 | Alta         |        0.352994   |
| GW-CTX-02     | CVE-2019-19781 | Media        |        0.351662   |
| ADC-CTX-02    | CVE-2023-3519  | Media        |        0.326213   |
| SRV-VPN-02    | CVE-2018-13379 | Media        |        0.308509   |
| SRV-DC-01     | CVE-2020-1472  | Alta         |        0.304526   |
| SRV-DC-02     | CVE-2020-1472  | Alta         |        0.304526   |
| SRV-WEB-04    | CVE-2021-41773 | Media        |        0.298246   |
| SRV-FILE-02   | CVE-2017-0144  | Alta         |        0.296554   |
| SRV-WEB-02    | CVE-2023-25690 | Alta         |        0.282013   |
| RTR-GPON-01   | CVE-2018-10561 | Media        |        0.271656   |
| SRV-WIKI-01   | CVE-2022-26134 | Media        |        0.262233   |
| SRV-APP-03    | CVE-2021-44228 | Media        |        0.262233   |
| SRV-CMS-01    | CVE-2018-7600  | Media        |        0.252919   |
| SRV-ERP-02    | CVE-2019-2725  | Media        |        0.244936   |
| MOB-GER-02    | CVE-2021-30883 | Media        |        0.219715   |
| MOB-GER-01    | CVE-2021-1048  | Media        |        0.219715   |
| SRV-LEGACY-03 | CVE-2019-0708  | Media        |        0.215956   |
| WKS-FIN-05    | CVE-2021-34527 | Media        |        0.215573   |
| WKS-FIN-06    | CVE-2021-34527 | Media        |        0.215573   |
| WKS-VTA-03    | CVE-2021-34527 | Media        |        0.215573   |
| SRV-WIKI-02   | CVE-2023-22515 | Media        |        0.214799   |
| WKS-RRHH-01   | CVE-2022-30190 | Media        |        0.205493   |
| SRV-FILE-03   | CVE-2020-0796  | Media        |        0.190953   |
| WKS-ADM-01    | CVE-2021-4034  | Media        |        0.179461   |
| SRV-FILE-04   | CVE-2017-0144  | Media        |        0.177932   |
| SRV-WEB-05    | CVE-2023-25690 | Media        |        0.169208   |
| CAM-INT-03    | CVE-2021-33044 | Baja         |        0.163705   |
| CAM-INT-02    | CVE-2021-36260 | Baja         |        0.163696   |
| TEL-VOIP-02   | CVE-2020-3161  | Baja         |        0.14552    |
| TEL-VOIP-01   | CVE-2020-3161  | Baja         |        0.14552    |
| SRV-DB-02     | CVE-2016-6662  | Alta         |        0.144821   |
| SRV-DB-01     | CVE-2016-6662  | Alta         |        0.144821   |
| SRV-LNX-02    | CVE-2023-4911  | Media        |        0.132276   |
| SRV-LNX-01    | CVE-2022-0847  | Media        |        0.132276   |
| SRV-DOCKER-01 | CVE-2022-0492  | Media        |        0.132276   |
| AP-WIFI-01    | CVE-2021-20090 | Baja         |        0.119755   |
| WKS-DEV-03    | CVE-2021-3156  | Baja         |        0.112646   |
| WKS-DEV-02    | CVE-2021-3156  | Baja         |        0.112646   |
| SRV-LEGACY-02 | CVE-2014-0160  | Baja         |        0.107978   |
| SRV-LEGACY-01 | CVE-2014-0160  | Baja         |        0.107978   |
| WKS-VTA-04    | CVE-2021-34527 | Baja         |        0.107786   |
| WKS-RRHH-02   | CVE-2022-30190 | Baja         |        0.102746   |
| WKS-DEV-04    | CVE-2021-4034  | Baja         |        0.0897305  |
| WKS-TI-01     | CVE-2016-5195  | Baja         |        0.0814969  |
| PRN-RRHH-02   | CVE-2021-39238 | Baja         |        0.0194947  |
| PRN-FIN-01    | CVE-2021-39238 | Baja         |        0.0194947  |
| SRV-LNX-03    | CVE-2021-33909 | Media        |        0.010873   |
| SRV-LNX-05    | CVE-2023-32233 | Baja         |        0.00728206 |
| PLC-OT-01     | CVE-2021-22779 | Alta         |        0.00622923 |
| SRV-LNX-04    | CVE-2022-2588  | Baja         |        0.00326171 |

El riesgo residual aceptado asciende a **12.2990** unidades de riesgo. Se recomienda: (a) aplicar mitigaciones compensatorias de bajo costo (segmentación de red, reglas WAF, monitoreo reforzado) sobre estos activos, y (b) re-evaluar la priorización en el próximo ciclo con EPSS actualizado, dado que la probabilidad de explotación es dinámica.

## 6. Análisis Detallado, Conclusiones y Hoja de Ruta

### 6.1 Análisis Detallado de Hallazgos

El análisis sobre el parque tecnológico de 68 activos revela una concentración crítica de riesgo en la infraestructura perimetral y de acceso remoto. El modelo dinámico (Markov), que pondera la probabilidad de explotación a 30 días junto con la criticidad del negocio, demuestra que el riesgo real no se correlaciona linealmente con la severidad teórica (CVSS).

**Patrones observados y familias de CVE:**
*   **Perímetro e Infraestructura Crítica:** El tope del ranking está dominado por vulnerabilidades de ejecución remota de código y bypass de autenticación en pasarelas corporativas y dispositivos de borde (VPN-PA-01, ADC-CTX-01, VPN-PULSE-01, LB-F5-01, FW-EDGE-01). Estos activos combinan puntuaciones CVSS máximas (9.4 a 10.0) con valores EPSS extremadamente altos ($\approx 0.999$), resultando en las probabilidades de explotación a 30 días más elevadas del inventario ($>0.56$).
*   **Divergencia CVSS vs EPSS/Markov:** Se identifican desviaciones significativas que justifican el uso del modelo predictivo. Casos como **CAM-EXT-01** (CVE-2021-36260, criticidad Media) presentan un CVSS de 9.8 pero un riesgo dinámico elevado (0.467) impulsado por una alta probabilidad de explotación (0.779). Inversamente, servidores de bases de datos como **SRV-DB-01/02** (CVE-2016-6662, criticidad Alta y CVSS 9.8) quedan relegados al puesto 49 debido a un EPSS bajo (0.677) y menor probabilidad de explotación a corto plazo (0.206).
*   **Concentración de Riesgo:** Los primeros 11 activos del ranking (todos de criticidad Alta, abarcando pasarelas, servidores web, de correo y transferencia de ficheros) representan una fracción desproporcionada del riesgo total inicial (17.6676), siendo liderados por **VPN-PA-01** (CVE-2024-3400) con un riesgo dinámico individual de 0.5861.

---

### 6.2 Conclusiones

1.  **Exposición Perimetral Inmediata:** Los primeros 5 activos del ranking (**VPN-PA-01, ADC-CTX-01, VPN-PULSE-01, LB-F5-01 y FW-EDGE-01**) concentran la mayor probabilidad de compromiso externo, con métricas de riesgo dinámico superiores a 0.53 y una probabilidad de explotación que supera el 53% en los próximos 30 días.
2.  **Ineficiencia del Modelo Tradicional (CVSS):** El Test A/B demuestra que priorizar exclusivamente bajo criterios estáticos de severidad (CVSS) deja un riesgo residual de **13.1248**, mientras que la priorización basada en el modelo estocástico de Markov reduce el riesgo residual a **12.2990**.
3.  **Eficiencia Cuantificada:** La aplicación del modelo predictivo propuesto otorga una **ventaja analítica y de mitigación del 6.3%** frente al enfoque tradicional, optimizando la asignación de recursos limitados.
4.  **Validación del Presupuesto Operativo:** Con una capacidad limitada a 10 parches iniciales, el uso del ranking dinámico asegura que el esfuerzo de remediación se concentre estrictamente en los vectores de ataque con mayor probabilidad real de materialización en el corto plazo.

---

### 6.3 Hoja de Ruta de Resolución

El plan de remediación se estructura en tres fases estrictas basadas en el orden de riesgo dinámico del ranking, asignando contenciones para los activos diferidos:

*   **Fase 1: Ventana de 72 Horas (Mitigación de Borde y Críticos Superiores)**
    *   *Alcance (Top 5 del ranking):* 
        *   `VPN-PA-01` (CVE-2024-3400)
        *   `ADC-CTX-01` (CVE-2023-4966)
        *   `VPN-PULSE-01` (CVE-2019-11510)
        *   `LB-F5-01` (CVE-2022-1388)
        *   `FW-EDGE-01` (CVE-2023-20198)
    *   *Acción:* Aplicación de parches de emergencia o mitigaciones del fabricante (mitigación de ceros días / configuraciones de bloqueo perimetral).
    *   *Medidas compensatorias para diferidos en esta ventana:* Activación de reglas de bloqueo estricto en WAF/IPS y restricción de acceso geográfico/IP para los servicios expuestos no parcheados inmediatamente.

*   **Fase 2: Ventana de 7 Días (Servicios Internos Críticos y Exposición Media-Alta)**
    *   *Alcance (Posiciones 6 a 15 del ranking):* 
        *   `SRV-VPN-01`, `SRV-WEB-03`, `SRV-MAIL-01`, `SRV-WEB-01`, `SRV-MAIL-02`, `SRV-MFT-01`, `CAM-EXT-01`, `SRV-APP-01`, `SRV-APP-02`, `NAS-QNAP-01`, `SRV-ERP-01`.
    *   *Acción:* Despliegue masivo de actualizaciones para vulnerabilidades con EPSS cercano a 1.0 (Log4Shell, servidores de correo y transferencia de ficheros).
    *   *Medidas compensatorias para diferidos:* Microsegmentación de red para aislar segmentos vulnerables, desactivación temporizada de servicios no esenciales (ej. paneles de cámaras externos) y monitoreo reforzado mediante SIEM/EDR enfocado en técnicas de movimiento lateral y ejecución de código.

*   **Fase 3: Ventana de 30 Días (Resto del Inventario y Remediación Estructural)**
    *   *Alcance (Posiciones 16 a 68 del ranking):* 
        *   Resto de activos identificados con riesgo dinámico decreciente (desde `SRV-VC-01` hasta `SRV-LNX-04`).
    *   *Acción:* Parcheo sistemático de estaciones de trabajo, servidores secundarios, entornos de desarrollo y dispositivos de red de baja criticidad.
    *   *Medidas compensatorias:* Aplicación de parches virtuales vía reglas de mitigación perimetral y control de cuentas privilegiadas (PAM) en los sistemas operativos legados o pendientes de actualización.

*   **Criterio de Re-evaluación:** 
    Se ejecutará un ciclo de escaneo de validación posterior a cada fase (T+72h, T+7d, T+30d) recalculando el modelo de Markov. El cierre de cada ticket de vulnerabilidad estará condicionado a que el riesgo dinámico residual del activo retorne a umbrales aceptables por la política de la organización (< 0.05).

## 7. Supuestos, Limitaciones y Próximos Pasos

**Supuestos del modelo:** probabilidad diaria de detección p_d = 0.1 y de mitigación
p_m = 0.05 base, moduladas por un factor de capacidad operativa según tipo de activo;
el EPSS se interpreta como probabilidad de explotación a 30 días y se convierte a tasa
diaria equivalente; la criticidad de negocio se pondera Alta = 1.0, Media = 0.6,
Baja = 0.3, multiplicada por el factor de exposición (Internet 1.0 / DMZ 0.85 /
Interna 0.7); los CVE en catálogo KEV reciben un piso de EPSS efectivo de 0.9.

**Limitaciones:** el modelo no captura dependencias entre activos (cadenas de ataque
multi-host) ni la variación temporal del EPSS dentro del horizonte; los factores por
tipo de activo son estimaciones documentadas, no aprendidas de datos; la asignación de
activos a pilares NIST CSF es una aproximación de la función de negocio expuesta.

**Próximos pasos recomendados:** re-ejecutar la priorización semanalmente con EPSS
actualizado; realizar análisis de sensibilidad sobre p_d y p_m; validar
retrospectivamente el ranking contra explotaciones observadas (backtesting).

---
*Informe generado automáticamente por el framework de priorización estocástica de
vulnerabilidades (Cadenas de Markov + NVD/EPSS/KEV + MITRE ATT&CK). Los indicadores
cuantitativos provienen directamente del modelo matemático; ningún valor numérico
es generado por IA.*
