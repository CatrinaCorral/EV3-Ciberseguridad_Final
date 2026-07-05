EV3-Ciberseguridad_Final

Descripción

Este proyecto implementa un pipeline de DevSecOps que integra prácticas de seguridad a lo largo del ciclo de vida del desarrollo de software (SDLC). La solución incorpora automatización de compilación, pruebas, análisis de seguridad, gestión de dependencias, monitorización y generación de evidencia de trazabilidad mediante Jenkins y herramientas de código abierto.

Objetivos

- Automatizar el proceso de integración y despliegue continuo (CI/CD).
- Detectar y corregir vulnerabilidades de seguridad durante el desarrollo.
- Gestionar de forma segura las dependencias del proyecto.
- Mantener evidencia de auditoría mediante documentación y trazabilidad automatizadas.
- Monitorizar el comportamiento de la aplicación en ejecución.

Tecnologías utilizadas

- Python
- Flask
- Docker
- Jenkins
- GitHub
- OWASP Dependency-Check
- OWASP ZAP
- Bandit
- Pytest
- Prometheus
- Grafana
- Dependabot

Pipeline de CI/CD

El pipeline implementado automatiza las siguientes etapas:

1. Obtención del código fuente desde GitHub.
2. Construcción de la imagen Docker.
3. Ejecución de pruebas unitarias.
4. Despliegue en un entorno de staging.
5. Análisis de dependencias (SCA).
6. Análisis dinámico de seguridad (DAST).
7. Generación automática de documentación y trazabilidad del build.

Seguridad implementada

Durante el desarrollo se identificaron y mitigaron distintas vulnerabilidades, incluyendo:

- Protección contra SQL Injection mediante consultas parametrizadas.
- Desactivación del modo debug en producción.
- Validación de acceso para prevenir vulnerabilidades de tipo IDOR.
- Incorporación de cabeceras HTTP de seguridad recomendadas.
- Actualización de dependencias vulnerables mediante Dependabot y OWASP Dependency-Check.

La estrategia de seguridad combina:

- SAST: Bandit.
- SCA: OWASP Dependency-Check.
- DAST: OWASP ZAP.

Gestión de dependencias

Las dependencias del proyecto se mantienen mediante:

- Versionado controlado en "requirements.txt".
- Supervisión automática con Dependabot.
- Verificación continua durante cada ejecución del pipeline utilizando OWASP Dependency-Check.

Monitorización

El entorno incorpora:

- Prometheus para la recolección de métricas.
- Grafana para la visualización del estado del sistema y métricas de la aplicación.

Esta monitorización permite detectar comportamientos anómalos y verificar el estado de los servicios en tiempo real.

Documentación y trazabilidad

Cada ejecución del pipeline genera automáticamente evidencia del proceso, incluyendo:

- Reportes de pruebas.
- Resultados de análisis de seguridad.
- Reportes de dependencias.
- Archivo de trazabilidad del build.
- Historial de cambios mediante Git y GitHub.

Esto facilita auditorías, mantenimiento y seguimiento de la evolución del proyecto.

Estructura del proyecto

.
├── .github/
│   └── dependabot.yml
├── docs/
├── Jenkinsfile
├── Dockerfile
├── docker-compose.yml
├── docker-compose-grafana.yml
├── prometheus.yml
├── requirements.txt
├── vulnerable_app.py
├── DEPENDENCY_MANAGEMENT.md
└── README.md

Documentación adicional

Para información detallada sobre la implementación de seguridad y la gestión de dependencias, consultar:

- "DEPENDENCY_MANAGEMENT.md"
- Documentación de trazabilidad del proyecto.
- Reportes generados por Jenkins en cada ejecución del pipeline.

Autor

Proyecto desarrollado como parte de la evaluación EV3 – Ciberseguridad, aplicando prácticas de DevSecOps, automatización de pruebas y seguridad en el ciclo de vida del desarrollo de software.
