# Awesome LLM Extraction [![Awesome](https://awesome.re/badge.svg)](https://awesome.re)

Tools for turning documents and text into structured data with language models: guaranteed-valid JSON from any model,
PDFs and scans to clean text, and the benchmarks that tell you whether any of it is right.

Every entry is open source, actively maintained and checked by hand. Each section starts with where to begin.

## Contents

- [Which tool do I need?](#which-tool-do-i-need)
- [Structured output](#structured-output)
  - [Constrained decoding engines](#constrained-decoding-engines)
  - [Libraries](#libraries)
  - [Serving with structured output built in](#serving-with-structured-output-built-in)
- [Document parsing](#document-parsing)
  - [PDF and office documents to text](#pdf-and-office-documents-to-text)
  - [OCR](#ocr)
  - [Vision-language models for documents](#vision-language-models-for-documents)
  - [Tables](#tables)
  - [Low-level PDF access](#low-level-pdf-access)
- [Information extraction](#information-extraction)
- [Benchmarks and datasets](#benchmarks-and-datasets)
- [Contributing](#contributing)

## Which tool do I need?

| You want to | Start with |
|---|---|
| Get JSON that always matches a schema from a model you run yourself | Your server's built-in structured output ([vLLM](#serving-with-structured-output-built-in), [SGLang](#serving-with-structured-output-built-in), [llama.cpp](#serving-with-structured-output-built-in)), which uses [XGrammar](#constrained-decoding-engines) or [llguidance](#constrained-decoding-engines) underneath |
| Get typed Python objects from a hosted or local model, with retries | [Instructor](#libraries) or [PydanticAI](#libraries) |
| Constrain generation directly inside your own code | [Outlines](#libraries) or [Guidance](#libraries) |
| Turn PDFs into Markdown for RAG or training data | [Docling](#pdf-and-office-documents-to-text), [Marker](#pdf-and-office-documents-to-text) or [MinerU](#pdf-and-office-documents-to-text) |
| Read scanned or photographed documents | [PaddleOCR](#ocr) or [Surya](#ocr), or a [document VLM](#vision-language-models-for-documents) |
| Pull specific fields (names, dates, totals) out of text with source positions | [LangExtract](#information-extraction) |
| Get tables out of digital PDFs | [Camelot](#tables) or [pdfplumber](#tables) |
| Know whether your pipeline is actually right | [OmniDocBench](#benchmarks-and-datasets) for parsing, [JSONSchemaBench](#benchmarks-and-datasets) for constrained decoding |

## Structured output

A schema in the prompt is a request. Constrained decoding makes it a guarantee: at each step, tokens that would break
the schema are masked out, so the output always parses. It guarantees shape, not truth, so still check the values.

### Constrained decoding engines

The grammar engines that serving frameworks plug in. Start with whichever your server already uses.

- [XGrammar](https://github.com/mlc-ai/xgrammar) - Fast grammar-constrained decoding for JSON Schema, regex and context-free grammars; a default backend in vLLM and SGLang.
- [llguidance](https://github.com/guidance-ai/llguidance) - Rust engine behind Guidance, with low per-token overhead and broad JSON Schema support.
- [Outlines Core](https://github.com/dottxt-ai/outlines-core) - Rust core of Outlines that compiles schemas and regexes into token-level automata.
- [LM Format Enforcer](https://github.com/noamgat/lm-format-enforcer) - Enforces JSON Schema or regex output and works with Transformers, vLLM and llama.cpp.

### Libraries

- [Instructor](https://github.com/567-labs/instructor) - Typed outputs from Pydantic models across most hosted and local providers, with validation and automatic retries. Good default.
- [PydanticAI](https://github.com/pydantic/pydantic-ai) - Agent framework from the Pydantic team where every result is a validated Pydantic object.
- [Outlines](https://github.com/dottxt-ai/outlines) - Constrained generation with JSON Schema, Pydantic, regex and grammars, for local models and many servers.
- [Guidance](https://github.com/guidance-ai/guidance) - Interleave prompts, generation and constraints in Python with fine control over each step.
- [BAML](https://github.com/BoundaryML/baml) - Language for typed LLM functions with schema-aware parsing that repairs almost-valid output.
- [Guardrails](https://github.com/guardrails-ai/guardrails) - Validators for LLM output (format, PII, toxicity, custom rules) that check and correct responses.
- [TypeChat](https://github.com/microsoft/TypeChat) - Uses TypeScript (or Python) type definitions as the schema for natural-language interfaces.
- [DSPy](https://github.com/stanfordnlp/dspy) - Declarative typed signatures for LLM programs, with optimizers that tune prompts against a metric.

### Serving with structured output built in

If you serve your own model, structured output is usually one request parameter away.

- [vLLM](https://github.com/vllm-project/vllm) - OpenAI-compatible `response_format` with JSON Schema, plus regex, choice and grammar options.
- [SGLang](https://github.com/sgl-project/sglang) - JSON Schema, regex and EBNF constraints with fast grammar backends.
- [llama.cpp](https://github.com/ggml-org/llama.cpp) - GBNF grammars and JSON Schema for GGUF models on CPU or GPU.
- [Ollama](https://github.com/ollama/ollama) - Pass a JSON Schema as `format` to get structured output from local models.

## Document parsing

### PDF and office documents to text

- [Docling](https://github.com/docling-project/docling) - PDF, DOCX, PPTX, HTML and images to Markdown or JSON with layout, tables and reading order. Good default.
- [Marker](https://github.com/datalab-to/marker) - Fast, accurate PDF to Markdown and JSON, with optional LLM help for hard pages.
- [MinerU](https://github.com/opendatalab/MinerU) - Complex PDFs and office files to Markdown or JSON, strong on scientific papers and formulas.
- [MarkItDown](https://github.com/microsoft/markitdown) - Lightweight conversion of office files, PDFs and more to Markdown for LLM input.
- [Unstructured](https://github.com/Unstructured-IO/unstructured) - Partitions many file types into typed elements (titles, tables, list items) for pipelines.
- [olmOCR](https://github.com/allenai/olmocr) - Linearizes PDFs into clean text with a fine-tuned VLM, built for large-scale dataset creation.
- [Open Parse](https://github.com/Filimoa/open-parse) - Layout-aware chunking of PDFs that keeps headings, tables and sections together.
- [Xberg](https://github.com/xberg-io/xberg) - Rust-core document extraction of text, metadata and tables with bindings for several languages (formerly Kreuzberg).

### OCR

- [PaddleOCR](https://github.com/PaddlePaddle/PaddleOCR) - Full OCR toolkit with detection, recognition, layout and table parsing in many languages. Good default.
- [Surya](https://github.com/datalab-to/surya) - OCR, layout analysis, reading order and table recognition in 90+ languages.
- [docTR](https://github.com/mindee/doctr) - Text detection and recognition models with a simple Python API, on PyTorch or TensorFlow.
- [Tesseract](https://github.com/tesseract-ocr/tesseract) - The classic open OCR engine: fast on clean printed text, weaker on complex layouts.

### Vision-language models for documents

Read the page image directly, which handles layouts and handwriting that break classic OCR.

- [Qwen3-VL](https://github.com/QwenLM/Qwen3-VL) - Open VLM family with strong document, chart and OCR performance in several sizes.
- [DeepSeek-OCR](https://github.com/deepseek-ai/DeepSeek-OCR) - OCR model that compresses page images into few vision tokens for long documents.
- [dots.ocr](https://github.com/studio-dots-ai/dots.ocr) - Single VLM for multilingual layout detection and content recognition.
- [Zerox](https://github.com/getomni-ai/zerox) - Sends each page to a vision model and returns Markdown; works with hosted or local VLMs.

### Tables

- [Camelot](https://github.com/camelot-dev/camelot) - Extracts tables from text-based PDFs into pandas DataFrames.
- [pdfplumber](https://github.com/jsvine/pdfplumber) - Character-level access to PDFs with reliable table extraction and visual debugging.

### Low-level PDF access

- [PyMuPDF](https://github.com/pymupdf/PyMuPDF) - Very fast PDF reading, rendering and text extraction (AGPL; check the license for commercial use).

## Information extraction

- [LangExtract](https://github.com/google/langextract) - Extracts entities and fields from long text with an LLM, linking every value to its exact source span.
- [Tallymark](https://github.com/alleyxd/Tallymark) - Receipts and invoices to validated JSON with a local VLM and constrained decoding, with a benchmark on CORD. *(maintainer's project)*

## Benchmarks and datasets

- [OmniDocBench](https://github.com/opendatalab/OmniDocBench) - Document parsing benchmark across page types with text, table, formula and reading-order metrics.
- [Omni OCR Benchmark](https://github.com/getomni-ai/benchmark) - Compares OCR and VLM extraction accuracy on real documents with JSON field scoring.
- [JSONSchemaBench](https://github.com/epfl-dlab/jsonschemabench) - About 10K real-world JSON schemas for testing constrained decoding engines for coverage, speed and quality.
- [CORD](https://github.com/clovaai/cord) - Receipt dataset with line items and totals, a standard test for receipt extraction.
- [SROIE](https://github.com/zzzDavid/ICDAR-2019-SROIE) - ICDAR 2019 scanned receipts: OCR and key information extraction.

## Contributing

Suggestions are welcome. A project fits if it is open source, maintained (commits in the last year), and helps turn
documents or text into structured data. Open a pull request with one line in the matching section: name, link, and
what it does in plain words. Links are checked every week.

## License

[CC0 1.0](LICENSE). To the extent possible under law, the authors have waived all copyright and related rights.
