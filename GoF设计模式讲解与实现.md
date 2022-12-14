---
description: v20220419
---

# GoF设计模式讲解与实现

> 本材料由 [yukiyama](https://iyukiyama.github.io/) 完成。

本材料旨在记录和分享设计模式的学习过程和总结，试图提炼每一种模式的核心要点，并给出经过验证的展现模式工作过程的代码。期望读者在细读每一个模式的讲解后，能够理解每一种模式的工作原理，并能够根据理解独立实现该模式的代码。

学习过程中我主要参考了程杰的《大话设计模式》和[这个网站](https://link.zhihu.com/?target=https%3A//link.juejin.cn/%3Ftarget%3Dhttp%3A%2F%2Fc.biancheng.net%2Fview%2F1317.html)。前者以生活场景化，对话化的形式讲解，在设计模式初学者中广受好评，但无关主旨的背景介绍较多，且无Java示例代码。后者网站虽然用的是Java，但每一个示例都用窗体展示，夹杂了大量无关模式的awt和swing代码，不利于集中理解模式的核心内容。另外，网上许多关于设计模式的文章，总是充斥着很多抽象说明和到处复制粘贴的UML图，对初学者理解模式本身反而造成干扰。例如下面这种抽象描述，实际上是对模式完全理解之后的高度概括，很多文章上来就写这种抽象定义，纯属浪费时间。这种描述对模式识别的理解不但不是必须的，对初学者来说甚至是有害的。

> 状态（State）模式的定义：对有状态的对象，把复杂的“判断逻辑”提取到不同的状态对象中，允许状态对象在其内部状态发生改变时改变其行为。

在本材料中我讲侧重于每一个模式的代码实现而非连篇累牍的文字来讲解，代码示例都已经过验证。每一个模式讲解顺序如下：

* **模式说明**：简要说明模式使用场景和该模式的优点，例如相比简单粗暴方法（通常是if-esle或者单个类封装等）的好处，另外像组合模式里有透明方式和安全方式，单例模式里有饿汉方式和懒汉方式，模板模式里有钩子方法等，也会简要说明这些不同的方式方法。
* **结构**：列明该模式用到的抽象和具体类。
* **代码演示**：可执行测试的该模式的示例代码，关键语句附有注释。

在进入具体模式讲解前，先给出模式一览，对每个模式做简要说明，也给出设计原则一览，可在具体模式讲解中体会各项原则。
