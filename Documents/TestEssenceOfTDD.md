# What is TDD?

Test-driven development (TDD) is a software development process that relies on the repetition of a very short development cycle: first an (initially failing) automated test case is written that defines a desired improvement or new function. Then, the minimum amount of code is written to pass that test, and finally one refactors the new code to acceptable standards. - [from wikipedia](https://en.wikipedia.org/wiki/Test-driven_development)

TDD is a kind of rule for software development. _If you are a genius, you don’t need these rules. If you are a dolt, the rules won’t help. -  Kent Beck_

# Essence of TDD

1. TDD is __a development method__, it is not a testing method or about unit testing. If you use TDD as testing method mainly, what you are doing has nothing to do with TDD.
2. TDD concerns not only about external quality but also internal quality. Internal quality of codes decays over time if they are not cleaned periodically. __Refactoring__ is must-do cleaning process even if we had upfront design. Test has noting to do with internal quality but it is prerequisite of refactoring. 
3. Software development is an evolution process instead of constructing as designed. We need upfront preliminary design. However evolving design by source code is more important. We have to do it because software development is learning and practicing process. 

# External and Internal quality

1. External quality is the fitness for purpose of the software. It means all the properties of the software as a product that users can experience and enjoy, e.g. conformity to their expectations, reliability, ease of use and comfort, etc.
2. Internal quality is about the design of the software, everything the software does not do, e.g. concise(or no duplication), simplicity, cohesion, low coupling, clarity, etc. It's the implementation, which the customer never directly sees, and often does not want to spend time or money on. Internal quality mean all the properties of the software as seen by the developers that are desirable in order to facilitate the process of creating a good product.
3. Why we need good internal quality while customer does not want to spend time or money on? The reason is that we do not want increase cost for adding new features or changing existing features in the future which customers want. There is no value to increase internal quality for a software which need not be maintained in the future, if this kind of non-toy software exists. 

[Internal And External Quality](http://c2.com/cgi/wiki?InternalAndExternalQuality)

# Source code is design

This argument comes from [What Is Software Design? By Jack W. Reeves](http://www.developerdotstar.com/mag/articles/PDF/DevDotStar_Reeves_CodeAsDesign.pdf). A specification says what to do, a design document says how to do it.  Design document needs some tools to be described, e.g. UML diagrams, block diagrams, natural languages, programming languages. When the document is detailed enough, complete enough, and unambiguous enough that it can be interpreted mechanistically, whether by a computer or by an assembly line worker, then you have a design document. What tools to be use in design document doesn't matter, these are all communication tools. However, we need several level of abstractions to describe design, e.g. architectures(top level design), internal building blocks(class design), and implementations(low level design), in order to communicate better among designers. 

Designing is a process, we need many creative works in implementation and refactoring after detailed design, it has not been completed until you have written and tested the code. The only way we validate a software design is by building it and testing it. There is no silver bullet, and no “right way” to do design. 

# Indivisuals are more important

TDD is a kind of design process. A process, which gives steps how to work, is important. However a process per se could not make good design which needs creative work. Executors(developers) of process produce creative work result, design document. These indivisuals need disciplines and practices, developers need to know what is good software design, e.g. software design principles, design patterns, and what is bad software design, e.g. code smells, anti-patterns. What we are doing is to create big combination of sequences, conditionals and loops, not more and not less. Software design is all about how to decrease the complexity of the combinations. Indivisuals need training and learning by practice forever.

# What is unit?

When talking about unit, the main confusion comes from the understanding of TDD and unit test. A test case in TDD (Let's say it TDD test from here on) to express the application functionality that needs to be implemented. A TDD test matters behavior, a unit test case matters code structure. TDD is used to develop a unit of work in every red-green-refactoring cycle. On the other hand, unit test is used to find bugs in a basic unit of source code, which is a class in object-oriented applications. TDD is all about development of software, unit test is all about finding bugs. 

The difficulty of determining a unit of work is that there is no such kind of precise definition like a class. A TDD test can deal with as little as a function or as much as several classes. A unit of work can be determine by considering following aspects.

 - When a TDD test fails, it should be easier to detect the position of defect from the source code. 
 - When source codes are changed by refactoring, TDD tests should be changed as little as possible, i.e. TDD test  should not depend on application structure as much as possible.

The smaller a unit of work is, the easier position of defect can be detected . The bigger a unit of work is, the less a TDD test depends on application structure. These two aspects is unity of opposites. Like any other science and engineering, software development has full of contradictions, and permeated with law of "the unity of opposites" for contradiction. And how easy is easy, how little is little, how much is much. We need some trade-off when writing TDD test cases.  

# Survery of some test related techniques

| Technique   | 2011 | 2012 | 2013 | 2014 | 2015 |
|:-----------:|:----:|:----:|:----:|:----:|:----:|
| TDD         | 38%  | 40%  | 38%  | 34%  | 33%  |
| BDD         | 9%   | 10%  | 12%  | 9%   | 10%  |
| Refactoring | 48%  | 48%  | 47%  | 36%  | 37%  |
| Unit Test   | 70%  | 74%  | 72%  | 64%  | 63%  |

[From VersionOne](https://www.versionone.com/)


