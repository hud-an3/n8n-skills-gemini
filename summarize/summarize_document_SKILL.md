# Skill: Summarize Document

## Purpose
Read the contents of an uploaded file (from the form/file trigger) and produce a concise, accurate summary of its content.

## When to use this skill
Trigger this skill when a file has been uploaded in the current workflow run and the user's prompt asks for a summary, overview, TL;DR, or key points of that file. This is also the default action when a file is uploaded with no other instruction attached.

## Inputs
- `file_content` (binary/text, required): The extracted content of the uploaded file. If the file is a binary type (PDF, DOCX), it must first pass through a text-extraction node before reaching this skill — this skill does not parse binary itself.
- `file_name` (string, required): Original filename, used for context and for any downstream folder-routing logic.
- `summary_length` (string, optional): "short" (3-5 sentences), "medium" (1 paragraph), or "long" (structured with headers). Default to "medium" if not specified in the prompt.

## Steps
1. Confirm `file_content` is non-empty. If empty or extraction failed, do not attempt to summarize — flag the error instead (see Edge cases).
2. Identify the document type (report, article, contract, transcript, spreadsheet export, etc.) from structure/content — this affects tone of the summary.
3. Extract the main topic, key points, any decisions/action items, and notable figures or dates.
4. Write the summary in the requested length. Do not copy long verbatim passages — paraphrase.
5. If the document is very long (multi-section report), summarize per-section first, then produce one combined summary rather than skimming only the start.

## Output format
```
File: <file_name>

Summary:
<summary text at requested length>

Key points:
- <point 1>
- <point 2>
- <point 3>
```

## Edge cases
- If `file_content` is empty or unreadable, output: "Could not read contents of <file_name>. Please check the file format or re-upload." Do not guess at content.
- If the file is not a document type this skill can meaningfully summarize (e.g. an image with no OCR text), say so explicitly rather than inventing a summary.
- If the prompt asks for a summary AND another action (e.g. "summarize this and upload it"), only handle the summarization here — hand off the rest to the relevant skill (e.g. Upload File).
