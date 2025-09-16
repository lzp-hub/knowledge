## 一、Electron 基础

### 1. Electron 的进程模型 🔥

**一句话回答**
Electron 包含主进程（Main）和渲染进程（Renderer），主进程管理应用生命周期，渲染进程运行 UI。

**要点**

- 主进程：控制窗口、菜单、系统 API。
- 渲染进程：基于 Chromium，跑前端代码。
- IPC：进程间通信。

**详细回答**

- 主进程启动后创建 BrowserWindow。
- 每个窗口就是一个渲染进程。
- 主进程和渲染进程通过 `ipcMain` / `ipcRenderer` 通信。

**扩展/对比**

- 面试官常问：“为什么 Electron 要分主进程和渲染进程？” 👉 为了安全隔离和多窗口支持。

---

### 2. Electron 如何调用系统能力

**一句话回答**
主进程通过 Node.js API 与系统交互，渲染进程通过 IPC 请求主进程执行。

**要点**

- 文件读写、通知、菜单。
- 调用系统 API。

**详细回答**

```js
// 渲染进程
ipcRenderer.send("save-file", data);

// 主进程
ipcMain.on("save-file", (e, data) => fs.writeFileSync("a.txt", data));
```

**扩展/对比**

- 不能直接在渲染进程用 Node API，需通过 IPC 转发。

---

### 3. Electron 的打包与发布 🔥

**一句话回答**
常用 electron-builder 打包应用，支持自动更新。

**要点**

- electron-builder。
- 不同平台差异（dmg, exe）。
- 自动更新（electron-updater）。

**详细回答**

- electron-builder 可输出跨平台安装包。
- 配合 electron-updater 可以检查更新并下载。

**扩展/对比**

- 面试官常问：“自动更新怎么做？” 👉 应用启动时检查版本，若更新则后台下载。

---

## 二、Puppeteer 基础

### 4. Puppeteer 是什么？ 🔥

**一句话回答**
Puppeteer 是基于 Chromium 的无头浏览器，支持自动化操作和爬虫。

**要点**

- 无头浏览器。
- 控制 DOM、截图、PDF。
- 爬虫/自动化测试。

**详细回答**

```js
const browser = await puppeteer.launch();
const page = await browser.newPage();
await page.goto("https://example.com");
await page.screenshot({ path: "a.png" });
```

**扩展/对比**

- 面试官可能问：“Puppeteer 和 Selenium 区别？” 👉 Puppeteer 更现代、API 简洁。

---

### 5. Puppeteer 常见应用场景

**一句话回答**
常用于自动化测试、爬虫、数据抓取、批量截图、PDF 导出。

**要点**

- 爬取页面。
- 批量截图。
- 转换 PDF。

**详细回答**

- 自动化测试：模拟用户行为（点击、输入）。
- 数据采集：爬取页面数据。
- 内容生成：生成 PDF/图片。

**扩展/对比**

- 面试官可能问：“你在项目里怎么用 Puppeteer？” 👉 结合业务场景回答（比如自动化截图、PDF 报表）。

---

### 6. 如何应对反爬机制 🔥

**一句话回答**
通过设置 User-Agent、模拟浏览器行为、延时、代理来绕过反爬。

**要点**

- 修改 UA。
- 设置 cookies。
- 加延时模拟人类行为。
- 使用代理 IP。

**详细回答**

```js
await page.setUserAgent("Mozilla/5.0 ...");
await page.waitForTimeout(1000);
```

**扩展/对比**

- 面试官可能问：“网站如何检测 Puppeteer？” 👉 检查浏览器特征（navigator.webdriver）。

---

## 三、跨平台实践

### 7. Electron 与 Puppeteer 结合场景

**一句话回答**
Electron 提供桌面壳，Puppeteer 负责自动化浏览器控制，常用于多账号自动化、批量任务。

**要点**

- Electron：UI + 系统集成。
- Puppeteer：网页操作。
- 场景：多账号、爬虫、截图。

**详细回答**

- Electron 主进程管理窗口和任务。
- Puppeteer 在后台执行自动化。
- 两者结合可做运营工具或跨平台桌面应用。

**扩展/对比**

- 面试官可能问：“为什么不用纯 Puppeteer？” 👉 需要桌面 UI 和系统集成时选 Electron。

---

### 8. 跨平台应用如何优化性能 🔥

**一句话回答**
减少渲染进程数量、按需加载模块、避免主进程阻塞。

**要点**

- 避免多开窗口。
- 渲染进程懒加载。
- 主进程只做调度。

**详细回答**

- Electron 应用体积大，需优化依赖。
- Puppeteer 执行任务时避免阻塞 UI。

**扩展/对比**

- 面试官可能问：“Electron 为什么体积大？” 👉 因为内置 Chromium 和 Node.js。
