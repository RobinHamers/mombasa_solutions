# Climate Resilience Mitigation Pipeline

This project provides an automated pipeline for generating location-specific climate and disaster resilience mitigation actions using Large Language Models (LLMs). It processes spatial data containing risk levels for heat, flood, and fire, and generates actionable recommendations for individuals and local authorities.

## Overview

The pipeline takes a dataset of H3 hexagonal indices, each with associated risk metrics, and uses the Gemini 2.5 Flash model to produce tailored mitigation strategies. It leverages reference documents (PDFs) to provide context-aware suggestions.

## Key Features

- **Asynchronous Processing**: Efficiently handles multiple API requests to the LLM.
- **Checkpointing**: Saves progress periodically to allow resuming in case of interruptions.
- **H3 Spatial Indexing**: Uses H3 hexagons for standardized spatial analysis.
- **Context-Aware Recommendations**: Integrates local risk data, points of interest, and reference documents.
- **Structured Output**: Generates results in a GeoJSON format, ready for mapping.

## Repository Structure

- `run_llm_pipeline_async.py`: The main script that orchestrates the data loading, LLM calls, and result saving.
- `prompt_func.py`: Contains the logic for prompt engineering, system instructions, and Google GenAI integration.
- `documents/`: Directory containing reference PDFs used by the LLM for context.
- `clustered_data.gpkg`: Sample spatial data in GeoPackage format.

## Setup

1. **Install Dependencies**:
   ```bash
   pip install -r requirements.txt
   ```

2. **Google Cloud Authentication**:
   Ensure you have access to Google Cloud Vertex AI and have authenticated your environment:
   ```bash
   gcloud auth application-default login
   ```

3. **Environment Variables**:
   - `GOOGLE_CLOUD_REGION`: (Optional) Set your preferred Google Cloud region (defaults to `global`).

## Usage

To run the pipeline:

```bash
python run_llm_pipeline_async.py
```

### Configuration

You can adjust several parameters in `run_llm_pipeline_async.py` and `prompt_func.py`:

- `BATCH_SIZE`: Number of rows to process before saving a checkpoint.
- `EXIT`: Limit the number of rows to process (useful for testing).
- `MODEL_ID`: The Gemini model version to use (default: `gemini-2.5-flash`).
- `PROJECT_ID`: Your Google Cloud project ID.
- `DATABASE_PTH`: Path to the input dataset (CSV or GeoPackage).
- `PDF_URI_LST`: List of Google Cloud Storage URIs for reference PDF documents.

## Input Data

The input data should be a GeoPackage or CSV file containing at least the following columns:
- `h3_10`: H3 index at resolution 10.
- `heat_risk`, `flood_risk`, `fire_risk_202502`: Risk scores (typically 1-4).
- `geometry`: Spatial information.
- Additional columns for local context (e.g., `sealed_surfaces`, `tree_count_sum`, `pois`, etc.).

## Output

The script generates a GeoJSON file (e.g., `rh_async_humanitech_dargo_emergency_solutions_v300.geojson`) containing:
- Original H3 index and geometry.
- Up to 3 generated solutions for each risk type (Fire, Heat, Flood).
- (Optional) Explanations/Chain-of-thought for the proposed solutions.
