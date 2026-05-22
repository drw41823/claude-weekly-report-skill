# Weekly Report Generator

> [English](README.md)

一个 Claude Code 技能，用于生成 LaTeX 格式的精美周报。专为需要每周向导师汇报的学生和研究人员设计。

## 功能

- **交互式访谈** — 询问你本周的工作内容、详细程度、图表需求、作者信息等
- **点击式选择** — 详细程度使用选项选择（详细 / 简洁 / 跳过），无需打字
- **信息完整性检查** — 发现信息缺口时会主动询问，不会凭空编造
- **生成前预览** — 先生成纯文本内容供你审核，确认后再生成 LaTeX
- **结构化输出** — 生成带标题页、目录和编号章节的 `.tex` 项目
- **打包下载** — 所有文件打包成 zip，可直接编译

## 安装

将本目录复制到你的项目 `.claude/skills/weekly-report/`：

## 使用方法

在 Claude Code 中说：

- "write my weekly report"
- "generate weekly report"
- "周报"
- "/weekly-report"

Skill 会引导你完成后续步骤。

## 输出格式

```
weekly_report_YYYY-MM-DD/
├── main.tex
├── 1_highlights.tex
├── 2_section.tex
├── ...
└── figure/

weekly_report_YYYY-MM-DD.zip
```

使用 `pdflatex main.tex` 编译（运行两次以生成目录）。

## 设计原则

- **不编造** — 从不凭空捏造细节，信息缺失时会主动询问
- **先预览再生成 LaTeX** — 你先看到纯文本内容，确认后再生成代码
- **亮点简洁** — 3-8 个字的标签，不是完整句子
- **你控制详细程度** — 每个部分都可以选择详细 / 简洁 / 跳过
- **以导师为读者** — 以向导师汇报的视角撰写
