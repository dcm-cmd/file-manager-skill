# 文件管理技能（file-manager-skill）使用手册(以WorkBuddy为例)

> 功能简介：本地系统和远程服务器的文件搜索、上传和下载文件/目录、文件/目录重命名、重复文件检索、文件统计、修改文件/目录权限、共享文件、首次登录绑定服务器后续免密登录
> 适配Agent： WorkBuddy、QoderWork、Traework、KIMIWork、Claude Code
> 适配系统：Windows、Linux

---

## 第 1 章 下载

### 1.1 GitHub 地址

```
https://github.com/dcm-cmd/file-manager-skill
```

### 1.2 三种下载方式（任选一种）

**方式 1：git clone（推荐）**

```bash
git clone https://github.com/dcm-cmd/file-manager-skill.git
```

**方式 2：下载 ZIP**

打开上面那个地址，点绿色 `<> Code` 按钮 → `Download ZIP`，下载后解压。

**方式 3：命令行下压缩包**

```bash
curl -L -o file-manager-skill.zip https://github.com/dcm-cmd/file-manager-skill/archive/refs/heads/main.zip
```

## 第 2 章 装到 WorkBuddy

> 在 WorkBuddy 的「技能」→「从本地添加技能」导入第 1 章下的压缩包，**不用手写任何配置**。

### 2.1 上传技能包

把整个 `file-manager-skill` 文件夹（或先压成 ZIP）按下面几步导进去：

1. 打开 WorkBuddy
2. 左侧栏「专家·技能·连接器」→「技能」右上角+（添加技能）→「上传技能」
3. 导入技能，将第 1 章下载好的skill压缩成ZIP格式拖拽上传
4.  等上传完成，列表里出现「文件管理」就装好了

### 2.2 哪些文件可以不用放进压缩包

整个目录里 WorkBuddy 真正用到的就四样：

- `package.json` — 告诉 WorkBuddy 装 ssh2 依赖，WorkBuddy 会自动装，不用手动跑 `install.js`，也不用改 `mcp.json`
- `SKILL.md` — 技能描述（出现在面板里）
- `mcp` 文件夹里的 `server.js` — MCP 入口
- `scripts` 文件夹 — 核心代码，全部带上

下面这些文件可以不用放进压缩包上传到workbuddy

- `.git` 目录（版本控制数据）
- `.claude` 目录（Claude Code 专用配置）
- `adapters` 目录（其它 Agent 适配示例）
- `config` 目录（配置模板）
- `references` 目录（详细文档）
- `LICENSE` 文件
- `README.md` 文件

注：直接打包整个文件夹也可以（WorkBuddy 会自己挑能用的）

### 2.3 重启 WorkBuddy

装完重启一次 WorkBuddy 让技能生效。


## 第 3 章 在 WorkBuddy 里使用

### 3.1 绑定服务器（仅首次要做一次）

打开 WorkBuddy，在对话框中添加刚上传的技能（file-manager），输入需要绑定的服务器IP，账号和密码，首次绑定以后同一账号的后续登录不用再次输入密码

输入后自动完成：

- 生成 4096 位 RSA 密钥对
- 公钥注入服务器 → 之后再连这台就不用输密码了
- 配置保存到 `~/.file-manager/servers.json`

要绑第二、第三台，把 `你的服务器名` 换成别的再跑一遍就行。


### 3.2 功能使用

直接跟 WorkBuddy 用中文说话就行，例如：

> "把 D 盘的 report.pdf 上传到服务器的XX目录"

WorkBuddy 会自动调用 file-manager 的工具去执行。

### 3.3 能干啥

| 你想做 | 跟 WorkBuddy 说一句 |
|--------|-------------------|
| 上传文件 | "把 xxx 上传到服务器的 XX目录" |
| 下载文件 | "从服务器的XX目录下载到本地" |
| 删文件 / 目录 | "删除服务器的XX文件" |
| 改权限 | "把 XX文件 改成 755" |
| 批量找文件 | "在 ~/logs 下找所有 .log" |
| 跑个命令 | "在服务器上跑 `ls -lah`" |
| 共享给同事 | "把 XX文件/目录共享给 alice，只读" |

更细的提示词模板见第 4 章。

### 3.4 危险操作会停一下问你要确认

下面这几类操作 WorkBuddy 会停下来让你在对话窗输入 `YES` 才执行：

- 递归删除（删除目录）
- 批量删除
- 移动覆盖
- 给容器 `full` 权限

看到提示说 `YES` 就继续，否则直接回车就取消了。

---

## 第 4 章 提示词速查（直接复制用）


### 4.1 上传文件

**你可以这样说**：

```
把 D:\report.pdf 上传到 服务器XX 文件夹
```

**需要告诉他**：本地文件路径 + 远端目录

**✅ 结果**：会告诉你上传成功，文件落在哪、用了多久。

---

### 4.2 下载文件

**你可以这样说**：

```
从 服务器名 的 XX文件/目录 下载到本地 D:\
```

**📋 需要告诉他**：远端文件路径 + 本地保存目录

**✅ 结果**：会告诉你下载完成、文件大小、落地路径。

---

### 4.3 删 / 改 / 移

**删文件**：

```
删除 服务器的XX文件
```

**删目录**（会要二次确认）：


**批量删**（会要二次确认）：

```
清空 服务器 的 ~/tmp 下所有 .tmp 文件
```

**改权限**：

```
把 服务器 的 ~/scripts/run.sh 改成 755
```

```
把 服务器 的 ~/scripts 下所有 .sh 都改成可执行
```

**移动 / 重命名**：

```
把 服务器的  ~/a.txt 改名叫 b.txt
```

```
把 服务器 的 ~/old 整个目录移到 ~/backup
```

---

### 4.4 批量找

**按文件名找**：

```
在 服务器 的 ~/projects 下找所有名字带 test 的 .log
```

**按大小找**：

```
在 服务器 的 ~/logs 下找大于 100MB 的文件
```

**按内容找**：

```
在 服务器 的 ~/src 下找内容包含 TODO 的文件
```

---

### 4.5 共享给同事

**只读分享**：

```
把 服务器 的 ~/reports 共享给 alice，让她只能看不能改
```

**让他可写**：

```
把 服务器 的 ~/draft 共享给 bob，让他可以读也可以写
```

**完全授权**（会要二次确认）：

```
把 服务器 的 ~/project 共享给 tom，让他可以管理整个目录
```

**撤销共享**：

```
撤销 服务器 的 ~/r.pdf 对 alice 的共享
```

**看已有的共享**：

```
看 服务器 上现在有哪些共享
```

### 4.6 跑 SSH 命令（示例）

```
在 服务器 上跑 ls -lah ~/projects
```

```
在 服务器 上跑 df -h 看磁盘
```

```
在 服务器 上跑 docker ps 看容器
```


