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

| Fase              | Actividad                                 | Duración estimada | Resultado           |
|-------------------|-------------------------------------------|-------------------|---------------------|
|✅ 1              | Definir dataset inicial (100 docs mixtos) | 1 día             | Scope PoC           |
|✅ 2              | Preparar ambiente local                   | 1-2 días          | Entorno configurado |
|🛠️ 3              | Implementar extracción y parsing          | 2-3 días          | Datos legibles      |
|🧠 4              | Embedding + vector DB                     | 2 días            | Vector DB activa    |
|🤖 5              | RAG con interfaz simple funcional         | 2 días            | Demostrador         |
|📊 6              | Validación de precisión                   | 1-2 días          | Reporte PoC         |
|📍 7              | Estimación de costos & escalado           | 1 día             | Go/No-Go            |
  
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


## 🧩 PLAN TÉCNICO GRANULAR POR MÓDULO

### 1. Extracción PostgreSQL

    Objetivo: Obtener documentos codificados en base64 desde una tabla de PostgreSQL.
    
    Stack:
    
    psycopg2 o SQLAlchemy
    
    pandas (para manipular resultados)
    
    dotenv o pydantic para configuración
    
    Tareas:
    
    Conectar a la base PostgreSQL usando credenciales desde .env.
    
    Ejecutar query SELECT id, filename, data_base64 FROM documentos.
    
    Guardar cada resultado en /tmp/files/{id}_{filename}.
    
    Registrar logs de extracción con timestamps.
    
    Manejar errores de conexión y timeouts.
    
    Output: Archivos binarios temporales (base64 intactos).
    Criterio de éxito: 100% de los registros válidos descargados.

### 2. Extracción desde S3

    Objetivo: Descargar archivos binarios (PDF, DOCX, TXT, etc.) desde buckets S3.
    
    Stack:
    
    boto3
    
    dotenv o yaml para configuraciones
    
    Tareas:
    
    Configurar conexión a AWS S3 (credenciales IAM, región, bucket).
    
    Listar archivos bajo prefijo (/docs/).
    
    Descargar archivos a /tmp/files/.
    
    Validar tamaño y checksum.
    
    Manejar excepciones (ClientError, NoSuchKey).
    
    Output: Archivos locales descargados.
    Criterio de éxito: 100% de los archivos descargados sin errores.

### 3. Decodificación

    Objetivo: Convertir documentos base64 a su formato original binario.
    
    Stack:
    
    base64
    
    os, io
    
    Tareas:
    
    Leer archivos .b64.
    
    Decodificar usando base64.b64decode().
    
    Guardar como .pdf, .docx, .txt según metadata.
    
    Validar integridad (intentos de apertura).
    
    Output: Archivos binarios “ready”.
    Criterio de éxito: Todos los archivos decodificables se convierten sin error.

### 4. Parsing de contenido

    Objetivo: Convertir documentos binarios a texto limpio.
    
    Stack:
    
    Apache Tika o textract
    
    pdfplumber / docx / PyMuPDF
    
    Tareas:
    
    Detectar tipo MIME (PDF, DOCX, TXT).
    
    Extraer texto.
    
    Limpiar saltos de línea, caracteres no imprimibles, duplicados.
    
    Guardar .txt con mismo ID en /parsed/.
    
    Output: Texto plano por documento.
    Criterio de éxito: ≥95% de texto legible en archivos válidos.

### 5. Chunking

    Objetivo: Dividir documentos en bloques útiles para embeddings.
    
    Stack:
    
    LangChain.text_splitter
    
    tiktoken (para limitar tokens)
    
    numpy o pandas
    
    Tareas:
    
    Definir tamaño de chunk (p.ej. 500 tokens con 50 de solapamiento).
    
    Aplicar sobre cada texto.
    
    Generar lista con chunk_id, source_id, text_chunk.
    
    Exportar DataFrame a /chunks/.
    
    Output: Lista de chunks listos para embedding.
    Criterio de éxito: Todos los documentos divididos de forma consistente.

### 6. Generación de embeddings

    Objetivo: Convertir chunks en vectores semánticos.
    
    Stack:
    
    openai (text-embedding-3-large) o InstructorEmbedding (HF)
    
    tqdm para tracking
    
    Tareas:
    
    Cargar cada chunk de texto.
    
    Llamar API de embeddings en batch (manejar rate limits).
    
    Guardar vectores + metadata (chunk_id, source_id, embedding).
    
    Persistir en archivo parquet o JSONL.
    
    Output: Matriz de embeddings con metadata.
    Criterio de éxito: ≥99% de embeddings generados correctamente.

### 7. Almacenamiento en Vector DB

    Objetivo: Persistir embeddings y metadata en pgvector o Qdrant.
    
    Stack:
    
    pgvector (PostgreSQL extension)
    
    SQLAlchemy / psycopg2
    
    Tareas:
    
    Crear tabla:
    
    CREATE TABLE documents_vectors (
      id SERIAL PRIMARY KEY,
      doc_id TEXT,
      chunk_id TEXT,
      content TEXT,
      embedding VECTOR(1536)
    );
    
    
    Insertar embeddings en batch.
    
    Implementar índice vectorial (ivfflat).
    
    Verificar búsqueda vectorial con cosine_distance.
    
    Output: Base vectorial consultable.
    Criterio de éxito: Latencia < 300ms por consulta top-K.

### 8. Motor RAG

    Objetivo: Integrar recuperación semántica + LLM para generar respuestas.
    
    Stack:
    
    LangChain o LlamaIndex
    
    FastAPI
    
    OpenAI GPT-4o o Llama 3 70B
    
    Tareas:
    
    Implementar Retriever que:
    
    Reciba la query del usuario.
    
    Busque los top-K chunks similares.
    
    Implementar LLM Handler:
    
    Construya prompt contextual (query + chunks).
    
    Llame al modelo LLM.
    
    Devolver respuesta enriquecida + referencias.
    
    Output: Respuestas naturales con contexto citado.
    Criterio de éxito: ≥75% de relevancia en validación manual.

### 9. Métricas y Validación

    Objetivo: Medir precisión, recall, latencia y robustez.
    
    Stack:
    
    scikit-learn (precision/recall)
    
    time, logging
    
    matplotlib o seaborn
    
    Tareas:
    
    Generar conjunto de Q&A de prueba.
    
    Medir:
    
    Tiempo total por consulta.
    
    Relevancia (top-K).
    
    Fallos por tipo de documento.
    
    Documentar resultados en reporte final PoC.
    
    Output: Informe con KPIs técnicos y gráficos.
    Criterio de éxito: Cumplir métricas definidas (<3s, ≥75% relevancia).

### 10. Infraestructura y Despliegue

    Objetivo: Ejecutar PoC de forma reproducible y portable.
    
    Stack:
    
    Docker Compose
    
    FastAPI + Uvicorn
    
    .env + settings.yaml
    
    Tareas:
    
    Crear contenedores para:
    
    API (FastAPI)
    
    PostgreSQL con extensión pgvector
    
    Tika o extractor de texto
    
    Configurar red interna y volúmenes persistentes.
    
    Verificar endpoints /health, /query, /embedding.
    
    Output: PoC ejecutable con un solo comando.
    Criterio de éxito: docker-compose up levanta entorno funcional completo.

### 📊 Resumen de dependencias entre módulos
    PostgreSQL ─┐
    S3 ─────────┤
                 ↓
          Extracción + Decodificación
                 ↓
             Parsing → Chunking
                 ↓
            Embeddings → Vector DB
                 ↓
               RAG Engine
                 ↓
              Validación
