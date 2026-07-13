# GitKit

一个原生 macOS Git 桌面客户端 —— **Tauri (Rust) + React + Tailwind**。

当前进度:**第 1 步 脚手架 + UI 接入**已完成。界面已从 mockup 改造为填满真实系统窗口,使用**原生红黄绿灯**、可拖拽标题栏、系统字体(SF Pro)。Git 操作尚未接入(下一步)。

## 环境要求

- macOS + Xcode 命令行工具 ✅
- Node ≥ 18 ✅
- Rust (rustup) ✅

## 首次运行

```bash
cd "路径/GitKit"

# 1. 清掉沙箱残留的坏 node_modules(只需一次)
rm -rf node_modules package-lock.json

# 2. 安装前端依赖
npm install

# 3. 启动开发模式(首次会编译 Rust,约几分钟;之后秒开)
npm run tauri dev
```

第一次 `npm run tauri dev` 会下载并编译 Rust 依赖,耗时较久属正常。窗口起来后即是原生 App,左上角是系统红黄绿灯。

## 打包 .app

```bash
# 通用二进制(Apple Silicon + Intel)先加编译目标,只需一次
rustup target add aarch64-apple-darwin x86_64-apple-darwin

npm run tauri build
```

产物在 `src-tauri/target/release/bundle/`。

## 目录结构

```
GitKit/
├── index.html            # 前端入口
├── vite.config.ts        # Vite + Tailwind
├── src/
│   ├── main.tsx
│   ├── App.tsx           # 全部 UI(已原生化)
│   └── styles/           # Tailwind v4 + 主题变量
└── src-tauri/            # Rust 后端
    ├── Cargo.toml
    ├── tauri.conf.json   # 窗口/图标/权限配置
    ├── src/lib.rs        # 后续 Git 命令写在这里
    ├── capabilities/     # 前端可调用的权限白名单
    └── icons/            # App 图标
```

## 路线图

- [x] 1. 脚手架 + UI 接入(原生窗口/红黄绿灯/系统字体)
- [x] 2. 接真实 Git(读):项目切换弹窗、打开仓库、真实分支/提交历史/提交图/提交详情/改动状态/diff
- [x] 2.5 分支树:文件夹分组、置顶、隐藏、当前分支高亮;提交卡片重做(分支归属/合并来源);双击切换分支(确认+储藏)
- [ ] 3. 写操作:Push / Fetch / Merge / Cherry-pick / 暂存与提交 / 创建 MR
- [ ] 4. 多套提交者身份(设置里维护,项目级切换)
- [ ] 5. 自建 GitLab 集成(Token 存 Keychain,创建 MR)
- [ ] 6. 原生打磨:vibrancy 毛玻璃、窗口打包

## Rust 后端命令(src-tauri/src/git.rs)

只读命令,均直接调用系统 `git`(复用你的 SSH/凭证):`open_repo` `git_branches`
`git_log`(含 `--topo-order` 图形拓扑) `git_status` `commit_files` `commit_file_diff`
`working_file_diff`。前端封装在 `src/git.ts`,`computeGraph()` 从 parents 计算泳道。
