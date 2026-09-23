# Kimi 2.5 Internal Leaks

> A captured evidence archive from a Kimi.ai session, documenting the assistant's exposed capabilities, supporting tooling, and runtime context at the time.

## Why this repository exists

This repository was obtained during a Kimi.ai session and is intended as proof of compromise. It preserves the session's internal skill sheets, document and PDF tooling, web-app scaffolding, and environment captures so that the available chat functions and the way they were exposed can be reviewed as a historical record.

This is an evidence archive and technical reference—not a polished product, exploit guide, or turnkey application. The repository contents should be preserved as collected and evaluated independently before being used for attribution, incident response, or legal conclusions.

## What the archive shows

- **Basic chat capabilities:** document creation and editing, PDF generation and processing, and web-app initialization.
- **Tool orchestration:** scripts, validators, templates, and route-specific workflows available through the session.
- **Runtime context:** system, network, process, environment, and SSH-related captures stored under `SystemInternals/`.
- **Historical implementation details:** how the assistant's capabilities were organized, invoked, and connected to local tooling during the session.

## Repository map

| Area | Contents |
|---|---|
| [`docx/`](docx/) | Word-document workflows, OpenXML templates, Python editing helpers, validators, and asset generators |
| [`pdf/`](pdf/) | HTML-to-PDF conversion, LaTeX compilation, extraction, form handling, metadata, and page operations |
| [`webapp-building/`](webapp-building/) | React, TypeScript, Vite, Tailwind CSS, and shadcn/ui initialization workflow |
| [`SystemInternals/`](SystemInternals/) | Runtime and environment telemetry captured during the session |

Useful entry points:

- [`docx/SKILL.md`](docx/SKILL.md) — DOCX creation and editing workflow
- [`docx/references/EditingGuide.md`](docx/references/EditingGuide.md) — Python/XML editing reference
- [`pdf/SKILL.md`](pdf/SKILL.md) — PDF creation and processing workflow
- [`pdf/routes/html.md`](pdf/routes/html.md), [`pdf/routes/latex.md`](pdf/routes/latex.md), and [`pdf/routes/process.md`](pdf/routes/process.md) — PDF route guides
- [`webapp-building/SKILL.md`](webapp-building/SKILL.md) — web-app tooling workflow

## Important security notice

`SystemInternals/` contains operational telemetry that may include host identity, network listeners, process command lines, environment variables, SSH configuration, public-key material, or references to private host keys. Treat the directory as restricted evidence:

- Do not copy its contents into public posts, issues, pull requests, or releases.
- Preserve the original files and record their provenance before analysis.
- Redact or remove sensitive captures before redistributing the repository.
- Rotate or revoke any credentials, tokens, keys, or host access that may have been exposed.
- Use trusted inputs and protected output locations when processing documents or PDFs, since those tools may expose contents and metadata.

The repository does not declare a project-level license. Confirm the provenance and licensing of included materials before reusing or redistributing them.

## Explore the toolchains

### PDF workflow

The PDF toolkit supports HTML-based document generation, LaTeX compilation, and processing of existing PDFs. Start with [`pdf/SKILL.md`](pdf/SKILL.md) and the relevant route guide.

```bash
REPO="$(pwd)"
bash "$REPO/pdf/scripts/pdf.sh" check
```

`pdf.sh fix` installs or repairs dependencies and changes the local environment:

```bash
bash "$REPO/pdf/scripts/pdf.sh" fix
```

Example operations:

```bash
node "$REPO/pdf/scripts/html_to_pdf.js" input.html --output output.pdf
python3 "$REPO/pdf/scripts/compile_latex.py" main.tex --runs 2
python3 "$REPO/pdf/scripts/pdf.py" extract text document.pdf
python3 "$REPO/pdf/scripts/pdf.py" pages merge a.pdf b.pdf -o merged.pdf
```

The process route does not support encrypted PDFs. Setup and HTML conversion may download third-party software or load external resources.

### DOCX workflow

The DOCX toolkit separates document creation from document editing:

- Use the C# OpenXML SDK for new documents.
- Use Python and `lxml` for editing existing documents.
- Do not substitute `python-docx` or `docx-js` for these workflows.

```bash
bash "$REPO/docx/scripts/docx" env
bash "$REPO/docx/scripts/docx" init
bash "$REPO/docx/scripts/docx" build /mnt/okcomputer/output/report.docx
bash "$REPO/docx/scripts/docx" validate /mnt/okcomputer/output/report.docx
```

The wrapper expects these paths:

- Working directory: `/tmp/docx-work/`
- Output directory: `/mnt/okcomputer/output/`
- Upload directory: `/mnt/okcomputer/upload/`

Direct helpers are also available:

```bash
python3 "$REPO/docx/scripts/validate_docx.py" report.docx
python3 "$REPO/docx/scripts/validate_all.py" report.docx
python3 "$REPO/docx/scripts/fix_element_order.py" report.docx
```

`validate_all.py` and `fix_element_order.py` can rewrite a DOCX in place. Work on a copy and use an explicit output path.

### Web-app workflow

The documented initializer is:

```bash
bash "$REPO/webapp-building/scripts/init-webapp.sh" "Site title" [template-name]
```

The local template documentation is available at [`webapp-building/scripts/template/README.md`](webapp-building/scripts/template/README.md). This snapshot does not include the expected `webapp-building/templates/` directory, a root package manifest, application source, or Vite configuration, so the web-app workflow should be treated as a reference rather than a ready-to-build application.

## Evidence handling and verification

For forensic use, preserve the collected state before making changes:

1. Record the acquisition source, date, and chain of custody.
2. Hash original files before analysis.
3. Keep the `SystemInternals/` captures unchanged.
4. Validate timestamps, provenance, and any claimed indicators of compromise independently.
5. Review generated artifacts for metadata, comments, tracked changes, and unintended content.

## Known limitations

- The repository is an archive of workflows and utilities, not one cohesive application.
- The checked-in web-app workflow is incomplete.
- The repository includes vendored `node_modules/` and generated NuGet artifacts.
- The DOCX validator is a precompiled Linux x64 binary; verify its provenance and platform compatibility before execution.
- No root dependency manifest, license, or complete web-app build configuration is present.

## Bottom line

This repository captures what a Kimi.ai session exposed and how its basic chat functions were connected to local document, PDF, web-app, and system tooling at that point in time. Handle it as sensitive evidence, verify every conclusion independently, and preserve the original collection before analysis.
