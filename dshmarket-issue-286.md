# Market settings section crashes (blank) on dsh 0.1.1-rc.2 - React removeChild NotFoundError

- 链接: https://github.com/dsh-market/dsh-market/issues/286
- 作者: paolongtaoyiwei
- 状态: closed | 评论数: 6
- 创建时间: 2026-08-23T03:31:13Z

## 问题描述

## Environment
- dsh version: 0.1.1-rc.2 (npm `latest`, launched via `npx @deepseek-ai/dsh web`)
- dshmarket version: 1.18.1 (also reproduced on 1.17.1)
- Install: `dsh plugin --profile web add dshmarket`

## Symptom
Settings > Plugin Market renders a blank panel. Browser console:

```
NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.
...
slot entry crashed in 'settings.section'
```

## What works
Host routes are fine: `/dsh-market/registry` returns HTTP 200 with 1884 plugins; `/dsh-market/status` shows version 1.18.1 and `dshmarket` is `live`.

## Suspected cause
Client bundle still built against `@deepseek-ai/dsh-client-ui-primitives ^0.1.0-rc.6` (see package.json devDependencies), while dsh 0.1.1 ships primitives 0.1.1-rc.2 -> React reconciliation breaks with `removeChild`.

## Expected
Market should render on dsh 0.1.1-rc.2 (current `latest`), or the supported dsh range should be documented.

## 结论（重要）

**这是 Microsoft Edge 浏览器专属的兼容性问题，Chrome 浏览器下完全正常。**

根因：Edge 的图片懒加载干预会在 React 之外替换 <img> 标签为占位符，导致 React 在 commit 阶段调用 removeChild 时找不到子节点而崩溃（NotFoundError）。

## 评论

### 评论 1 — fkysly (2026-08-23T05:07:29Z)

我按你给的环境完整复现了一遍，**没能重现**——想请你补几个信息。

做法：全新目录，装 `@deepseek-ai/dsh@0.1.1-rc.2`（npm `latest`），`dsh plugin --profile web add`，真实浏览器打开。**1.18.1 和 1.19.0-beta.4 各测一次，设置 → 插件 → 插件市场 都正常展开**，控制台零错误，市场主页面也正常（1884 条插件全部渲染）。

关于你怀疑的 primitives 版本：这个判断方向对，但**这个包不会被打进我们的产物**——它在 `tsdown.config.ts` 的 `CLIENT_EXTERNALS` 里，运行时由宿主的 `__ModuleLoader__` 提供。所以 `devDependencies` 里写的 `^0.1.0-rc.6` 只影响我们本地的类型检查，不决定浏览器里实际加载哪个版本；宿主给什么就是什么。也就是说 devDependency 和运行时版本不一致本身不构成这个崩溃的原因。

`removeChild NotFoundError` 通常来自两个 React 实例，或者有代码在 React 之外直接改了 DOM。我这边单装市场复现不出来，所以想问：

1. **是否同时装了其它带前端的插件？** 特别是像 `@linxin666/dsh-web-ui-all` 这类"全家桶"——另一份报告（#271）的环境里有它。麻烦试试只留 dshmarket、其它前端插件全禁用，还崩不崩。
2. **完整的控制台栈**（现在贴的被截断了），尤其是 `removeChild` 上面那几帧是哪个包的代码。
3. **硬刷新试过吗**（Ctrl+Shift+R）？宿主升级后浏览器可能还缓存着上一版的客户端产物，新旧两份在同一页面上跑正是会产生这类 reconciliation 错误的场景。
4. `profiles/web/package.json` 的 `dependencies` 全文。

如果 1 或 3 命中，那我们至少知道要往哪个方向做防护。现在这个状态我没法定位——盲改一个复现不了的崩溃，改完也没法验证是不是真修了。


### 评论 2 — paolongtaoyiwei (2026-08-23T05:38:02Z)


补充：已复现并确认根因——崩溃是 Microsoft Edge 特有的，Chrome 里完全正常。

环境：dsh （latest）、dshmarket 、单插件（无其它前端插件）、 硬刷新后仍崩。0.1.1-rc.21.18.1Ctrl+Shift+R
浏览器：Edge （正式版 64 位）。换 Chrome 后「插件市场」正常展开、1884 条插件全部渲染、零报错。141.0.3537.71
Edge 控制台除 （栈全在宿主 React DOM commit 阶段，市场代码只在 边界）外，还紧跟一条： （go.microsoft.com 链接，Edge 专属懒加载干预）。NotFoundError: Failed to execute 'removeChild' ...componentDidCatch[Intervention]Images loaded lazily and replaced with placeholders. Load events are deferred.
根因：市场卡片头像（）和截图缩略图（，每卡最多 3 张）用 渲染，一页几十到上百个。Edge 的懒加载干预会把这些 替换成占位符（在 React 之外改 DOM），React 随后 找不到子节点就崩。OwnerAvatarCardShot<img loading="lazy" decoding="async" fetchPriority="low"><img>removeChild

### 评论 3 — paolongtaoyiwei (2026-08-23T05:39:15Z)

> 我按你给的环境完整复现了一遍，**没能重现**——想请你补几个信息。
> 
> 做法：全新目录，装 （npm ），，真实浏览器打开。**1.18.1 和 1.19.0-beta.4 各测一次，设置 → 插件 → 插件市场 都正常展开**，控制台零错误，市场主页面也正常（1884 条插件全部渲染）。`@deepseek-ai/dsh@0.1.1-rc.2``latest``dsh plugin --profile web add`
> 
> 关于你怀疑的 primitives 版本：这个判断方向对，但**这个包不会被打进我们的产物**——它在 的 里，运行时由宿主的 提供。所以 里写的 只影响我们本地的类型检查，不决定浏览器里实际加载哪个版本；宿主给什么就是什么。也就是说 devDependency 和运行时版本不一致本身不构成这个崩溃的原因。`tsdown.config.ts``CLIENT_EXTERNALS``__ModuleLoader__``devDependencies``^0.1.0-rc.6`
> 
> `removeChild NotFoundError`通常来自两个 React 实例，或者有代码在 React 之外直接改了 DOM。我这边单装市场复现不出来，所以想问：
> 
> 1. **是否同时装了其它带前端的插件？** 特别是像  这类"全家桶"——另一份报告（`@linxin666/dsh-web-ui-all`[插件市场（dshmarket）在国内网络下导致 Web UI 卡死无法进入 [Plugin Market 在慢速 CN 网络上导致 Web UI 挂机/终止] #271](https://github.com/dsh-market/dsh-market/issues/271)）的环境里有它。麻烦试试只留 dshmarket、其它前端插件全禁用，还崩不崩。
> 2. **完整的控制台栈**（现在贴的被截断了），尤其是  上面那几帧是哪个包的代码。`removeChild`
> 3. **硬刷新试过吗**（Ctrl+Shift+R）？宿主升级后浏览器可能还缓存着上一版的客户端产物，新旧两份在同一页面上跑正是会产生这类 reconciliation 错误的场景。
> 4. `profiles/web/package.json`的  全文。`dependencies`
> 
> 如果 1 或 3 命中，那我们至少知道要往哪个方向做防护。现在这个状态我没法定位——盲改一个复现不了的崩溃，改完也没法验证是不是真修了。

这是控制台栈
index-ClqxG24t.js:56  NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.
    at r0 (index-ClqxG24t.js:56:26225)
    at Nt (index-ClqxG24t.js:56:27527)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27790)
    at Nt (index-ClqxG24t.js:56:27677)
ul @ index-ClqxG24t.js:56
H3.a.componentDidCatch.r.callback @ index-ClqxG24t.js:56
u3 @ index-ClqxG24t.js:54
s0 @ index-ClqxG24t.js:56
l0 @ index-ClqxG24t.js:56
U4 @ index-ClqxG24t.js:56
G4 @ index-ClqxG24t.js:56
Pn @ index-ClqxG24t.js:56
f0 @ index-ClqxG24t.js:56
an @ index-ClqxG24t.js:54
G4 @ index-ClqxG24t.js:56
Pn @ index-ClqxG24t.js:56
c0 @ index-ClqxG24t.js:56
Z @ index-ClqxG24t.js:41
se @ index-ClqxG24t.js:41
client.js?rev=79b59d365f3b:525  slot entry crashed in 'settings.section': NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.
    at r0 (index-ClqxG24t.js:56:26225)
    at Nt (index-ClqxG24t.js:56:27527)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27790)
    at Nt (index-ClqxG24t.js:56:27677)
componentDidCatch @ client.js?rev=79b59d365f3b:525
H3.a.componentDidCatch.r.callback @ index-ClqxG24t.js:56
u3 @ index-ClqxG24t.js:54
s0 @ index-ClqxG24t.js:56
l0 @ index-ClqxG24t.js:56
U4 @ index-ClqxG24t.js:56
G4 @ index-ClqxG24t.js:56
Pn @ index-ClqxG24t.js:56
f0 @ index-ClqxG24t.js:56
an @ index-ClqxG24t.js:54
G4 @ index-ClqxG24t.js:56
Pn @ index-ClqxG24t.js:56
c0 @ index-ClqxG24t.js:56
Z @ index-ClqxG24t.js:41
se @ index-ClqxG24t.js:41
(索引):1 [Intervention]Images loaded lazily and replaced with placeholders. Load events are deferred. See https://go.microsoft.com/fwlink/?linkid=2048113


### 评论 4 — fkysly (2026-08-23T16:30:37Z)

有进展了——**根因找到并已在 1.20.3 修复**，请升级验证：

```
dsh plugin --profile web add dshmarket
```

我之前复现不出来，是因为漏了一步。#293 的报告者把它查到了行：崩溃发生在**打开插件卡片或截图预览之后**，不是打开市场本身。我此前只切 tab，从没打开过 lightbox。

根因是双 React 根争抢同一个 portal 容器：

- 市场的截图预览 `createPortal(..., document.body)`
- 宿主的设置对话框也是 portal 到 `document.body`
- 两者是**独立的 React 根**，各自增删 `document.body` 的子节点，顺序谁也不掌握
- 宿主那一侧最终对一个已被市场移动过的节点调用 `removeChild` → `NotFoundError` → 被 `settings.section` 槽位捕获 → 面板变白

这也解释了为什么它是**间歇性**的、为什么和版本无关（1.17.1 到 1.20.2 都能复现）、以及为什么你我环境不同就复现不了——它取决于挂载顺序。

顺带更正我之前的一个判断：我说过你怀疑的 primitives devDependency 版本不构成原因，这一条仍然成立（那个包是外部依赖，运行时由宿主提供）。但我当时据此认为"没有可查的方向"，那是错的——真正的方向在 portal，我没想到。

修法是让市场的预览挂进**自己的容器**，宿主的根从此只看到一个它从不碰的节点。

**需要说明**：我依然没能在自己的环境里复现这个崩溃，所以这不是"实测崩溃已消失"，而是"崩溃赖以发生的共享容器已不存在"。如果升级后你那边还会白屏，请回来说，并附上控制台栈——那意味着还有第二条路径。


### 评论 5 — fkysly (2026-08-23T17:03:07Z)

根因已定位并修复（**1.20.3**），关闭此条；仍复现请重开并附控制台栈。

真因是双 React 根争抢 `document.body` 做 portal 容器 —— 详见 #293，那位报告者把它查到了行。这解释了为什么它间歇、为什么和版本无关（1.17.1～1.20.2 都能复现）、以及为什么我在干净环境下始终复现不出来：它取决于挂载顺序。

你怀疑的 primitives devDependency 版本不构成原因这一条仍然成立（那个包是外部依赖、运行时由宿主提供）。但我当时据此认为"没有可查的方向"是错的——方向在 portal，我没想到。


### 评论 6 — HYB54188 (2026-08-23T17:43:45Z)

升级 1.20.4 后白屏仍复现，Edge 专属，进入插件市场「挂载时」即崩溃。
环境：dsh 0.1.1-rc.2（npm latest）、dshmarket 1.20.4、Windows 11、Edge 151.0.4129.101、region=china（gh-proxy）、无其他前端插件（profile 仅 @deepseek-ai/dsh-base + @deepseek-ai/dsh-web-app + dshmarket）。
关键信息：已确认无懒加载图片干预（市场卡片图片已改为 eager，Edge 的 [Intervention] 消息消失），removeChild 崩溃仍发生在宿主 React 提交阶段（index-ClqxG24t.js:56 r0/Nt/o0），且 1.18.1 / 1.20.2 / 1.20.4 均复现；同环境 Chrome 零报错（用户 A 在 #286 中的结论一致）。
控制台栈：
```
NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.
    at r0 (index-ClqxG24t.js:56:26225)
    at Nt (index-ClqxG24t.js:56:27527)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27991)
    at Nt (index-ClqxG24t.js:56:27677)
    at o0 (index-ClqxG24t.js:56:27790)
    at Nt (index-ClqxG24t.js:56:27677)
client.js?rev=79b59d365f3b:525 slot entry crashed in 'settings.section': NotFoundError: Failed to execute 'removeChild' on 'Node': The node to be removed is not a child of this node.
    at r0 (index-ClqxG24t.js:56:26225) ...
componentDidCatch @ client.js?rev=79b59d365f3b:525
```
这应是你说的「第二条路径」：市场挂载进宿主 settings.section 槽位时，宿主 React 与市场在 Edge 下争抢 DOM 顺序导致 removeChild 崩溃。请优先修复 Edge 兼容（或明确标注 Edge 暂不支持）。


