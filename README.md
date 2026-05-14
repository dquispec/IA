# Chatbot de Reclamos con RAG + Qwen
Proyecto de ia - finetuning

Sistema de respuesta automática a reclamos de clientes usando **Retrieval-Augmented Generation (RAG)** con el modelo **Qwen2.5** de Alibaba, ejecutado sobre Google Colab y conectado con Hugging Face.

## Descripción

Este proyecto implementa un asistente que responde reclamos de clientes imitando el estilo y tono de respuestas previas del equipo de atención. En lugar de hacer fine-tuning del modelo, utiliza RAG: busca casos históricos similares al nuevo reclamo y los inyecta como contexto en el prompt del modelo para generar una respuesta consistente.

## Tecnologías utilizadas

- **Qwen2.5-3B-Instruct** — Modelo de lenguaje (LLM)
- **Hugging Face Transformers** — Carga y ejecución del modelo
- **Sentence-Transformers** — Generación de embeddings multilingües
- **FAISS** — Búsqueda vectorial por similitud
- **Google Colab** — Entorno de ejecución con GPU gratuita

## Requisitos previos

- Cuenta en [Hugging Face](https://huggingface.co/) con token de acceso
- Cuenta de Google para usar Colab
- GPU activada en Colab (Entorno de ejecución > Cambiar tipo > T4 GPU)

## Instalación

1. Clona el repositorio:

```bash
git clone https://github.com/tu-usuario/tu-repo.git
cd tu-repo
```

2. Abre el notebook en Google Colab.

3. Configura tu token de Hugging Face como Secret en Colab:
   - Icono de llave en la barra lateral
   - "Add new secret"
   - Nombre: `KEY_HF`
   - Valor: tu token de Hugging Face
   - Activa "Notebook access"

4. Instala las dependencias (primera celda del notebook):

```python
!pip install -q transformers sentence-transformers faiss-cpu accelerate bitsandbytes
```

## Estructura del proyecto

```
.
├── README.md
├── chatbot_reclamos.ipynb    # Notebook principal
└── data/
    └── knowledge_base.py     # Base de conocimiento (reclamos + respuestas)
```

## Cómo funciona

1. **Carga del modelo**: Qwen2.5 se descarga desde Hugging Face usando el token.
2. **Generación de embeddings**: cada reclamo histórico se convierte en un vector numérico.
3. **Indexación**: los vectores se almacenan en un índice FAISS para búsqueda rápida.
4. **Consulta**: cuando llega un nuevo reclamo, se buscan los K casos más similares.
5. **Generación**: los casos similares se inyectan como contexto en el prompt de Qwen, que responde imitando el estilo del equipo.

## Uso

```python
respuesta = responder_reclamo("Mi pedido no ha llegado y ya pasaron 10 días")
print(respuesta)
```

Salida esperada:

```
Lamentamos la demora. Por favor envíanos tu número de pedido y verificaremos
el estado de envío en un plazo de 24 horas hábiles.
```

## Base de conocimiento

La base de conocimiento incluye reclamos categorizados en:

- Envíos y entrega
- Producto defectuoso o dañado
- Devoluciones y reembolsos
- Facturación y pagos
- Cuenta y acceso
- Suscripciones
- Atención al cliente
- Garantía
- Información del producto
- Promociones y descuentos

Para añadir nuevos casos, edita la lista `knowledge_base` con el formato:

```python
{
    "reclamo": "Texto del reclamo del cliente",
    "respuesta": "Respuesta estándar del equipo"
}
```

## Configuración

Parámetros ajustables en la función `responder_reclamo`:

- `top_k` (default: 2) — número de casos similares a recuperar
- `max_new_tokens` (default: 200) — longitud máxima de la respuesta
- `temperature` (default: 0.3) — creatividad de la respuesta (más bajo = más consistente)

## Posibles mejoras

- Cargar la base de conocimiento desde CSV o base de datos
- Implementar feedback loop para mejorar las respuestas
- Añadir clasificación automática de categorías
- Desplegar como API con FastAPI o Gradio
- Migrar a fine-tuning con LoRA cuando haya suficientes datos
