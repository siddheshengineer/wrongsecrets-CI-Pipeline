# Building a Symptom‑Aware GenAI Assistant That Recommends Local Care

_By FENRlR, Siddhesh Nikam, Kimaya Gaikwad_

**Capstone Project — GenAI Intensive Course (Q1 2025)**

> **TL;DR** We built a conversational health companion that translates everyday symptom descriptions into evidence‑based explanations and recommends credible, nearby providers — all with empathy and responsible disclaimers.

---

## The Problem: From “Dr Google” to a Symptom‑Aware Companion

Millions of people open a browser every day to diagnose themselves:

*“Why does my stomach hurt after eating?”*  
*“Is a persistent cough serious?”*  
*“Should I see a doctor for this rash?”*

Yet the torrent of unvetted pages, conflicting opinions, and one‑size‑fits‑all answers often leaves readers more anxious than informed.

What if an assistant could interpret those symptoms in plain language **and** point you toward qualified help? That’s the challenge we took on.

---

## Our Idea in 30 Seconds

We created a **symptom‑aware GenAI assistant** that

* understands free‑text symptom descriptions,
* provides trustworthy, citation‑backed explanations, and
* suggests local healthcare facilities that can actually help.

### What Makes It Different

- **Natural‑language symptom understanding** — tell it _exactly_ how you feel, no check‑boxes required  
- **Evidence‑based responses** — grounded in **2 200+ peer‑reviewed medical PDFs**  
- **Locality‑aware recommendations** — surfaces verified clinics _near you_  
- **Empathetic tone** — acknowledges uncertainty, offers next steps  
- **Responsible disclaimers** — reminds users it’s _not_ a doctor, just smart guidance

---

## How We Built It

### 1. Grounding the Model in Trusted Medical Knowledge

We parsed, chunked, and embedded thousands of authoritative PDFs (cardiology, oncology, rare diseases, …) into a ChromaDB vector store using Google Gemini’s `text‑embedding‑004` model.

```python
for pdf_file in all_pdfs:
    text = extract_text_from_pdf(pdf_file)
    chunks = chunk_text(text)
    ids = [f"{pdf_file}_{i}" for i in range(len(chunks))]
    db.add(documents=chunks,
           metadatas=[{"source": pdf_file}] * len(chunks),
           ids=ids)
```

> ⚡ **Smart shortcut:** we persisted the finished collection and load it in **seconds**:
>
> ```python
> chroma_client = chromadb.PersistentClient(path=config["DB_FOLDER"])
> db = chroma_client.get_or_create_collection(
>         name=config["DB_NAME"],
>         embedding_function=embed_fn)
> ```

---

### 2. Prompt Engineering + Function Calling

#### `RAG_process()` — Interpreting Symptoms with Retrieval‑Augmented Generation

```python
def RAG_process(userinput, hist):
    embed_fn.document_mode = False
    results = db.query(query_texts=[userinput], n_results=1)
    passages = get_related_passages(results)
    prompt = build_prompt(userinput, passages)
    return genai.generate_content(prompt)
```

1. **Embed** the user’s question.  
2. **Retrieve** the most relevant medical passages.  
3. **Generate** an answer that cites its sources.

#### `doctor_lookup()` — Hyper‑local Provider Search

```python
def doctor_lookup(location, symptoms):
    prompt = build_google_prompt(location, symptoms)
    response = genai.generate_content_with_google_search(prompt)
    return parse_doctor_results(response)
```

When the user mentions a location, we pivot to a Google‑Search‑powered tool call that returns structured results: name, address, phone, specialty.

---

### 3. Smart Chat Logic & Intuitive UI

Using spaCy NER, we detect place names on the fly and route each message:

```python
def chat_logic(userinput, hist):
    if check_loc(userinput):
        return doctor_lookup(loc, symptoms_loc)
    else:
        return RAG_process(userinput, hist)
```

All of this lives behind a clean [Gradio](https://gradio.app/) interface so users can just… chat.

---

## Challenges & Limitations

| Challenge | Why It Matters |
|-----------|---------------|
| **Scope of knowledge** | We cover well‑established topics; edge cases & new studies require updates. |
| **Subtle context** | Nuanced or culturally‑specific symptom descriptions can fool the model. |
| **API rate limits** | External LLM + search quotas can throttle throughput. |
| **Location resolution** | spaCy struggles with rural towns & ambiguous toponyms. |

---

## What’s Next?

- **EHR integration** for personalized context (with privacy safeguards)  
- **One‑click appointment booking** with recommended providers  
- **Expanded symptom coverage** including rare diseases & pediatric cases  
- **Longitudinal tracking** to flag worrying trends over time  
- **Multilingual & multimodal** support (voice, images, video)

---

## Takeaways

*Generative AI can’t replace physicians, but it can make the journey to one far less confusing.* By combining trustworthy content, retrieval‑augmented generation, and location‑aware search, we offer users clarity, empathy, and actionable next steps.

> When you’re not sure what your symptoms mean, let GenAI guide you — and point you to the care you deserve.

---

### Medium Publishing Checklist ✅

1. **Cover image**  
   – Try an AI‑generated stethoscope wrapped around a chat bubble for instant context.
2. **Tags**  
   – `generative‑ai`, `healthcare`, `rag`, `llm`, `gemini`, `chromadb`.
3. **Canonical link**  
   – Point to your project repo or academic write‑up.
4. **CTA**  
   – Invite feedback, GitHub stars, or beta testers.

---

#### Responsible Medical Disclaimer

This article offers general health information and **does not** constitute medical advice. Always consult a qualified professional for personalised diagnosis or treatment.
