# 什么是测试驱动开发

测试驱动开发(TDD)是一种软件开发过程，它重复执行很短的开发周期：首先编写失败的自动测试用例，它定义期望的改进或新特性。其次，编写最少限度的代码使测试用例通过。最后，重构新代码使它满足可接受的标准。 - [来自wiki](https://en.wikipedia.org/wiki/Test-driven_development)

TDD是一种软件开发的规则。_如果你是个天才，无需这些规则。如果你是个傻瓜，这些规则没有多大帮助。 -  Kent Beck_

# TDD的本质

1. TDD是__一种开发方法__, 它并不是一种测试方法或单元测试。如果认为TDD主要是测试方法，那么这和TDD毫无相关。
2. TDD不仅关心软件的外部质量，而且更关心内部质量。随着软件的维护，如果不随时清理软件，它的内部质量或多或少地腐化。因而，即使我们进行了前期设计，也需要__重构__的途径来整理代码。测试本和内部质量不相干，但它是重构的前提。 
3. 软件开发是进化过程，而不是根据既有设计构造代码的过程。我们需要前期初步设计，但基于代码的进化设计更为重要。这是因为软件开发是学习和实践的过程。 

# 外部和内部质量

1. 外部质量是指满足软件目的的程度。客户使用软件产品时所能体验到的所有特性属于外部质量。例如，满足用户期望的程度，可靠性，易用性等。
2. 内部质量是关于软件设计的事情，软件所不做的特性就是内部质量。例如，无重复代码，简单性，内聚，耦合，易理解性等。这些是客户无法直接看到的实现问题，而且他们常常不想对这些花费时间和金钱。内部质量是软件开发人员为了顺利推行软件过程所需要的所有特性。
3. 为什么我们需要提高客户所不关心的内部质量？其理由是当客户将来增加功能或修改已有功能时，我们不想提高价格。对于将来不需进行维护的软件，没有必要提高内部质量。除非玩具软件，我们怀疑存在这种不需要维护的软件。 

[参见](http://c2.com/cgi/wiki?InternalAndExternalQuality)

# 源代码是设计

本节论述来自[What Is Software Design? By Jack W. Reeves](http://www.developerdotstar.com/mag/articles/PDF/DevDotStar_Reeves_CodeAsDesign.pdf)。 根据软件开发人员的共识，规格描述做什么，设计文档描述如何做。我们需要一些工具来描述设计文档，这些工具可能是UML图，自然语言，程序设计语言等。当设计文档足够详细，足够完整并且无二义性，以便由计算机或生产线的工人可机械性地解释它们时，我们认为完成了设计。描述文档的工具只是用于沟通，采用何种工具不很重要。从这个角度看，代码是最终的设计文档。然而，为了易于沟通，我们常常需要多种抽象级别来描述设计。例如，体系结构（顶层设计），内部构件（类设计）和实现（底层设计）。

设计是一个过程，从详细设计我们仍需要很多创造性的工作来完成编码和重构，只有编写完所有代码及其测试代码，设计才算完成。确认软件设计的唯一途径是构造它并对它进行测试，并不存在所谓的银弹，也不存在“最有效的设计方法”。 

# 源代码并不是全部

编写代码之前，我们需要比源代码具有更高一级抽象级的设计文档，它用于描述系统的整体框架，以及部件之间的动态的相互作用。 体系结构设计或多或少是一种设计规格。但是，内部构件的设计文档(详细设计)并不都是设计规格，而是思考或沟通的一种工具。开始编码之前，这种文档可以是__非正式的__，它们需要演进。进行编码时，详细设计的作用是一种编码的指导原则。编码完成后，详细设计文档的作用是帮助维护人员更有效地理解代码。虽然源代码可认为是最终设计文档，但是由于其抽象级别仍然较低，所以需要通过多种辅助性文档来帮助开发人员理解既有代码。抽象是处理复杂问题的最有效的方法之一。高抽象级的文档更有效地帮助我们理解代码。 

# 开发人员更为重要

TDD是一种设计过程。过程确定工作步骤，它很重要。 但过程本身并不能产出好设计，好设计需要创造性的工作。过程的执行者（即开发人员）产出创造性的工作成果，也就是设计文档。这些开发人员需要修养和实践。他们需要知道什么是好的设计，例如软件设计原则，设计模式等。同时，也需要知道什么是不好的设计，例如代码味道，反模式等。说白了，软件开发是在构造一种大组合，它由顺序，分支和循环组成，不会多，也不会少。目前，我们尚无法摆脱可计算的范围。软件设计的根本就是如何降低这种组合的复杂性。为此，开发人员需要不断培训和学习。

# 什么是单元

当谈论什么是单元时，常常混淆TDD和单元测试。TDD中的测试用例（这里我们称它为TDD测试）描述我们将要实现的应用程序的功能。TDD测试关心行为，而单元测试关心代码的结构。TDD的每次红-绿-重构开发周期将产出单元工作（unit of work）。另一方面，单元测试用于发现隐藏于源代码的Bug，在面向对象中类是基本单元。再次强调，TDD是关于软件开发的过程，单元测试是关于发现Bug的过程。 

确定单元工作的困难在于并不存在类似于类的精确定义。TDD测试涉及到的代码范围可以小到一个函数，也可以大到若干个类。确定一个单元工作时，需要考虑如下因素。

 - 当TDD测试失败后，在源代码中，我们需要易于定位Bug的准确位置。
 - 采用重构修改源代码时，TDD测试尽量不要受到影响，即不需要修改它们。换句话说，TDD测试尽可能独立于应用程序的结构。

通常，单元工作越小，越易于确定Bug的位置；单元工作越大，越独立于应用程序的结构。这两个因素是对立统一的。和其它科学和工程一样，软件开发也充满着各种矛盾，无处不在对立统一。另外，很难说如何容易才容易，如何小才小，如何独立才独立。编写TDD测试时，我们需要不同程度的折中。

# TDD和BDD本质上相同的

TDD和BDD(_行为驱动开发_)本质上是相同的开发方法。在这种开发方法中，编写代码之前首先描述__单位工作__ 的行为，其次编码实现该行为，然后重构相应代码。 从这个意义上讲，这两种开发方法都是行为驱动的开发方法。广义上，BDD是TDD的别名。不管TDD，还是BDD本质上截然不同于测试。测试只是这种开发方法的副产品。但是，TDD所用的词汇”__单元测试__”误导着许多开发人员，导致编写测试用例来描述函数/方法或类如何工作。这种脆弱的测试用例常常仅仅用于确认程序单元所做的处理是正确运行的。同时，由于这种测试用例过于依赖于程序代码结构，代码的重构导致测试用例的修改。

在实际运用中，BDD用于更大一点的单位工作，而TDD用于更小一点的单位工作。BDD通常采用更高一级的语言来描述行为。例如，在Gherkin语言中，可使用规范化的自然语言来表述行为，而TDD采用xUnit框架，它采用开发软件时所使用的程序设计语言。

# 关于测试相关技术的调查报告

| Technique   | 2011 | 2012 | 2013 | 2014 | 2015 |
|:-----------:|:----:|:----:|:----:|:----:|:----:|
| TDD         | 38%  | 40%  | 38%  | 34%  | 33%  |
| BDD         | 9%   | 10%  | 12%  | 9%   | 10%  |
| Refactoring | 48%  | 48%  | 47%  | 36%  | 37%  |
| Unit Test   | 70%  | 74%  | 72%  | 64%  | 63%  |

[From VersionOne](https://www.versionone.com/)


