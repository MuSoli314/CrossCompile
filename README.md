# Rust 交叉编译：在 macOS 上编译 Linux 可执行文件

在开发过程中，开发者可能需要在 macOS 上为 Linux 平台编译可执行文件，这就涉及到交叉编译。本文介绍了如何在 macOS 上使用 Rust 进行交叉编译，特别是针对 `x86_64-unknown-linux-musl` 目标。

## MAC安装必要的工具

### 1. 添加目标平台
```bash
rustup target add x86_64-unknown-linux-musl
```
这条命令用于添加 `x86_64-unknown-linux-musl` 目标平台。

### 2. 安装 `musl-cross`
```bash
brew install filosottile/musl-cross/musl-cross
```
`musl-cross` 是一个用于交叉编译的工具链，支持将代码编译为基于 MUSL 的静态链接可执行文件。

## 配置 Cargo

在用户主目录下创建或编辑 `.cargo/config` 文件，添加以下内容：
```toml
[target.x86_64-unknown-linux-musl]
linker = "x86_64-linux-musl-gcc"
```
这段配置指定了在编译 `x86_64-unknown-linux-musl` 目标时使用的链接器。

## 编译项目

创建一个新的 Rust 项目并进行编译：
```bash
cargo new cross-compiling
cd cross-compiling
cargo build --release --target x86_64-unknown-linux-musl
```
上述步骤将生成适用于 Linux 的可执行文件。

## 处理 OpenSSL 依赖

在使用 OpenSSL 时，可能会遇到以下错误：
```
error: failed to run custom build command for `openssl-sys v0.9.76`
```
这是由于缺少必要的库和工具。可以通过以下步骤解决：

### 1. 安装 OpenSSL、pkg-config 和 Perl
```bash
brew install openssl pkg-config perl
```

### 2. 在 `Cargo.toml` 中添加依赖
```toml
[dependencies]
openssl-sys = "0.9"
openssl = { version = "0.10.33", features = ["vendored"] }
```
添加 `vendored` 特性可以使 OpenSSL 在编译时包含其自身的源代码，从而避免系统库的问题。

完成上述步骤后，再次运行编译命令即可生成适用于 Linux 的可执行文件。
