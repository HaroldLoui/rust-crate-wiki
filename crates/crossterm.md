---
crate: crossterm
version: 0.29.x
updated: 2026-05-01
source: https://docs.rs/crossterm/latest/crossterm/
---

# crossterm — 跨平台终端操作库

## 快速开始

```rust
use crossterm::event::{read, Event, KeyCode};
use crossterm::terminal::{enable_raw_mode, disable_raw_mode};
use crossterm::cursor;
use std::io::{stdout, Write};

// 启用 raw mode 以读取事件
enable_raw_mode()?;

// 读取键盘事件
if let Event::Key(key) = read()? {
    match key.code {
        KeyCode::Char('q') => println!("Quit!"),
        KeyCode::Up => println!("Up arrow"),
        _ => {}
    }
}

disable_raw_mode()?;

// Command API — 推荐用法
use crossterm::execute;
use crossterm::cursor::MoveTo;

execute!(
    stdout(),
    MoveTo(10, 5),           // 移动到第10列第5行
    cursor::Show,             // 显示光标
)?;
```

## 核心模块

| 模块 | 说明 |
|------|------|
| `cursor` | 光标控制（移动、显示/隐藏、样式） |
| `event` | 事件读取（键盘、鼠标、resize、粘贴） |
| `style` | 颜色和属性（前景色、背景色、下划线等） |
| `terminal` | 终端控制（raw mode、屏幕尺寸、滚动等） |
| `clipboard` | 剪贴板操作 |
| `tty` | 检测是否为 TTY |

## 顶层 Trait

| Trait | 说明 |
|-------|------|
| `Command` | 所有命令的底层 trait |
| `ExecutableCommand` | 在 Write 类型上直接执行命令 |
| `QueueableCommand` | 在 Write 类型上排队执行命令 |
| `SynchronizedUpdate` | 同步终端更新（防闪烁） |

## 顶层宏

| 宏 | 说明 |
|----|------|
| `execute!(writer, cmd1, cmd2, ...)` | 立即执行一个或多个命令 |
| `queue!(writer, cmd1, cmd2, ...)` | 排队命令，稍后统一 flush |

## cursor — 光标控制

### 结构体（命令）

| 结构体 | 说明 |
|--------|------|
| `Show` | 显示光标 |
| `Hide` | 隐藏光标 |
| `EnableBlinking` | 启用光标闪烁 |
| `DisableBlinking` | 禁用光标闪烁 |
| `SavePosition` | 保存光标位置 |
| `RestorePosition` | 恢复光标位置 |
| `MoveUp(n)` | 光标上移 n 行 |
| `MoveDown(n)` | 光标下移 n 行 |
| `MoveLeft(n)` | 光标左移 n 列 |
| `MoveRight(n)` | 光标右移 n 列 |
| `MoveTo(col, row)` | 移动到指定列和行（0-based） |
| `MoveToColumn(col)` | 移动到指定列 |
| `MoveToRow(row)` | 移动到指定行 |
| `MoveToNextLine(n)` | 下移 n 行并回到行首 |
| `MoveToPreviousLine(n)` | 上移 n 行并回到行首 |

### 枚举

| 枚举 | 说明 |
|------|------|
| `SetCursorStyle` | 设置光标外观 |

`SetCursorStyle` 变体：

| 变体 | 说明 |
|------|------|
| `DefaultUserShape` | 终端默认 |
| `BlinkingBlock` | 闪烁方块 |
| `SteadyBlock` | 实心方块 |
| `BlinkingUnderline` | 闪烁下划线 |
| `SteadyUnderline` | 实心下划线 |
| `BlinkingBar` | 闪烁竖线 |
| `SteadyBar` | 实心竖线 |

### 函数

| 函数 | 说明 |
|------|------|
| `position()` | 获取当前光标位置 `(col, row)` |

### 示例

```rust
use crossterm::cursor;
use crossterm::execute;
use std::io::stdout;

// 移动光标并设置形状
execute!(
    stdout(),
    cursor::MoveTo(0, 0),
    cursor::SetCursorStyle::BlinkingBlock,
    cursor::Show,
)?;

// 获取位置
let (col, row) = cursor::position()?;
```

## event — 事件读取

### 枚举

| 枚举 | 说明 |
|------|------|
| `Event` | 终端事件 |
| `KeyCode` | 按键代码 |
| `KeyEventKind` | 按键类型（Press/Release/Repeat） |
| `KeyModifiers` | 修饰键位标志 |
| `MouseButton` | 鼠标按键 |
| `MouseEventKind` | 鼠标事件类型 |
| `MediaKeyCode` | 媒体键代码 |
| `ModifierKeyCode` | 修饰键代码 |

### `Event` 变体

| 变体 | 说明 |
|------|------|
| `Key(KeyEvent)` | 键盘事件 |
| `Mouse(MouseEvent)` | 鼠标事件 |
| `Resize(u16, u16)` | 终端大小变化 (列, 行) |
| `Paste(String)` | 粘贴内容（需启用 bracketed paste） |
| `FocusGained` | 终端获得焦点 |
| `FocusLost` | 终端失去焦点 |

### `KeyCode` 变体

| 变体 | 说明 |
|------|------|
| `Char(char)` | 字符键 |
| `Enter` | 回车 |
| `Tab` | Tab |
| `Backspace` | 退格（macOS 为 Delete） |
| `Esc` | Escape |
| `Up` / `Down` / `Left` / `Right` | 方向键 |
| `Home` / `End` | Home / End |
| `PageUp` / `PageDown` | PageUp / PageDown |
| `Delete` | Delete（macOS 为 Forward Delete） |
| `Insert` | Insert |
| `F(n)` | 功能键 F1-F24 |
| `CapsLock` / `ScrollLock` / `NumLock` | 锁定键 |
| `PrintScreen` / `Pause` / `Menu` | 特殊键 |
| `Modifier(ModifierKeyCode)` | 修饰键本身 |
| `Media(MediaKeyCode)` | 媒体键 |
| `Null` | 未知键 |

### 结构体

| 结构体 | 说明 |
|--------|------|
| `EventStream` | 异步事件流（实现 `Stream`） |
| `KeyEvent` | 键盘事件（code + modifiers + kind + state） |
| `KeyEventState` | 键盘事件状态 |
| `KeyModifiers` | 修饰键位标志（shift/ctrl/alt/super/hyper/meta） |
| `KeyboardEnhancementFlags` | 键盘增强标志 |
| `MouseEvent` | 鼠标事件（kind + button + column + row + modifiers） |
| `PushKeyboardEnhancementFlags` | 命令：推送键盘增强标志 |
| `PopKeyboardEnhancementFlags` | 命令：弹出键盘增强标志 |
| `EnableMouseCapture` | 命令：启用鼠标捕获 |
| `DisableMouseCapture` | 命令：禁用鼠标捕获 |
| `EnableBracketedPaste` | 命令：启用 bracketed paste |
| `DisableBracketedPaste` | 命令：禁用 bracketed paste |
| `EnableFocusChange` | 命令：启用焦点变化事件 |
| `DisableFocusChange` | 命令：禁用焦点变化事件 |

### 函数

| 函数 | 说明 |
|------|------|
| `read()` | 阻塞读取一个事件 |
| `poll(timeout)` | 非阻塞检查是否有事件可用（返回 bool） |

### 示例

```rust
use crossterm::event::{self, Event, KeyCode, KeyModifiers};
use crossterm::terminal::{enable_raw_mode, disable_raw_mode};

enable_raw_mode()?;

// 带超时的非阻塞轮询
if event::poll(std::time::Duration::from_millis(500))? {
    let event = event::read()?;

    match event {
        Event::Key(key) => {
            match key.code {
                KeyCode::Char('c') if key.modifiers == KeyModifiers::CONTROL => {
                    println!("Ctrl+C pressed");
                }
                KeyCode::Up => println!("Up arrow"),
                _ => {}
            }
        }
        Event::Mouse(mouse) => {
            println!("Mouse at ({}, {})", mouse.column, mouse.row);
        }
        Event::Resize(w, h) => {
            println!("Terminal resized to {}x{}", w, h);
        }
        Event::Paste(text) => {
            println!("Pasted: {}", text);
        }
        Event::FocusGained => println!("Focus gained"),
        Event::FocusLost => println!("Focus lost"),
    }
}

disable_raw_mode()?;
```

## style — 颜色和样式

### 枚举

| 枚举 | 说明 |
|------|------|
| `Color` | 颜色值 |
| `Attribute` | 文本属性 |
| `Colored` | 颜色种类（Fg/Bg/Underline） |

### `Color` 变体

| 变体 | 说明 |
|------|------|
| `Reset` | 重置颜色 |
| `Black` / `DarkGrey` | 黑色 / 深灰 |
| `Red` / `DarkRed` | 红色 / 深红 |
| `Green` / `DarkGreen` | 绿色 / 深绿 |
| `Yellow` / `DarkYellow` | 黄色 / 深黄（棕色） |
| `Blue` / `DarkBlue` | 蓝色 / 深蓝 |
| `Magenta` / `DarkMagenta` | 品红 / 深品红 |
| `Cyan` / `DarkCyan` | 青色 / 深青 |
| `White` / `Grey` | 白色 / 灰色 |
| `Rgb { r, g, b }` | 24位真彩色 |
| `AnsiValue(val)` | 256色索引 |

### `Attribute` 常见变体

| 变体 | 说明 |
|------|------|
| `Reset` | 重置所有属性 |
| `Bold` | 加粗 |
| `Dim` | 暗色 |
| `Italic` | 斜体 |
| `Underlined` | 下划线 |
| `DoubleUnderlined` | 双下划线 |
| `CrossedOut` | 删除线 |
| `Reverse` | 反转前景/背景色 |
| `Hidden` | 隐藏文本 |
| `NoBold` / `NoItalic` / `NoUnderline` 等 | 取消特定属性 |

### 结构体

| 结构体 | 说明 |
|--------|------|
| `SetForegroundColor(color)` | 命令：设置前景色 |
| `SetBackgroundColor(color)` | 命令：设置背景色 |
| `SetUnderlineColor(color)` | 命令：设置下划线颜色 |
| `SetAttribute(attr)` | 命令：设置单个属性 |
| `SetAttributes(attrs)` | 命令：设置多个属性 |
| `ResetColor` | 命令：重置颜色 |
| `SetColors(colors)` | 命令：同时设置前景/背景色 |
| `SetStyle(style)` | 命令：设置完整样式 |
| `Print(content)` | 命令：打印可显示内容 |
| `PrintStyledContent(content)` | 命令：打印带样式的内容 |
| `StyledContent<T>` | 带样式的内容包装 |
| `ContentStyle` | 完整的样式描述（fg + bg + attrs + underline_color） |
| `Colors` | 前景色+背景色的元组 |
| `Attributes` | 属性位集合 |

### Trait

| Trait | 方法 | 说明 |
|-------|------|------|
| `Stylize` | `.with(fg)`, `.on(bg)`, `.bold()`, `.italic()`, `.underline()`, `.dimmed()`, `.crossed_out()`, `.reverse()`, `.style()` 等 | 在任意类型上链式设置样式 |

### 函数

| 函数 | 说明 |
|------|------|
| `available_color_count()` | 终端支持的颜色数 |
| `force_color_output(force)` | 强制/禁用颜色输出 |
| `style(content)` | 创建一个可链式设置样式的 StyledContent |

### 示例

```rust
use crossterm::style::{self, Color, Attribute, Stylize, PrintStyledContent};
use crossterm::execute;
use std::io::stdout;

// 方法一：Stylize trait
execute!(
    stdout(),
    PrintStyledContent("Hello".with(Color::Red).on(Color::Black).bold()),
)?;

// 方法二：命令式
use crossterm::style::{SetForegroundColor, SetBackgroundColor, SetAttribute, Print};

execute!(
    stdout(),
    SetForegroundColor(Color::Rgb { r: 255, g: 100, b: 0 }),
    SetBackgroundColor(Color::DarkBlue),
    SetAttribute(Attribute::Italic),
    Print("Styled text"),
    ResetColor,
)?;
```

## terminal — 终端控制

### 结构体（命令）

| 结构体 | 说明 |
|--------|------|
| `Clear(type)` | 清屏（见 ClearType） |
| `ScrollUp(n)` | 向上滚动 n 行 |
| `ScrollDown(n)` | 向下滚动 n 行 |
| `SetSize(w, h)` | 设置终端大小 |
| `SetTitle(title)` | 设置终端标题 |
| `EnableLineWrap` | 启用自动换行 |
| `DisableLineWrap` | 禁用自动换行 |
| `EnterAlternateScreen` | 进入备用屏幕 |
| `LeaveAlternateScreen` | 离开备用屏幕 |
| `BeginSynchronizedUpdate` | 开始同步更新（防闪烁） |
| `EndSynchronizedUpdate` | 结束同步更新 |
| `WindowSize` | 获取窗口大小 |

### 枚举

| 枚举 | 变体 | 说明 |
|------|------|------|
| `ClearType` | `All`, `FromCursorDown`, `FromCursorUp`, `CurrentLine`, `UntilNewLine` | 清屏方式 |

### 函数

| 函数 | 说明 |
|------|------|
| `enable_raw_mode()` | 启用 raw mode（逐键读取） |
| `disable_raw_mode()` | 禁用 raw mode |
| `is_raw_mode_enabled()` | 检查 raw mode 是否启用 |
| `size()` | 获取终端尺寸 `(columns, rows)` |
| `window_size()` | 获取窗口像素尺寸 |
| `supports_keyboard_enhancement()` | 检查是否支持键盘增强协议 |

### 示例

```rust
use crossterm::terminal::{self, ClearType, EnterAlternateScreen, LeaveAlternateScreen};
use crossterm::execute;
use std::io::stdout;

// 进入备用屏幕
execute!(stdout(), EnterAlternateScreen, terminal::Clear(ClearType::All))?;

// 获取终端尺寸
let (cols, rows) = terminal::size()?;
println!("Terminal: {}x{}", cols, rows);

// 离开备用屏幕
execute!(stdout(), LeaveAlternateScreen)?;
```

## clipboard — 剪贴板

| 类型 | 说明 |
|------|------|
| `CopyToClipboard(text)` | 命令：复制文本到剪贴板 |
| `ClipboardType` | 枚举：Clipboard / Primary / Selection |
| `ClipboardSelection` | 结构体：选择剪贴板 |

## tty — TTY 检测

| Trait | 方法 | 说明 |
|-------|------|------|
| `IsTty` | `is_tty()` | 检测是否为 TTY |

## Command API — 三种执行方式

crossterm 提供三种方式来执行终端命令：

### 1. `execute!` 宏（即时执行）

```rust
use crossterm::execute;
use crossterm::cursor::MoveTo;
use std::io::{stdout, Write};

execute!(stdout(), MoveTo(10, 5), cursor::Show)?;
```

### 2. `queue!` 宏（批量排队）

```rust
use crossterm::queue;
use crossterm::style::{SetForegroundColor, Color::Red};
use std::io::{stdout, Write};

queue!(
    stdout(),
    SetForegroundColor(Red),
    Print("Red text"),
    ResetColor,
)?;
stdout().flush()?; // 一次性写入
```

### 3. Trait 方法（`ExecutableCommand` / `QueueableCommand`）

```rust
use crossterm::ExecutableCommand;
use crossterm::cursor::MoveTo;
use std::io::stdout;

stdout()
    .execute(MoveTo(0, 0))?
    .execute(cursor::Show)?;
```

## 同步更新（Synchronized Update）

```rust
use crossterm::SynchronizedUpdate;
use crossterm::cursor::MoveTo;
use std::io::stdout;

stdout().synchronized_update(|w| {
    execute!(w, MoveTo(0, 0), Print("Hello"))?;
    // 所有更新一次性渲染，避免闪烁
    Ok(())
})?;
```

## 特性标志 (Feature Flags)

| 特性 | 说明 |
|------|------|
| `default` | 基本终端操作 |
| `events` | 事件支持 |
| `futures` | 异步 EventStream（需 futures crate） |
| `futures-core` | 仅 EventStream 核心流 trait |
| `serde` | 序列化支持 |
| `synchronized-update` | 同步更新支持 |
| `docs` | 文档生成相关 |

## 注意

- **0.28 → 0.29**: API 基本稳定，新增 FocusGained/FocusLost 事件、MediaKeyCode/ModifierKeyCode 枚举
- **raw mode** 是读取事件的先决条件（`enable_raw_mode()`）
- **Command API**（`execute!`/`queue!`/`ExecutableCommand`/`QueueableCommand`）是推荐用法
- 所有命令结构体都实现了 `Command` trait，可直接传给 `execute!` / `queue!`
- 异步事件读取使用 `EventStream`（需开启 `futures` feature）
