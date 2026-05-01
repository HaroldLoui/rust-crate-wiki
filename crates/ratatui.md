---
crate: ratatui
version: 0.30.x
updated: 2026-05-01
source: https://docs.rs/ratatui/latest/ratatui/
---

# ratatui — Terminal User Interfaces

## 快速开始

```rust
use ratatui::prelude::*;

// 快速初始化 (crossterm 后端)
let mut terminal = ratatui::init();

// 绘制 UI
terminal.draw(|frame| {
    let area = frame.area();
    let block = Block::bordered().title("Hello Ratatui!");
    frame.render_widget(block, area);
})?;

// 恢复终端
ratatui::restore();
```

## 初始化 / 恢复

| 函数 | 说明 |
|------|------|
| `ratatui::init()` | 初始化终端 (crossterm, raw mode, alternate screen, panic hook) → `DefaultTerminal` |
| `ratatui::init_with_options(options)` | 带选项初始化 → `DefaultTerminal` |
| `ratatui::try_init()` | 不安装 panic hook 的版本 → `Result<DefaultTerminal>` |
| `ratatui::try_init_with_options(options)` | 带选项 + 不安装 panic hook → `Result<DefaultTerminal>` |
| `ratatui::restore()` | 恢复终端 (离开 alternate screen, 关闭 raw mode) |
| `ratatui::try_restore()` | 恢复终端 (带 Result) → `Result<()>` |
| `ratatui::run(f)` | 初始化 → 运行闭包 → 自动恢复；`f: FnOnce(&mut DefaultTerminal) -> R` |

`DefaultTerminal` = `Terminal<CrosstermBackend<Stdout>>`

## 核心 Struct: `Terminal`

| 方法 | 说明 |
|------|------|
| `Terminal::new(backend)` | 创建终端 (全屏 viewport) |
| `Terminal::with_options(backend, options)` | 创建终端 (自定义 viewport) |
| `.draw(f)` | 绘制 UI：`f: FnOnce(&mut Frame<'_>)` → `Result<CompletedFrame>` |
| `.try_draw(f)` | 绘制 UI (闭包可返回错误) |
| `.get_frame()` | 获取当前 Frame 引用 |
| `.current_buffer_mut()` | 当前缓冲区的可变引用 |
| `.backend()` / `.backend_mut()` | 后端引用 |
| `.flush()` | 刷新屏幕 |
| `.resize(area)` | 手动调整大小 |
| `.autoresize()` | 自动调整大小 |
| `.clear()` | 清除屏幕 |
| `.hide_cursor()` / `.show_cursor()` | 光标控制 |
| `.set_cursor(x, y)` / `.set_cursor_position(pos)` | 定位光标 |
| `.get_cursor()` / `.get_cursor_position()` | 获取光标位置 |
| `.size()` | 获取终端尺寸 → `Result<Size>` |
| `.insert_before(height, draw_fn)` | 在顶部插入内容（如滚动日志） |

### `TerminalOptions`

| 字段 | 说明 |
|------|------|
| `viewport: Viewport` | 视口配置 |

### `Viewport`

| Variant | 说明 |
|---------|------|
| `Viewport::Fullscreen` | 全屏（默认） |
| `Viewport::Inline(height)` | 内联模式，指定高度 |
| `Viewport::Fixed(area)` | 固定区域 `Rect` |

### `CompletedFrame`

| 字段 | 说明 |
|------|------|
| `.buffer` | 渲染后的 `Buffer` |
| `.area` | 渲染区域 `Rect` |
| `.count` | 帧号 (自增计数器) |

## 核心 Struct: `Frame`

| 方法 | 说明 |
|------|------|
| `.area()` | 获取当前绘制区域 `Rect` |
| `.size()` | 与 area() 相同 |
| `.render_widget(widget, area)` | 渲染消耗型 Widget |
| `.render_stateful_widget(widget, area, state)` | 渲染有状态 Widget |
| `.set_cursor(x, y)` | 设置光标位置 |
| `.set_cursor_position(pos)` | 设置光标位置 (支持 Into<Position>) |
| `.buffer_mut()` | 缓冲区可变引用 |
| `.count()` | 当前帧号 |

## 布局

### `Layout`

```rust
use ratatui::layout::{Layout, Direction, Constraint, Flex};

// 垂直分割
let [top, bottom] = Layout::vertical([Constraint::Length(5), Constraint::Fill(1)])
    .areas(area);

// 水平分割
let [left, right] = Layout::horizontal([Constraint::Percentage(30), Constraint::Fill(1)])
    .margin(1)
    .areas(area);
```

| 方法 | 说明 |
|------|------|
| `Layout::new(direction, constraints)` | 创建布局 |
| `Layout::vertical(constraints)` | 垂直布局 (列) |
| `Layout::horizontal(constraints)` | 水平布局 (行) |
| `.direction(direction)` | 设置方向 |
| `.constraints(constraints)` | 设置约束 |
| `.margin(margin)` | 设置外边距 |
| `.horizontal_margin(h)` | 水平边距 |
| `.vertical_margin(v)` | 垂直边距 |
| `.flex(flex)` | 设置 Flex 模式 |
| `.spacing(spacing)` | 元素间距 |
| `.areas::<N>(area)` | 分割区域 → `[Rect; N]` (编译时大小) |
| `.try_areas::<N>(area)` | 分割区域 → `Result<[Rect; N]>` |
| `.spacers::<N>(area)` | 含间隔区域 |
| `.split(area)` | 分割 → `Rc<[Rect]>` (运行时大小) |
| `.split_with_spacers(area)` | 分割 (含间隔) |

### `Rect`

| 方法 | 说明 |
|------|------|
| `.area()` | 面积 `u32` |
| `.left()` / `.right()` / `.top()` / `.bottom()` | 各边坐标 |
| `.inner(margin)` | 减去 Margin |
| `.outer(margin)` | 加上 Margin |
| `.offset(offset)` | 偏移 |
| `.resize(size)` | 调整尺寸 |
| `.union(other)` | 并集 |
| `.intersection(other)` | 交集 |
| `.intersects(other)` | 是否相交 |
| `.contains(position)` | 是否包含点 |
| `.clamp(other)` | 裁剪 |
| `.rows()` / `.columns()` / `.positions()` | 迭代器 |
| `.as_position()` / `.as_size()` | 转换 |
| `.centered_horizontally(c)` | 水平居中 |
| `.centered_vertically(c)` | 垂直居中 |
| `.centered(h, v)` | 双向居中 |
| `.layout::<N>(layout)` | 直接对 Rect 布局 |
| `.layout_vec(layout)` | 布局 → Vec |

### `Constraint`

| Variant | 说明 |
|---------|------|
| `Constraint::Min(u16)` | 最小长度 |
| `Constraint::Max(u16)` | 最大长度 |
| `Constraint::Length(u16)` | 固定长度 |
| `Constraint::Percentage(u16)` | 百分比 |
| `Constraint::Ratio(u32, u32)` | 比例 (如 `Ratio(1, 3)`) |
| `Constraint::Fill(u16)` | 按比例填充剩余空间 |

### `Direction`

- `Direction::Vertical` — 垂直分割 (列)
- `Direction::Horizontal` — 水平分割 (行)

### `Flex`

- `Flex::Stretch` — 填满 (默认)
- `Flex::Center` — 居中
- `Flex::Start` — 左/上对齐
- `Flex::End` — 右/下对齐
- `Flex::SpaceAround`
- `Flex::SpaceBetween`

## 样式

### `Style`

```rust
use ratatui::style::{Style, Color, Modifier};

let style = Style::new()
    .fg(Color::Yellow)
    .bg(Color::Black)
    .add_modifier(Modifier::BOLD | Modifier::ITALIC);
```

| 方法 | 说明 |
|------|------|
| `Style::new()` / `Style::reset()` | 创建/重置 |
| `.fg(color)` | 前景色 |
| `.bg(color)` | 背景色 |
| `.underline_color(color)` | 下划线颜色 |
| `.add_modifier(m)` | 添加修饰符 |
| `.remove_modifier(m)` | 移除修饰符 |
| `.has_modifier(m)` | 检查修饰符 |
| `.patch(other)` | 合并 Style |

### `Stylize` Trait (方法链)

所有 Widget 和文本类型都实现了 `Stylize`，可直接链式调用：

```rust
"Hello".red().on_blue().bold().italic()
```

| 方法 | 说明 |
|------|------|
| `.fg(color)` / `.bg(color)` | 前/背景色 |
| `.reset()` | 重置样式 |
| `.add_modifier(m)` / `.remove_modifier(m)` | 修饰符 |
| `.red()` / `.green()` / `.blue()` / `.yellow()` / ... | 前景色快捷 |
| `.on_red()` / `.on_green()` / `.on_blue()` / ... | 背景色快捷 |
| `.bold()` / `.italic()` / `.underlined()` / `.dim()` / ... | 修饰符快捷 |
| `.not_bold()` / `.not_italic()` / ... | 移除修饰符 |

### `Color`

```rust
Color::Reset
Color::Black | Color::Red | Color::Green | Color::Yellow
Color::Blue | Color::Magenta | Color::Cyan | Color::Gray
Color::DarkGray | Color::LightRed | ... | Color::White
Color::Rgb(r, g, b)          // 24-bit 颜色
Color::Indexed(n)             // 256 色
Color::from_u32(0xRRGGBB)
Color::from_hsl(hsl)
Color::from_hsluv(hsluv)
```

### `Modifier`

- `BOLD`, `DIM`, `ITALIC`, `UNDERLINED`, `SLOW_BLINK`, `RAPID_BLINK`, `REVERSED`, `HIDDEN`, `CROSSED_OUT`

## 文本

### `Text`

```rust
Text::raw("hello")
Text::styled("hello", Style::new().red())
    .style(Style::new().green())
    .alignment(HorizontalAlignment::Center)
```

| 方法 | 说明 |
|------|------|
| `Text::raw(content)` | 纯文本 |
| `Text::styled(content, style)` | 带样式的文本 |
| `.width()` / `.height()` | 尺寸 |
| `.style(style)` | 设置样式 |
| `.patch_style(style)` | 合并样式 |
| `.reset_style()` | 重置样式 |
| `.alignment(align)` | 对齐方式 |

### `Line`

```rust
Line::raw("hello")                          // 纯文本行
Line::styled("hello", Style::new().blue())  // 带样式行
Line::from("hello")                         // 从 &str 转换
    .spans(vec![Span::raw("a"), Span::styled("b", style)])
    .style(Style::new().red())
    .alignment(HorizontalAlignment::Right)
    .left_aligned() | .centered() | .right_aligned()
```

| 方法 | 说明 |
|------|------|
| `Line::raw(content)` | 纯文本行 |
| `Line::styled(content, style)` | 带样式行 |
| `.spans(spans)` | 设置 Spans |
| `.style(style)` | 行样式 |
| `.alignment(align)` | 对齐 |
| `.width()` | 显示宽度 |
| `.styled_graphemes(style)` | 样式化字素迭代器 |

### `Span`

```rust
Span::raw("hello")
Span::styled("world", Style::new().green().bold())
    .content("new content")
    .style(Style::new().red())
    .patch_style(other_style)
    .reset_style()
```

| 方法 | 说明 |
|------|------|
| `Span::raw(content)` | 纯文本段 |
| `Span::styled(content, style)` | 带样式段 |
| `.content(content)` | 设置内容 |
| `.style(style)` | 设置样式 |
| `.patch_style(style)` | 合并样式 |
| `.reset_style()` | 重置样式 |
| `.width()` | 显示宽度 |

## Widgets

### Widget 系统

```rust
// Widget trait — 消耗型
pub trait Widget {
    fn render(self, area: Rect, buf: &mut Buffer);
}

// StatefulWidget trait — 有状态
pub trait StatefulWidget {
    type State;
    fn render(self, area: Rect, buf: &mut Buffer, state: &mut Self::State);
}
```

渲染方式:
- `frame.render_widget(widget, area)` — 消耗型 Widget
- `frame.render_stateful_widget(widget, area, &mut state)` — 有状态 Widget

### `Block`

```rust
Block::bordered()
    .title("Title")
    .title_top("Top")
    .title_bottom("Bottom")
    .title_style(Style::new().bold())
    .title_alignment(HorizontalAlignment::Center)
    .border_style(Style::new().cyan())
    .border_type(BorderType::Rounded)
    .borders(Borders::ALL)
    .style(Style::new().on_white())
    .padding(Padding::uniform(1));
```

| 方法 | 说明 |
|------|------|
| `Block::bordered()` | 带边框 Block |
| `.title(title)` / `.title_top()` / `.title_bottom()` | 设置标题 |
| `.title_style(style)` | 标题样式 |
| `.title_alignment(align)` | 标题对齐 |
| `.border_style(style)` | 边框样式 |
| `.border_type(type)` | 边框类型 |
| `.borders(flag)` | 显示哪些边 |
| `.border_set(set)` | 自定义边框字符 |
| `.style(style)` | 背景样式 |
| `.padding(padding)` | 内边距 |
| `.inner(area)` | 获取内容区域 |

### `Paragraph`

```rust
Paragraph::new("hello\nworld")
    .block(Block::bordered().title("Paragraph"))
    .style(Style::new().white())
    .wrap(Wrap { trim: false })
    .scroll((0, 0))
    .alignment(HorizontalAlignment::Center)
    .left_aligned() | .centered() | .right_aligned();
```

| 方法 | 说明 |
|------|------|
| `.block(block)` | 包装块 |
| `.style(style)` | 文本样式 |
| `.wrap(wrap)` | 自动换行 |
| `.scroll(offset)` | 滚动偏移 |
| `.alignment(align)` | 对齐 |
| `.line_count(width)` | 行数 |
| `.line_width()` | 最大行宽 |

### `List`

```rust
let items = vec!["Item 1", "Item 2", "Item 3"];
List::new(items)
    .block(Block::bordered().title("List"))
    .highlight_symbol(">> ")
    .highlight_style(Style::new().bold().fg(Color::Yellow))
    .repeat_highlight_symbol(true)
    .highlight_spacing(HighlightSpacing::Always)
    .direction(ListDirection::TopToBottom)
    .scroll_padding(1);
```

### `Table`

```rust
Table::new(rows, [Constraint::Length(10), Constraint::Fill(1)])
    .header(Row::new(["Col1", "Col2"]))
    .footer(Row::new(["Total", "100"]))
    .block(Block::bordered().title("Table"))
    .column_spacing(1)
    .widths([Constraint::Percentage(30), Constraint::Fill(1)])
    .highlight_style(Style::new().bg(Color::DarkGray))
    .highlight_symbol("> ")
    .flex(Flex::Start);
```

### `Gauge`

```rust
Gauge::default()
    .block(Block::bordered().title("Progress"))
    .percent(75)            // 百分比 (0-100)
    .ratio(0.75)            // 比例 (0.0-1.0)
    .label(Span::raw("75%"))
    .style(Style::new().fg(Color::Cyan))
    .gauge_style(Style::new().fg(Color::Green))
    .use_unicode(true);     // Unicode 填充字符
```

### `BarChart`

- `BarChart::default().data(&bars).block(block).bar_width(3).bar_gap(1)`
- `Bar::default().value(v).label("x".into()).style(style).text_value("...")`
- `BarGroup::default().label("Group".into()).bars(&[...])`

### `Chart`

- `Chart::new(datasets).block(block).x_axis(axis).y_axis(axis)`
- `Dataset::default().name("series").data(&data).marker(Marker::Dot).style(style)`
- `Axis::default().title("X").bounds([0.0, 100.0]).labels(vec!["0".into(), "50".into(), "100".into()])`

### `Tabs`

```rust
Tabs::new(["Tab1", "Tab2", "Tab3"])
    .block(Block::bordered().title("Tabs"))
    .select(0)
    .style(Style::new().fg(Color::White))
    .highlight_style(Style::new().bold().fg(Color::Yellow));
```

### `Sparkline`

- `Sparkline::default().data(&[1, 2, 3, 4, 5]).block(block).style(style)`

### `Scrollbar`

- `Scrollbar::default().orientation(ScrollbarOrientation::VerticalRight).begin_symbol(None).end_symbol(None).track_symbol(Some("|"))`
- 需要 `ScrollbarState::new(max).position(pos)`

### `Clear`

- `Clear` — 清除指定区域的背景

### `Padding`

- `Padding::uniform(n)` / `Padding::horizontal(h)` / `Padding::vertical(v)` / `Padding::new(l, r, t, b)`

## 模块

| 模块 | 说明 |
|------|------|
| `layout` | 布局系统 (Layout, Rect, Constraint, Direction, Flex) |
| `widgets` | 所有内置 Widget (Block, Paragraph, List, Table, Gauge, Chart, Tabs 等) |
| `style` | 样式系统 (Style, Color, Modifier, Stylize trait) |
| `text` | 文本类型 (Text, Line, Span) |
| `buffer` | 屏幕缓冲区 (Buffer, Cell) |
| `backend` | 后端抽象 (Backend trait, CrosstermBackend, TestBackend) |
| `symbols` | 符号常量 (部分框线、标记等) |
| `prelude` | 常用类型重导出 |
| `init` | 初始化和生命周期钩子 |

## 示例

### 完整应用

```rust
use ratatui::prelude::*;
use ratatui::widgets::*;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    let mut terminal = ratatui::init();
    loop {
        terminal.draw(|frame| {
            let area = frame.area();
            let block = Block::bordered().title("Hello");
            let paragraph = Paragraph::new("Press 'q' to quit")
                .block(block)
                .alignment(HorizontalAlignment::Center);
            frame.render_widget(paragraph, area);
        })?;
        // 事件处理 (需要额外 crate, 如 crossterm::event)
        if crossterm::event::poll(std::time::Duration::from_millis(200))? {
            if let crossterm::event::Event::Key(key) = crossterm::event::read()? {
                if key.code == crossterm::event::KeyCode::Char('q') {
                    break;
                }
            }
        }
    }
    ratatui::restore();
    Ok(())
}
```

### 使用 `ratatui::run` (自动管理生命周期)

```rust
use ratatui::prelude::*;
use ratatui::widgets::*;

fn main() -> Result<(), Box<dyn std::error::Error>> {
    ratatui::run(|terminal: &mut DefaultTerminal| {
        terminal.draw(|frame| {
            frame.render_widget(Block::bordered().title("Quick"), frame.area());
        })
    })?;
    Ok(())
}
```

### 有状态 Widget (List)

```rust
let mut state = ListState::default();
state.select(Some(0));

terminal.draw(|frame| {
    let list = List::new(vec!["A", "B", "C"])
        .highlight_style(Style::new().fg(Color::Yellow));
    frame.render_stateful_widget(list, area, &mut state);
})?;
```

### 自定义 Widget

```rust
struct MyWidget;

impl Widget for MyWidget {
    fn render(self, area: Rect, buf: &mut Buffer) {
        for x in area.left()..area.right() {
            for y in area.top()..area.bottom() {
                buf.get_mut(x, y).set_char('*');
            }
        }
    }
}
```

### 手动创建 Terminal (非 crossterm)

```rust
use ratatui::{Terminal, backend::TestBackend};
let backend = TestBackend::new(80, 24);
let mut terminal = Terminal::new(backend)?;
```

## 注意

- **v0.30**: 引入新的 Widget 系统 (`WidgetRef`, `StatefulWidgetRef`)，但 `Widget` trait 仍然是主要接口
- **v0.29 → v0.30**: `Terminal::with_options` 改为接收 `TerminalOptions` struct 而非多个参数；新增 `ratatui::run()` 简化应用生命周期
- 推荐用法: 简单的应用用 `ratatui::init()` / `ratatui::run()`；需要手动控制用 `Terminal::new(backend)`
- 可用的后端: `crossterm` (默认), `termion`, `termwiz`, `TestBackend` (测试用)
- 所有文本实现 `Stylize` trait: `"hello".red().on_blue().bold()`
- 布局推荐使用 `Layout::areas::<N>()` 编译时数组而非 `Layout::split()` 运行时 `Rc<[Rect]>`
