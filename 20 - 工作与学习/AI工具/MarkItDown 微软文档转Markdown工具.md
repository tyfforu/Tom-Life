# MarkItDown：微软开源「万物转Markdown」工具

> 来源：微信公众号「舒哥带你学python」
> GitHub: 44.6k ⭐

## 是什么

微软出的轻量级 Python 开源工具，核心功能只有一个：**把各种文件格式统统转成结构清晰的 Markdown**。

和其他转换工具不同，它会尽量保留标题、列表、表格、链接这些文档结构，输出内容 LLM 友好，不只是干巴巴的纯文本。

## 支持的格式

| 类别 | 格式 |
|------|------|
| 办公文档 | Word、PowerPoint、Excel |
| PDF | ✅ |
| 图片 | 支持 EXIF 元数据 + OCR 文字识别 |
| 音频 | 支持语音转文字 + 元数据提取 |
| 网页 | HTML、Wikipedia 等有特殊优化 |
| 结构化数据 | CSV、JSON、XML |
| 档案 | ZIP（递归处理内部文件） |
| 其他 | YouTube 链接、EPUB |

## 安装

```bash
# 推荐全功能版，一次装好所有依赖
pip install 'markitdown[all]'

# 开发者源码安装
git clone git@github.com:microsoft/markitdown.git
cd markitdown
pip install -e 'packages/markitdown[all]'
```

环境要求：**Python 3.8+**

## 使用方法

### 命令行
```bash
markitdown path-to-file.pdf -o document.md
# 或管道输出
cat file.docx | markitdown > output.md
```

### Python API
```python
from markitdown import MarkItDown

md = MarkItDown()
result = md.convert("test.xlsx")
print(result.text_content)
```

### 结合 LLM 生成图片描述
```python
from markitdown import MarkItDown
from openai import OpenAI

client = OpenAI()
md = MarkItDown(llm_client=client, llm_model="gpt-4o")
result = md.convert("example.jpg")
print(result.text_content)
```

## 为什么需要它

1. **非结构化 → 结构化**：Markdown 比乱糟糟的 PDF/Word 更适合 AI 处理和分析
2. **训练数据预处理**：将 PDF 转为 Markdown 让模型更好理解文档结构，提升泛化能力
3. **复杂文档解析**：数学公式能自动转为 LaTeX，减少手动修正
4. **知识管理**：Markdown 易于标注、归档、检索，团队协作更方便
5. **数据源集成**：Markdown 格式天然适合向量数据库存储和搜索

## 适用场景

- 文档分析和内容索引
- RAG（检索增强生成）流程中的文档预处理
- 批量处理多种格式文件喂给 LLM
- 知识库构建和数据清洗

## 注意事项

输出主要面向**文本分析工具**设计，如果追求高保真的人类阅读体验，可能需要配合其他工具使用。

---

**一句话总结**：微软出品，专门解决「文件太多喂不进 AI」的痛点，一键转成 LLM 爱读的 Markdown。
