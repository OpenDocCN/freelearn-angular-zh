# 前言

Angular 2 是对之前的 Angular 1.x 框架的一大进步，后者成为了历史上最流行的前端 JavaScript 框架。

这是一个完全重写的、基于 Web 标准和现代 API 构建的最新平台。有了 Angular 2，你可以构建面向浏览器、服务器、移动设备和桌面的 JavaScript 应用，这要归功于 Angular 将视图层与平台核心和服务解耦的架构。

本书将专注于 Angular 的 UI 层：组件。我们将探索丰富的 API 和多种可用选项，用于构建和组合强大的用户界面和视图组件。

# 本书涵盖的内容

第一章，“Angular 2 组件架构”，概述了构建前端应用程序的现有流行架构模式，以及依赖于组合自包含自定义组件的新方法。

第二章，“使用 angular-cli 设置 Angular 2 开发环境”，涵盖了使用 angular-cli 设置开发环境。

第三章，“TypeScript 入门”，涵盖了 TypeScript 语言的基础知识以及你需要了解的内容。

第四章，“构建基本组件”，涵盖了构建基本组件的步骤。

第五章，“构建动态组件”，涵盖了将静态组件转换为动态组件的步骤，使用核心指令和数据绑定。

第六章，“组件通信”，涵盖了使组件彼此通信的不同方法。

第七章，“将所有内容放在一起”，涵盖了构建手风琴组件和组件生命周期。

第八章，“集成第三方组件”，涵盖了从流行的 Bootstrap 库集成工具提示小部件。

第九章 *Angular 2 指令*，介绍了在 Angular 2 中使用指令的用法。

# 本书所需内容

您需要知道如何阅读和编写 JavaScript。其他技术，如 C#或 Java，可能有助于您理解语法，但并非强制性。

需要具备一定的 Web 开发经验和相关技术，如 HTML 和 CSS，因此请确保您对此有所了解。

# 本书适合对象

如果您是具有一定 Angular 经验的前端开发人员，希望了解 Angular 2 组件并使用它们来创建强大的用户界面，那么本书适合您。

这本书也适合想要升级他们的知识和技能的 Angular 1.x 开发人员。

# 约定

在本书中，您将找到一些区分不同信息类型的文本样式。以下是一些示例以及它们的含义解释。

文本中的代码词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 句柄显示如下：“我们可以通过使用`include`指令来包含其他上下文。”

代码块设置如下：

```ts
class Product {
  private id: number;
  private color: string;

  constructor(id:number, color:string) {
    this.id = id;
    this.color = color;
  }
}
```

任何命令行输入或输出都以以下方式编写：

```ts
**$ npm uninstall -g angular-cli**
**$ npm cache clean**

```

**新术语**和**重要单词**以粗体显示。您在屏幕上看到的单词，例如菜单或对话框中的单词，会以这种方式出现在文本中：“单击**下一步**按钮会将您移至下一个屏幕。”

### 注意

警告或重要说明以这样的方式出现在一个框中。

### 提示

提示和技巧看起来像这样。
