College Knowledge Assistant (Retrieval-first RAG)
What it does
- Accepts at least five PDF/TXT files.
- Extracts text, splits it into chunks, embeds chunks, and indexes them with FAISS.
- Retrieves passages for a question and displays filename/page metadata.
- Abstains when no retrieved passage meets a configurable distance threshold.
- Includes a limited heuristic warning for some conflicting percentages/dates.
Important limitation
This version deliberately does not use an LLM. It retrieves source passages instead of composing a natural-language answer. Embedding similarity does not prove a passage answers a question. Conflict detection is heuristic and is not comprehensive. Scanned PDFs may require OCR.
Setup
1. Create/activate a Python virtual environment.
2. Install dependencies:
   pip install -r requirements_rag.txt
3. Run:
   streamlit run college_rag_assistant.py
4. Upload at least five PDF/TXT documents, click Process documents, then ask a question.
The first run may download the Hugging Face embedding model and needs internet access.
Architecture
PDF/TXT upload -> text extraction -> chunking -> Hugging Face embeddings -> FAISS index -> similarity retrieval -> relevance threshold -> source passages and metadata.
Test plan (10 example tests)
Use documents with known content and replace expected details with the actual facts in your sample documents.
1. Direct fact: ask for a clearly stated rule. Expected: the source passage containing the rule is retrieved.
2. Two-document question: ask for one fact from each of two documents. Expected: relevant passages from both documents appear among results when top-k and threshold allow.
3. Unanswerable: ask an unrelated question. Expected: no answer if all results exceed the calibrated distance limit; otherwise inspect threshold behavior.
4. Paraphrase: ask a question using synonyms. Expected: a semantically related passage is retrieved.
5. Conflict: include two files with different attendance percentages. Expected: relevant passages are shown; heuristic may flag a percentage conflict.
6. PDF source: ask about text on a known page. Expected: filename and page number are displayed.
7. TXT source: ask about a fact in a TXT file. Expected: filename is displayed and page is N/A.
8. Empty query: click Get answer without typing. Expected: warning to enter a question.
9. Fewer than five files: click Process documents with fewer than five. Expected: upload-count error.
10. Unreadable/scanned PDF: upload a PDF with no extractable text. Expected: warning/error or no-readable-text message; OCR is not implemented.
Technical issue for demo
A common issue is scanned PDFs returning empty text because they contain page images rather than selectable text. Explain that PyPDF extracts embedded text but does not perform OCR. A future fix is adding OCR for scanned files.