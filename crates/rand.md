---
crate: rand
version: 0.10.x
updated: 2026-05-01
source: https://docs.rs/rand/latest/rand/
---

# rand — Random Number Generators

## 快速开始

```rust
use rand::Rng;

let mut rng = rand::rng();           // 创建 CSPRNG
let x: u32 = rng.random();           // 随机整数
let y: f64 = rng.random();           // 随机浮点数 [0, 1)
let z = rng.random_range(1..=100);   // 范围随机
```

## 核心 Trait: `Rng`

- `fn next_u32(&mut self) -> u32` — 生成下一个 u32
- `fn next_u64(&mut self) -> u64` — 生成下一个 u64
- `fn fill_bytes(&mut self, dest: &mut [u8])` — 填充字节切片

### 自动提供的方法

| 方法 | 说明 |
|------|------|
| `.random::<T>()` | 生成任意 Standard 分布的类型 |
| `.random_range(range)` | 生成指定范围的值 |
| `.sample(distr)` | 用指定分布采样 |
| `.sample_iter(distr)` | 返回迭代器，反复采样 |
| `.gen_iter()` | (deprecated, 用 sample_iter) |

## 核心函数

| 函数 | 说明 |
|------|------|
| `rand::rng()` | 创建线程安全的 CSPRNG（推荐） |
| `rand::random::<T>()` | 一步生成随机值 |

## RNG 实现

| Struct | 说明 |
|--------|------|
| `StdRng` | 确定性 CSPRNG（可种子化） |
| `OsRng` | 操作系统熵源 |
| `SysRng` | 系统级 RNG（比 OsRng 更轻量） |

## 模块

| 模块 | 说明 |
|------|------|
| `distr` | 概率分布（Uniform, Normal, Bernoulli 等） |
| `seq` | 序列采样（shuffle, choose, choose_multiple） |
| `rngs` | RNG 实现（StdRng, OsRng 等） |
| `prelude` | 常用 Trait 的预导入 |

## 示例

### 范围随机数
```rust
use rand::Rng;
let mut rng = rand::rng();
let dice = rng.random_range(1..=6);
```

### 从切片随机选
```rust
use rand::seq::IndexedRandom;
let choices = [1, 2, 3, 4];
let mut rng = rand::rng();
let pick = choices.choose(&mut rng);
```

### 打乱切片
```rust
use rand::seq::SliceRandom;
let mut items = vec![1, 2, 3, 4, 5];
let mut rng = rand::rng();
items.shuffle(&mut rng);
```

### 自定义分布
```rust
use rand::distr::{Distribution, Uniform};
let mut rng = rand::rng();
let die = Uniform::new(1, 7)?;
let roll = die.sample(&mut rng);
```

## 注意

- **rand 0.10 较大变化**: `thread_rng()` → `rng()`, `gen()` → `random()`, `gen_range()` → `random_range()`
- `choose()` / `shuffle()` 现在在 `rand::seq::IndexedRandom` / `SliceRandom` 下
- 确定性 RNG 用 `StdRng::seed_from_u64(42)`
- 加密安全用 `rng()` 或 `OsRng`
