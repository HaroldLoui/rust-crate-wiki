---
crate: whoami
version: 2.1.x
updated: 2026-05-02
source: https://docs.rs/whoami/latest/whoami/
---

# whoami — 当前用户与系统环境信息

## 快速开始

```rust
// 用户信息
println!("username: {}", whoami::username().unwrap_or_default());
println!("realname: {}", whoami::realname().unwrap_or_default());
println!("account:  {}", whoami::account().unwrap_or_default());

// 设备信息
println!("hostname:    {}", whoami::hostname().unwrap_or_default());
println!("devicename:  {}", whoami::devicename().unwrap_or_default());
println!("distro:      {}", whoami::distro().unwrap_or_default());

// 系统信息
println!("platform: {}", whoami::platform());
println!("cpu_arch: {}", whoami::cpu_arch());
println!("desktop:  {}", whoami::desktop_env().map(|e| e.to_string()).unwrap_or_default());

// 语言偏好
println!("lang: {}", whoami::lang_prefs().unwrap_or_default());
```

## 函数

所有函数都是无参数的顶层自由函数。返回值大部分是 `Result<String, Error>`，
少数返回 enum 或 struct。

### 用户信息

| 函数 | 返回值 | 说明 |
|------|--------|------|
| `username()` | `Result<String>` | 获取用户名 |
| `username_os()` | `Result<OsString>` | 获取用户名（原生 OsString） |
| `realname()` | `Result<String>` | 获取用户真实（全）名 |
| `realname_os()` | `Result<OsString>` | 获取用户真实名（OsString） |
| `account()` | `Result<String>` | 获取账户名（可能含域/服务器） |
| `account_os()` | `Result<OsString>` | 获取账户名（OsString） |
| `lang_prefs()` | `Result<LanguagePreferences>` | 获取用户首选语言偏好 |

### 设备信息

| 函数 | 返回值 | 说明 |
|------|--------|------|
| `devicename()` | `Result<String>` | 获取设备名（"Pretty Name"） |
| `devicename_os()` | `Result<OsString>` | 获取设备名（OsString） |
| `hostname()` | `Result<String>` | 获取主机名 |
| `distro()` | `Result<String>` | 获取操作系统发行版名称及版本 |

### 系统平台信息

| 函数 | 返回值 | 说明 |
|------|--------|------|
| `platform()` | `Platform` | 获取平台类型 |
| `desktop_env()` | `Option<DesktopEnvironment>` | 获取桌面环境（如有） |
| `cpu_arch()` | `CpuArchitecture` | 获取 CPU 架构 |

## Struct

| Struct | 说明 |
|--------|------|
| `Error` | I/O 错误，可转换为 `std::io::Error` |
| `Language` | 口语语言标识符（支持 Display, FromStr, PartialEq） |
| `LanguagePreferences` | 用户语言偏好（包含 `Language` 列表） |

## Enum

### `Platform` — 底层平台

| Variant | 说明 |
|---------|------|
| `Android` | Android |
| `Bsd` | BSD 系列 |
| `Fuchsia` | Fuchsia |
| `Hurd` | GNU Hurd |
| `Illumos` | illumos |
| `Ios` | iOS |
| `Linux` | Linux |
| `Mac` | macOS |
| `Netbsd` | NetBSD |
| `Openbsd` | OpenBSD |
| `Redox` | Redox OS |
| `Solaris` | Solaris |
| `Wasi` | WebAssembly WASI |
| `Windows` | Windows |

### `DesktopEnvironment` — 桌面环境

| Variant | 说明 |
|---------|------|
| `Android` | Android |
| `Aqua` | Aqua (macOS) |
| `Cinnamon` | Cinnamon |
| `Console` | 纯终端（无图形环境） |
| `Cosmic` | COSMIC (Pop!_OS) |
| `Ermine` | Ermine |
| `Gnome` | GNOME |
| `Hyprland` | Hyprland |
| `I3` | i3 |
| `Ios` | iOS |
| `Lxde` | LXDE |
| `Mate` | MATE |
| `Niri` | Niri |
| `Openbox` | Openbox |
| `Orbital` | Orbital (Redox) |
| `Plasma` | KDE Plasma |
| `Ubuntu` | Ubuntu (Unity) |
| `Unknown` | 未知 |

### `CpuArchitecture` — CPU 架构（Non-exhaustive）

| Variant | 说明 |
|---------|------|
| `Arm64` | AArch64 |
| `ArmV5` | ARMv5 |
| `ArmV6` | ARMv6 |
| `ArmV7` | ARMv7 |
| `I386` | Intel 80386 |
| `I586` | Intel Pentium |
| `I686` | Intel Pentium Pro/II |
| `Mips` | MIPS (32-bit) |
| `Mips64` | MIPS64 |
| `Mips64El` | MIPS64 LE |
| `MipsEl` | MIPS LE |
| `PowerPc` | PowerPC (32-bit) |
| `PowerPc64` | PowerPC64 |
| `PowerPc64Le` | PowerPC64 LE |
| `Riscv32` | RISC-V 32-bit |
| `Riscv64` | RISC-V 64-bit |
| `S390x` | IBM z/Architecture |
| `Sparc` | SPARC (32-bit) |
| `Sparc64` | SPARC64 |
| `Unknown` | 未知 |
| `Wasm32` | WebAssembly 32-bit |
| `Wasm64` | WebAssembly 64-bit |
| `X64` | x86-64 / AMD64 |

### `Width` — CPU 地址宽度（Non-exhaustive）

| Variant | 说明 |
|---------|------|
| `Bits32` | 32-bit |
| `Bits64` | 64-bit |

## Type Aliases

| Alias | 说明 |
|-------|------|
| `Result<T>` | `Result<T, Error>` — 本 crate 的 Result 别名 |

## 示例

### 打印所有系统信息

```rust
println!("Username:  {}", whoami::username().unwrap_or_default());
println!("Real Name: {}", whoami::realname().unwrap_or_default());
println!("Hostname:  {}", whoami::hostname().unwrap_or_default());
println!("Platform:  {}", whoami::platform());
println!("CPU Arch:  {}", whoami::cpu_arch());
```

### 检查桌面环境

```rust
match whoami::desktop_env() {
    Some(whoami::DesktopEnvironment::Gnome) => println!("Running GNOME"),
    Some(whoami::DesktopEnvironment::Plasma) => println!("Running KDE Plasma"),
    Some(whoami::DesktopEnvironment::Console) => println!("No desktop (TTY only)"),
    None => println!("Could not detect desktop environment"),
    _ => println!("Other desktop environment"),
}
```

### 语言偏好

```rust
let prefs = whoami::lang_prefs().unwrap_or_default();
for lang in prefs.iter() {
    println!("Language: {}", lang);
}
```

## 注意

- **跨平台**: 支持 Linux, macOS, Windows, BSD, Redox, WASM, Android, iOS 等
- 大部分函数返回 `Result`，需处理 `unwrap()` 或 `?`
- `platform()`, `cpu_arch()`, `desktop_env()` 不返回 `Result`，总是有值
- `LangPreferences` 在非桌面环境可能为空
- `CpuArchitecture` 和 `Width` 是 Non-exhaustive enum，匹配时需加 `_ =>` 通配分支
- v2.x 对比 v1.x 主要变化：函数返回 `Result` 而非直接 `String`
