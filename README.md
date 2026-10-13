# DocuMind AI
### Pharmaceutical Document Intelligence & Evidence Assistant

**Author:** Ashlee Gill  
**Project:** Pfizer Externship — Final Project  
**Main notebook:** `Final Project - Ashlee Gill.ipynb`

## Overview

DocuMind AI is a document question-answering chatbot I developed as my final project for the Pfizer externship. Built in Google Colab with a Gradio interface, it helps users explore pharmaceutical PDFs by extracting their text, finding relevant passages, and generating answers supported by document evidence.

The project combines PDF extraction, optical character recognition (OCR), document classification, metadata, hybrid search, reranking, and retrieval-augmented generation (RAG). RAG supplies the language model with relevant document passages before it generates an answer.

## Problem and Purpose

Pharmaceutical documents can contain important information spread across certificates of quality, packaging specifications, declarations, and technical reports. Some PDFs contain selectable text, while others are scanned images. Finding a specific storage condition, product detail, or compliance statement can require reviewing multiple pages manually.

I built DocuMind AI to make these documents searchable through natural-language questions and to let users inspect the source files, page numbers, and passages behind each response.

## Features

- **Multiple PDF uploads:** Process several documents together.
- **Digital and scanned PDF extraction:** Extract text with PyMuPDF and use Tesseract OCR when a page contains fewer than 50 characters of extracted text.
- **OCR preprocessing:** Apply grayscale conversion, contrast enhancement, sharpening, and thresholding to scanned pages.
- **Document classification:** Use keyword rules to identify certificates of quality, packaging specifications, BSE/TSE declarations, cover letters, compliance documents, and technical reports. Users can override the detected category.
- **Page-level metadata:** Preserve the source filename, document type, page number, chunk ID, OCR status, and text counts.
- **Hybrid retrieval:** Combine FAISS semantic search with BM25 keyword search, followed by CrossEncoder reranking.
- **Query routing:** Recognize general questions, evidence requests, comparisons, summaries, and explicit page lookups.
- **Evidence inspection:** Display retrieved passages and source/page references alongside answers.
- **Evaluation tools:** Test questions against manually verified answers and export detailed results to CSV.

## How It Works

1. **Upload and extract:** Read each PDF page and apply OCR where needed.
2. **Classify and clean:** Assign a document category and normalize extracted text.
3. **Chunk and tag:** Split each page into overlapping 180-word chunks with a 40-word overlap, preserving metadata.
4. **Index:** Generate MiniLM embeddings for FAISS and build a BM25 keyword index.
5. **Route and retrieve:** Choose a retrieval approach based on the question. General, evidence, and comparison questions use hybrid search and reranking; page lookups select page-matched chunks, while summaries sample chunks across the indexed content.
6. **Generate and display:** Provide selected evidence to FLAN-T5 and return an answer with retrieved sources, evidence, timing, and a retrieval-score indicator.

## Technology Stack

| Component | Tool or Model |
| --- | --- |
| Development environment | Google Colab / Python |
| Web interface | Gradio |
| PDF extraction | PyMuPDF |
| OCR and image processing | Tesseract, pytesseract, Pillow |
| Embeddings | `sentence-transformers/all-MiniLM-L6-v2` |
| Semantic search | FAISS |
| Keyword search | BM25 (`rank-bm25`) |
| Reranking | `cross-encoder/ms-marco-MiniLM-L-6-v2` |
| Answer generation | `google/flan-t5-base` through Hugging Face Transformers |
| Data handling and evaluation | pandas, NumPy |
| Model execution | PyTorch, Accelerate |

The final project uses Google's FLAN-T5 model without requiring a Gemini API key. Gemini and LlamaIndex appear in supporting externship experiments rather than the final application's implementation.

## Getting Started

1. Upload `Final Project - Ashlee Gill.ipynb` to Google Colab.
2. Run the notebook cells in order. The first cell installs the Python dependencies and Tesseract OCR.
3. Allow the embedding, reranking, and language models to download. Internet access is required for installation and initial model downloads.
4. Run the final launch cell to open the Gradio application.
5. Upload PDFs in **Document Chat**, choose automatic detection or a document category, and select **Process & Index Documents**.
6. Ask a question and review the retrieved evidence and source pages.

The notebook selects a GPU when available and otherwise uses the CPU. The launch cell uses `share=True`, which creates a temporary public Gradio link. Use `share=False` if a public link is not needed.

## Application Tabs

### Document Chat

Upload and process PDFs, ask questions, filter retrieval by document type, adjust the number of retrieved chunks, and enable or disable query routing. The dashboard reports document, page, chunk, and OCR-page counts.

### Evidence Explorer

Inspect retrieval separately from answer generation. Results include source filenames, document types, pages, hybrid scores, reranker scores, OCR status, and chunk IDs.

### Evaluation Lab

Enter a question, expected answer, exact source filename, and expected page. Run the evaluation to view per-question results and download a CSV report.

## Testing and Development

My supporting externship work included Python fundamentals, structured data extraction, regular expressions, PDF text and table extraction, image preprocessing, and OCR experiments. I compared Tesseract, PaddleOCR, and EasyOCR, explored embedding models and chunking strategies, worked with page-level metadata and query routing, and tested language-model responses using Gemini and Phi-2.

These exercises provided the foundation for the final document assistant and its evaluation workflow.

The final application's evaluation engine reports:

| Metric | What the implementation measures |
| --- | --- |
| Answer accuracy | Whether the normalized expected answer appears within the generated response |
| Citation accuracy | Whether the expected filename appears in the returned source list |
| Recall@K | Whether at least one retrieved chunk matches the expected source/page; implemented as a hit rate |
| Precision@K | The fraction of retrieved chunks matching the expected source/page |
| Mean reciprocal rank | The average reciprocal rank of the first matching chunk |
| Response timing | Average retrieval, generation, and total response time |

The supplied final notebook records successful model loading, interface construction, and a Gradio launch. It does not include saved final evaluation results, so no benchmark percentages are claimed here. The automated metrics are simple checks and should be supplemented with manual answer and evidence review.

## Current Limitations

- OCR quality depends on scan clarity and page layout.
- Document classification and query routing use keyword rules.
- FLAN-T5 inputs are truncated to 512 tokens. The prompt includes at most four evidence chunks, even when more chunks are retrieved, so useful context or the question may be truncated.
- Returned source references identify retrieved evidence; they do not independently prove that every generated statement is supported.
- The retrieval confidence label is a score-based heuristic, not a calibrated probability of answer correctness. Page and summary routes use fixed scores.
- Document indexes are held in memory, rebuilt when documents are processed, and lost when the runtime restarts.

## Skills Demonstrated

Python programming, PDF extraction, OCR preprocessing, text cleaning, metadata design, embeddings, vector search, keyword retrieval, reranking, RAG, prompt design, query routing, Gradio interface development, and evaluation design.

## Project Context

DocuMind AI is an educational prototype created for my Pfizer externship final project. It demonstrates how document extraction and conversational AI can work together to support pharmaceutical document review.

