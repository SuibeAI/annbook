# Annotated Book

Annotated Book（`annbook`）是一个用于创建和维护 annotated Jupyter Book 课程中心的 Codex / Claude Code skill。它可以帮助你初始化 book hub、从论文/代码/模型主题创建教学型 notebook、生成和预览 Jupyter Book，并配置 GitHub Pages 自动发布。

默认写作风格参考 annotated 深度学习教程：先讲概念，再讲张量形状和公式，然后给出代码、最小示例和结果解释。

## 安装

### Codex

将该 skill 克隆到 Codex skills 目录：

```bash
mkdir -p "${CODEX_HOME:-$HOME/.codex}/skills"
git clone https://github.com/suibeai-skills/annbook.git \
  "${CODEX_HOME:-$HOME/.codex}/skills/annbook"
```

安装后重启 Codex。之后可以通过显式提到 skill 来使用：

```text
Use $annbook to run annbook bookhub-init for this repository.
Use $annbook to create an annotated notebook about CNN feature extraction.
Use $annbook to build and preview this book.
```

更新方式：

```bash
cd "${CODEX_HOME:-$HOME/.codex}/skills/annbook"
git pull
```

### Claude Code

Claude Code 会从 `~/.claude/skills/<skill-name>/SKILL.md` 发现个人 skill，也会从项目内的 `.claude/skills/<skill-name>/SKILL.md` 发现项目级 skill。

安装为个人 Claude Code skill：

```bash
mkdir -p "$HOME/.claude/skills"
git clone https://github.com/suibeai-skills/annbook.git \
  "$HOME/.claude/skills/annbook"
```

或只安装到当前项目：

```bash
mkdir -p .claude/skills
git clone https://github.com/suibeai-skills/annbook.git \
  .claude/skills/annbook
```

Claude Code 会监听已有 skill 目录中的 `SKILL.md` 变化；如果会话启动时父目录 `.claude/skills` 还不存在，安装后建议重启 Claude Code。

## Book Hub 目录结构

在一个保存教学 notebook 和相关材料的仓库中使用 `annbook`：

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

`books/` 存放课程 notebook。`codes/` 存放可复用代码实现。`papers/` 和 `slides/` 存放论文、讲义等参考材料。`book_generate.sh` 用于生成 Jupyter Book，`book_start.sh` 用于启动本地预览服务。

## 常用命令

### 初始化 Book Hub

```text
Use $annbook to run annbook bookhub-init.
```

典型结果：

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

`bookhub-init` 默认包含 GitHub Pages 配置。仓库推送到 GitHub 后，Pages workflow 会将 `./books` 构建到 `./_build/books` 并发布。

### 创建 Annotated Notebook

从主题创建 notebook：

```text
Use $annbook to run annbook book-new for CNN feature extraction.
```

从论文创建 notebook：

```text
Use $annbook to create an annotated notebook from the Vision Transformer paper.
```

从代码创建 notebook：

```text
Use $annbook to explain this MultiHeadAttention implementation as an annotated notebook.
```

推荐 notebook 结构。Notebook 标题使用一级标题，其他章节使用二级标题：

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

对于计算机视觉 notebook，`annbook` 应解释图像张量 `(B, C, H, W)`、特征图尺寸变化、patch/token 形状、注意力矩阵、损失函数、评价指标和可视化含义。

如果提供的参考资料、本地源码或搜索结果已经清楚说明了模型结构，`annbook` 也可以补充：

- 张量流动表格：列出每个主要模块以及模块之后的张量维度；
- Mermaid 流程图：说明数据流、模型 pipeline、训练流程或可视化流程。

如果资料不够明确，这两项可以不生成。不要编造没有依据的张量维度或结构细节。

### 生成 Book

```text
Use $annbook to run annbook book-generate.
```

典型命令：

```bash
./book_generate.sh
```

该命令使用脚本缺省参数，等价于 `./book_generate.sh ./books ./_build/books directory`。

生成的 HTML 输出目录：

```text
_build/books/
```

### 启动本地预览

```text
Use $annbook to run annbook book-start.
```

典型命令：

```bash
./book_start.sh
```

该命令使用脚本缺省目录 `./books`。

默认本地地址：

```text
http://127.0.0.1:8000
```

如果 `8000` 端口被占用，可以要求使用其他端口：

```text
Use $annbook to start the book on port 8765.
```

### 添加或修复 GitHub Pages

```text
Use $annbook to run annbook book-pages.
```

该命令会创建或修复：

```text
.github/workflows/pages.yml
```

workflow 会在 push 到 `main` 或手动触发时运行。它会安装 Jupyter Book，使用缺省参数执行 `./book_generate.sh`，上传 `_build/books`，并通过 GitHub Pages 发布。

## 完整示例

下面示例从一个空仓库开始，创建第一个 annotated notebook。

### 1. 创建仓库

```bash
mkdir -p ~/courses/computer_vision_book
cd ~/courses/computer_vision_book
git init
```

在该目录启动 Codex：

```bash
codex -C ~/courses/computer_vision_book
```

### 2. 初始化 Book Hub

向 Codex 提出请求：

```text
Use $annbook to run annbook bookhub-init for a computer vision course.
```

预期结果：

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

### 3. 创建 Notebook

向 Codex 提出请求：

```text
Use $annbook to create an annotated notebook named AnnotatedVisionTransformer.ipynb about Vision Transformer.
```

预期行为：

- 读取已有 `AGENTS.md`。
- 检查 `codes/`、`papers/`、`slides/` 和已有 notebooks。
- 在 `books/` 下创建 notebook。
- 解释 patch embedding、token 序列形状、位置编码、注意力矩阵形状、MLP block、分类头、损失函数和最小可运行示例。
- 在参考资料足够明确时，加入张量流动表格和 Mermaid 模型流程图。
- 包含结果解释和参考资料。

### 4. 生成 Book

```text
Use $annbook to run annbook book-generate.
```

预期输出：

```text
_build/books/
```

### 5. 本地预览

```text
Use $annbook to run annbook book-start.
```

打开：

```text
http://127.0.0.1:8000
```

### 6. 通过 GitHub Pages 发布

检查生成配置后提交并推送仓库：

```bash
git add .
git commit -m "Initialize annotated book hub"
git push origin main
```

当仓库启用 GitHub Pages 后，GitHub Actions 会自动构建并部署 book。

## 写作约定

- 默认使用中文编写教学内容。
- 重要英文术语首次出现时保留英文，例如 `视觉 Transformer (Vision Transformer, ViT)`。
- Markdown 单元和代码单元自然交替。
- 在关键操作前后说明张量形状。
- 当模块级维度可以从资料中明确确定时，加入张量流动表格。
- 当模型或流程足够清晰时，加入 Mermaid 流程图。
- 如果资料存在歧义，可以省略张量表格或流程图，不要编造细节。
- 优先使用小型可运行示例，不默认执行长时间训练。
- 优先复用 `codes/` 中已有实现。
- 可视化前说明要观察什么，可视化后解释观察结果。
- 保证 notebook 适合 Jupyter Book 渲染。

## 维护流程

长期维护课程内容时，可以使用下面的循环：

1. 将论文、讲义或可复用代码加入 `papers/`、`slides/` 或 `codes/`。
2. 使用 `annbook book-new` 创建或更新 notebook。
3. 对 notebook 做最小验证。
4. 运行 `annbook book-generate`。
5. 运行 `annbook book-start` 做视觉检查。
6. 如果 Pages 发布配置需要修复，运行 `annbook book-pages`。
7. 检查改动后提交并推送。
