# Pythno Pull Request (Code Review) Guide

This document is a comprehensive check-list for code reviewers and for the reviewed developers as a pre-check guide before submitting a PR. The document is focused on Python but also refers to documentation, configuration, and inline SQL query practices.

## Table of Contents

1. Code Correctness
2. Submit PR on GitHub
3. Code Design
  3.1. General
  3.2. Python
4. Code Conventions

## Code Correctness

Assignee: reviewer + reviewed developer

1. If you are the reviewer - pull the relevant branches to your local machine.
2. Check all the tests have passed, both on your local machine and both on GitHub. Don’t ever leave tests commented just to pass this phase. Delete the test if it’s unnecessary or fix the broken parts.
3. Make sure all the new code you added is covered with tests. Check that with some code coverage tool. The code review should be done on the tests as well.
4. Make sure with your task reviewer and business owner that you developed the right thing according to the specifications.

## Submit PR on GitHub

Assignee: reviewed developer

Assuming that you already know GIT and that you developed on your own branch and now you are finally ready to merge your work to the main branch. How do you do that?

Step Zero: on PR you merge your work to the main working branch. However, to avoid merge conflicts on the main branch, you should first merge the working branch (say, develop) to your branch. That way, your branch gets updated with other new merges, and you can verify that tests still pass and that your work is valid. If there are conflicts they don’t affect the develop branch but only yours, and you can solve them peacefully. To do so you can run the following cmd/terminal commands:
> git checkout develop
> git pull
> git checkout feature/my_branch
> git merge develop
> git push

Before you keep going, re-check the code correctness steps (pulling develop might break some tests).

Step One: go to the repository on GitHub.

Step Two: go to pull requests at the top bar.

Step three: click on New pull request and set the two branches you would like to merge. Notice, you merge your work to the main/develop/master branch.

Step Four: write some notes and assign at least two code reviewers in the top right panel. 

Finally, you can create the pull request.

## Code Design

Assignee: reviewer + reviewed developer

You are encouraged to review the [Python Style Guide](https://github.com/abotzer/notes/blob/main/Programming/python_style_guide.md) to see the comprehensive list of tips.

### General Design Issues

1. SRP - does every file/method/class have a single clear responsibility? Split them if not.
2. Open/Closed Principle - are there code parts that might require changes in the future? If so, remove them or make them more generic to support that. How much effort will be required to add new functionality to your new code?
3. LSP - If the project migrates to a new solution (DB / Cloud Platform / DS tool), will your code be affected?
4. Looking at the new classes and interfaces that were created, is there a chance that one would like to use only part of the functionality in that class/interface? if so, separate it according to that insight.
4. Is your code testable? Is it written in a way we could use mocks instead of real-time components? Do we use dependency injection where needed?
5. Are there code duplications? (even two) - if so, extract them to their own function.
6. Thinking of possible future changes to your code, how many modules need to be changed to support each change?
7. If we arbitrarily delete some line of code, how many tests will fail (ideally, 1)? If that’s not the case - fix it. How long does it take for that test to fail (ideally, immediately)?
8. Are there magic numbers/strings/constants/file names in your code? Turn them into parameters or move them to a configuration file.
9. Does the code violate the law of diameter? (aka, self.a.b.c.d(params…)).
10. Is there any implicit temporal coupling?
11. Does your code raise exceptions when it needs to? Are they clear and concise?
12. Are there magic numbers/constants in the code itself? Extract them to a single configuration file.
13. Can your code be used for other purposes? Should it?
14. Are the implementation decisions you took are reversible? Are there more subtle alternatives? 
15. Is the new code covered with unit tests? Do the tests handle extreme cases? Try to delete code and see what fails.

### Python specifics

1. Check “is/is not” are used in comparisons to None.
2. Check Exceptions Handling:
3. Minimize try-except clauses as possible. 
4. Do not use mutable objects (such as lists and dictionaries) as default values in a function definition.
5. Make sure the data structures that are used are optimal. For example, if we want to store a set of values and query that set, we better use Set or Dictionary and not a List.
6. Replace if len(x) == 0 with if not x (Empty sequences are evaluated to False).
7. For and while loops are typically bad practice - use list comprehension instead. However, don’t nest 2 (not to mention 2+) comprehensions together. It’s not readable.
8. Use decorators - @abstractmethod, @overrides
9. Avoid using the + and += operators to accumulate a string within a loop.
10. If a class is abstract, explicitly inherit from ABC.
11. Avoid nested code. No more than two tiers of if statements. Prefer code extraction.
12. Make sure configuration is set for develop/master mode.

## Code Conventions

Assignee: reviewer + reviewed developer

1. Do all variables have indicative names?
2. Do all variables, classes, file names, etc. satisfy naming conventions?
3. Does every method, class, and module include docstrings in the right format?
4. Are the imports separated in the right order? Are there any redundant imports?
5. Are there too long functions? Split to submethods if so.
6. Are the tests’ names indicative? Do you understand what each test checks?
7. Did you lint your code?
8. Remove prints, commented code, or any other debugging leftovers.
9. Don’t ever iterate over range(some_list) and then access the list with some_list[i]. Just iterate over the list.
10. Use built-in functions if possible.
