---
layout: page
---


**[Previous Section](../index.html)** | **[Index](../index.html)** | **[Next Section](setting_up_your_development_environment.html)**

What is a production-level classifier?
========================================================

This book is about developing production-ready machine learning systems. 

Or at least those are the buzzwords. What do these terms really mean? When we say production-ready
we mean software that:

- we must be confident is correct
- is running in real-time on live data
- can easily be maintained by someone who first comes across the code base
- needs to have a deployment process, a way to turn code that is in development to code that is live
- is flexible enough and does not box us in as to allow quick development of new features
- can scale to large traffic volumes

Even from the very first requirement, we have already departed from the typical book on machine learning
algorithms. Running your algorithms by hand and observing the results should not satisfy us. The world
of software developers has learned this lesson painfully and eventually came up with the correct solution:
testing. By writing **unit tests**--code that tests our code--we can scale our projects to arbitrarily large
sizes; if we are ever worried that changing complex functions messes up other dependencies we have
forgotten about or another developer created, good testing will catch this before it breaks any live system.

Deployment and scaling will only be minimally covered in this book. However, constantly keeping in mind
that the code we write will have to be running 24/7 with heavy utilization will guide the design
choices we make.

To keep the projects we write maintainable and flexible, we will adopt the same philosophy as that
of the most successful set of tools ever written: the Unix tool chain. Every important piece of software
in the world, from internet servers to traffic routing to medical diagnostics to military applications
runs on software descended from the same toolset--that of the Linux and Unix operating systems.

The main advantage Unix has is that every program written for its core has to do only one job,
and that job well. The connecting strand is a piping notation that combines the tools's results:
`grep "some example" some/file.txt | wc -l > number_examples.txt` will run the `grep` program to find
lines containing `"some example"` in `some/file.txt`. It passes its results to `wc`, which counts the
number of such lines, and `>` stores this number in `number_examples.txt`. This is a relatively simple example,
but it should be kept in mind that almost all the operations one needs to manipulate and run complex programs
on the Linux operating system can be accomplished with these tools.

Our goal throughout this book is to develop such a set of tools for writing production-ready learning systems.

What is a learning system?
------

Crack open any machine learning textbook and you will find a slew of fancy mathematics written by
smart statisticians to take some data about the past and predict what it will look like in the future.

Although writing a program that accomplishes such a goal will be the over-arching theme, our focus will
be on taking these algorithms and making them *available to others*--the "systems" part of 
"learning systems."

Another domain that has drawn inspiration from the Unix philosophy is the ecosystem of modern web development.
When you surf sites on the internet, coordinated requests are constantly being made between a great
network of servers talking to each other for information. We will try to take the algorithms we use
to train classifiers from our data sets and make them black boxes. The goal is to be able to create a
collection of live programs constantly receiving new data, using it to re-train our models, and wrapping 
everything in a web-accessible interface that other parts of our infrastructure can use to make predictions
with our models.

**[Previous Section](../index.html)** | **[Index](../index.html)** | **[Next Section](setting_up_your_development_environment.html)**
