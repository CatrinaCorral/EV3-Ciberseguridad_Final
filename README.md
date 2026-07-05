EV3-Ciberseguridad_Final

Descripción

Este proyecto corresponde a la implementación de un entorno DevSecOps, integrando prácticas de seguridad dentro del ciclo de vida del desarrollo de software (SDLC). El objetivo es automatizar el proceso de desarrollo, pruebas y despliegue, incorporando controles de seguridad, gestión de dependencias, monitorización y trazabilidad de manera continua.

Características principales

- Pipeline de Integración y Despliegue Continuo (CI/CD).
- Automatización de pruebas.
- Integración de herramientas de seguridad.
- Gestión de dependencias.
- Monitorización de la aplicación.
- Generación de documentación y evidencia de cada ejecución.

Tecnologías utilizadas

- Python
- Flask
- Docker
- Jenkins
- GitHub
- OWASP ZAP
- OWASP Dependency-Check
- Bandit
- Pytest
- Prometheus
- Grafana

Estructura del proyecto

.
├── .github/
├── Jenkinsfile
├── Dockerfile
├── docker-compose.yml
├── docker-compose-grafana.yml
├── prometheus.yml
├── requirements.txt
├── vulnerable_app.py
├── README.md
├── DEPENDENCY_MANAGEMENT.md
└── SDLC_SECURITY_DOCUMENTATION.md

Documentación

El proyecto incluye la siguiente documentación:

- README.md: descripción general del proyecto.
- SDLC_SECURITY_DOCUMENTATION.md: documentación del proceso de seguridad, trazabilidad y actividades realizadas durante el SDLC.
- DEPENDENCY_MANAGEMENT.md: gestión de dependencias y actualizaciones de seguridad.

Objetivo

Demostrar la aplicación de prácticas de DevSecOps mediante la integración de herramientas y procesos que permitan mejorar la seguridad, automatizar las validaciones y mantener la trazabilidad durante el desarrollo de software.
