# ARTEX-release

ARTEX 预编译二进制的云端构建与分发仓库(不含源码,源码在 [bear20252026/ARTEX](https://github.com/bear20252026/ARTEX))。

## 是什么

所有现存的 ARTEX 备份仓库(mhtsec/ARTEX 等)都只有源码、没有 Release;原作者 `Autumn-27/ARTEX` 的 Docker 镜像与 Release 均已失效。本仓库用 **GitHub Actions 云端编译**,从源码构建出开箱即用的单文件二进制并发布到 Releases,本机无需安装 Go/Node。

## 构建流程

流水线 `.github/workflows/build-release.yml`:

1. 检出 `bear20252026/ARTEX` 指定 ref(默认 `v0.3.12`)
2. 前端 `web/`:`npm ci` + `npm run build:static` 静态导出
3. 将 `web/out` 拷贝至 `server/webui/dist` 供 go:embed 内嵌
4. `CGO_ENABLED=0 go build -tags embedui` 交叉编译 5 个目标:
   `linux/amd64`、`linux/arm64`、`darwin/amd64`、`darwin/arm64`、`windows/amd64`
5. 打 zip(含 `start.sh`/`start.bat`/`config.example.json`)并生成 `SHA256SUMS.txt`
6. 对 linux/amd64 产物做 `-h` 冒烟测试后发布 Release

## 手动触发

Actions → build-release → Run workflow → 可改 `source_ref`(源码 tag)与 `version`(发布版本号)。

## 运行时依赖

- PostgreSQL(必须)
- LLM API Key:Anthropic 或任意 OpenAI 兼容端点(可在 Web UI「LLM 配置」页填写)
- 解压后运行 `start.bat`(Windows)/ `start.sh`(Linux/macOS),访问 `http://localhost:8787`,首次进 `/setup`

## 许可与限制

- 源码许可证 AGPL-3.0;本仓库仅为预编译分发,源码公开可用。
- 上游作者附加限制:仅供本地学习研究与隔离环境验证,**严禁对任何线上/第三方系统发起实际扫描、测试或攻击**。
