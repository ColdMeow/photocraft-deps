# PhotoCraft 离线依赖输入

本仓库只分发可公开再分发的第三方依赖、Rust wasm 标准库、nextest、Clippy 与测试语料；不含 PhotoCraft 私有 fork 源码、内部 Git bundle、凭据或业务素材。

## 对应源码与准备方式

采用外网准备指南的 **方式 B**：联合 product `5e7e9223d35c6104fc58103666edfe0ac07b8bad` 和官方 main `c6af3dcb98b17ceeb38dbf9fd200e6a684a088f1`（0.20.1）生成 vendor。内网合并提交 `53f9ec58cb18d729ae43d2eff3ddab8e62c969be` / 配套 bundle 未提供，未在本轮检出或验证；不要将两份源码通过自检等同于内网合并结果通过。

`MANIFEST.txt` 记录最终版本、摘要、验证结果。不会更改 `qx_photocraft` 的 product 或业务分支。

### 2026-11-06 补充

在原有依赖上追加官方 `2c68cc6b9590f7100417a8f42bca9b9d67c40406`（11 月 4 日交接基线）及 `8f7d916ba5ce6d94f2334d47bb23b84ff6cbaff2`（本次查询 main 的固定快照）的依赖，保留旧包。后者与前一会话快照 `a259993118ac7ce1d4c828a385abbc3191841e86` 的 Cargo.lock 字节一致。

- 交接缺失的 4 包：`lz4_flex 0.11.6`、`ntapi 0.4.3`、`objc2-io-kit 0.3.2`、`sysinfo 0.40.1`。
- 最新基线额外 13 包：`aes 0.8.4`、`block-padding 0.3.3`、`bytecount 0.6.9`、`cbc 0.1.2`、`cipher 0.4.4`、`ecb 0.1.2`、`inout 0.1.4`、`lopdf 0.38.0`、`md-5 0.10.6`、`nom_locate 5.0.0`、`rangemap 1.8.0`、`stringprep 0.1.5`、`unicode-normalization 0.1.26`。
- Git 依赖 revision、corpus pins 没有变化。共 924 个 vendor 包；本次不会更新应用分支或发布应用。
- 新增官方 Rust 1.99.1 Windows x64 Clippy，来源见 `tools/PROVENANCE.txt`。

## 内容

- `vendor/`：版本化目录，保留上游许可证与 `.cargo-checksum.json`。
- `vendor-config.toml`：cargo vendor 原始输出，包含准备机绝对路径；内网必须改 directory。
- `preparation/patch-inputs/`：仅含公开依赖的补充清单，用于收集工作区声明但主依赖图未使用的 egui patch 包。没有私有应用代码。
- `rustlib/wasm32-unknown-unknown/`：Rust 1.99.1 标准库；必须搭配精确相同工具链。
- `tools/cargo-nextest.exe`：官方预构建 Windows x64 nextest，来源和校验见 `tools/PROVENANCE.txt`。
- `tools/clippy/`：Rust 1.99.1 官方 Windows x64 组件，保留其许可证与通知。
- `licenses/`：Rust/nextest 许可与通知；各 vendor/corpus 自带许可继续适用，本仓库不把所有内容重新授权成单一许可证。
- `corpus/`、`corpus-manifests/`：已许可的公开样本、固定 manifest 和明确遗漏说明。
- `SHA256SUMS.txt`：交付文件逐项摘要（不含自身及 Git 元数据）。

## 必须注意：corpus 不完整

**51 个 `ag-psd` 图像文件没有公开分发。** 它们的 LICENSE 明确排除图像/画笔文件，不能仅凭项目标记 MIT 推断图片可再分发。缺失路径与预期摘要见 `corpus-manifests/PUBLIC-OMISSIONS.txt`。

其它已交付样本按固定 manifest 校验。完整 `cargo xtask test-corpus` 仍需内网维护者在具备合法使用/转移权限的前提下另行准备这51个文件；不要删除 manifest 条目或降低语料门槛来伪装完整。语料缺失不影响普通编译的 vendor 完整性判断。

## 内网使用

1. 经公司镜像克隆本仓库，保持所有字节原样：`git config core.autocrlf false`。根 `.gitattributes` 为 `* -text`；不使用 Git LFS。
2. 在本仓库外复制 `vendor-config.toml`，只改末尾 `[source.vendored-sources] directory` 为内网 vendor 的绝对路径，例如 `E:/SVN/GitLab/photocraft-deps/vendor`。保留所有 git revision replacement。
3. 在 PhotoCraft 源码根目录执行：

```powershell
cargo check --frozen --offline --config E:/local-config/photocraft-vendor.toml -p photocraft -p photocraft-cli --features heif
cargo check --frozen --offline --config E:/local-config/photocraft-vendor.toml --workspace --all-targets
```

模板路径需按实际修改。内网合并 Cargo.lock 如需更新，先保留原件并记录差异；本仓库不提供或猜测未取得的内网锁文件。

4. 检查 `rustc --version` 精确为 `1.99.1 (58cf8f9c3 2026-10-13)`。运行 `rustc --print sysroot`，将标准库目录复制到该 sysroot 的 `lib/rustlib/wasm32-unknown-unknown/`。不要覆盖不同 Rust 版本的标准库；不要用本仓库的组件 manifest 覆盖 rustup 全局安装状态。
5. 将 `tools` 加入当前终端 PATH，然后 `cargo nextest --version`。需要 VS C++/Windows SDK，本文不提供这些商业工具的安装包。
6. 将 corpus 带入源码的 `corpus/`，保留布局/许可；准备缺失样本后执行语料校验。`craft-fonts` 按公司镜像另行获取，不包含在本仓库。
7. `cargo xtask wasm` 会启动子 Cargo。仅给外层 Cargo 传 `--config` 不保证子命令继承；在不提交的位置设置 Cargo source replacement（如专用 CARGO_HOME/config.toml），并设置当前终端 `CARGO_NET_OFFLINE=true`，再执行 xtask。

### 离线使用 Clippy（Windows x64）

仅适用于 `rustc 1.99.1 (58cf8f9c3 2026-10-13)`；不要将此组件混用于其它版本。打开 VS x64 Native Tools 终端，按实际路径设置：

```powershell
$deps = 'E:/SVN/GitLab/photocraft-deps'
$sysroot = (rustc --print sysroot).Trim()
$env:PATH = "$sysroot/bin;$deps/tools/clippy/bin;$env:PATH"
$env:RUSTC = "$sysroot/bin/rustc.exe"
$env:CLIPPY_DRIVER_PATH = "$deps/tools/clippy/bin/clippy-driver.exe"
$env:CARGO_NET_OFFLINE = 'true'
& "$deps/tools/clippy/bin/cargo-clippy.exe" clippy --frozen --offline --config E:/local-config/photocraft-vendor.toml --workspace --all-targets -j 2 -- -D warnings
```

显式执行组件，避免 PATH 中的 rustup shim 尝试联网安装。组件依赖该 sysroot/bin 内的编译器 DLL；不必覆盖 rustup 安装状态。交付验证只证明组件可运行并检测 lint，不代表应用全量 Clippy 已通过。内存有限时使用 `-j 2`，本次默认并发检查曾发生编译器内存分配失败。

## 生成说明

原始双源码 vendor 在空 CARGO_HOME 离线检查时发现 `egui_demo_lib` 等三个未使用 patch 不在输出目录。为避免依赖准备机缓存，最终生成同时 sync `preparation/patch-inputs/Cargo.toml`：

```text
cargo vendor --locked --versioned-dirs --sync <official>/Cargo.toml --sync <patch-inputs>/Cargo.toml <photocraft-deps>/vendor
```

执行位置为固定 product 源码目录。补充图的 Cargo.lock 单独保留，不改两份应用 Cargo.lock。来源、检查结果以 MANIFEST 为准。

更新依赖时重新取得准确源码/bundle，再生成、校验、离线检查、审查许可、提交。不能只运行 cargo vendor 成功就声称内网可编译。
