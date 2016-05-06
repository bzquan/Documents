# TDD would die if ...

# What is TDD?

Test-driven development (TDD) is a software development process that relies on the repetition of a very short development cycle: first an (initially failing) automated test case is written that defines a desired improvement or new function. Then, the minimum amount of code is written to pass that test, and finally one refactors the new code to acceptable standards. - [from wikipedia](https://en.wikipedia.org/wiki/Test-driven_development)

# Essence of TDD

1. TDD is a development method instead of testing method. If you use TDD as testing method mainly, TDD would die. Or what you are doing has nothing to do with TDD. TDD is a kind of rule for software development. __If you are a genius, you don’t need these rules. If you are a dolt, the rules won’t help. -  Kent Beck__
2. TDD would die if you focus external quality mainly and negleting internal quality. Internal quality of codes decays over time if they are not cleaned periodically. Test has noting to do with internal quality design do. Refactoring __must__ do process of cleaning even if we had upfront design. 
 - External quality is the fitness for purpose of the software. It means all the properties of the software as a product that users can experience and enjoy, e.g. conformity to their expectations, reliability, ease of use and comfort, etc.
 - Internal quality is about the design of the software, everything the software does not do, e.g. concision(or no duplication), simplicity, cohesion, low coupling, clarity, etc. It's the implementation, which the customer never directly sees, and often does not want to spend time or money on. Internal quality mean all the properties of the software as seen by the developers that are desirable in order to facilitate the process of creating a good product.
 - Why we need good internal quality while customer does not want to spend time or money on? The reason is that we do not want increase cost for adding new features or changing existing features in the future which customers want. There is no value to increase internal quality for a software which need not be maintained in the future, if this kind of non-toy software exist. 
 - We need better solution instead of best solution if exists. In many aspects of life, quality is something we trade-off for cost.
3. Behavior matters for test case, structure matters for developing.  
4. Software development is evolution process instead of constructing as designed   


# What is unit?

T.B.D.


# Survery of some test related techniques

| Technique   | 2011 | 2012 | 2013 | 2014 | 2015 |
|:-----------:|:----:|:----:|:----:|:----:|:----:|
| TDD         | 38%  | 40%  | 38%  | 34%  | 33%  |
| Refactoring | 48%  | 48%  | 47%  | 36%  | 37%  |
| BDD         | 9%   | 10%  | 12%  | 9%   | 10%  |
| Unit Test   | 70%  | 74%  | 72%  | 64%  | 63%  |

[From VersionOne](https://www.versionone.com/)


