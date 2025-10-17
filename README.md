# 📄 PDF Geodata Extraction

An integrated pipeline for **transforming unstructured geological PDFs into structured, mappable, and analysable datasets**.  
The system performs **comprehensive text cleaning** and combines **LLM-based extraction** with **rule-based NLP methods** (*spaCy*, *GeoText*, and *Regex*) to identify valid geographic locations.  
It also extracts **quantitative geological information** such as **rock types, structural terms, and formation ages**, enabling richer contextual understanding of each study.  
Extracted locations are then **geocoded using GeoPy Nominatim** to obtain precise coordinates and visualised through **interactive Folium maps**, producing accurate and interpretable spatial outputs for further research.


---

## ✨ Overview

This pipeline was developed for geological literature — for example, papers such as *The Markoye Fault (2007, Tshibubudze)* — to automatically extract and analyse both **spatial** and **quantitative geological information** from scientific PDFs.  
It identifies geographic locations, filters out irrelevant geological terms, extracts contextual data such as **rock types**, **structural features**, and **formation ages**, and geocodes the validated locations for visualisation and analysis.

**Core approach:**
- **LLM + Rule-based NLP (spaCy, GeoText, Regex)** → Work in tandem to extract and validate geographic locations mentioned in the text.  
- **Dedicated LLM Quantitative Extractor** → Identifies and categorises geological context terms such as rock type, structure, and age for enriched analysis.  
- **Cleaning & Filtering** → Removes non-geographic, duplicate, or noisy terms to ensure high-quality extracted data.  
- **Geocoding with GeoPy Nominatim** → Converts validated location names into precise latitude-longitude coordinates.  
- **Mapping & Evaluation** → Uses **Folium** to visualise extracted points and study areas, and produces quantitative summaries for further interpretation.


---

## 🧱 Repository Structure

| Folder | Description |
|---------|--------------|
| **data_pdf_input/** | Contains all the raw scientific PDFs used as input. Each file serves as the text source for extraction. |
| **Final_output/** | Holds final deliverables: merged geocoded CSVs and Folium HTML maps. Also includes the quantitative summaries of extracted data for each paper. |
| **Geo_Pipeline_Evaluation/** | Evaluation of LLM + rule-based extraction performance and geocoding accuracy using GeoPy Nominatim. |
| **Model_data/** | Contains intermediate outputs from the pipeline’s four main components — results from **text_cleaning**, **LLM_Extraction**, **Rules_extraction**, and the **Geocoded_cleaned_data**. |
| **Pipeline_Components_For_Location/** | Houses the four standalone component notebooks that were later merged into the unified extraction and geocoding pipeline (`Extraction_of_Locations&Geocoding_Pipeline.ipynb`).| 
| **Research_runs/** | Contains experimental data, older versions of maps, and archived notebooks from earlier tests where NLP and rule-based extraction were used together with geocoding. |
| **.env** | API keys and environment variables (e.g., OpenAI). |
| **.gitignore** | Excludes checkpoints, cache, and large outputs from version control. |
| **LICENSE** | MIT License. |
| **README.md** | You’re reading it — repository documentation. |

---

## 📘 Key Notebooks

| Notebook | Description |
|-----------|--------------|
| `Extraction_of_Locations&Geocoding_Pipeline.ipynb` | Performs the complete end-to-end process — text cleaning, NLP and rule-based + LLM extraction, GeoPy Nominatim geocoding, and map generation for visual validation. |
| `Quantitativedata_LLM_extraction.ipynb` | Extracts **quantitative data** and contextual information directly from the PDFs, such as rock type, structure, and age. |
| `Main_Study_Area_Extraction_Final.ipynb` | Identifies and visualises the **main study area** mentioned across papers, plotting the consolidated region of interest on an interactive map. |


---

## ⚙️ Dependencies and Imports

```python
# PDF parsing
from pdfminer.high_level import extract_pages
from pdfminer.layout import LTTextContainer

# Data handling & utilities
import pandas as pd
import numpy as np
import json, re, os, io, csv, ast, time, itertools
from typing import List, Tuple
from collections import defaultdict
from pathlib import Path

# NLP & text normalization
import spacy
from geotext import GeoText
import unidecode

# LLM integration (for text and quantitative data extraction)
import openai
from openai import OpenAI

# Geocoding & coordinate tools
from geopy.geocoders import Nominatim
from geopy.extra.rate_limiter import RateLimiter
from pyproj import Transformer

# Visualization
import folium
```

## 💡 How to Use

1. **Add PDFs**  
   Place all input PDFs inside the `data_pdf_input/` folder.

2. **Run the Extraction and Geocoding Pipeline**  
   Open and execute `Extraction_of_Locations&Geocoding_Pipeline.ipynb`.

   - **Step 1:** Extract geographic locations using a **hybrid LLM + rule-based approach** (Regex, spaCy, GeoText).  
   - **Step 2:** Clean the results by removing irrelevant geological text such as rock types, structures, and ages.  
   - **Step 3:** Geocode the validated locations using **GeoPy Nominatim** to obtain latitude and longitude coordinates.  
   - **Step 4:** Automatically generate interactive **Folium maps** to visualize extracted locations and study areas.

3. **Inspect Intermediate Data**  
   - Review intermediate extraction and geocoding outputs in `Model_data/`.  
   - Evaluate accuracy and consistency using the notebooks in `Geo_Pipeline_Evaluation/`.

4. **Run the Quantitative Data Extractor**  
   Execute `Quantitativedata_LLM_extraction.ipynb` to extract **quantitative geological information** such as **rock types**, **structural terms**, and **formation ages** from the PDFs and datasets for contextual analysis.

5. **Access Final Outputs**  
   - Final merged CSVs, quantitative summaries, and Folium maps are available in the `Final_output/` folder.  
   - Older experimental maps and archived notebooks are stored in `Research_runs/`.

## 🗺️ Generating Interactive Maps
----

Each geocoded CSV can be visualised as an interactive Folium map with automatic colouring by location type and transparency for accuracy (solid = exact, faint = approximate).
Maps are automatically saved as .html files inside Final_output/.

## 🧠 Environment Setup
- Install dependencies manually using:
```bash
pip install pandas numpy spacy geotext folium pdfminer.six geopy pyproj openai unidecode tqdm
```
- The .env file stores API keys (e.g., OpenAI) and geocoding rate-limit configurations.

## 🧩 Notes & Tips
----
- **Consistency:** Keep the **LLM + rule-based extraction pipeline** deterministic by maintaining uniform preprocessing and prompt logic across runs.  
- **Geocoding Reliability:** Use **GeoPy Nominatim** carefully — respect rate limits and verify coordinates against expected regions.  
- **Quantitative Extraction:** Ensure the secondary LLM correctly identifies **rock types**, **structural terms**, and **formation ages**; review ambiguous cases manually.  
- **Provenance:** For every record, store `pages`, `source_pdf`, and `geocode_str` to maintain transparency and traceability.  
- **Duplicates:** Deduplicate by `(location_clean, rounded_latlon)` and retain only the highest-confidence or most contextually relevant entries.  
- **Accuracy & Visualization:** In Folium maps, use **transparency** (solid = exact, faint = approximate) to communicate coordinate confidence.  
- **Quality Control:** Periodically cross-check a random subset of extracted and geocoded data to ensure the pipeline’s overall precision and recall remain stable.


## 🔍 Future Work
----

  1) Enhance both of the pipelines to support multi-lingual and context-aware disambiguation.

  2) Integrate an evaluation dashboard (Streamlit) for quick map inspection.

  3) Introduce uncertainty metrics for approximate coordinates.

  4) Combine Geolocation_extraction and Quantitative_data_extraction pipelines.
     

## 📜 License
----

MIT License © 2025 — see LICENSE.

## 🙌 Acknowledgments

Built using: **spaCy**, **GeoText**, **pdfminer.six**, **geopy**, **pyproj**, **folium**, and the **OpenAI Python SDK**.

---

> *This repository is part of ongoing research into automating geospatial data extraction from scientific literature — combining LLM-assisted extraction with rule-based NLP methods for extraction, a secondary LLM for quantitative data (rock type, structure, age), and GeoPy Nominatim for precise geocoding and mapping.*


