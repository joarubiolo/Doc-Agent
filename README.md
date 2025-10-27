# 📘 Prueba de Concepto (PoC): Motor de Búsqueda Semántica + RAG LLM sobre Documentos en PostgreSQL (base64) y S3

## 🎯 Objetivo General

Validar la viabilidad técnica y eficiencia de implementar un sistema de
búsqueda semántica y respuesta asistida por LLM (RAG - Retrieval
Augmented Generation) utilizando documentos almacenados en: - ✅
PostgreSQL: en formato base64. - ✅ Storage S3: archivos binarios (PDF,
DOCX, TXT, etc.).

## 🏗️ Arquitectura del flujo PoC

    PostgreSQL (base64 docs)   S3 (binary/PDF/etc.)
               │                         │
               └────▶ Extractor (Python) ─┘
                            │
           🧾 Decodificación + Parsing (PDF/TXT/DOCX/TIKA)
                            │
           📍 Chunking (división del documento en bloques)
                            │
     🧮 Generación de embeddings (OpenAI / HuggingFace)
                            │
     📦 Almacenamiento en vector DB (PGVector / Qdrant / Pinecone)
                            │
     👤 Usuario consulta en lenguaje natural
                            │
     🔍 Retrieval de Top-K similares + metadata
                            │
     🤖 RAG con LLM (GPT/Llama/Mistral) → Respuesta enriquecida

## 📦 Módulos que deben implementarse (mínimo viable)

    1     Extracción PostgreSQL: Obtener documentos en base64 
            Output: Archivos binarios temporales
    
    2     Extracción desde S3: Descarga de archivos binarios 
            Output: Archivos locales                                 

    3     Decodificación   base64 → PDF/DOCX/TXT 
            Output: Binary ready

    4     Parsing de contenido: Convertir binarios a texto
            Output: Texto limpio por documento                 

    5     Chunking: Dividir documentos en bloques útiles
            Output: Lista de chunks                     

    6     Generación de embeddings: Crear vectores semánticos
            Output: Matriz de embeddings                

    7     Almacenamiento en Vector DB: Persistencia de embeddings con metadata
            Output: Base vectorial consultable              

    8     Motor RAG: LLM + recuperación contextual
            Output: Respuestas enriquecidas                              

    9     Métricas y validación: Evaluar precisión, recall, latencia
            Output: Informe PoC      

## 🛠️ Stack Técnico Sugerido (PoC Ágil)

| Componente       | Opción Recomendada                         | Alternativa Self-Hosted            |
|------------------|---------------------------------------------|------------------------------------|
| **Lenguaje**     | Python 3.10+                                | Go, NodeJS                         |
| **Framework IA** | LangChain                                   | LlamaIndex                         |
| **Vector DB**    | pgvector (extensión PostgreSQL existente)   | Qdrant / Weaviate                  |
| **Embeddings**   | OpenAI `text-embedding-3-large`             | InstructorXL (HuggingFace)         |
| **LLM**          | GPT-4o                                      | Llama 3 70B                        |
| **Storage**      | AWS S3                                      | MinIO                              |
| **Framework API**| FastAPI                                     | Flask                              |


## 📊 Criterios de Éxito de la PoC

| Métrica                  | Criterio Exitoso                         | Validación / Método                |
|---------------------------|------------------------------------------|------------------------------------|
| **Tiempo de respuesta**   | < 3s promedio para búsqueda + RAG        | Prueba controlada                  |
| **Relevancia**            | ≥ 75% (feedback experto)                | Ranking manual / Q&A               |
| **Robustez**              | Indexar ≥ 100 documentos sin errores     | Prueba de estrés inicial           |
| **Costos**                | Alineado al presupuesto                  | Estimación mensual                 |
| **Facilidad de integración** | Integrable al pipeline actual          | Validación de arquitectura         |
| **Escalabilidad**         | Proyectable a 10K documentos             | Evaluación técnica                 |

## 🚀 Próximos pasos (roadmap)

  -----------------------------------------------------------------------
  Fase              Actividad         Duración estimada Resultado
  ----------------- ----------------- ----------------- -----------------
  ✅ 1              Definir dataset   1 día             Scope PoC
                    inicial (100 docs                   
                    mixtos)                             

  ✅ 2              Preparar ambiente 1-2 días          Entorno
                    local                               configurado

  🛠️ 3              Implementar       2-3 días          Datos legibles
                    extracción y                        
                    parsing                             

  🧠 4              Embedding +       2 días            Vector DB activa
                    vector DB                           

  🤖 5              RAG con interfaz  2 días            Demostrador
                    simple                              funcional

  📊 6              Validación de     1-2 días          Reporte PoC
                    precisión                           

  📍 7              Estimación de     1 día             Go/No-Go
                    costos & escalado                   
  -----------------------------------------------------------------------

## 📁 Estructura sugerida de repositorio

    /poc-rag-docs/
    │
    ├── ingestion/
    │   ├── fetch_postgres.py
    │   ├── fetch_s3.py
    │   ├── decode_and_parse.py
    │
    ├── vectorization/
    │   ├── chunking.py
    │   ├── embeddings.py
    │   ├── vector_store.py
    │
    ├── rag/
    │   ├── retriever.py
    │   ├── llm_handler.py
    │
    ├── api/
    │   ├── server.py (FastAPI)
    │
    ├── config/
    │   ├── settings.yaml
    │
    └── README.md
