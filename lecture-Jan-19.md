# Lecture: Course Introduction

Course Canvas page: 

    https://iu.instructure.com/courses/1946118

## Introductions

* Jeremy Siek
* Chao-Hong Chen

## Goals

Learn how to

* create algorithms
  to solve computational problems and
  implement them in Java

* analyze algorithms
  How much time?
  How much space?

## How to succeed in this course

Attend the lectures and ask questions, either on Zoom
  or you can also post your question on Slack.
  If you like someone else's question, give it a thumbs up.

Read the assigned chapters from the textbook.
(On Canvas, scroll down to the Schedule of Topics.)
Complete the assigned exercises.

There will be overlap between the textbook and the lecture. 
That is good.
Humans learn through repetition.

Attend the labs and complete the programming assignments.  When you
get stuck, ask for help! Talk to your classmates, Chao-Hong, and me.

Test your code thoroughly.

Use a debugger to find bugs and improve your understanding of Java.

IntelliJ IDE Community Edition (CE), JetBrains


## Structure of the course

Weekly programming assignment to implement an algorithm. 
Submit to the autograder:
  
        https://autograder.sice.indiana.edu/web/course/20

Homework exercises every few weeks.
Submit on Canvas.

Quiz every two weeks (on Canvas).

Midterm exam as a Canvas quiz in week 8.

Final exam as a Canvas quiz in the last week.

The programming assignments and exercises can be done collaboratively
but every student must turn in the assignment.

The quizzes and exams are to be done individually, without any aids
(closed book), and will be proctored using Respondus Monitor.

## Grading

* Assignments and exercises: 20%
* Quizzes: 20%
* Midterm exam: 30%
* Final exam: 30%


## Resources

* Zoom for lectures. (Also in-person outdoors later in Spring.)

* Slack for questions and discussion.

* IntelliJ for developing Java programs.

* Textbook: Introduction to Algorithms 3rd Ed. (CLRS)


## What is an algorithm?

A detailed recipe for solving a computational problem.

A *computational problem* is a question about data of a specified form.
For example, what is the minimum number in an array of integers?

An *instance* of a problem is actual data of the specified form. 
For example, the array `[4,3,7,2,9]`.

Given any instance of the problem, the recipe must:
1. always finish 
2. each step in the recipe must be precisely defined
  and only take a finite amount of time
  (so a person or computer could do it)
3. produce output that answers the question

We refer to 1. as "termination" and 3. as "correctness".

The author of an algorithm must give a precise argument that explains
why the algorithm always terminates.

Similarly, the author must give an argument for why it always returns
a correct answser.


## Example computational problems

* Human genome sequence assembly
* Shortest distances among destinations (google map)
* Analyzing social networks
* Internet search 
* Scheduling of manufacturing tasks & supply chain.


## Algorithm Analysis

There are often multiple algorithms for the same problem.

Which one is the best? That is, which one takes the least amount of
time and/or space?

Complication: the amount of time and space used by an algorithm often
depends on the input data. 

We usual focus on the worst-case scenario and come up with a function
of input size to time.

Furthermore, we only care about large differences in time as the input
size grows, so we study the asymptotic behavior of the functions and
ignore constant factors.

Let `n` be the size of the input and `T(n)` be the worse-case time.

* logarithmic:

        T(n) = log(n)
        T(n) = 2 log(n) + 5
        
* linear: 

        T(n) = n + 20
        T(n) = 1.5 n
        T(n) = 3 n + 2
        T(n) = 20 n

* quadratic

        T(n) = n^2 + n + 100
        T(n) = 10 n^2

* exponential

        T(n) = 2^n
        T(n) = 3^n


# Minimum Number Problem

## Problem

Given a non-empty array of numbers, which number is the smallest?

## Algorithm

Scan through all the elements from left to right and keep track of
which one is the smallest so-far.

    static public int minimum(int A[]) {
        assert(0 < A.length);
        int min = A[0];
        int i = 1;
        while (i != A.length) {
            if (A[i] < min) {
                min = A[i];
            }
            i = i + 1;
        }
        return min;
    }


## Student Group Activity:  alternative algorithms (5 minutes)

Login to Google Drive with your IU account and go to the Google
document given in today's announcement on our Canvas page.

Share your name and where you are from with the other students
in your group. Choose a person to record your answers.

Think of a different algorithm for computing the minimum number in an
array? Is it faster or slower than the `minimum` function?
