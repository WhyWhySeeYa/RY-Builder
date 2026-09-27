# RY-Builder

**公开的 macOS 构建 runner。** 在 GitHub 托管的 `macos-14` 上构建私有项目
`WhyWhySeeYa/xxqg`（Theos + SwiftUI），并把打好的包发布回**私有仓库**的 Release。

公开仓库的 Actions 免计费 —— 这就是把构建放在这里的原因（私有仓库的 macOS
runner 按 10 倍分钟计费，2000 分钟/月的免费额度只够约 200 分钟）。

## 一次性设置

在 **本仓库** `Settings → Secrets and variables → Actions` 新建：

| Secret | 说明 |
|---|---|
| `XXQG_PAT` | fine-grained PAT，**仅**授权 `WhyWhySeeYa/xxqg`，权限 `Contents: Read and write` |

## 怎么用

`Actions → Build xxqg (SwiftUI) → Run workflow`，填：

- `source_ref` —— 私有仓库的分支名或 commit SHA（默认 `feat/new-ui`）
- `ry_use_swiftui_menu` —— `1` 用新的 SwiftUI 菜单，`0` 回退到旧 UIKit 菜单
- `publish_release` —— 是否把产物发布到私有仓库 Release

构建完成后，`.deb` 会出现在 [私有仓库 Releases](https://github.com/WhyWhySeeYa/xxqg/releases)。

## 源码为什么不会外泄

1. 源码由 runner 用 `XXQG_PAT` 经 GitHub API 拉 tarball 到临时目录，**从不进入本仓库的 git 历史**
2. 产物用 `gh release create --repo 私有仓库` 直接发布，**不产出公开可下载的 Artifact**
3. 只允许 `workflow_dispatch` 触发 —— 需要本仓库写权限才能运行，外人无法触发

## 注意

- 本仓库的 Actions **日志是公开的**：Theos 编译输出会显示文件路径与符号名。
  若这也不可接受，请改用私有仓库直接跑 `macos-14`。
- 工作流 pin 了 theos 提交 `88506b2c` 与 iPhoneOS SDK `16.5`，与上游保持一致。
