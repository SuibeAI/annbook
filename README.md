# Annotated Book

Annotated Book (`annbook`) is a Codex/Claude Code skill for creating and maintaining annotated Jupyter Book course hubs. It helps you initialize a book workspace, create teaching-oriented notebooks from papers, code, or model topics, build and preview the book, and configure GitHub Pages publishing.

The default writing style is based on annotated deep learning tutorials: explain concepts first, then tensor shapes and formulas, then code, then minimal examples and result interpretation.

## Install

### Codex

Clone this skill into the Codex skills directory:

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/suibeai-skills/annbook.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/annbook"
```

Restart Codex after installation. Then use the skill by mentioning it:

```text
Use $annbook to run annbook bookhub-init for this repository.
Use $annbook to create an annotated notebook about CNN feature extraction.
Use $annbook to build and preview this book.
```

To update:

```bash
cd "${CODEX_HOME:-$HOME/.codex}/skills/annbook"
git pull
```

### Claude Code

Claude Code discovers personal skills from `~/.claude/skills/<skill-name>/SKILL.md` and project skills from `.claude/skills/<skill-name>/SKILL.md`.

Install as a personal Claude Code skill:

```bash
mkdir -p "$HOME/.claude/skills"
git clone https://github.com/suibeai-skills/annbook.git \
  "$HOME/.claude/skills/annbook"
```

Or install it only for one project:

```bash
mkdir -p .claude/skills
git clone https://github.com/suibeai-skills/annbook.git \
  .claude/skills/annbook
```

Claude Code watches existing skill directories for `SKILL.md` changes, but restart Claude Code if the parent `.claude/skills` directory did not exist when the session started.

## Book Hub Layout

Use `annbook` inside a repository that stores teaching notebooks and related source material:

```text
book-hub/
├── AGENTS.md
├── README.md
├── book_generate.sh
├── book_start.sh
├── books/
│   ├── annotated-cnn/
│   │   └── AnnotatedCNN.ipynb
│   └── annotated-attention/
│       └── AnnotatedAttention.ipynb
├── codes/
├── papers/
├── slides/
└── .github/
    └── workflows/
        └── pages.yml
```

`books/` contains the course notebooks. `codes/` stores reusable implementations. `papers/` and `slides/` provide source material for annotated explanations. `book_generate.sh` builds the book, and `book_start.sh` starts a local preview.

## Common Commands

### Initialize a Book Hub

```text
Use $annbook to run annbook bookhub-init.
```

Typical result:

```text
books/
codes/
papers/
slides/
README.md
book_generate.sh
book_start.sh
.github/workflows/pages.yml
```

`bookhub-init` includes GitHub Pages configuration by default. When the repository is pushed to GitHub, the Pages workflow builds `./books` into `./_build/books` and publishes it.

### Create an Annotated Notebook

Create a notebook from a topic:

```text
Use $annbook to run annbook book-new for CNN feature extraction.
```

Create a notebook from a paper:

```text
Use $annbook to create an annotated notebook from the Vision Transformer paper.
```

Create a notebook from code:

```text
Use $annbook to explain this MultiHeadAttention implementation as an annotated notebook.
```

Expected notebook structure. The notebook title should be a first-level heading, and all other sections should be second-level headings:

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

For computer vision notebooks, `annbook` should explain image tensors such as `(B, C, H, W)`, feature map size changes, patch/token shapes, attention maps, losses, metrics, and visualizations when relevant.

### Generate the Book

```text
Use $annbook to run annbook book-generate.
```

Typical command:

```bash
./book_generate.sh ./books ./_build/books
```

The generated HTML is written to:

```text
_build/books/
```

### Start a Local Preview

```text
Use $annbook to run annbook book-start.
```

Typical command:

```bash
./book_start.sh ./books
```

Default local URL:

```text
http://127.0.0.1:8000
```

If port `8000` is occupied, ask the agent to use another port:

```text
Use $annbook to start the book on port 8765.
```

### Add or Repair GitHub Pages

```text
Use $annbook to run annbook book-pages.
```

This creates or repairs:

```text
.github/workflows/pages.yml
```

The workflow runs on push to `main` and on manual dispatch. It installs Jupyter Book, runs `book_generate.sh`, uploads `_build/books`, and deploys through GitHub Pages.

## Complete Example

This example starts from an empty repository and creates a first annotated notebook.

### 1. Create a Repository

```bash
mkdir -p ~/courses/computer_vision_book
cd ~/courses/computer_vision_book
git init
```

Start Codex in that directory:

```bash
codex -C ~/courses/computer_vision_book
```

### 2. Initialize the Book Hub

Ask Codex:

```text
Use $annbook to run annbook bookhub-init for a computer vision course.
```

Expected result:

```text
books/
codes/
papers/
slides/
README.md
book_generate.sh
book_start.sh
.github/workflows/pages.yml
```

### 3. Create a Notebook

Ask Codex:

```text
Use $annbook to create an annotated notebook named AnnotatedVisionTransformer.ipynb about Vision Transformer.
```

Expected behavior:

- Read `AGENTS.md` when present.
- Inspect `codes/`, `papers/`, `slides/`, and existing notebooks.
- Create a notebook under `books/`.
- Explain patch embedding, token sequence shape, positional encoding, attention matrix shape, MLP block, classifier head, loss, and a minimal runnable example.
- Include result interpretation and references.

### 4. Build the Book

```text
Use $annbook to run annbook book-generate.
```

Expected output:

```text
_build/books/
```

### 5. Preview Locally

```text
Use $annbook to run annbook book-start.
```

Open:

```text
http://127.0.0.1:8000
```

### 6. Publish with GitHub Pages

Commit and push the repository after checking the generated configuration:

```bash
git add .
git commit -m "Initialize annotated book hub"
git push origin main
```

GitHub Actions will build and deploy the book automatically when Pages is enabled for the repository.

## Authoring Conventions

- Write teaching content in Chinese by default.
- Keep important English terms on first mention, for example `视觉 Transformer (Vision Transformer, ViT)`.
- Use Markdown and code cells in a natural alternation.
- Explain tensor shapes before and after important operations.
- Prefer small runnable examples over long training jobs.
- Reuse implementations from `codes/` when available.
- Explain what to inspect before a visualization and what the visualization shows after it.
- Keep notebooks suitable for Jupyter Book rendering.

## Maintenance Workflow

Use this loop for long-term course maintenance:

1. Add papers, slides, or reusable code to `papers/`, `slides/`, or `codes/`.
2. Use `annbook book-new` to create or update a notebook.
3. Run a minimal notebook validation.
4. Run `annbook book-generate`.
5. Run `annbook book-start` for visual inspection.
6. Use `annbook book-pages` if Pages publishing needs repair.
7. Commit and push after reviewing the changes.

