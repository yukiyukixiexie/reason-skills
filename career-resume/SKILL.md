# career-resume

简历诊断与优化 skill。输入简历文件（PDF/MD/TXT）和目标 JD（可选），输出完整的分析报告和修改建议。

## 触发方式

```
/career-resume
```

## 使用示例

```
/career-resume                          # 交互式引导
/career-resume resume.pdf               # 直接分析简历
/career-resume resume.pdf jd.txt        # 简历 + JD 匹配分析
```

---

## Skill 执行流程

### Step 0 — 收集输入

如果用户未提供文件，询问：
1. 简历文件路径（或直接粘贴文本）
2. 目标岗位 JD（可选，有则做关键词匹配）
3. 目标行业 / 公司类型（可选）

---

### Step 1 — 读取简历内容

- PDF：用 Read 工具读取
- MD/TXT：用 Read 工具读取
- 用户粘贴文本：直接使用

---

### Step 2 — 整体评分

输出评分卡，格式如下：

```
┌─────────────────────────────┐
│      简历整体评分            │
├──────────────┬──────────────┤
│ 总分          │  B+          │
├──────────────┼──────────────┤
│ 结构清晰度    │  ★★★★☆       │
│ 量化成果      │  ★★★☆☆       │
│ 关键词密度    │  ★★★★☆       │
│ 表达精准度    │  ★★★☆☆       │
│ 格式规范性    │  ★★★★★       │
└──────────────┴──────────────┘
```

评分标准：
- A：顶级候选人水平，直接投递
- B+：有竞争力，小幅优化后投递
- B：中等水平，需针对性修改
- C：需要较大重构

---

### Step 3 — 逐模块分析

按简历模块逐一分析，每个模块输出：

```
## [模块名] — 当前问题

**问题**：XXX
**Before**：原文
**After**：建议修改版
**改动理由**：XXX
```

模块顺序：
1. 个人信息 / 联系方式
2. 教育背景
3. 工作经历 / 实习经历（重点）
4. 项目经历
5. 技能 & 证书
6. 其他（自我评价、兴趣爱好等——通常建议删除）

---

### Step 4 — STAR 原则检查

针对每条工作/项目经历，检查是否符合 STAR 结构：
- **S**ituation：背景是否清晰
- **T**ask：职责是否明确
- **A**ction：行动是否具体
- **R**esult：结果是否量化

对缺少量化数据的条目，给出补充建议（如"如无具体数字，可用 '显著提升'、'缩短 XX%' 等相对描述"）。

---

### Step 5 — JD 关键词匹配（如有 JD）

```
## JD 关键词匹配分析

匹配度：72%

✅ 已覆盖关键词：Python, 数据分析, SQL, 项目管理
❌ 缺失关键词：Tableau, A/B测试, 用户增长
⚠️  弱覆盖关键词：机器学习（仅提及1次，建议强化）

建议：在技能栏补充 Tableau；在项目经历中加入 A/B 测试相关描述。
```

---

### Step 6 — 三版输出建议

| 版本 | 策略 | 适用场景 |
|------|------|----------|
| 保守版 | 最小改动，修正明显问题 | 急于投递，改动风险低 |
| 标准版 | 结构调整 + 语言优化 | 通用投递，平衡效果与工作量 |
| 激进版 | 全面重构，突出核心优势 | 有时间打磨，针对顶级公司 |

每版给出具体改动清单（不是重写全文，而是精确的 diff 式建议）。

---

### Step 7 — 投递策略建议

基于简历分析，给出：
- 推荐投递的公司层级（头部/中腰部/初创）
- 需要额外准备的材料（作品集、GitHub、portfolio）
- 简历提交格式建议（PDF、文件命名规范）

---

### Step 8 — 输出汇总

最后输出一个行动清单：

```
## 行动清单（优先级排序）

🔴 必须修改（影响通过率）
  □ 工作经历第2条缺少量化数据，补充具体数字
  □ 技能栏删除"熟悉Office"等无效词

🟡 建议优化（提升竞争力）
  □ 项目经历加入技术栈描述
  □ 个人简介改为岗位定向版本

🟢 可选调整（锦上添花）
  □ 统一时间格式（2023.06 vs 2023/06）
  □ 删除照片（外资公司通常不需要）
```

---

### Step 9 — 输出修改后的 PDF（可选）

用户确认修改方案后，自动生成修改后的 PDF。

**技术路径**（已验证可用，无需额外安装）：
```
原始 .docx / .pdf / 粘贴文字
    ↓ textutil 或 python-docx 提取内容
修改后内容（应用 Step 3-7 建议）
    ↓ 生成干净的 HTML 模板
    ↓ Chrome headless 渲染
简历_reviewed.pdf
```

#### 9.1 提取原始内容

```bash
# .docx 文件
textutil -convert txt -stdout "简历.docx"

# 或用 python-docx 逐段读取（保留结构）
python3 -c "
from docx import Document
doc = Document('简历.docx')
for para in doc.paragraphs:
    print(para.text)
"
```

#### 9.2 生成 HTML 模板

根据分析结果，将修改后的内容填入以下 HTML 模板，保持专业简历排版：

```html
<!DOCTYPE html>
<html lang="zh">
<head>
<meta charset="UTF-8">
<style>
  * { margin: 0; padding: 0; box-sizing: border-box; }
  body {
    font-family: "PingFang SC", "Hiragino Sans GB", "Microsoft YaHei", Arial, sans-serif;
    font-size: 10.5pt; color: #1a1a1a; background: #fff;
    padding: 28px 36px; line-height: 1.55;
  }
  .name { font-size: 22pt; font-weight: 700; letter-spacing: 3px; text-align: center; margin-bottom: 6px; }
  .contact { text-align: center; font-size: 9.5pt; color: #444; margin-bottom: 14px; }
  .contact span { margin: 0 10px; }
  .section-title {
    font-size: 11pt; font-weight: 700; letter-spacing: 1px;
    border-bottom: 1.5px solid #1a1a1a; padding-bottom: 2px; margin: 12px 0 7px 0;
  }
  .entry-header { display: flex; justify-content: space-between; align-items: baseline; margin-bottom: 2px; }
  .entry-left { font-weight: 700; }
  .entry-right { font-size: 9.5pt; color: #444; white-space: nowrap; margin-left: 10px; }
  ul { padding-left: 16px; margin-bottom: 4px; }
  li { margin-bottom: 2px; }
  .entry { margin-bottom: 9px; }
  .skills-label { font-weight: 600; }
</style>
</head>
<body>
  <!-- 按简历内容填充 -->
</body>
</html>
```

#### 9.3 Chrome headless 导出 PDF

```bash
"/Applications/Google Chrome.app/Contents/MacOS/Google Chrome" \
  --headless=new \
  --disable-gpu \
  --print-to-pdf="简历_reviewed.pdf" \
  --print-to-pdf-no-header \
  --no-margins \
  "file:///tmp/resume_reviewed.html"
```

#### 执行规则

1. **先确认再生成**：展示行动清单后，询问"是否生成修改后的 PDF？"，用户确认后执行
2. **输出路径**：默认保存在原文件同目录，文件名加 `_reviewed` 后缀
3. **中文简历用中文模板**，英文简历用英文模板（调整字体和间距）
4. **只改确认项**：未确认的修改不写入 HTML

#### 环境要求

- macOS + Google Chrome（已内置，无需安装）
- `python-docx`：`pip3 install python-docx`（读取 .docx）
- `textutil`：macOS 内置（读取 .docx 纯文字）

---

## 注意事项

- 分析时使用中文输出（除非简历是英文）
- 英文简历用英文分析
- 不凭空捏造数字，量化建议要基于实际内容
- 保持原作者的表达风格，不过度美化
