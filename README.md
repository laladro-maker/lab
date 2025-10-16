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
import streamlit as st
import yaml
import hashlib
from datetime import datetime

# --- Configuración de la app ---
st.set_page_config(page_title="Evaluador LegalTech", layout="centered")
st.title("⚖️ Evaluador de Casos Jurídicos")
st.markdown("Esta app analiza hechos y aplica reglas jurídicas predefinidas para generar un dictamen en Markdown.")

# --- Cargar reglas desde YAML ---
def cargar_reglas():
    try:
        with open("data/rules.yml", "r", encoding="utf-8") as f:
            return yaml.safe_load(f) or []
    except FileNotFoundError:
        st.error("No se encontró 'data/rules.yml'. Asegúrate de crear el archivo.")
        return []

reglas = cargar_reglas()

# --- Casos demo ---
casos_demo = {
    "": {"tipo": "", "descripcion": ""},
    "Doble venta": {
        "tipo": "doble_venta",
        "descripcion": "Se vendió el mismo inmueble a dos compradores distintos."
    },
    "Administrador no inscrito": {
        "tipo": "admin_no_inscrito",
        "descripcion": "El administrador actuó sin estar inscrito en el Registro Mercantil."
    }
}

# --- Captura de hechos ---
st.subheader("📝 Captura de hechos")
demo = st.selectbox("Cargar caso demo (opcional):", list(casos_demo.keys()))
datos = casos_demo.get(demo, {"tipo": "", "descripcion": ""})

col1, col2 = st.columns([2,1])
with col1:
    tipo_caso = st.text_input("Tipo de caso", value=datos["tipo"], placeholder="Ej: doble_venta", help="Identificador que coincide con 'id' en data/rules.yml")
    descripcion = st.text_area("Descripción del caso", value=datos["descripcion"], placeholder="Describe brevemente los hechos...", help="Resumen claro y concreto de los hechos relevantes.")
with col2:
    st.info("Ejemplos rápidos:")
    st.write("- doble_venta")
    st.write("- admin_no_inscrito")
    st.write("")
    st.caption("Carga un caso demo para autocompletar los campos.")

# --- Evaluar reglas ---
def evaluar_reglas(tipo):
    if not tipo:
        return []
    activadas = []
    for regla in reglas:
        # Comparación segura por id
        if str(regla.get("id")).strip().lower() == str(tipo).strip().lower():
            activadas.append(regla)
    return activadas

# --- Generar dictamen Markdown ---
def generar_dictamen(tipo, descripcion, reglas_aplicadas):
    try:
        with open("data/templates/dictamen.md", "r", encoding="utf-8") as f:
            plantilla = f.read()
    except FileNotFoundError:
        plantilla = (
            "# Dictamen\n\n"
            "**Fecha:** {fecha}\n\n"
            "## Hechos\n{descripcion}\n\n"
            "## Análisis\n{analisis}\n\n"
            "## Conclusión\n{conclusion}\n\n"
            "## Riesgos\n{riesgos}\n\n"
            "## Próximos pasos\n{pasos}\n"
        )

    analisis = "\n".join([f"- {r.get('analisis','')}" for r in reglas_aplicadas]) or "Sin análisis disponible."
    conclusion = "\n".join([f"- {r.get('conclusion','')}" for r in reglas_aplicadas]) or "Sin conclusión disponible."
    riesgos = "\n".join([f"- {r.get('riesgo','')}" for r in reglas_aplicadas]) or "No se identificaron riesgos."

    texto = plantilla.format(
        fecha=datetime.now().strftime("%Y-%m-%d %H:%M"),
        tipo=tipo,
        descripcion=descripcion,
        analisis=analisis,
        conclusion=conclusion,
        riesgos=riesgos,
        pasos="Revisar documentación y confirmar validez registral."
    )
    return texto

# --- Acción de análisis ---
st.markdown("---")
if st.button("🔍 Analizar caso"):
    if not tipo_caso.strip() or not descripcion.strip():
        st.warning("Por favor completa los campos 'Tipo de caso' y 'Descripción'.")
    else:
        activadas = evaluar_reglas(tipo_caso)
        st.write("### 🔎 Trazas")
        st.write("**Hechos capturados:**")
        st.write(f"- Tipo: `{tipo_caso}`")
        st.write(f"- Descripción: {descripcion}")

        if not activadas:
            st.warning("No se encontró ninguna regla que coincida con el tipo de caso proporcionado.")
        else:
            st.success(f"Se activaron {len(activadas)} regla(s).")
            st.write("**Reglas activadas:**")
            for r in activadas:
                st.write(f"- **{r.get('nombre')}** — {r.get('descripcion')}")

            # Generar dictamen
            dictamen = generar_dictamen(tipo_caso, descripcion, activadas)

            # Mostrar vista previa del dictamen
            st.write("### 📄 Vista previa del dictamen")
            st.code(dictamen, language="markdown")

            # Botón de descarga
            st.download_button(
                label="📥 Descargar dictamen (.md)",
                data=dictamen.encode("utf-8"),
                file_name=f"dictamen_{tipo_caso}.md",
                mime="text/markdown"
            )

            # Calcular hash SHA-256
            hash_sha = hashlib.sha256(dictamen.encode("utf-8")).hexdigest()
            st.write("### 🔐 Hash SHA-256 del dictamen")
            st.code(hash_sha)

            st.info("Guarda el hash para verificar la integridad del dictamen posteriormente.")

st.markdown("---")
st.caption("App LegalTech • Streamlit + YAML • Sin base de datos • © 2025")
- id: doble_venta
  nombre: "Doble venta de inmueble"
  descripcion: "Regula la prioridad entre compradores en caso de doble venta."
  analisis: "El primer comprador que inscribe su derecho en el Registro prevalece frente al segundo."
  conclusion: "Riesgo alto: el segundo comprador no está protegido si no inscribió."
  riesgo: "Pérdida de propiedad para el comprador no inscrito."

- id: admin_no_inscrito
  nombre: "Administrador no inscrito"
  descripcion: "Analiza los efectos de la falta de inscripción del administrador."
  analisis: "Los actos son válidos frente a terceros de buena fe, pero generan responsabilidad interna."
  conclusion: "Riesgo medio: se recomienda subsanar la falta de inscripción a la brevedad."
  riesgo: "Posible nulidad interna y sanción por incumplimiento formal."
# 🧾 Dictamen Jurídico

**Fecha:** {fecha}  
**Tipo de caso:** {tipo}

---

## Hechos
{descripcion}

---

## Cuestiones
Determinación de los efectos jurídicos y riesgos asociados al tipo de caso identificado.

---

## Análisis
{analisis}

---

## Conclusión
{conclusion}

---

## Riesgos
{riesgos}

---

## Próximos pasos
{pasos}

---

**Fin del dictamen.**
[theme]
primaryColor = "#1E3A8A"
backgroundColor = "#F9FAFB"
secondaryBackgroundColor = "#E5E7EB"
textColor = "#111827"
font = "sans serif"
1. Crear estructura de carpetas.
2. Crear cada archivo con el contenido provisto.
3. Hacer commit y push al repositorio en GitHub.
4. Desplegar en Streamlit Cloud apuntando a app.py.
5. Probar los dos casos demo listados en README.md.

