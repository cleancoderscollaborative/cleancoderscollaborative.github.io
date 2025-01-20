---
layout: post
title:  "Single Responsibility (One Source of Change)"
date: 2025-01-13 08:00:00 -0500
categories: code projects
author: Joel Mussman
---

# Single Responsiblity

I will start off by saying that Uncle Bob expresses regret at naming this principle.
He has gravitated toward "One Source of Change", or for only making changes to something
because one entity requires those changes.
Craig Larman's GRASP principles of high-cohesion and low-coupling are closely
related to Uncle Bob's single responsibility.  

In one presentation Uncle Bob redefines a *responsibility* as a *person*, which sounds kind
of funny but he really means the module is responsible to only one person.
The thing is, that really isn't true and even his presentation works against that.
The code is responsible to one entity, which could be a group of people who are aligned
with each other.
What the code cannot do is serve conflicting entities at the same time.
All that means is
have multiple pieces of code that may share common stuff, but the different pieces each
support their own entity.

Everybody seems to have a complicated way of saying this, but it really is simple if we do not
approach it from the theory.
Here is what happened in my career, this is what works and why!

## High-Cohesion

I originally came at this aspect of coding with cohesion.
I got that from COBOL and database record design, even before relational databases were available
(look up Indexed-Sequential Access Method).

When you are designing the record layout in a simple database, you do not want to put unrelated
information next to each other.
We learned it makes records too big, and some things do not scale horizontally, like items in a shopping cart.
Exactly how many items will you allow if each one needs its own columns?

We figured out pretty quickly that you should have multiple files with different types of records, and the
application can go to the file containing the information it needs.
That keeps the application from having to wade through excess information.
You could even link things together: the item records can have the index of the shopping cart record in the
other file.
This is where relational databases came from, instead of the application juggling multiple files we
moved the logic into the database engine and created the structured query language!

## On to Code!

COBOL, Fortran, and Basic programs back in the day were pretty monolithic, the whole program was in one file (or deck of punched cards).
When I moved to C, the *compiler* could individually compile source files, and the *linker* would
put everything together in one executable.
C was a *procedural* language.
In a nut-shell that means that it had global variables and functions.
You really should avoid global data as much as possible, pass functions what they need as
parameters. But that is a tale for another day.

We started having the linker put C programs together from multiple libraries of functions.
That promoted code reuse by sharing libraries, but even if we were not sharing we still split
the program up into pieces.
There would be a module with *main*, the entry for the program.
Other modules would handle different aspects of the user interface.
Still other modules would manage the data, and at least one module for data persistence.

A few huge benefits here:
* Reusability/sharing of modules.
* Not everything is at risk of a mistake when a file is modified.
* We created data-hiding by limiting access to modules by the linker (C-language *static*).
* We had subsitutability at the function level, we could dynamically decide which function
should be called in a particular scenario (as long as they accepted the same parameters).

So when we wrote a module, everything in the code for the module was cohesive with everything
else.
If a piece of data or a process was not closely related to what the module did, it did not belong there.

Does this sound familar? Like what you have been told about single responsibility?

## Some Real Code

I have been teaching object-oriented programming for decades, since back when C++ was new.
After a while, I came up with a simple question: what will you put in a bank account?

```python
class BankAccount:

    def __init__(self):

        self.number = ''
        self.balance = 0.0
        self.name = ''
        self.address = ''
        self.telephone_number = ''
```

Forgive me, I skipped all the property definitions and constraints and whatnot.
What is important is what is wrong with this class?

If you still haven't figured it out, let me ask three questions:
* What happens if their are multiple account holders?
* What happens when someone has multiple accounts?
* When do you use the holder information along with the balance?
The only place I can think of is the printed statement, and that is not an argument for the
inmformation to be cohesive.

Think about that for a moment:
* If we add multiple holders to one account, how many do we allow?
This is horizontal scaling and 
one of the truths of programming is someone will always need exactly one more.
* If a holder has multiple accounts, their information is duplicated in multiple places.
    * Waste of resources
    * Waste of time
    * Guaranteed that at least one place will have incorrect values.

Cohesion is the red-flag to break that class apart:

```python
class BankAccount:

    def __init__(self):

        self.number = ''
        self.account_holders = []
        self.balance = 0.0

class Customer:

    def __init__(self):

        self.id_ = ''
        self.accounts = []
        self.name = ''
        self.address = ''
        self.telephone_number = ''
```

And if you were wondering, the normal forms (rules) in relational database require tables to be
split for exactly the same reasons.

## Low-Coupling

I mentioned at the beginning that Larman's coupling had a bearing too.
In C we can pass function-pointers around, a reference to where a function is in memory.
You do not have to hard-wire client code to use a particular version of a function (you can),
you could pass the client the pointer to the function you want it to use in a particular scenario.

A lot of our functions were using global data.
We learned quickly that if one version of the function was doing stuff another did not and the
client code depended on that happening, then we were in trouble.
It seemed that if one function did it, then the others should too.
If they were not all doing it, why?
Perhaps it did not belong in the function at all?
Maybe something else should be providing that.

This is the basis of *low-coupling*.
If the client code depends on a result in one function and is in trouble when
another substituted function does not provide it, the dependency is too tightly coupled.

The same thing happens with OOP too.
When the client code depends on a specific feature in a subclass (the concrete implemenation),
the super-class interface (the abstraction) can no longer be used.

```python
class CommercialBankAccount(BankAccount):

    def __init__(self)
        
        super().__init__()
        self.fein = ''

def post_taxes(accounts):

    for account in acounts:

        post_tax(acount.fein)   # dependent on all accounts being type CommercialBankAccount
```
And, just consider this: who does the *federal employer identification number* belong to?
The account?
The customer?
Maybe we need a third entity: the business?

This is also the basis of the *Dependency Inversion Principle*
but that also is a tale for another day.

## Single Responsibility (or One Source of Change)

High-cohesion and low-coupling have a huge bearing on the idea of single responsibility.
If client code is too tightly coupled to the subclass, you have to wonder if what it is
dependent on in the subclass really belongs there.
Maybe it belongs someplace else.

Did the dependency get added for one client or multiple clients?
This is where I do differ from Bob a bit!
There may be one principal client for a piece of code, but there can also be other clients.

My rule is:
If the principal client or all the clients that use the code require the functionality,
and the functionality is cohesive, then the functionality probably is where it belongs.
But if it is not required by the principal client, or all the other clients, then it probably
does not belong here.

Remember: the solution to most problems in OOP is to create another class!

## Conclusion

My goal is to help you learn a simple rule that you can measure what you are coding against.
The rule does not necessarily  handle all aspects of the principle.
Fundamentally, to me *single responsibility* means that anything at any level should be about
managaging one thing, it should be *cohesive*.
A program should be about managing one thing: a document, a shopping cart.
A module, a method or function, even a statement should be focused doing one thing.

```C
// C-language code
int x = 5;
int y = ++x / 2;    // Violation: two things happening! x is incremented as well.
```

Uncle Bob said exactly the same thing in an interview once.
That made me extremely happy, because this is what I was teaching successful programmers to do,
and when a name was put to it I extended it to all levels because it fits!
But I felt a bit guilty about appropriating it that way, until I heard Uncle Bob say it too.

If you want every last nuance about the prinicple,
Uncle Bob can go on and on in multiple books and videos and opine that he named it wrong.
Craig Larman's book ([Applying UML and Patterns](https://www.amazon.com/Applying-UML-Patterns-Introduction-Object-Oriented/dp/0131489062)) is over 700 pages long,
lots to get into there.
So if you want to debate the theory edge cases, awesome!
We can do that another day.

I just need you to leave with something simple that covers 90%
of what you need to know to write better code.
