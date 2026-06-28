# GKI Kernel Build Workflow

通用 GKI 内核编译工作流，基于 GitHub Actions 自动编译 Android GKI 内核，支持自定义补丁、工具链、内核名称后缀，并打包 AnyKernel3 刷机包。

## 目录结构

```
.
├── .github/workflows/
│   └── build-kernel.yml      # GitHub Actions 工作流
├── AnyKernel3/               # AnyKernel3 刷机包模板（WildKernels 版）
│   ├── anykernel.sh
│   ├── META-INF/
│   └── tools/
├── README.md                 # 本文件
└── .gitignore
```

## 快速开始

### 1. Fork 此仓库

点击右上角 **Fork** 将此仓库复制到你的 GitHub 账户下。

### 2. 触发构建

进入你的仓库 → **Actions** → **Build Kernel** → **Run workflow**，填写参数：

| 参数 | 说明 | 默认值 |
|---|---|---|
| `kernel_repo` | 内核源码仓库 (owner/repo) | `Star-ZER0/android_gki_kernel_5.10_common` |
| `kernel_branch` | 内核源码分支 | `android12-5.10-2026-04_r1` |
| `kernel_version` | 内核版本号（用于打包命名） | `5.10.252` |
| `clang_download_url` | Clang 工具链下载 URL | _(可选)_ |
| `clang_dir_name` | Clang 解压后目录名 | _(可选)_ |
| `patch_urls` | 补丁 URL 列表，每行一个 | Droidspaces KBI 补丁 |
| `kernel_suffix` | 内核名称后缀 | `custom` |
| `build_config` | GKI 构建配置文件 | `common/build.config.gki.aarch64` |
| `lto_mode` | LTO 模式 (thin/full/none) | `thin` |
| `ccache_enable` | 启用 ccache 加速 | `false` |
| `anykernel3_enable` | 打包 AnyKernel3 刷机包 | `true` |
| `create_release` | 创建 GitHub Release | `true` |

### 3. 获取产物

构建完成后会自动上传构建产物：
- **Kernel_Build_Artifacts**: 内核镜像 (Image)、.config、Module.symvers 等
- **Kernel_ZIP**: AnyKernel3 格式的刷机包，可直接在 Recovery 中刷写

如果开启了 Release，还会自动创建一个 GitHub Release 并上传 ZIP。

## 环境变量

工作流通过 `env:` 定义默认环境变量，可在工作流步骤中直接引用：

| 变量 | 默认值 |
|---|---|
| `KERNEL_REPO` | `Star-ZER0/android_gki_kernel_5.10_common` |
| `KERNEL_BRANCH` | `android12-5.10-2026-04_r1` |
| `KERNEL_VERSION` | `5.10.252` |

## AnyKernel3

本仓库使用 [WildKernels/AnyKernel3](https://github.com/WildKernels/AnyKernel3)（`gki-2.0` 分支）作为刷机包模板，相较于其他 fork 版本：

- ✅ **无 `$ramdisk` 变量名 Bug**
- ✅ **增加了 GKI 版本检查**，非 GKI 设备自动中止
- ✅ **结构清晰**：`split_boot` → `unpack_ramdisk` → `write_boot`

> ⚠️ 注意：某些 AnyKernel3 fork 版本存在 `$ramdisk` 大小写错误，会导致 Recovery 中执行 `chown -R 0:0 /` 破坏根文件系统权限，刷入后无法开机。本仓库已使用修复版本。

## 自定义补丁

在运行工作流时，在 `patch_urls` 参数中输入补丁 URL，每行一个。工作流会自动下载并按顺序应用：

```
https://example.com/patch1.patch
https://example.com/patch2.patch
```

留空则不应用任何补丁。

## 许可证

- 工作流脚本：MIT
- AnyKernel3：Apache 2.0 / osm0sis @ xda-developers
