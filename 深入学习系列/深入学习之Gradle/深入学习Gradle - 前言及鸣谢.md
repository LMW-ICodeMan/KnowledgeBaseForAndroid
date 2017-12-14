# Gradle学习笔记

---

## 学习理由

在Github上面经常可以看到别人发布的开源控件，我们在使用时可以直接在Gradle中使用compile的方法依赖对方的库，觉得很神奇就去看了下库的概念，但是并没有去详细的研究和理解。

在之后的使用中遇到过一些问题：

- 有些依赖直接在app的build.gradle中compile就可以了，有些则需要还需要配置project下的build.gradle
- 当项目的开发人员偷懒，不及时修改文档，这种情况下我们需要如何去知道当前可依赖的最新版本
- 当自己需要自己的控件被别人使用时，不知道怎么去操作

由于遇到了这些问题，发现自己对于仓库以及Gradle的了解程度还远远不够，所以去收集了解了一下网上的资料及相关文档，然后将这一过程总结成档，以便之后参阅。

## 带着问题去学习

- 什么是仓库？

- 怎么创建仓库？

- 什么是Gradle?

- 怎么使用Gradle？

上面是我这次学习主要希望了解和解决的一些问题，本文的主要目的是对Gradle的学习，而我们在使用Gradle的时候，最频繁的操作就是依赖别人的库，所以在学习Gradle之前首先对仓库的概念及使用进行了学习。


## 目录介绍

## 如果喜欢

如果你喜欢这篇文章，你可以在我的主页上帮我点赞

> [个人网站：www.icodeman.com]{http://www.icodeman.com}

> [GitHub : KnowledgeBaseForAndroid]{https://github.com/LMW-ICodeMan/KnowledgeBaseForAndroid}

> [CSDN : ICodeMan](http://blog.csdn.net/codetraveller)

## 鸣谢

[跟我学Gradle(文集)](http://www.jianshu.com/nb/3906477)
