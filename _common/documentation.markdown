---
layout: post
title:  "Documentation Standard"
date:   2018-09-27 11:43:16 +0200
permalink: /docs/
weight: 60
---

## Doxygen

For code documentation generation we use [Doxygen](http://www.doxygen.nl/).
To generate the documentation locally you can run `gen_docs.sh`. Make sure you have doxygen installed!

It is also possible to visit the version build by the build server [here]().

## Comment Style

We use the **Qt Style** to document our code for doxygen. For a in-depth explenation you can check out the [doxygen documentation](http://www.doxygen.nl/manual/docblocks.html)

Commenting class usages, namespaces, functions and member variables should be done in **the header**. Comments about specific code are allowed to be in both header and source files.

## Laying Out Comments

*The following text is a extract from Code Complete 2e Edition*

Comments done well can greatly enhance a program’s readability; comments done
poorly can actually hurt it. The layout of comments plays a large role in whether they
help or hinder readability

**Indent a comment with its corresponding code** 

Visual indentation is a valuable aid to understanding a program’s logical structure, and good comments don’t interfere with
the visual indentation. For example, what is the logical structure of the routine shown
in the following example?

```vb
For transactionId = 1 To totalTransactions
' get transaction data
	GetTransactionType( transactionType )
	GetTransactionAmount( transactionAmount )

' process transaction based on transaction type
	If transactionType = Transaction_Sale Then
		AcceptCustomerSale( transactionAmount )
	
	Else
		If transactionType = Transaction_CustomerReturn Then
' either process return automatically or get manager approval, if required
			If transactionAmount >= MANAGER_APPROVAL_LEVEL Then
' try to get manager approval and then accept or reject the return
' based on whether approval is granted
 				GetMgrApproval( isTransactionApproved )
 				If ( isTransactionApproved ) Then
 					AcceptCustomerReturn( transactionAmount )
 				Else
 					RejectCustomerReturn( transactionAmount )
 				End If
 			Else

' manager approval not required, so accept return
 				AcceptCustomerReturn( transactionAmount )
			End If
		End If
	End If
Next 
```

In this example, you don't get much of a clue to the logical structure because the comments completely obscure the visual indentation of the code. The following example contains the same code but with properly indendet comments:

```vb
For transactionId = 1 To totalTransactions
	' get transaction data
	GetTransactionType( transactionType )
	GetTransactionAmount( transactionAmount )

	' process transaction based on transaction type
	If transactionType = Transaction_Sale Then
		AcceptCustomerSale( transactionAmount )
	
	Else
		If transactionType = Transaction_CustomerReturn Then
			' either process return automatically or get manager approval, if required
			If transactionAmount >= MANAGER_APPROVAL_LEVEL Then
				' try to get manager approval and then accept or reject the return
				' based on whether approval is granted
 				GetMgrApproval( isTransactionApproved )
 				If ( isTransactionApproved ) Then
 					AcceptCustomerReturn( transactionAmount )
 				Else
 					RejectCustomerReturn( transactionAmount )
 				End If
 			Else

				' manager approval not required, so accept return
 				AcceptCustomerReturn( transactionAmount )
			End If
		End If
	End If
Next 
```

the logical structure is more apparent in the latter example. One study of the effectiveness
of commenting found that the benefit of having comments was not conclusive, and
the author speculated that it was because they “disrupt visual scanning of the program”
(Shneiderman 1980). From these examples, it’s obvious that the style of commenting
strongly influences whether comments are disruptive.

**Set off each comment with one blank line** 

If someone is trying to get an overview
of your program, the most effective way to do it is to read the comments without
reading the code. Setting comments off with blank lines helps a reader scan the code.
An example is shown in the following example:

```java 
// comment zero
CodeStatementZero;
CodeStatementOne;

// comment one
CodeStatementTwo;
CodeStatementThree;
```

## Self Documenting Code

*The following text is a extract from Code Complete 2e Edition*

In contrast to external documentation, internal documentation is found within the
program listing itself. It’s the most detailed kind of documentation, at the sourcestatement
level. Because it’s most closely associated with the code, internal documentation
is also the kind of documentation most likely to remain correct as the
code is modified.

The main contributor to code-level documentation isn’t comments, but good programming
style. Style includes good program structure, use of straightforward and
easily understandable approaches, good variable names, good routine names, use of
named constants instead of literals, clear layout, and minimization of control-flow and
data-structure complexity.

Here’s a code fragment with poor style:

```java
for ( i = 2; i <= num; i++ ) {
meetsCriteria[ i ] = true;
}
for ( i = 2; i <= num / 2; i++ ) {
j = i + i;
while ( j <= num ) { 
meetsCriteria[ j ] = false;
j = j + i;
}
}
for ( i = 2; i <= num; i++ ) {
if ( meetsCriteria[ i ] ) {
System.out.println ( i + " meets criteria." );
}
}
```

What do you think this routine does? It’s unnecessarily cryptic. It’s poorly documented
not because it lacks comments, but because it lacks good programming style.
The variable names are uninformative, and the layout is crude. Here’s the same code
improved—just improving the programming style makes its meaning much clearer:

```java
for ( primeCandidate = 2; primeCandidate <= num; primeCandidate++ ) {
	isPrime[ primeCandidate ] = true;
}

for ( int factor = 2; factor < ( num / 2 ); factor++ ) {
	int factorableNumber = factor + factor;
	while ( factorableNumber <= num ) {
		isPrime[ factorableNumber ] = false;
		factorableNumber = factorableNumber + factor;
	}
}

for ( primeCandidate = 2; primeCandidate <= num; primeCandidate++ ) {
	if ( isPrime[ primeCandidate ] ) {
		System.out.println( primeCandidate + " is prime." );
	}
}
```

Unlike the first piece of code, this one lets you know at first glance that it has something
to do with prime numbers. A second glance reveals that it finds the prime numbers
between 1 and Num. With the first code fragment, it takes more than two glances
just to figure out where the loops end.

The difference between the two code fragments has nothing to do with comments—
neither fragment has any. The second one is much more readable, however, and
approaches the Holy Grail of legibility: self-documenting code. Such code relies on
good programming style to carry the greater part of the documentation burden. In
well-written code, comments are the icing on the readability cake.

### To Comment or Not to Comment

Effective commenting isn’t that time-consuming. Too many comments are as bad as
too few, and you can achieve a middle ground economically.

commenting might be difficult because the words to describe what the program
is doing don’t come easily. That’s usually a sign that you don’t understand what
the program does. The time you spend “commenting” is really time spent understanding
the program better, which is time that needs to be spent regardless of whether you
comment.

Following are guidelines for commenting efficiently:

**Use the Pseudocode Programming Process to reduce commenting time** If you outline
the code in comments before you write it, you win in several ways. When you finish
the code, the comments are done. You don’t have to dedicate time to comments. You
also gain all the design benefits of writing in high-level pseudocode before filling in
the low-level programming-language code.

**Integrate commenting into your development style** The alternative to integrating
commenting into your development style is leaving commenting until the end of the
project, and that has too many disadvantages. It becomes a task in its own right, which
makes it seem like more work than when it’s done a little bit at a time. Commenting
done later takes more time because you have to remember or figure out what the code
is doing instead of just writing down what you’re already thinking about. It’s also less
accurate because you tend to forget assumptions or subtleties in the design.
The common argument against commenting as you go along is “When you’re concentrating
on the code, you shouldn’t break your concentration to write comments.” The
appropriate response is that, if you have to concentrate so hard on writing code that
commenting interrupts your thinking, you need to design in pseudocode first and
then convert the pseudocode to comments. Code that requires that much concentration
is a warning sign.

If your design is hard to code, simplify the design before you worry about comments
or code. If you use pseudocode to clarify your thoughts, coding is straightforward and
the comments are automatic.

### Optimum Amount of Comments

Capers Jones points out that studies at IBM found that a commenting density of one
comment roughly every 10 statements was the density at which clarity seemed to
peak. Fewer comments made the code hard to understand. More comments also
reduced code understandability (Jones 2000).

This kind of research can be abused, and projects sometimes adopt a standard such as
“programs must have one comment at least every five lines.” This standard addresses
the symptom of programmers’ not writing clear code, but it doesn’t address the cause.

If you use the Pseudocode Programming Process effectively, you’ll probably end up with
a comment for every few lines of code. The number of comments, however, will be a side
effect of the process itself. Rather than focusing on the number of comments, focus on
whether each comment is efficient. If the comments describe why the code was written
and meet the other criteria established in this chapter, you’ll have enough comments.
