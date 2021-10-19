﻿### Introduction
Parsers form an essential part of any programming language. Java programming language offers many open-source parsers that developers can choose the right one based on their requirements. However, it is hard for them to find the right one that fully meets their needs. This leads them to opt to create their custom parsers. Most existing parsers tend not to fulfill developers' needs, and they come with many performance issues.

This article helps the learners and developers understand several tools and libraries used when building custom parsers in Java.

### Table of contents
- [Introduction](#introduction)
- [Prerequisites](#prerequisites)
- [Concept of parser and parsing](#concept-of-parser-and-parsing)
- [Rules and grammars](#rules-and-grammars)
- [Parsing in Java](#parsing-in-java)
- [Popular tools and libraries for generating parsers in Java](#popular-tools-and-libraries-for-generating-parsers-in-Java)
- [Pros and cons of parsing tools and libraries](#pros-and-cons-of-parsing-tools-and-libraries)
- [Wrapping up](#wrapping-up)
- [Further reading](#further-reading)

### Prerequisites
To better understand and follow along the article below the learner should possess:
- Basic understanding of [how parser works](https://www.sciencedirect.com/topics/computer-science/parsing-process)
- Good knowledge in application development in [Java programming language](https://www.javatpoint.com/java-tutorial)
- Good understanding of [regular languages}(https://ecomputernotes.com/compiler-design/regular-expression) and [context free grammars](https://brilliant.org/wiki/context-free-grammars/)

### Concept of parser and parsing
Parsing involves breaking down and analyzing a block of code into smaller code components using some rules. These components are then interpreted, modified, or managed according to the developers’ needs to derive a more profound meaning or understanding.

It requires a particular program to perform the actual parsing called a parser. A parser is made up of two parts, the scanner (tokenizer/lexer) and the parser. Lexer and parser perform tasks in an orderly manner. It means that the lexer will first read the input data and generates a list of tokens. Then the parser reads the tokens generated and outputs the results.

In parsing, the lexer reads input characters from a given alphabet and converts them into a sequence of tokens. Regular languages easily recognize tokens.
Lexer recognizes plain characters or words in a given alphabet, and regular expressions engines can easily implement lexers.
On the other hand, Parser recognizes the structure of the language, making it complex for regular expressions to recognize.

### Rules and grammars
Rules refer to the instructions that lexers or parsers follow when performing parsing, whereas the set of these rules forms a grammar. Grammar consist set of rules that defines how each line of code or construct is composed.

For instance, to set the rule for an `IF statement`, the parser developer has to specify whether the statement should start with the `IF` keyword then the left parenthesis (`(`) should follow. An `expression` should be inserted and then the right parenthesis (`)`), and finally the `statement`.

If the code or text does not follow the rule, the parser will capture that error, displaying a syntax error.

### Parsing in Java
There are three ways of parsing in Java:
- Through using an existing library.
- Through using a tool or library to build a parser.
- By building a custom parser from scratch according to the developers' needs.

#### Use of existing library for parsing
The method is best-known for parsing popular and supported languages such as XML or HTML. A library can be considered best if it supports APIs that can create and modify documents in a given language. In most cases, this is an added feature that cannot be found in many existing parsers. The downside of the existing libraries is that they are not very common, and most of them only support common languages.

#### Building custom Java parser
When developers have a set of unique requirements, this is the best option for them. The reason is that the language they may be trying to parse cannot be parsed using the existing parsers. Also, the parser-generating tools may not have the required features.

The developers may also need to achieve the best possible performance or even integrate the components and opt for this method. The downside of this method is that it is much complex and time-consuming.

Also, the developer needs to possess skills of coding a parser in Java or even hire an experienced developer to work along with hence more costly.

#### Use of existing tools and libraries to build a parser
It is among the popularly used and the best method of generating the parsers in Java. It is considered a flexible and less time-consuming way to build a parser in Java.

The following section will see how we can use existing tools and libraries to generate a parser.

### Popular tools and libraries for generating parsers in Java
Parser generators are the tools designed to code the parsers, whereas parser combinators are the libraries used to create parsers. In Java language, when building a parser, a lexical analyzer or lexers tool is used to analyze regular languages.

The following are different types of tools used in writing a Java parser:

#### JFlex
It is a lexer generator that uses [Deterministic Finite Automata](https://www.javatpoint.com/deterministic-finite-automata) (DFA). The input is matched according to the defined grammar called specs and executes the defined action. [JFlex](https://jflex.de/) can be used as a standalone tool or combined with other parser generators such as CUP or ANTLR since JFlex is also a lexer generator.

The grammar or the spec contains the following parts and is usually separated with a symbol `%%`:
- The usercode that is usually included in the generated class
- The macros/options
- The lexer rules

#### ANTLR
It is a popularly used context-free parser generator in Java. [ANTLR](https://www.antlr.org/) is not limited to only Java since it can also generate parsers for other languages.

ANTLR grammar contains two parts that are, the lexer and the parser rules. The two rules are implicitly defined since lexer rules refer to the rules that start with an uppercase letter, and the parser rules are the ones that start with a lowercase letter. Lexer and parser rules can also be explicitly defined.

#### APG
It is a recursive descent parser that uses [Augmented BNF](https://en.wikipedia.org/wiki/Augmented_Backus%E2%80%93Naur_form) variation. [APG](https://sabnf.com/) made up of more operators such as [syntactic predicates](https://en.wikipedia.org/wiki/Syntactic_predicate) and and custom-made matching functions. An APG grammar is simple, clean and easy to grasp to a new developer. It is popularly known in writing Java, JavaScript, C, and C++ parsers.

#### Coco/R
It uses [attribute grammar](http://melt.cs.umn.edu/silver/tutorial/4_attribute_grammars/) to generate a scanner and a parser that is [recursive-decent](https://www.geeksforgeeks.org/recursive-descent-parser/). In attribute grammar, the semantic rules are defined in an [EBNF](https://tomassetti.me/ebnf/) variant and then interpreted in order to modify the methods of a resulting parser.

It is well documented and offers several grammar samples to provide good knowledge of the tool. It is well known in writing parsers for Java, C++, and C# languages.

#### CUP
It is a Look-Ahead LR ([LALR](https://www.geeksforgeeks.org/lalr-parser-with-examples/)) parser, popularly known in generating parsers for Java. [CUP](http://www2.cs.tum.edu/projects/cup/) can work well with JFlex to generate the right parts of the parser. It also offers an Eclipse plugin used by developers in writing grammar.

#### JavaCC
It generates parsers for Java and its grammar has all the actions and custom code necessary for writing parsers in Java. [JavaCC](https://cs.lmu.edu/~ray/notes/javacc/) grammar file is not as clean as that of ANTLR and most of its parts contain Java source code.

It is well documented and has been used significantly in essential projects such as JavaParser. It does not have much grammar although it comes with its grammar repository.

#### JavaParser
It is a Java parser library that parses Java code. [JavaParser](https://javaparser.org/) offers lexical preservation and [pretty-printing](https://en.wikipedia.org/wiki/Prettyprint). This implies that Java code can be parsed, modified, and printed back with an original format or pretty printed.  It is possible to integrate it with [JavaSymbolSolver](https://github.com/javaparser/javasymbolsolver) and also support all Java versions.

### Pros and cons of parsing tools and libraries
Most of the parsing tools and libraries started as research papers and thesis. The advantage of these tools and libraries they are freely available from their developers.

However, most of these tools do not have proper documentation and user guides, as they have not been developed to be used by a large community. Also, most of the tools stop been maintained by their developers with no updates.

### Wrapping up
Parsers development in Java is a complex topic compared to Java application development. Developers have a huge task when building parsers for Java. Due to this, tools should be utilized to make the development process easier when building custom parsers.

It is advisable for developers first to research the tools they intend to use and carefully inspect their documentation.

### Further reading
Below are the resources to learn more about creating custom parsers for Java:
- [A guide to parsing algorithms](https://www.javacodegeeks.com/2017/09/guide-parsing-algorithms-terminology.html)
- [Understanding JFlex](https://jflex.de/manual.html)
- [Java with ANTLR](https://www.baeldung.com/java-antlr)
- [Learn APG](https://sabnf.com/documentation-2/)
- [The Compiler Generator COCO/R](https://ssw.jku.at/Research/Projects/Coco/)
- [Overview of CUP](http://monash.edu/science-education/2015/resources/conceptual-understanding-procedure/)
- [Introduction to JavaCC](https://web.cs.wpi.edu/~kal/courses/compilers/JAVACC/JavaccPaser.htm)
- [Parsing with JavaParser](https://www.tabnine.com/code/java/methods/japa.parser.JavaParser/parse)
