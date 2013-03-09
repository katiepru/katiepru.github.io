---
layout: post
title: "Prefix Tree Implementation in C"
date: 2013-03-09 20:45
comments: true
categories: 
---

What is a Prefix Tree?
======================

Basic Concept
-------------

A prefix tree is a data structure typically used for storing and sorting
large lists of words in an efficient manner. A prefix tree is very similar
to a standard binary tree, only with each node having 26 children instead of
just 2. A prefix tree starts with a root node, which does not contain data
relevant to any word, but rather acts as a starting point. If we are using
the standard English alphabet, this root node will have pointers to 26 children,
one for each letter of the alphabet. Each of these nodes will also have 26 
children, and so on.

Node Contents
-------------

Most implementations of prefix trees share a basic Node structure used for 
storing the data:

- **Character**: each node, with the exception of the root node, represents a
letter in the alphabet. The root node can be any character that is not in the
alphabet. I personally use a space as the root character, as it is most often 
used as a word delimeter.

- **Boolean is_word**: A boolean is needed to tell whether or not a full word
was formed and ended at the current node. Because it is a tree, each node has a
unique path to it from the root node. Simply traversing through the tree and
checking whether these booleans are true is an easy way to obtain all of the 
words that were stored in the tree. Also, the value of the boolean does not 
affect whether or not the node can have children.

- **Pointer Array/Linked List**: 
