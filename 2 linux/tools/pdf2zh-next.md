## 一、基础用法

### 最简单的翻译（使用默认 Google 翻译）

```bash
pdf2zh_next input.pdf
```

这会在当前目录生成 `input-dual.pdf`（双语对照）和 `input-mono.pdf`（纯译文）。

### 指定输入文件位置

```bash
pdf2zh_next /path/to/your/document.pdf
```

---

## 二、使用 DeepSeek API（你最关心的）

### 方式一：直接指定 API Key

```bash
pdf2zh_next input.pdf --deepseek --deepseek-api-key "sk-你的密钥"
```

### 方式二：指定模型和 API Key

```bash
pdf2zh_next input.pdf --deepseek --deepseek-model "deepseek-chat" --deepseek-api-key "sk-你的密钥"
```

### 方式三：设置环境变量（推荐，避免泄露）

**Linux/Mac**:
```bash
export DEEPSEEK_API_KEY="sk-你的密钥"
pdf2zh_next input.pdf --deepseek
```

**Windows PowerShell**:
```powershell
$env:DEEPSEEK_API_KEY="sk-你的密钥"
pdf2zh_next input.pdf --deepseek
```

---

## 三、常用参数组合

### 1. 指定源语言和目标语言

```bash
pdf2zh_next input.pdf --lang-in en --lang-out zh-CN --deepseek
```

### 2. 只翻译特定页面

```bash
# 翻译第1-10页
pdf2zh_next input.pdf --pages 1-10 --deepseek

# 翻译第1,3,5页和第7-10页
pdf2zh_next input.pdf --pages 1,3,5,7-10 --deepseek
```

### 3. 指定输出目录

```bash
pdf2zh_next input.pdf --output ./translated --deepseek
```

### 4. 只输出双语版或纯译文版

```bash
# 只输出双语对照版
pdf2zh_next input.pdf --no-mono --deepseek

# 只输出纯译文版
pdf2zh_next input.pdf --no-dual --deepseek
```

### 5. 调整翻译质量相关参数

```bash
# 设置最小翻译文本长度（字符数）
pdf2zh_next input.pdf --min-text-length 20 --deepseek

# 使用自定义系统提示词
pdf2zh_next input.pdf --custom-system-prompt "请将以下学术论文内容翻译成专业流畅的中文" --deepseek
```

### 6. 使用术语表（提高专业术语翻译准确性）

```bash
pdf2zh_next input.pdf --glossaries ./my_terms.txt --deepseek
```

术语表格式（每行）：
```
PDF,便携式文档格式
API,应用程序接口
```

### 7. 控制并发请求（避免被限流）

```bash
# QPS（每秒请求数）限制为 2
pdf2zh_next input.pdf --qps 2 --pool-max-workers 2 --deepseek
```

---

## 四、其他翻译服务

### 使用 OpenAI

```bash
pdf2zh_next input.pdf --openai --openai-api-key "sk-xxx" --openai-model "gpt-4"
```

### 使用 Gemini

```bash
pdf2zh_next input.pdf --gemini --gemini-api-key "xxx" --gemini-model "gemini-pro"
```

### 使用 Ollama（本地模型）

```bash
pdf2zh_next input.pdf --ollama --ollama-model "qwen2.5:7b" --ollama-host "http://localhost:11434"
```

---

## 五、图形界面模式

### 启动 Web UI

```bash
pdf2zh_next --gui
```

启动后访问 `http://localhost:7860`，可在界面中上传文件、选择翻译服务、配置参数。

### 启用分享模式（生成公网链接）

```bash
pdf2zh_next --gui --share
```

### 指定端口

```bash
pdf2zh_next --gui --server-port 8080
```

---

## 六、高级功能

### 1. 启用调试模式（查看详细日志）

```bash
pdf2zh_next input.pdf --debug --deepseek
```

### 2. 忽略缓存（重新翻译所有内容）

```bash
pdf2zh_next input.pdf --ignore-cache --deepseek
```

### 3. OCR 处理（针对扫描版 PDF）

```bash
pdf2zh_next input.pdf --ocr-workaround --deepseek
```

自动检测并启用 OCR：

```bash
pdf2zh_next input.pdf --auto-enable-ocr-workaround --deepseek
```

### 4. 翻译表格内容（实验性功能）

```bash
pdf2zh_next input.pdf --translate-table-text --deepseek
```

### 5. 预热/下载必要资源

```bash
pdf2zh_next --warmup
```

### 6. 查看版本

```bash
pdf2zh_next --version
```

---

## 七、完整示例

假设你要翻译 `xv6(MIT).pdf` 这个文件，使用 DeepSeek API：

```bash
# 设置 API Key（一次性）
export DEEPSEEK_API_KEY="sk-你的真实密钥"

# 执行翻译（英文到中文，双语输出，并发数2，只翻译前20页）
pdf2zh_next ./xv6\(MIT\).pdf \
  --lang-in en \
  --lang-out zh \
  --pages 1-20 \
  --pool-max-workers 2 \
  --output ./result \
  --deepseek
```

执行后会生成：
- `./result/xv6(MIT)-dual.pdf` — 中英双语对照版
- `./result/xv6(MIT)-mono.pdf` — 纯中文版

---

## 八、获取所有帮助信息

```bash
pdf2zh_next --help
```

或查看特定服务的参数：

```bash
pdf2zh_next --help | grep -A 20 "DeepSeekSettings"
```

---

## 注意事项

1. **API 费用**：DeepSeek API 按 token 计费，翻译前注意账户余额
2. **文件路径**：路径中包含空格或括号等特殊字符时，需要用引号包裹
3. **并发控制**：免费版 API 有 QPS 限制，建议设置 `--qps 1` 或 `--qps 2`
4. **长文档**：可设置 `--max-pages-per-part` 分部分处理，避免内存溢出