# remis-fpk

`remis-fpk` 是一个只读解析器和安全解包工具，支持目前观察到的《Surviving Mars》FLPK v1 格式。它会在解包前检查归档结构与文件内容；不会执行 Lua，不会修改输入归档，也不会把文件重新打包成归档。

本独立软件包从 [Project Remis](https://github.com/Drlinglong/Remis) 提取，采用 AGPL-3.0-only 许可证。这是独立的社区工具，不是 Paradox Interactive 或 Haemimont Games 的官方软件，也未获其背书。软件不包含游戏文件或专有 Mod 归档。用户应自行确认有权检查和解包输入文件；解出的游戏资源仍受适用的游戏条款和权利人权利约束。

## 安装

```console
git clone https://github.com/Drlinglong/remis-fpk.git
cd remis-fpk
python -m pip install .
```

也可以直接从 Git 安装：

```console
python -m pip install "remis-fpk @ git+https://github.com/Drlinglong/remis-fpk.git"
```

本项目尚未发布到 PyPI。

## 用法

校验归档并输出 JSON 文件清单，其中包含归档 SHA-256：

```console
remis-fpk list ModContent.fpk
```

解包时必须提供 `list` 返回的 SHA-256，目标目录必须尚不存在。程序先写入新的暂存目录；只有所有文件都通过检查后，才会将暂存目录移动到目标位置：

```console
remis-fpk extract ModContent.fpk unpacked-mod --expected-sha256 <上一步输出的SHA256>
```

Python API 为 `remis_fpk.inspect_archive(path)` 和
`remis_fpk.extract_archive(path, destination, expected_sha256=...)`。

## 安全边界与格式支持

读取器支持原始文件条目，以及目前观察到的分块 Zstandard 容器，包括压缩块与精确长度字面块混合的文件。程序限制归档、索引、单文件、总解压量、条目数、嵌套深度和 Zstandard 窗口大小。它会拒绝不安全的 Windows 路径、重复路径、重叠数据、符号链接、损坏记录和不支持的 FLPK 变体。目标目录已存在时不会覆盖。工具不提供 FLPK 创建或重新打包功能。

格式和默认限额见 [FORMAT.md](FORMAT.md)。这些限额描述的是当前实现，不代表对其他游戏版本或归档变体提供支持。

## 开发

```console
python -m pip install -e ".[test]"
python -m pytest -q
python -m build
```

CI 会在 Windows 和 Ubuntu、Python 3.10 与 3.13 的组合中分别构建并安装 wheel 和源码分发包，然后针对已安装的软件包及命令行入口运行合成夹具测试。
