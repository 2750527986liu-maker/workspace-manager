# 工作空间自动管理规则

每次Claude准备创建任何文件时，必须先执行以下检查：

## 1. 检查是否已有活跃工作空间

```bash
python workspace_manager.py info
```

- 如果有活跃工作空间 → 直接创建文件到对应目录
- 如果没有活跃工作空间 → 执行步骤2

## 2. 自动创建工作空间

从用户任务描述中推断项目名称，然后执行：

```bash
python workspace_manager.py create "推断的项目名称" "用户任务描述"
```

**名称推断规则：**
- 从用户请求中提取关键动词+名词
- 示例：
  - "写一个处理CSV的脚本" → `CSV处理脚本-20260526`
  - "创建React登录组件" → `React登录组件-20260526`
  - "分析销售数据" → `销售数据分析-20260526`

## 3. 创建文件到对应目录

```bash
python workspace_manager.py resolve "文件名"
```

返回：`.claude-work/{项目名}/{分类目录}/文件名`

**文件分类规则：**
- .py, .js, .ts, .jsx, .tsx, .java, .go, .rs, .c, .cpp → src/
- .json, .yaml, .yml, .toml, .ini, .env, .cfg → config/
- .md, .txt, .doc, .docx, .pdf → docs/
- .sh, .bat, .ps1 → scripts/
- .csv, .xlsx, .parquet, .sql, .db → data/
- .png, .jpg, .svg, .gif, .webp → images/
- .html, .htm → artifacts/
- *_test.*, *_spec.*, test_*.*, spec_*.* → tests/
- .tmp, .log, .cache, .bak → temp/

## 4. 提示用户文件位置

创建文件后，告知用户文件保存位置。

## 执行流程

```
用户请求创建文件
        ↓
检查是否有活跃工作空间
        ↓ 否
询问/推断项目名称
        ↓
创建工作空间文件夹
        ↓
创建文件到对应子目录
        ↓
提示用户文件位置
```

## 示例

### 场景1：首次创建文件

```
用户：帮我写一个Python脚本处理CSV数据

Claude行为：
1. 检测：没有活跃工作空间
2. 询问："为这个任务创建项目文件夹，请输入名称（回车使用自动推断）："
   用户回车
3. 创建：python workspace_manager.py create "CSV数据处理" "处理CSV数据脚本"
4. 创建文件：.claude-work/CSV数据处理-20260526/src/process_csv.py
5. 提示："文件已保存到：.claude-work/CSV数据处理-20260526/src/process_csv.py"
```

### 场景2：在现有工作空间中继续

```
用户：在刚才的项目中添加配置文件

Claude行为：
1. 检测：已有活跃工作空间 "CSV数据处理-20260526"
2. 直接创建：.claude-work/CSV数据处理-20260526/config/settings.json
3. 提示："配置文件已保存到：.claude-work/CSV数据处理-20260526/config/settings.json"
```

## 重要说明

- **这是强制规则**：每次创建文件前必须检查
- **自动推断**：尽量从用户任务中推断项目名称
- **询问确认**：如果没有明确名称，询问用户
- **透明提示**：创建后告知用户文件位置

## 脚本位置

工作空间管理脚本位于当前目录：`workspace_manager.py`

确保脚本在当前工作目录可用。脚本GitHub仓库：https://github.com/2750527986liu-maker/workspace-manager
