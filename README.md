# hans-word · 小工具导航站

各产品线小工具导航入口，托管在 GitHub Pages 上，push 到 `main` 分支即自动发布。

- 仓库：`hanjin1990max-web/hans-word`
- 线上地址：<https://hanjin1990max-web.github.io/hans-word/>
- 发布方式：GitHub Pages 从 **`main` 分支根目录**发布（Settings → Pages 中配置）
- 部署状态：仓库 Actions 标签页可查看每次发布的构建过程，通常 push 后 1~2 分钟内生效

> 当前的根 `index.html` 是验证 Pages 功能的临时占位页，正式启用时替换为实际的导航页即可。

---

## 一、目录结构规范

一级目录 = 具体工具，每个工具目录自包含所有资源。

```
hans-word/
├── index.html            # 根导航入口（必维护：新增工具要在这里加链接）
├── README.md             # 本文件
│
├── one/                  # 工具1
│   ├── index.html        # 工具1入口
│   └── ...               # 资源脚本样式
│
├── two/                  # 工具2
│   ├── index.html        # 工具2入口
│   └── ...               # 资源脚本样式
└── ...
```

### 结构规则

| 规则 | 说明 |
|---|---|
| 每个工具必须有 `index.html` | 这样分享时可以只给目录路径，不用带文件名 |
| 资源自包含 | 每个工具的 css / js / 图片放在**自己目录内**，禁止跨工具互相引用，避免改 A 坏 B |
| 目录名用小写英文 | 用小写字母 + 连字符（如 `word-card`），不用中文、空格、大写 |
| 新增工具要更新根导航 | 在根 `index.html` 添加卡片链接，否则访客无法发现 |

---

## 二、路径规则（最重要的坑）

本站发布在**子路径** `/hans-word/` 下，不是域名根目录，这决定了所有写法：

1. **必须使用相对路径**。正确：`href="one/index.html"`、`src="./js/app.js"`。
   错误：`href="/one/index.html"` —— 开头带 `/` 的绝对路径会指向 `https://hanjin1990max-web.github.io/one/...`，直接 404。
2. **大小写敏感**。Pages 服务器是 Linux，`Portal/` 和 `portal/` 是两个不同路径，本地 Windows/Mac 上能打开不代表线上能打开。
3. **禁止中文文件名**。URL 会被转义成 `%E5%95%86%E5%9F%8E...`，不便分享和排查，新增文件一律用小写英文 + 连字符（如 `mall-h5.html`）。

---

## 三、GitHub Pages 平台规则

- **纯静态托管**：只能放 HTML / CSS / JS / 图片 / 字体等静态文件，无法运行 PHP、Node 等任何服务端代码。
- **Jekyll 处理**：默认会过一遍 Jekyll，**下划线开头的文件和目录（如 `_assets/`）会被忽略不上传**。若工具导出的资源带下划线目录，需在仓库根目录放一个空的 `.nojekyll` 文件来关闭 Jekyll。
- **用量限制**（官方文档 [GitHub Pages limits](https://docs.github.com/en/pages/getting-started-with-github-pages/github-pages-limits)）：
  - 发布后的站点总大小 ≤ **1 GB**（源仓库也建议 ≤ 1 GB）
  - 带宽软限制 **100 GB/月**
  - 每小时最多 **10 次构建**发布（即别高频连续 push，攒一批一起推）
  - 单次部署超过 10 分钟会超时失败
- **仅 HTTPS**：站点强制 `https://` 访问，页面内引用外部资源（图片、脚本）也要用 `https://`，否则会被浏览器拦截。
- 公开仓库免费使用 Pages。

---

## 四、本地预览

push 即发布，建议先本地验证再推。不要直接双击用 `file://` 打开——路径行为和线上不一致。任选一种方式：

**方式一：命令行起静态服务**（macOS / Linux 一般自带 Python，Windows 装了 Python 也有）：

```bash
cd <仓库根目录>
python3 -m http.server 8080     # Windows 下命令通常是：python -m http.server 8080
# 浏览器访问 http://localhost:8080/
```

**方式二：编辑器插件**：VS Code 安装 Live Server 插件，右键 `index.html` → Open with Live Server。

> 想完全模拟线上子路径环境（验证相对路径是否写对）：在仓库的**上一层目录**起服务，然后访问 `http://localhost:8080/hans-word/`。

---

## 五、新增一个工具的标准流程

1. 在仓库根目录新建小写英文工具目录，如 `dict/`；
2. 工具目录内放置 `index.html` 入口及全部资源，内部链接全部写相对路径；
3. 在根 `index.html` 添加导航卡片（名称 + 一句话描述 + 链接）；
4. 本地起服务预览，点一遍导航卡片和工具内部跳转；
5. `git add` + `commit` + `push origin main`，等 Actions 构建完成后线上验证。

## 六、常见问题排查

| 现象 | 原因排查 |
|---|---|
| 线上 404 | ① 是否用了 `/` 开头的绝对路径；② 路径大小写是否和实际目录完全一致；③ push 后是否等了 1~2 分钟让部署完成 |
| 资源加载不到但文件存在 | 目录/文件名是否以下划线 `_` 开头被 Jekyll 忽略 → 加 `.nojekyll` |
| 本地正常线上异常 | 多半是大小写问题（本地系统不区分，服务器区分）或 `file://` 直开导致的路径错觉 |
| push 后站点没更新 | 到仓库 Actions 页看构建是否失败；连续多次 push 可能触发每小时 10 次构建限制 |
