# General Principles

## Guiding principles

* The vast majority of engineering time is spent trying to understand
  existing code and then modify it rather than to write new code. Over
  the lifetime of a piece of code it will be read many more times than
  it will be modified.  Thus it makes sense to do some optimization for readability
  and understandability even if it might take some extra time to write the
  code.
* Having basic conventions for code within a team is more important than what
  those conventions are. Most preferences about code are somewhat
  arbitrary so it is more important to pick something than to try to find
  the global maximum of all conventions.
* Organizing code around structural patterns not only makes reading and
  understanding the code faster across the team but also provides a
  shared language to give feedback about the design and organization of
  a specific piece of code.


## Engineering values

Values are the factors you use to make a decision about a
problem you have not faced before.  They express what you as a team or
as an individual find important in your work.  Agreeing upon certain
values as a team is critical because it makes it clear what is and is
not important and provides a way for people to offer grounded feedback
to others.

## SOLID

SOLID is a set of design principles that guide the development of all
object oriented software.  Applying them can help you write code that
is easier to understand and maintain and more importantly easy for other
people to read and follow.

### Links

- [SOLID](https://en.wikipedia.org/wiki/SOLID_(object-oriented_design))
- [Single responsibility principle](https://en.wikipedia.org/wiki/Single_responsibility_principle)
- [Open/Closed principle](https://en.wikipedia.org/wiki/Open/closed_principle)
- [Liskov substitution principle](https://en.wikipedia.org/wiki/Liskov_substitution_principle)
- [Interface segregation principle](https://en.wikipedia.org/wiki/Interface_segregation_principle)
- [Dependency inversion principle](https://en.wikipedia.org/wiki/Dependency_inversion_principle)


## Knowledge sharing

Knowledge sharing is critical among team members so as to avoid single
points of failure.  There are two types of ways to share knowledge:

1. Direct - As in walking someone through some code, having them
   review a pull request, writing documentation.
2. Indirect - As in make the code easy enough to read that other engineers
   can understand it without significant effort.

The value of the indirect is that it lessens the need for the direct
since people can learn themselves with a high level explanation and code
they can dig into.  Both are important for productivity since
(especially at a small company) engineers may need to jump into
different parts of the codebase relatively frequently.
