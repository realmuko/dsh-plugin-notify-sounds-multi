# FORK NOTES — dsh-plugin-notify-sounds（本地多段音效版）

本目录是 [`dsh-plugin-notify-sounds`](https://github.com/BillCx330/dsh-plugin-notify-sounds)
**1.8.6 的本地 fork**，只做了一处功能补丁：**每个触发事件可以挂多段自定义音效，播放时随机选一段**。

## 来源与署名（必读）

- 原作者：**BillCx330**
- 原仓库：<https://github.com/BillCx330/dsh-plugin-notify-sounds>
- npm 包：`dsh-plugin-notify-sounds`（本 fork 的基线是 `1.8.6`，取自 npm registry 的原始发布包）
- 许可证：**MIT**（见 `LICENSE`，版权归 BillCx330；本 fork 保留原文，未改动）

MIT 允许修改与再分发，但再分发时必须**保留原版权声明和许可证全文**，并且不能暗示原作者为你的改动背书。
本文件即为「已修改」的说明。若发布到 GitHub/npm，README 里请照抄上面三行来源信息并链回原仓库。

## 本 fork 改了什么

上游 1.8.6 的自定义音效是「一个触发事件一份音频」（`customSound`）。
本 fork 在宿主 schema 里新增了三个 JSON 字段：

| 字段 | 触发时机 |
|---|---|
| `customSounds` | 任务完成（running → idle） |
| `decisionCustomSounds` | 需要决策（审批 / 计划评审） |
| `questionCustomSounds` | 需要回答提问 |

每个字段是一段 JSON：`[{"n":"文件名","d":"base64"}, ...]`，播放时从该组里随机挑一段。
设置页相应改成可以一次上传/删除多段音效。

改动范围（对照上游 1.8.6）：

```
lib/index.js  |  +14        仅 Config schema 增加 6 个字段
lib/client.js |  +188 -92   设置页多段管理 + 播放随机选择
```

`git log` 里两个提交即「上游 1.8.6 原样导入」和「本地补丁」，`git diff HEAD~1` 就是全部改动。

## 为什么 fork 而不是继续用上游新版

上游 **1.9.0 起**改成了「固定 3 个自定义槽位」（`custom1File` … `custom3File`），
不再认识 `customSounds` 这组字段。直接升级会让配置里的多段音效全部失效（静音），
所以这里停在 1.8.6 + 补丁。

代价：1.9.x 的新功能（如 0.2.2 设置页图标适配、系统通知等）不会自动获得。
如果上游以后重新支持多段音效，优先合并上游、弃用本 fork。

## 在本机怎么用（DSH desktop profile）

profile：`C:\Users\w\.dsh\profiles\desktop`

`package.json` 依赖指向本目录，而不是 registry：

```json
"dsh-plugin-notify-sounds": "file:D:/DSHplugins/dsh-plugin-notify-sounds"
```

这样 `pnpm install` / 插件管理器重装时，会从这个目录装，不会被装回 registry 的 1.9.x。

**注意：不要在 DSH「插件」页点本插件的「更新」**——那会把依赖重新指回 registry 的 1.9.x，
补丁会被覆盖。要升级上游，先在这里把补丁移植到新版，再改版本号。

## 如果要发布成新项目

1. GitHub fork 原仓库最省事（自带作者历史与 LICENSE），或新建仓库后把本目录推上去，
   README 顶部写明「fork of BillCx330/dsh-plugin-notify-sounds，MIT」。
2. npm 发布需要**换一个包名**（例如 `dsh-plugin-notify-sounds-multi` 或 `@你的scope/notify-sounds`），
   不能占用原包名；`package.json` 的 `repository`/`homepage` 指向新仓库，
   `description` 里注明「based on dsh-plugin-notify-sounds by BillCx330」。
3. 保留 `LICENSE` 原文（含 BillCx330 版权行），不要改成自己的名字。
4. 若只是自己用，**不必发布**：本地 `file:` 依赖就够了。
