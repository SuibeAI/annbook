---
name: annbook
description: Create and maintain Annotated Jupyter Book course hubs. Use when the user asks for annbook bookhub-init, book-new, book-generate, book-start, or book-pages; when they want to initialize an annotated book hub, generate a teaching notebook from a paper, code, or module topic such as CNN, Attention, ViT, or GradCAM, build or preview the book, or configure GitHub Pages publishing for annotated notebook courses.
---

# Annotated Book

## Core Rules

- Work in the current repository unless the user gives another path.
- Read project `AGENTS.md` before creating or modifying course content. Treat it as the local authority for language, structure, code style, and validation.
- Default to Chinese for teaching content; keep important English terms in parentheses on first use.
- Prefer the annotated style: concept and motivation, tensor/formula explanation, readable implementation, minimal runnable example, result interpretation, summary, and references.
- For notebooks, always explain input, output, and key intermediate tensor shapes. For computer vision content, use image tensors as `(B, C, H, W)` unless the local project uses another convention.
- When supplied references, local source material, or search results clearly specify the model/module structure, add a tensor flow table that lists each major module and the tensor shape after that module. If the shapes are ambiguous or unsupported, omit the table or mark the uncertainty instead of inventing dimensions.
- When the architecture or workflow is clear, add a concise Mermaid diagram for the data flow, model pipeline, training loop, or visualization pipeline. If the process is not clear enough, skip the diagram.
- Before writing new model code in a notebook, inspect `codes/` and nearby notebooks. Reuse local implementations when possible, especially common modules such as attention, CNN blocks, training loops, and visualization utilities.
- Keep scripts and infrastructure small and predictable. Avoid adding unrelated engineering scaffolding.
- After changing notebooks, run a minimal validation when feasible: execute affected lightweight cells, or at least verify the notebook JSON and imported names. If training exists, use a small validation setting.

## Commands

### `annbook bookhub-init`

Initialize an annotated book hub. Use this for a new course/book workspace or when a repository is missing the standard structure.

Create or verify:

- `books/`
- `codes/`
- `papers/`
- `slides/`
- `_build/` only if needed by a local build
- `README.md`
- `book_generate.sh`
- `book_start.sh`
- `.github/workflows/pages.yml`

Use bundled templates from `assets/` for:

- `book_generate.sh`
- `book_start.sh`
- `.github/workflows/pages.yml`

Rules:

- Do not overwrite existing scripts or workflow files without reading them first and preserving project-specific changes.
- Make copied shell scripts executable.
- Include Pages publishing by default. This is the integrated form of `book-pages`.
- If the hub is for a specific course, make `README.md` concise and explain where notebooks live and how to build/start the book.

### `annbook book-new`

Create a new annotated notebook from one of these sources:

- Paper or PDF.
- Existing code.
- A model or module topic such as CNN, Attention, ViT, Transformer block, GradCAM, object detection, or segmentation.
- Existing local slides, papers, or notebooks.

Workflow:

1. Read `AGENTS.md`.
2. Inspect `books/`, `codes/`, `papers/`, and `slides/` for relevant local material.
3. Choose a notebook path under `books/`, using an existing book/topic folder if appropriate.
4. Build the notebook with Markdown/code alternation rather than one long code block.
5. Use this default notebook outline unless local style clearly differs. The notebook title is a first-level heading; all other sections are second-level headings:
   - `# 标题`
   - `## 学习目标`
   - `## 研究背景与直觉`
   - `## 模型框架`
   - `## 模型组件`
   - `## 数据集与任务定义`
   - `## 损失函数定义`
   - `## 模型训练过程`
   - `## 最小可运行示例与结果解释`
   - `## 小结`
   - `## 参考资料与延伸阅读`
6. Include tensor shapes in prose and code comments where they clarify the data flow.
7. If the source is clear enough, add a tensor flow table. Prefer columns such as `阶段/模块`, `输入形状`, `操作`, `输出形状`, and `说明`.
8. If the model or workflow is clear enough, add a Mermaid flowchart. Keep node labels short and consistent with the notebook prose.
9. Add a small runnable example or visualization. For visual explanation, state what to inspect before the figure and interpret what is visible after it.
10. Update local book navigation if the project uses explicit `_toc.yml` or another manual index. If navigation is generated automatically by `book_generate.sh`, no TOC edit is needed.
11. Validate the changed notebook minimally.

Notebook writing constraints:

- Do not turn the notebook into an API list or paper translation.
- Do not assume the reader already understands the task, dataset, masks, losses, or visualizations.
- Prefer small examples and transparent teaching code over unnecessary abstraction.
- Do not fabricate tensor dimensions or architecture details. If references are insufficient, say that the tensor table or Mermaid diagram is omitted because the source is not explicit enough.
- Use folded Markdown details for thinking questions with answers.

### `annbook book-generate`

Build the annotated book.

Workflow:

1. Confirm `book_generate.sh` exists.
2. Confirm `book_generate.sh` is executable. If not, run `chmod +x book_generate.sh`.
3. Run the local script, normally:

```bash
./book_generate.sh
```

This is equivalent to:

```bash
./book_generate.sh ./books ./_build/books directory
```

4. If the project needs a different mode or path, pass it explicitly:

```bash
./book_generate.sh ./books ./_build/books directory
./book_generate.sh ./books ./_build/books source
./book_generate.sh ./books ./_build/books books
```

5. Report the output directory and summarize any build errors with the failing file or cell when available.

### `annbook book-start`

Preview the annotated book locally.

Workflow:

1. Confirm `book_start.sh` exists.
2. Confirm `book_start.sh` is executable. If not, run `chmod +x book_start.sh`.
3. Start the local service:

```bash
./book_start.sh
```

This is equivalent to `./book_start.sh ./books`.

4. If port `8000` is occupied, use another port with `PORT=...`.
5. Keep the server session running only when the user needs an active preview, and give the local URL.

### `annbook book-pages`

Install or repair GitHub Pages publishing for an existing hub. This is also included by default in `bookhub-init`.

Workflow:

1. Read existing `.github/workflows/pages.yml` if present.
2. Create or update a workflow that:
   - runs on push to `main` and `workflow_dispatch`;
   - installs Jupyter Book;
   - runs `./book_generate.sh`;
   - uploads `_build/books`;
   - deploys with `actions/deploy-pages`.
3. Keep repository-specific branch names, Python versions, install steps, or build arguments if they already exist and are intentional.
4. Do not commit or push unless the user explicitly asks.

## Bundled Assets

- `assets/book_generate.sh`: default annotated book build script.
- `assets/book_start.sh`: default local preview script.
- `assets/pages.yml`: default GitHub Pages workflow.
