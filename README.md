# 官网维护

[English](README_en.md)

官网是独立仓库 `cursimple/cursimple.github.io` 中的静态站点，推送到 `main` 后由 `.github/workflows/pages.yml` 验证并发布到 GitHub Pages（`https://cursimple.github.io/`），无需构建 Android。版本、APK 与 `update-feed` 仍来自应用仓库 `cursimple/cursimple-app`。

`pre` 分支的 Netlify 预览由 `.github/workflows/site-preview.yml` 负责，需要仓库密钥 `NETLIFY_AUTH_TOKEN` 与 `NETLIFY_SITE_ID`。

## 插件与组件入口

官网的「插件与组件」区和页尾分别跳转到插件收录页与组件收录页。「扩展组件」区介绍雨课堂通知和多平台通知，提供各自仓库与 Release 地址。组件注册表为 `cursimple/cursimple-components`，其收录页部署地址是 `https://cursimple.github.io/cursimple-components/`；该仓库的 Pages 首次启用与部署由其自身工作流负责。

平台能力与兼容要求以组件说明为准，网站不固定某个本地 ZIP 的版本号。新增文案需同步三种语言。

## 语言

首次访问默认简体中文，不随浏览器自动改为英文。用户可选择繁體中文或 English，选择会保存；分享链接中的 `?lang=` 优先于保存的选择，可同时保留 `#download` 等锚点。

`i18n.js` 三种语言使用相同的语义键。纯文本使用 `data-i18n`；包含图标或链接时只标记文字子元素。图片说明、读屏标签和元信息分别使用 `data-i18n-alt`、`data-i18n-aria-label`、`data-i18n-content`。动态文案用 `SiteI18n.t()`，日期用 `formatDate()`。

## 下载

页面图片先读 GitHub Pages 本站地址；某张图片加载失败或 8 秒无响应时，依次改走 jsDelivr 的三个节点（`cdn`、`fastly`、`gcore`）上本仓库 `main` 分支的同一路径，只处理 `assets/` 下的图片，站外图片不改写。更新日志里的图片同样适用。

下载按钮默认走国内加速直链：页面先用第一个镜像，再测速换成最先返回真实 `update.json` 的镜像，限流或报错的镜像不会被选中。用户切到「GitHub 直连」后会记住选择。镜像列表只放实测能下载 APK 且允许跨域测速的地址。

## 公告

在 `release-notes/` 中维护原文 `v<version>.md`、繁体 `v<version>.zh-TW.md` 和英文 `v<version>.en.md`。图片相对各自 Markdown 文件解析。

本地缺失时读取发布标签下的同名文档；缺少翻译时回退原文并提示。切换语言或版本会丢弃旧请求结果。每次点翻页按钮立即更新页面和指示，手动滑动也同步跟随。

## 验证

在仓库根目录执行：

```sh
node --check i18n.js
node --check releases.js
node --check main.js
node --test tests/*.test.cjs
node check-links.cjs
python3 -m http.server 8892 --bind 127.0.0.1 --directory .
```

检查默认中文、手动选择及刷新保留、手机菜单、下载架构和公告翻页。测试覆盖语言优先级、字典完整性、断网回退和竞争请求。
