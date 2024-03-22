# 示例道场

这个仓库展示了一个示例道场。

道场由[dojo.yml](./dojo.yml)定义。

它包含两个模块，`hello`和`world`。

模块`hello`包含挑战`apple`和`banana`。

模块`world`包含挑战`earth`、`mars`和`venus`。

每个挑战演示了不同的挑战定义功能，功能复杂性逐渐增加。

查看每个挑战的README以获取更多信息：
- [apple](./hello/apple)
- [banana](./hello/banana)
- [earth](./world/earth)
- [mars](./world/mars)
- [venus](./world/venus)

## `dojo.yml`的YAML结构

### Dojo

顶层对象是`Dojo`。它由六个属性组成：

- `id`：**必需**。Dojo的唯一标识符。
- `name`：**必需**。Dojo的显示名称。
- `type`：**可选**。此字段可以取值`welcome`、`course`、`topic`、`example`、`hidden`、`beginner`、`intermediate`、`advanced`。`course`将其放在“课程”部分。`topic`将Dojo放在“主题”部分。`hidden`意味着Dojo不会被列出（但仍然可以访问）。`beginner`将dojo放在“初级者”部分，其他以此类推，如果省略type字段或包含除这八个之外的值，则Dojo将出现在“更多”部分。
- `password`：**可选**。用户加入Dojo需要的密码。如果省略，则任何人都可以加入Dojo。
- `modules`：**必需**。`Module`对象的数组，`module`的参数，参考module部分。
- `award`:**可选**。子参数为`belt`,`emoji` ,内容为svg/png的文件名，`belt`的文件存放路径为：`/dojo_theme/static/img/belts/`。`emoji`的文件存放路径为：`/dojo_theme/static/img/dojo/`。

使用样例：
```
id: your_dojo_id

name: your_dojo_name

type: welcome/course/topic/example/hidden/beginner/intermediate/advanced

award:
  belt: PokeBall
  emoji: PokeBall

modules:
  - id: your_module_id
    icon: Pikachu
```

### Module

`modules`数组中的每个`Module`对象都包含以下属性：

- `id`：**必需**。模块的唯一标识符。
- `name`：**必需**。模块的显示名称。
- `challenges`：**必需**。`challenge`对象的数组,具体子参数参考challenge部分。
- `resources`：**可选**。`resources`参考resources的部分。
- `icon`：**可选**。关于模块的图片信息。`icon`的文件存放路径为：`/dojo_theme/static/img/dojo/`。

使用样例
```
name: your_module_name
challenges:

  - id: vscode
    name: your_challenge_name
    level: 1

resources:
  - name: your_resources_name
    type: lecture
    video: "829819163"
    playlist: "BV1bu4y197Zj"
  
  - name: "未包含的功能介绍"
    type: markdown
    content: |
        your_content_text
```

### resources

`resources`数组中包含以下属性：

- `name`：**必需**。资源的显示名称。
- `type`：**必需**。可选项为`lecture` / `markdown` `lecture`则为视频报告内容，`markdown`则为纯文本内容。
- `video`：**可选**。仅支持`bilibili`，内容为`cid`，如果选择`video`，则需要和playlist对应使用，`playlist`为BV号。
- `playlist`：**可选**。仅支持bilibili，内容为BV号，如果选择playlist，则需要和video对应使用，video为cid。
- `content`：**可选**。内容为显示的文字内容。
- `slides`：**可选**。仅支持github.io的page页面，关于资源的pdf信息。内容为pdf的文件名。

使用样例
```
resources:
  - name: your_resources_name
    type: lecture
    video: "438064585"
    playlist: "BV1mj411M7NZ"
    slides: "SetUID"

  - name: your_resources_name
    type: markdown
    content: |
      - <https://gist.github.com/mudongliang/7b68290c2b4d5da0b7140c8b0e1827d0>

```

### Challenge

模块中`challenges`数组的每个`Challenge`对象都包含以下属性：

- `id`：**必需**。挑战的唯一标识符。
- `name`：**必需**。挑战的显示名称。
- `icon`: **可选**。关于挑战的图片信息。`icon`的文件存放路径为：`/dojo_theme/static/img/dojo/`。
- `level`：**可选**。关于`challenge`的等级显示。

使用样例：
```
challenges:
  - id: level-0-0
    name: your_challenge_name
    level: 1
```
## 挑战编写规则

### Flag

Flag位于`/flag`，只能被`root`读取。
挑战将作为`root`执行。
没有其他真实情况。

不要假设flag有任何结构。
它可能有或没有前缀/后缀。
它可能是也可能不是50字节长。
这些事情会改变，如果你依赖它们，你的challenge会失败。

### 挑战

挑战是[`setuid`](https://en.wikipedia.org/wiki/Setuid)的。
这就是你的挑战将如何作为`root`执行的方式。

这*实际上*意味着什么：
- 进程将以`root`的**有效**用户身份运行。
- 进程将以`hacker`的**实际**用户身份运行。

虽然作为`root`的**有效**用户足以打开flag，但有一些注意事项。
当运行`/bin/sh`（链接到`/bin/dash`）时，如果没有提供`-p`标志，它将立即将**有效**用户设置为**实际**用户。
这意味着**有效**和**实际**用户都将是`hacker`，并且无法访问flag。
这影响了`system`，它最终只是运行`/bin/sh`。

挑战可以通过显式设置**实际**用户为**有效**用户来解决这个问题：
```c
setreuid(geteuid(), -1)