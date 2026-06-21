# Análisis de Experiencia de Cliente (CX): Ticketmaster vs Sector

![Status](https://img.shields.io/badge/Status-Completado-success)
![Python](https://img.shields.io/badge/Python-3.11.9%2B-blue)
![NLP](https://img.shields.io/badge/NLP-RoBERTa%20|%20BERTopic-orange)

## Descripción del Proyecto
Este proyecto utiliza técnicas avanzadas de **Procesamiento de Lenguaje Natural (NLP)** y **Machine Learning** para diagnosticar la Experiencia de Cliente (CX) de Ticketmaster en Trustpilot, comparándola frente a su competencia directa. 

A diferencia de los enfoques tradicionales, este análisis demuestra el peligro del *Target Leakage* en los datos, descarta por completo el sesgo numérico de las estrellas y se centra exclusivamente en extraer el valor real oculto en los textos de las reseñas.

### Metodología Aplicada:
- **Detección de Sesgos (Target Leakage):** Descarte de la puntuación de estrellas debido a una anomalía estadística (distribución artificial forzada en el dataset de origen).
- **Inferencia de Sentimiento Oculto:** Uso de **Transformers (RoBERTa)** pre-entrenados por CardiffNLP para puntuar el texto crudo en Positivo, Negativo o Neutral.
- **Filtrado Semántico Competitivo:** Cálculo de similitud por coseno mediante embeddings para aislar únicamente a los verdaderos competidores de nicho de Ticketmaster.
- **Modelado de Tópicos no Supervisado:** Algoritmo **BERTopic** (reducción UMAP + clústering HDBSCAN + representación c-TF-IDF) para descubrir automáticamente las temáticas de conversación.
- **Análisis de Causa Raíz:** Extracción de n-gramas (bigramas y trigramas) en los focos rojos operativos para detectar el fallo exacto en los procesos de negocio.

---

## Estructura de Análisis y Datos

Para optimizar el rendimiento y el almacenamiento, el flujo de trabajo está dividido en dos cuadernos de ejecución (*Jupyter Notebooks*):

### 1. `00_conversion_csv_a_parquet.ipynb` (Limpieza y Compresión)
Carga el dataset gigantesco original en `.csv` y lo optimiza exportándolo a un motor columnar ligero (`.parquet`).
> ⚠️ **IMPORTANTE:** El archivo original `Trustpilot-reviews-123k.csv` pesa más de 120MB y **NO está incluido** en este repositorio de GitHub. Quien desee reproducir el Notebook 00 desde cero, deberá descargar el CSV original y colocarlo en la carpeta `data/raw/`.

### 2. `01_analisis_Resenas_ticketmaster_Andreu.ipynb` (Pipeline Principal)
El motor de este proyecto. Si solo te interesa el análisis puro de datos, **puedes empezar directamente aquí**. Este notebook consume directamente el archivo `.parquet` optimizado (que **sí está incluido** en la carpeta `data/processed/`) y ejecuta los algoritmos de RoBERTa y BERTopic.

### 3. Carpeta `models/` (Caché y Modelos Locales)
Esta carpeta está diseñada para almacenar localmente los pesos pesados de los modelos de Inteligencia Artificial y evitar descargas o re-entrenamientos innecesarios en cada ejecución. 

* **RoBERTa:** Guarda en caché el modelo pre-entrenado de *Hugging Face* para la inferencia de sentimiento.
* **BERTopic:** Almacena el modelo de tópicos ya entrenado (que incluye los embeddings, la reducción de dimensionalidad UMAP y el clúster HDBSCAN). Si necesitas hacer predicciones sobre nuevas reseñas en el futuro, el Notebook puede cargar el estado exacto del modelo desde esta carpeta sin necesidad de entrenarlo de cero.

> ⚠️ **Nota de GitHub:** Por restricciones de peso (los modelos superan con creces los límites de Git), esta carpeta se encuentra excluida en el `.gitignore`. Sin embargo, la estructura del directorio se mantiene gracias al archivo `.gitkeep`. Cuando ejecutes el **Notebook 01** por primera vez, el código descargará y guardará automáticamente los modelos en esta ruta local.

---

## Instalación y Reproducción

Sigue estos pasos para levantar el proyecto en tu entorno local:

1. **Clonar el repositorio:**
   ```bash
   git clone <URL_DEL_REPOSITORIO>
   cd <NOMBRE_DEL_REPOSITORIO>
   ```

2. **Crear un entorno virtual aislado (Opcional pero recomendado):**
   ```bash
   python -m venv venv
   # En Windows:
   .\venv\Scripts\activate
   # En macOS/Linux:
   source venv/bin/activate
   ```

3. **Instalar las dependencias exactas:**
   Instala las librerías necesarias ejecutando el archivo limpio `requirements.txt`:
   ```bash
   pip install -r requirements.txt
   ```

4. **Ejecutar Jupyter:**
   ```bash
   jupyter notebook
   ```
   *Y abre el archivo `notebooks/01_analisis_Resenas_ticketmaster_Andreu.ipynb`.*

---

## Conclusiones de Negocio Destacadas
- Ticketmaster lidera la negatividad del sector, con casi un **65% de menciones puramente negativas**.
- La conversación está polarizada: el **50%** de las quejas giran en torno a la Experiencia con la App Móvil, y el **34.4%** se enfocan en Cancelaciones y Reembolsos.
- Al cruzar los tópicos con el sentimiento, detectamos que en áreas de **Soporte y Reembolsos la negatividad alcanza el 100%**, confirmando un cuello de botella grave frente a un mercado que oscila entre el 50% y el 81% en los mismos problemas.
- **La usabilidad web y pasarela de pagos** son el punto fuerte (0% quejas). El reto es resolver los cuellos de botella en la fase *post-venta*.

---
*Desarrollado como Proyecto Práctico de Deep Learning aplicado a negocio.*
