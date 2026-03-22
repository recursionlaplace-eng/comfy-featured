# 09_AFL_CANVAS_ComfyUI精选App管理更新文档

## 1. 这份文档是干什么的

这份文档专门回答两个管理问题：

- **云端精选 App 到底怎么维护**
- **能不能直接在 IDE 里新增、修改、下线精选**

结论先说：

- **可以**
- 而且建议就是通过 **IDE + GitHub 仓库文件** 的方式维护
- 不需要做后台 CMS，也不需要数据库录入页

这份文档默认配合：

- [08_AFL_CANVAS_ComfyApp_精选与收藏库开发方案.md](/E:/AFL%20CANVAS/开发待完成/08_AFL_CANVAS_ComfyApp_精选与收藏库开发方案.md)

一起使用。

---

## 2. 先讲清楚：“不用数据库”到底是什么意思

在这套方案里，“不用数据库”不是一句抽象口号，而是一个非常具体的技术约束。

这里的 **数据库**，指的是下面这些东西：

- Prisma 模型
- SQLite 表
- MySQL / PostgreSQL
- 云数据库表
- 任何“把精选、收藏、标签、最近使用”存进结构化 DB 的方案

也就是说，这次我们明确 **不做**：

- `featured_apps` 表
- `user_favorites` 表
- `app_tags` 关系表
- `recent_apps` 表
- 后台管理页提交后写数据库

这次要做的是：

- **精选**：通过 GitHub 仓库中的 JSON / 图片 / `.app.json` 文件维护
- **收藏**：通过本地公共目录 JSON 文件维护
- **最近使用**：通过本地 JSON 文件维护
- **标签**：通过本地 JSON 索引维护

所以“云端精选”本质上不是“上传到数据库”，而是：

- **把内容文件提交到 GitHub 仓库**
- 应用端再从 GitHub 拉取这些文件

---

## 3. 云端精选的推荐维护方式

最适合你现在需求的方式是：

- 建一个专门的 **Comfy Featured Content 仓库**
- 你直接在 IDE 里打开这个仓库
- 新增 / 修改精选 App
- 提交并 push 到 GitHub
- AFL CANVAS 再从这个仓库同步

### 为什么推荐单独仓库

相比把精选内容混在主项目仓库里，单独仓库更适合：

- 内容更新频繁
- 不想每次改精选都影响主项目代码
- 可以让“内容维护”和“程序开发”分离
- 后续如果要让别人帮你维护精选，也更方便

### 如果你暂时不想单独仓库

也可以先放在主仓库里维护。

但长期建议还是拆出去，因为：

- 云端精选本质上是“内容源”
- 最好不要和主程序版本强绑定

---

## 3.5 预置给用户的 Local Comfy App 好不好做

答案是：

- **好做**
- 而且建议直接做成 **Built-in 内置预置库**

也就是把你想预置给用户的 Local Comfy App 跟随软件一起打包：

```text
Public_assests/ComfyAppLibrary/featured_local/
  <app-id>/
    item.json
    cover.jpg
    workflow.app.json
```

这样用户安装后，窗口模式里直接就能看到这些预置项。

### 为什么这条路最合适

因为它天然符合“预置”的含义：

- 你随安装包发给用户
- 用户不需要手动导入
- 软件一装就有

### 但要注意一点

这类内置预置建议视为：

- **只读**
- **系统预置**

不要让用户直接修改它本体。

如果用户想自定义：

- 改标签
- 改名称
- 改参考图
- 改说明

应该写入用户自己的收藏副本，而不是回写内置目录。

---

## 4. 推荐的云端精选仓库结构

建议远端内容仓库结构如下：

```text
comfy-featured-repo/
  manifest.json
  apps/
    rh-flux-portrait-pro/
      item.json
      cover.jpg
    rh-anime-upscaler/
      item.json
      cover.jpg
    local-cinematic-lighting/
      item.json
      cover.jpg
      workflow.app.json
```

说明：

- `manifest.json`
  - 是全局索引
  - 用来列出哪些 App 需要被客户端展示
- `apps/<app-id>/item.json`
  - 是单个精选项的元数据
- `cover.jpg`
  - 是封面图
- `workflow.app.json`
  - 仅本地 Comfy 类型需要

---

## 5. 两类精选项该怎么维护

## 5.1 RunningHub 精选项

RunningHub 精选项推荐只维护：

- `webappId`
- 标题
- 简介
- 标签
- 封面图（可选）

推荐文件：

```json
{
  "id": "rh-flux-portrait-pro",
  "sourceMode": "rh",
  "title": "Flux Portrait Pro",
  "summary": "高质量人像工作流",
  "description": "适合半写实写真、棚拍人像和商业头像。",
  "tags": ["portrait", "flux", "photo"],
  "webappId": "123456",
  "cover": "cover.jpg",
  "status": "published"
}
```

说明：

- `webappId` 是 RH 真源
- 标题和说明可以在内容仓库里手工维护
- 封面可以：
  - 用 RH 拉回来的默认封面
  - 或你手动放一张 `cover.jpg` 覆盖默认封面

## 5.2 Local Comfy 精选项

本地 Comfy 精选项需要维护：

- 标题
- 简介
- 标签
- 本地 `.app.json`
- 封面

推荐文件：

```json
{
  "id": "local-cinematic-lighting",
  "sourceMode": "local",
  "title": "Cinematic Lighting",
  "summary": "电影感光影基础流程",
  "description": "适合建立统一风格的电影感打光与色调。",
  "tags": ["cinematic", "lighting", "lookdev"],
  "appFile": "workflow.app.json",
  "cover": "cover.jpg",
  "status": "published"
}
```

说明：

- `workflow.app.json` 直接放在该目录里
- AFL CANVAS 拉到后即可构造 Local App 包

---

## 6. manifest.json 怎么写

`manifest.json` 建议只做索引，不重复放所有详细内容。

推荐结构：

```json
{
  "version": 1,
  "updatedAt": "2026-03-22T12:00:00.000Z",
  "items": [
    {
      "id": "rh-flux-portrait-pro",
      "path": "apps/rh-flux-portrait-pro/item.json"
    },
    {
      "id": "local-cinematic-lighting",
      "path": "apps/local-cinematic-lighting/item.json"
    }
  ]
}
```

这样做好处是：

- 全局索引清晰
- 每个 App 单独管理
- 单个 App 修改时不容易和别人冲突

---

## 7. 你在 IDE 里的维护 workflow

这部分就是给你直接照着做的。

## 7.1 新增一个 RunningHub 精选

1. 在仓库 `apps/` 下创建目录  
   例如：`apps/rh-flux-portrait-pro/`
2. 新建 `item.json`
3. 填入：
   - `id`
   - `sourceMode: "rh"`
   - `title`
   - `summary`
   - `description`
   - `tags`
   - `webappId`
4. 放入 `cover.jpg`  
   如果你想手动指定封面
5. 在 `manifest.json` 中添加一条索引
6. 提交并 push

## 7.2 新增一个 Local Comfy 精选

1. 在 `apps/` 下创建目录  
   例如：`apps/local-cinematic-lighting/`
2. 放入 `workflow.app.json`
3. 新建 `item.json`
4. 填入：
   - `id`
   - `sourceMode: "local"`
   - `title`
   - `summary`
   - `description`
   - `tags`
   - `appFile`
5. 放入 `cover.jpg`
6. 在 `manifest.json` 中添加索引
7. 提交并 push

## 7.3 修改一个已有精选

你只需要改对应目录下的：

- `item.json`
- `cover.jpg`
- `workflow.app.json`（仅 local）

然后提交并 push 即可。

## 7.4 下线一个精选

推荐做法不是直接删目录，而是先在 `item.json` 中改：

```json
{
  "status": "archived"
}
```

然后：

- 要么保留在 `manifest.json` 里，由客户端过滤
- 要么直接从 `manifest.json` 移除

更稳妥的是：

- 先从 `manifest.json` 移除
- 目录保留一段时间

这样方便回滚。

---

## 8. AFL CANVAS 客户端如何读取这套内容

客户端建议流程：

1. 拉取远端 `manifest.json`
2. 遍历 `items`
3. 逐个读取 `item.json`
4. 根据 `sourceMode` 分流：
   - `rh`：构造成 RH 精选项
   - `local`：读取 `workflow.app.json`
5. 如果存在 `cover.jpg`，优先用仓库内封面
6. 缓存到本地公共目录

这意味着“上传云端精选”本质上不是上传到 AFL CANVAS 服务端，而是：

- 维护 GitHub 上的精选内容仓库
- AFL CANVAS 只是去读取它

---

## 9. 为什么这套方式很适合你

因为你的要求本质上是：

- 我想在 IDE 里维护
- 我不想再做一个后台管理系统
- 我希望新增精选像维护素材一样直接

这套 GitHub 内容仓库的方式刚好满足：

- 你可以直接在 IDE 新建文件夹和 JSON
- 你可以直接拖图片进去
- 你可以用 Git 版本管理所有精选内容
- 你可以随时回滚

这其实就是把“云端精选管理”做成了一个 **内容仓库工作流**。

---

## 9.5 软件更新后会不会把预置和用户收藏弄丢

这个问题一定要分开看。

### 1. 内置预置会不会丢

如果内置预置放在：

- `Public_assests/ComfyAppLibrary/...`

那么它属于应用安装内容的一部分。

这意味着：

- 软件更新时，它**会跟着新版本一起被替换**
- 这是正常行为
- 不应该把用户自己的内容放在这里

所以：

- **内置预置不会“无故消失”**
- 但它可能会被新版本更新、替换、增删

这正是你想要的预置行为。

### 2. 用户自己的收藏会不会丢

如果用户收藏放在系统用户目录，例如：

- Windows：`%APPDATA%/AFL Canvas/ComfyAppLibrary`
- 或 Electron 的 `app.getPath('userData')`

那么正常软件更新时：

- **不会因为更新而丢**

这也是当前音频库已经在做的事情。

所以真正的规则是：

- 内置预置：跟包走，可更新
- 用户收藏：跟用户目录走，不应随安装更新被覆盖

### 3. 什么情况下会丢

只有下面这些情况才容易丢：

- 你把用户收藏也错误地放进 `Public_assests`
- 用户卸载时手动勾选“删除用户数据”
- 你后续改了用户目录路径但没做迁移
- 你做了 portable 便携版，并把用户数据也放在程序目录旁边

所以从现在开始，结构上一定要定死：

- 用户收藏、最近使用、用户标签、用户参考图
- 全部放 `userData/AppData`

---

## 9.6 推荐最终分层

建议你后面直接按这套分层落地：

### A. 内置预置层

放在安装包内：

- `Public_assests/ComfyAppLibrary/featured_local/`

用途：

- 你给所有用户预置的 Local Comfy App

### B. 云端精选层

来源是 GitHub 内容仓库：

- 远端仓库维护
- 客户端同步到本地缓存

缓存建议放在用户目录下，避免下次启动重复拉取。

### C. 用户持久层

放在 `userData/AppData`：

- 用户收藏
- 最近使用
- 标签索引
- 用户参考图
- 用户导入的本地 App

---

## 10. 实现层面的建议

为了让这个 workflow 真正顺手，建议在 AFL CANVAS 项目里再约定一个固定配置文件，例如：

```json
{
  "remoteManifestUrl": "https://raw.githubusercontent.com/<owner>/<repo>/<branch>/manifest.json"
}
```

这样以后你只需要改远端仓库内容，不需要改代码。

如果后续你还想更方便一点，还可以在 AFL CANVAS 内部再补一个辅助能力：

- “从本地 `.app.json` 生成精选目录模板”
- “自动生成 `item.json`”

但这属于第二阶段增强，不是第一阶段必需。

---

## 11. 推荐的实际落地结论

### 你问“云端这部分我应该咋上传”

最准确的回答是：

- **不是上传到数据库**
- **而是提交到 GitHub 内容仓库**

### 你问“能不能在 IDE 里维护、添加”

答案是：

- **完全可以**
- 而且这是我最推荐的方式

### 推荐最终方案

- 云端精选：GitHub 内容仓库维护
- 本地收藏：本机公共目录 JSON 维护
- 客户端：拉取 manifest，同步缓存，窗口里展示

---

## 12. 给下一个窗口的执行建议

如果下一个开发窗口要开始实现“云端精选管理 workflow”，建议先做下面这几件事：

1. 定义远端内容仓库结构
2. 约定 `manifest.json` 格式
3. 约定 `item.json` 格式
4. 在 AFL CANVAS 里新增 `remoteManifestUrl` 配置
5. 新建同步逻辑：远端仓库 -> 本地公共缓存

等这条链路通了之后，你就真的可以只在 IDE 里维护精选内容了。
