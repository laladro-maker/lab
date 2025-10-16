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

Copia el contenido de cada archivo del presente documento.

### 3️⃣ Desplegar en Streamlit Cloud
1. Ve a [https://share.streamlit.io](https://share.streamlit.io)
2. Conecta tu cuenta de GitHub.
3. Selecciona el repositorio `legaltech-evaluator`.
4. Como archivo principal indica: `app.py`
5. Pulsa **Deploy**.

### 4️⃣ Probar la app
Abre la URL generada (ej. `https://usuario-legaltech-evaluator.streamlit.app`)  
Verás los formularios para ingresar hechos y generar dictamen.

---

## 🧪 Casos de prueba

### Caso 1: Doble venta
**Hechos ingresados:**
- Tipo de caso: `doble_venta`
- Descripción: “Se vendió el mismo inmueble a dos compradores distintos.”

**Resultado esperado:**
- Regla activada: “Prioridad del primer comprador inscrito en el Registro.”
- Conclusión: “Riesgo alto. El segundo comprador carece de protección registral.”
- Hash SHA-256 mostrado al final.
- Descarga del dictamen `.md` correcta.

---

### Caso 2: Administrador no inscrito
**Hechos ingresados:**
- Tipo de caso: `admin_no_inscrito`
- Descripción: “El administrador actúo sin estar inscrito en el Registro Mercantil.”

**Resultado esperado:**
- Regla activada: “Actos válidos frente a terceros de buena fe, pero responsabilidad interna.”
- Conclusión: “Riesgo medio. Recomendar subsanación de inscripción.”
- Hash SHA-256 mostrado al final.
- Descarga del dictamen `.md` correcta.

---

## 📚 Créditos
Desarrollado por un experto en LegalTech.  
Licencia MIT.
streamlit==1.38.0
pyyaml==6.0.2

