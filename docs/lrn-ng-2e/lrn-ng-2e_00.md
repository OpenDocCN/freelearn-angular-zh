# 前言

自 2010 年以来，我们已经走了很长的路，当时 AngularJS 首次发布。互联网并不是真正用来作为一个应用平台，而是用来呈现静态页面的。当然，随着开发人员开始将其越来越多地视为他们的主要应用平台，这种情况已经发生了改变。能够触达数十亿人的承诺实在太诱人了。这意味着网络必须成熟起来。多年来已经尝试了不同的方法，比如 JSP、GWT、.NET 的 Web Forms 等等，这些方法或多或少地取得了成功。显而易见的是，当 AngularJS 出现时，它被视为救世主。它让每个人都能够快速地使用 JavaScript、CSS、HTML 甚至使用 AJAX 创建应用程序。它仍然是构建小到中型应用程序的有效选择。

某物使用起来越容易，人们就越有可能像番茄酱一样开始不断地添加更多内容并在各处使用它。AngularJS 从来都不是为大型企业应用程序而设计的。互联网不断发展，浏览器中提供了越来越多的功能。有一个想法，希望将所有这些新功能纳入其中，但同时确保 AngularJS 可以用于真正的大型应用程序。做出了一个决定，从头开始创建 Angular，作为 AngularJS 的继任者会更容易。因此，2016 年 9 月 14 日，Angular 的发布版本问世。从那时起，Angular 的主要版本以惊人的速度发布。

我们现在使用的是第 5 版。这并不意味着 Angular 的核心概念已经改变，它们已经被保留下来。在这一过程中引入了某些重大变化，但每个主要版本首先都是为了修复错误，引入新功能，并真正致力于使 Angular 应用程序尽可能快速，占用空间尽可能小。这是在当今以移动为先的世界中值得追求的目标。

本书旨在向读者介绍 Angular 的所有主要方面，并向您展示如何构建小型、中型甚至大型应用程序。您并不需要太多的知识来开始使用 Angular 应用程序，但它有许多层面。随着应用程序规模的增长，您将希望关心如何使其更美观、更快速、更易于维护等等。本书就是以此为出发点编写的。慢慢阅读本书。如果您想读几章并构建一些应用程序，那就去做吧。如果您想直接跳入更高级的功能，那也可以。

我们希望您会像我们写作时一样享受阅读本书。

# 本书内容包括

第一章*，在 Angular 中创建我们的第一个组件*，介绍了语义版本控制。这是一个重要的概念，因此您可以根据自己的需求决定是否采用新版本。本章还向读者介绍了 Angular CLI，并且读者将迈出编写 Angular 应用程序的第一步。

第二章*，IDE 和插件*，向您介绍了最流行的 IDE。还描述了最常见的 Angular 插件和代码片段，以进一步提高开发人员的生产力。

第三章*，介绍 TypeScript*，介绍了 TypeScript，这是编写 Angular 应用程序的选择语言。TypeScript 不仅仅是添加类型。您的代码可以变得更加优雅和安全，使用正确的功能将为您节省大量输入时间。

第四章*，在我们的组件中实现属性和事件*，介绍了如何向组件发送数据以及如何将方法绑定到它们，以便组件能够与上游进行通信。

第五章*，使用管道和指令增强我们的组件*，展示了如何使用管道和指令使您的组件更一致和可重用。

第六章*，使用 Angular 组件构建应用程序*，直接着手于构建真实应用程序的目标。我们讨论了如何思考以及如何使用最常见的结构指令来控制数据的显示方式，并在被 UI 元素操作时如何行为。

第七章，使用 Angular 进行异步数据服务，介绍了 RxJS 库，它不仅帮助我们处理 AJAX，还促进了反应式应用程序模式。在 RxJS 下，所有异步事物都成为一个概念，这引入的可能性是无限的。

第八章，Firebase，解释了 Firebase，这是谷歌的产品，允许您拥有后端作为服务。Firebase 让您专注于构建 Angular 应用程序，同时它会处理几乎所有其他事情。最好的部分是 Firebase 的反应性，这使得像聊天应用程序和协作应用程序一样轻松创建。

第九章，路由，解释了路由的概念，这样您就可以轻松扩展您的应用程序。

第十章，Angular 中的表单，涵盖了处理表单和用户输入的两种主要方式：基于模板的和反应式方法。

第十一章，Angular Material，带您了解 Angular Material，它不仅提供美观的界面，还配备了一堆组件，使得快速组装令人印象深刻的应用程序变得轻而易举。

第十二章，使用 Angular 对组件进行动画处理，介绍了 Angular 如何支持开发人员利用和控制相当高级的动画。

第十三章，Angular 中的单元测试，解释了 Angular 中的单元测试。Angular 团队确实为测试添加了一流的支持，因此您只需很少的代码，就能测试您的所有可能构造。从组件、服务和指令到端到端测试，应有尽有。

附录 A，SystemJS，介绍了 SystemJS，这是一个模块加载器，曾经是设置 Angular 应用程序的唯一方式。这仍然是设置项目的有效方式。本附录将涵盖 SystemJS 的核心部分，并特别关注 Angular 设置部分。

附录 B，使用 Angular 的 Webpack，旨在向开发人员展示如何使用 Webpack 设置您的 Angular 项目。肯定存在一定数量的用户群体，他们希望完全控制 Web 项目的每个方面。如果您是其中之一，那么这个附录就是为您准备的。

# 这本书需要什么

为了真正欣赏这本书，我们假设您对 HTML、CSS 和 JavaScript 有一定程度的了解，以及使用 AJAX 调用服务。我们还假设您对 REST 有一定的了解。现代 Web 应用程序开发已经变得非常艰巨，但我们希望在阅读完本书后，您会觉得对正在发生的事情有更多的了解，并且您也会觉得能够承担起使用 Angular 进行下一个 Web 开发项目的责任。

由于您将花费大部分时间编写 JavaScript、HTML 或 CSS 代码，我们只假设您可以访问一个体面的文本编辑器。您使用的编辑器越成熟，您将获得的帮助就越多，这就是为什么我们在本书中介绍了一些插件和最佳实践，以使您的日常工作变得不那么痛苦。

# 这本书适合谁

这本书适用于没有 Angular 先前知识但在 JavaScript、Node.js、HTML 和 CSS 方面有经验，并且对单页面应用的概念相当熟悉的 Web 开发人员。

# 惯例

在本书中，您将找到一些文本样式，用于区分不同类型的信息。以下是这些样式的一些示例及其含义的解释。文本中的代码单词、数据库表名、文件夹名、文件名、文件扩展名、路径名、虚拟 URL、用户输入和 Twitter 用户名显示如下：“导入响应式`Forms`模块。”

代码块设置如下：

```ts
class AppComponent {
 title:string = 'hello app';
}
```

任何命令行输入或输出都将按照以下方式编写：

```ts
npm install -g @angular/cli
```

**新术语**和**重要单词**以粗体显示。屏幕上看到的单词，例如菜单或对话框中的单词，会在文本中以这种方式出现：“我们点击左侧的数据库菜单选项。”

警告或重要说明会以这种方式出现。提示和技巧会以这种方式出现。
