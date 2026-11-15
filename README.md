# Laboratorio: Computación Serverless con OpenFaaS y Python

* **Asignatura:** Introducción al Cómputo en la Nube
* **Unidad:** Unidad 3 – Competencias Fundamentales en la Nube
* **Estudiante:** Mayra Esmeralda Benavides Rivera

---

## Descripción de la Práctica

Este proyecto implementa un servicio bajo el modelo **Functions as a Service (FaaS)** utilizando **OpenFaaS**, **Kubernetes**, **Docker** y **Python**. El objetivo consiste en desplegar una función HTTP capaz de procesar solicitudes `GET` y `POST` para registrar datos de usuario, capturar información del entorno contenedorizado, generar un identificador único (`UUID v4`) y emitir un archivo `.json` descargable respaldado por una firma de integridad **SHA-256**.

---

## Adaptación del Entorno: GitHub Codespaces

Debido a restricciones de instalación de software local en el equipo cliente, toda la infraestructura descrita en la guía se implementó en un entorno de desarrollo en la nube mediante **GitHub Codespaces** (Linux), manteniendo intacta la arquitectura y los entregables requeridos:

| Componente de la Guía | Implementación en Codespaces | Propósito |
| :--- | :--- | :--- |
| **Docker Desktop (WSL 2)** | Motor Docker nativo en Codespaces | Construcción y ejecución de contenedores Linux |
| **Kubernetes (Kubeadm)** | Clúster local mediante `kind` | Orquestación de nodos y espacios de nombres (`openfaas`, `openfaas-fn`) |
| **Helm & OpenFaaS CLI** | Binarios CLI en Linux (`helm`, `faas-cli`) | Despliegue de OpenFaaS CE y gestión de plantillas |
| **Visual Studio Code** | Editor web integrado en Codespaces | Desarrollo de la función Python y control de versiones Git |

---

## Resumen del Procedimiento Realizado

1. **Configuración de Infraestructura:** Se levantó un clúster de Kubernetes con `kind`, se crearon los *namespaces* correspondientes y se desplegó **OpenFaaS Community Edition** mediante `Helm` con autenticación básica habilitada y política de imágenes locales (`imagePullPolicy=Never`).
2. **Desarrollo de la Función (`python3-http`):** Se descargó la plantilla oficial de Python mediante `faas-cli`, creando la función `registro-serverless` definida en `stack.yaml`. En `handler.py` se implementó la lógica para servir el formulario HTML y serializar la respuesta JSON.
3. **Construcción y Ejecución:** Se empaquetó la función en la imagen local `registro-serverless:v1` con `docker build` y se ejecutó el servicio expuesto en el puerto local `8081`.
4. **Generación de Evidencia:** Al enviar los datos de registro al servicio, la función generó el archivo JSON con metadatos reales del contenedor Linux, marca de tiempo UTC, token UUID y firma criptográfica SHA-256.

---

## Estructura del Repositorio

```text
laboratorio-serverless/
├── laboratorio-serverless/
│   ├── registro-serverless/
│   │   ├── handler.py          # Lógica principal de la función Serverless (GET/POST)
│   │   ├── handler_test.py     # Pruebas unitarias de la función
│   │   ├── requirements.txt    # Dependencias de Python
│   │   └── tox.ini             # Configuración de entorno de pruebas
│   ├── .gitignore              # Exclusión de archivos temporales y caché
│   ├── registro_db17e86a-4e00-4940-b236-ba72332a237e.json  # Evidencia generada
│   └── stack.yaml              # Manifiesto de despliegue de OpenFaaS
└── README.md                   # Documentación de la práctica
```

---

## Resultado Obtenido

La ejecución del servicio produjo exitosamente el archivo `registro_db17e86a-4e00-4940-b236-ba72332a237e.json`, cumpliendo con todos los criterios de evaluación de la guía:

* **Registro validado:** Nombre, apellido y correo electrónico institucional/aula virtual.
* **Identificador y seguridad:** Token UUID único y firma `sha256` calculada sobre los datos registrados.
* **Telemetría del contenedor:** Sistema operativo (`Linux`), versión de Python (`3.12.16`), identificador de contenedor (`hostname`) y plataforma (`OpenFaaS`).
