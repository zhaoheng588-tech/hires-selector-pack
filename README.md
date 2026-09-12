# HiRes 读取规则包（selector-pack）

`Product Image Downloader — SKU Export` 扩展的**远端读取规则**。亚马逊改版导致某些字段读不到时，
扩展会从这里拉一份新规则 —— 用户不用等扩展更新，我们也不用发新版。

- **`selector-pack.json`** —— 扩展真正拉取的文件（面板「规则地址」填它的 raw 地址）
- `selector-pack.reference.json` —— 内置规则的快照，照着抄改用；**扩展不读这个文件**

## 怎么改一条规则

1. 在 `selector-pack.reference.json` 里找到要覆盖的字段（例：`title`）
2. 把该字段拷进 `selector-pack.json` 的 `selectors.fields`；只想改某个站点就放
   `selectors.marketplaces["amazon.com"]`（站点规则优先于全局规则）
3. 改选择器，并**把 `selectors.version` +1**（面板会显示它，出问题时能立刻确认用户拿到哪版）
4. commit 到 `main` —— 用户最迟 6 小时（定时）、或下次有字段漏读时（30 分钟节流）自动拿到；
   面板里的「立即更新规则」可以立刻拉

## 硬规则（写在扩展 AGENTS.md §2.8）

- **只放数据**：CSS 选择器字符串。永远不要放代码 —— MV3 禁止远程代码
- **不要放 `entitlements`**：扩展会直接丢弃。远端配置不得影响授权（否则付费墙可被一行 JSON 拆掉）
- 远端规则**插在内置规则前面**：写错也只是多一次失败尝试，内置链永远兜底
- 这个仓库必须**公开**：私有 raw 需要 token，扩展里不能放 token

## 托管事实（实测）

| 项 | 值 |
|---|---|
| raw 地址 | `https://raw.githubusercontent.com/zhaoheng588-tech/hires-selector-pack/main/selector-pack.json` |
| CORS | `Access-Control-Allow-Origin: *`（扩展没有 host_permissions，必须靠 CORS） |
| 缓存 | `cache-control: max-age=300` → 提交后约 5 分钟全网生效 |
