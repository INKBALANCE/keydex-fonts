# keydex-fonts

Keydex 客户端「内置字体」用的托管仓库。这里只放 woff2 字体文件与对应的 `@font-face` CSS，
**不进入任何安装包**：客户端在用户点击字体卡片时才按需下载，然后缓存进本机 IndexedDB，
之后离线可用。字体文件本身不属于 Keydex 安装产物。

## 目录结构

| 目录 | 字体 | 上游来源 | 许可 |
| --- | --- | --- | --- |
| `lxgw-wenkai-mono/` | 霞鹜文楷 Mono（中文楷体 + 等宽西文） | [lxgw/LxgwWenKai](https://github.com/lxgw/LxgwWenKai) v1.522 | SIL OFL 1.1 |
| `lxgw-bright-code/` | Bright Code Italic（中文楷体**斜体** + Monaspace Argon 等宽西文） | [lxgw/LxgwBright-Code](https://github.com/lxgw/LxgwBright-Code) v2.922 | SIL OFL 1.1 |

每个字体目录包含：

- `result.css` —— 单条 `@font-face`，`src` 指向**同目录**的 woff2 文件；
- `*.woff2` —— 由上游 TTF 用 `fontTools + brotli` 转换（`flavor="woff2"`）；
- `OFL.txt` —— 上游 SIL Open Font License 1.1 原文。

## 客户端解析约定（改动前必读）

客户端从 CSS 里用正则 `url\((["']?)(?:\.\/)?([^"')]+\.woff2)\1\)` 提取字体文件，并把文件名拼成
`${assetBaseUrl}/${fileName}`。因此：

1. 文件必须是 **woff2**（其它格式解析不到，会报「字体清单为空」）；
2. `src` 里的 URL **只能是文件名**，不能带子目录，且 woff2 必须与 `result.css` 同目录；
3. 一个字体目录里可以放多个 woff2（多字重/多分片），客户端会全部下载后注入。

## 文件清单（v1.0.1）

| 文件 | 字节 | SHA-256 |
| --- | --- | --- |
| `lxgw-wenkai-mono/result.css` | 173 | `a989229c5b968f43dc7b09d8bde7db442a2ad13a34c5c8d7a65c3994317aa9a8` |
| `lxgw-wenkai-mono/LXGWWenKaiMono-Regular.woff2` | 8,065,404 | `c8fa31f208dd70f5466f1b2b534c4d1a40aa75376d02083a79ee18a8998fd5e5` |
| `lxgw-bright-code/result.css` | 173 | `ab38776d8f243a89589545b42ec66bfcd565df7bcf67f95066265a0d39b909f1` |
| `lxgw-bright-code/LXGWBrightCode-Italic.woff2` | 6,306,000 | `1fd07b625aff296575f9b23fbc2ffb7ea86a5cab4d23479716ca994562614290` |

原始 TTF 体积（未压缩，仅供对照）：霞鹜文楷 Mono 25,603,912 → **8,065,404**（31.5%）；
Bright Code Italic 14,936,292 → **6,306,000**（42.2%）。

> `lxgw-bright-code` 采用的是上游 **Italic** 字重，`result.css` 里的 `font-family` 为 `Bright Code Italic`；
> `@font-face` 仍声明 `font-style: normal`（否则 normal 上下文的文字不会匹配该字体），
> 因此客户端选中后界面文字整体呈斜体，这是预期效果而非缺陷。

## 版本与地址

固定版本（推荐，jsDelivr 会长期缓存）：

```text
https://cdn.jsdelivr.net/gh/INKBALANCE/keydex-fonts@v1.0.1/lxgw-wenkai-mono
https://cdn.jsdelivr.net/gh/INKBALANCE/keydex-fonts@v1.0.1/lxgw-bright-code
```

兜底地址（raw，同样带 `Access-Control-Allow-Origin: *`，但没有 CDN 缓存）：

```text
https://raw.githubusercontent.com/INKBALANCE/keydex-fonts/v1.0.1/lxgw-wenkai-mono
```

## 发新版本时

1. 覆盖字体文件后 commit，打新 tag（例如 `v1.0.1`）——不要改写已发布的 tag，客户端会按 tag 缓存；
2. 同步更新客户端 `FONT_DEFINITIONS` 里的 `cacheVersion`（IndexedDB 失效键）、`totalAssets`、`totalBytes`；
3. 本 README 的文件清单与 SHA-256 同步刷新。

## 许可

两套字体均为 SIL Open Font License 1.1，允许使用、修改、再分发（含商用与内嵌），
**不得单独售卖字体文件本身**，再分发须保留 `OFL.txt` 与版权声明。详见各目录下的 `OFL.txt`。
