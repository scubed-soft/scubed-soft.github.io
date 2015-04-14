---
layout: post
title: "Programming Best Practices: Constants"
date: 2015-04-14T13:56:05+08:00
author: Hubert Lee
---
The purpose of using a constant is to eliminate the use of *magic numbers*, also
known as
[unnamed numerical constants](https://en.wikipedia.org/wiki/Magic_number_%28programming%29#Unnamed_numerical_constants).
The problems with using a magic number in your code are manifold:

<!--more-->

1. Future developers reading the code (or yourself after a few weeks) will have
  difficulty identifying what that number represents and why you chose it.
  Introducing a constant gives you a chance to name the concept that number
  represents and also add a comment to explain your reasoning if necessary.
2. Magic numbers increase the opportunity for subtle errors and inconsistencies.
   For example, consider these two Ruby methods:

   ~~~
     def circumference(r)
       2 * 3.1415 * r
     end

     def area(r)
       3.1416 * r * r
     end
   ~~~
   {: .language-ruby}

   In the above code, the mathematical constant `pi` is used twice, but is
   rounded differently each time.
3. When the number is used in multiple places, it is difficult to change
  all instances of it correctly. While a simple find and replace can sometimes
  be sufficient, this is not possible when a single number represents multiple
  concepts. Digging through multiple files to verify if each instance of a
  number should be changed or not is not a scalable coding practice.

Despite the name "magic number," the problems described above are not limited
only to numeric types. These problems affect any hardcoded value in a codebase.

The above problems may seem minor at first, but each of these mistakes compounds
the others. As the size of the development team and the size of the codebase
grows, the problems that these poor practices present become increasingly acute.
Understanding these mistakes is the first towards avoiding them in your code.
