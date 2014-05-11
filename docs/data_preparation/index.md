---
layout: page
---


**[Previous Section](../introduction/troubleshooting.md)** | **[Index](../../index.md)** | **[Next Section](training_versus_prediction.md)**

Data Preparation
=====

R is a language that was developed from the start with data preparation in mind.
Everyone who applies machine learning in practice eventually comes to the same realization:
the greatest amount of time is spent not on modeling and parameter tuning but on
cleaning and manipulating data. This will be true for us as well. Like the Karate Kid
in Mr. Miyagi's first lesson, we must learn to perform the repetitive basics well
before we can move on to our ultimate goal: developing production-ready classifiers.

Browsing through the index of this book, you can immediately tell that over half the content
is about manipulating data. Most treatises on machine learning dive straight into the 
exciting learning algorithms and the pretty validation graphs. However, the 
audience of this text is serious data engineers who care about building maintainable
and scalable prediction systems that interface with a messy world. We do not have the luxury
of static, perfectly formatted data sets that plug and play into our ready algorithms.

We have no choice but to get our hands dirty. In the majority of cases in production systems,
the data being fed to our classifiers will be messy, incomplete, or even inconsistent. It is our
job to catch this before it catches us; because junk in means junk out.

The philosophy of good data preparation
------

When writing code, most people are comfortable in the castles of their IDEs (Integrated Development
Environment) for features like auto-completion and tabbing between files in a project. This is certainly
an improvement over Notepad, but the problem of finding the most efficient development environment was solved
a long time ago: the Unix editors vim and emacs.

If you have not heard of these editors, it is because those who have mastered one of them are rare breeds.
Both vim and emacs are based on a similar philosophy: 99% of the most common editing operations should be 
accessible with one or two keystrokes, and the editor should allow re-mapping keys and easy feature extensions
to support more for those with slightly different needs. Those who have fully mastered vim or emacs can
use muscle memory to perform hundreds of impossibly-efficient operations a minute in a trance-like editing zen:
copying entire paragraphs and replacing select words, moving snippets from several files simultaneously
and pasting the results in another file, writing macros that extract regular expressions from large text
files, all in a few key strokes.

The goal of the tools presented in the rest of this chapter are to replicate these powers for data preparation.
We want to be able to re-map multiple columns, impute whole datasets and filter out unnecessary rows--all in single lines of code. However, we have a terrible restriction that has defeated many data scientists.

**[Previous Section](../introduction/troubleshooting.md)** | **[Index](../../index.md)** | **[Next Section](training_versus_prediction.md)**
