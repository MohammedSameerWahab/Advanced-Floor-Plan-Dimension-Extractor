# Automated Floorplan Data Extraction Pipeline

This project is a robust, end-to-end pipeline developed to automate information extraction from architectural floorplan PDFs. It addresses a critical business need in the Architecture, Engineering, and Construction (AEC) industry by converting unstructured visual data into structured, machine-readable JSON format, thereby saving significant manual effort and reducing human error.

## Key Features

* **Automated OCR**: Employs an OCR engine to digitize text from image-based PDF documents.
* **Pattern Recognition**: Utilizes regex for precise identification of dimensions and component codes.
* **Data Standardization**: Converts extracted dimension strings into a consistent unit (float inches).
* **Structured Output**: Generates a clean, well-structured JSON file for easy integration with other software.
* **Bonus Visualization**: Produces an annotated PDF that visually confirms the accuracy of the extracted data.

## Tech Stack

* **Python 3.8+**
* **Core Libraries**: PyMuPDF (fitz), Pytesseract, OpenCV, NumPy
* **Environment**: Jupyter Notebook

---

## Technical Methodology

The pipeline follows a strategic, single-pass OCR approach optimized for the specific challenges of architectural documents.

1.  **Image Preprocessing**: The input PDF page is first rendered into a high-resolution image using PyMuPDF. This step is critical for enhancing the clarity of small-font text, directly improving OCR accuracy.
2.  **OCR Data Extraction**: The Tesseract engine processes the high-DPI image to extract all recognizable text, along with crucial metadata such as confidence scores and bounding box coordinates for each word.
3.  **Regex-based Pattern Matching**: The raw OCR output is parsed using targeted regular expressions. The logic is designed to intelligently identify two distinct entities:
    * **Dimensions**: Matches complex architectural formats (e.g., `12' 6"`).
    * **Component Codes**: Matches alphanumeric codes (e.g., `3040SH`).
4.  **Data Structuring & Serialization**: All validated data is standardized (e.g., dimensions converted to inches) and then structured into the final JSON output, including precise bounding box information for each dimension.

---

## Challenges & Strategic Decisions

This project involved several key challenges, which were addressed with specific, data-driven decisions:

* **Document Type Analysis**: Initial analysis suggested the PDF might be a hybrid file with both text and image layers. A two-pass (native text + OCR) approach was prototyped. However, debugging revealed that **no native text layer existed**, and all content was graphical. This insight led to the strategic decision to focus on a **single, highly optimized OCR pass**, which simplified the pipeline and concentrated efforts on improving image quality.

* **OCR Tuning & Limitations**: The OCR engine initially failed to detect a small component code (`2030SH`) due to its proximity to thick graphical lines. Various tuning strategies were considered. While increasing the rendering DPI to 600 improved overall detection, it was determined that aggressively pursuing this single missed entity led to a slight degradation in dimension-finding accuracy. A strategic decision was made to **prioritize the accuracy of the primary data (dimensions)**, accepting a minor, well-understood limitation of the OCR technology.

* **Output Visualization**: The initial bonus visualization was functionally correct but visually cluttered. To enhance usability, the solution was re-engineered to draw a **white background behind each text label**, ensuring the output is clean, professional, and highly legible, regardless of the underlying drawing complexity.

---

## Potential for Advanced AI Integration

This project provides a strong foundation for a more intelligent document analysis system. Future iterations could incorporate the following advanced AI/ML capabilities:

### Advanced Text & Layout Recognition

While regex is effective, its rigidity can be a limitation. The next-generation version of this tool could leverage more advanced models:

* **NLP for Entity Recognition**: Instead of regex, a **spaCy** model with custom Named Entity Recognition (NER) rules could be trained to identify `DIMENSIONS`, `ROOM_NAMES`, and `CODES` based on context, making the extraction far more robust to variations in notation.
* **Transformer-based Models**: For state-of-the-art performance, we could implement a multimodal Transformer model like **Microsoft LayoutLM** or **Donut**. These models process both the text and the document's visual layout simultaneously, allowing them to understand relationships between elements (e.g., knowing that "13'6" x 15'6"" is the dimension *for* the "MASTER BDRM" because it's located inside that room's boundary).

### RAG-Powered Conversational AI

The structured JSON output is a perfect knowledge base for a **Retrieval-Augmented Generation (RAG)** system. We could build a chatbot that allows a user to "talk" to the floorplan:

* **User Query**: "What are the dimensions of the master bedroom?"
* **RAG System**:
    1.  The system retrieves the relevant data from the JSON file (`MASTER BDRM`, `13'6"`, `15'6"`).
    2.  This retrieved data is fed as context to a Large Language Model (LLM).
    3.  The LLM generates a natural language answer: "The master bedroom is 13 feet 6 inches by 15 feet 6 inches."

### Interactive Web Application

To make this tool accessible to non-technical users (like architects or project managers), it could be deployed as a web application using **Streamlit** or **Flask**. This would provide a simple UI where a user could upload a floorplan PDF and immediately receive the extracted JSON data and the annotated visualization.