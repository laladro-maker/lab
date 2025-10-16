legaltech-evaluator/
├── app.py
├── requirements.txt
├── README.md
├── data/
│   ├── rules.yml
│   └── templates/
│       └── dictamen.md
└── .streamlit/
    └── config.toml
# lab
# App LegalTech en Streamlit: Evaluador de Casos Jurídicos

## 🧭 Descripción
Aplicación simple para juristas que:
1. Captura hechos de un caso (ej. doble venta, administrador no inscrito).
2. Evalúa reglas jurídicas desde `data/rules.yml`.
3. Genera un **dictamen descargable en Markdown**.
4. Calcula un **hash SHA-256** como evidencia de integridad del dictamen.

No requiere base de datos ni backend externo.

---

## 🚀 Instrucciones paso a paso

### 1️⃣ Crear repositorio en GitHub
1. Entra a [https://github.com/new](https://github.com/new)
2. Nombra el repositorio: `legaltech-evaluator`
3. Selecciona “Public” y marca “Add .gitignore → Python”
4. Crea el repositorio.

### 2️⃣ Crear archivos en GitHub
En el repositorio, crea estos archivos exactamente con este contenido y estructura:

