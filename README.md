# HtmlToTMP

一个用于 **将 HTML 文本转换为 Unity TextMeshPro 富文本** 的轻量工具。

适用于后端返回 HTML 内容、前端需要在 `TextMeshProUGUI` 中展示富文本样式的场景。

## 功能特性

- 将常见 HTML 标签转换为 TMP 富文本标签
- 支持嵌套标签解析
- 支持自动闭合未闭合标签
- 支持 HTML 实体解码
- 支持 `style` 中的部分样式解析
- 支持链接转换为 TMP `<link>` 标签

## 当前支持的标签

### 文本样式

- `<b>` / `<strong>` → `<b>`
- `<i>` / `<em>` → `<i>`
- `<u>` → `<u>`
- `<s>` / `<strike>` / `<del>` → `<s>`

### 结构标签

- `<br>` → 换行
- `<p>` → 段落换行
- `<div>` → 块级换行
- `<span>` → 解析 `style`

### TMP 兼容标签

- `<color=...>`
- `<size=...>`

### 其他标签

- `<font color="...">` → `<color=...>`
- `<a href="...">` → `<link="...">`，并附加默认下划线和蓝色样式

## 当前支持的 style 属性

在 `style="..."` 中目前支持：

- `color`
- `font-size`
- `background-color`（当前解析但未实际输出 TMP 背景标记）

### 颜色格式支持

- Hex：`#FF0000`
- RGB / RGBA：`rgb(255, 0, 0)`、`rgba(255, 0, 0, 1)`

### 字体大小支持

- `font-size: 24px` → `<size=24>`
- 其他值会原样保留

## 文件说明

- `HtmlToTmpConverter.cs`：核心转换器
- `TestHtmlConvert.cs`：Unity 示例脚本，用于测试转换结果

## 使用方法

### 1. 引入脚本

将以下脚本放入 Unity 项目：

- `HtmlToTmpConverter.cs`
- `TestHtmlConvert.cs`（可选）

### 2. 调用转换方法

```csharp
string html = "<p><span style=\"font-size: 36px; color: rgb(167, 237, 255);\">Hello TMP</span></p>";
string tmpRichText = HtmlToTmpConverter.Convert(html);
```

### 3. 赋值给 TextMeshProUGUI

```csharp
using TMPro;
using UnityEngine;

public class Example : MonoBehaviour
{
    public TextMeshProUGUI tmpText;

    void Start()
    {
        string html = "<p><strong style=\"color: #FFAA00; font-size: 32px;\">示例文本</strong></p>";
        tmpText.text = HtmlToTmpConverter.Convert(html);
    }
}
```

## 示例输入输出

### 输入

```html
<p><span style="font-size: 36px; color: rgb(167, 237, 255);">标题</span><br><strong>正文</strong></p>
```

### 输出

```text
<size=36><color=#A7EDFF>标题</color></size>
<b>正文</b>
```

## 实现说明

转换器采用逐字符扫描 + 栈结构的方式处理标签：

- 扫描原始 HTML 字符串
- 提取文本和标签内容
- 使用栈跟踪嵌套标签和闭合顺序
- 在遇到闭合标签时，按逆序补全对应 TMP 结束标签
- 最终自动闭合剩余未关闭标签

## 已知限制

- 不是完整 HTML 解析器，更适合富文本展示类内容
- 不支持完整 CSS，仅支持部分内联样式
- `background-color` 当前未输出 TMP 背景效果
- 不处理复杂 DOM 结构、脚本、样式表等内容
- 对异常 HTML 有一定容错，但不保证与浏览器渲染完全一致

## 适用场景

- 后端返回 HTML 富文本片段
- CMS / 配置表中存储的 HTML 文本展示
- 活动公告、描述文案、文章摘要等 UI 文本渲染
- Unity 中需要把简单 HTML 转成 TMP 标签的场景

## 后续可扩展方向

- 支持更多 HTML 标签
- 支持更多 CSS 样式解析
- 支持图片、列表、标题等结构转换
- 增加单元测试与更多输入样例
- 提供更严格的非法标签处理策略

## License

可按项目需求自由修改使用。
