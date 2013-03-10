---
layout: post
title: "Prefix Tree Implementation in C"
date: 2013-03-09 20:45
comments: true
categories: 
---

Basic Concept
-------------

A prefix tree (sometimes called a Trie), is a data structure typically used for 
storing and sorting large lists of words in an efficient manner. A prefix tree 
is very similar to a standard binary tree, only with each node having 26 
children instead of just 2. A prefix tree starts with a root node, which does 
not contain data relevant to any word, but rather acts as a starting point. If 
we are using the standard English alphabet, this root node will have pointers to
26 children, one for each letter of the alphabet. Each of these nodes will also 
have 26 children, and so on.

Node Contents w/ Code
---------------------

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

- **Pointer Array**: We also need a data structure to hold all of the pointers 
to the node's children. Since we are typicalyl working with a finite, relatively
small alphabet, an array will work just fine. Each index in the array can 
represent a letter (i.e. a = 0, b = 1, etc), and the array at each index will 
hold a pointer to a child node.

- **Pointer to Parent**: A pointer to the parent node is useful for backtracking
up the tree in order to determine the current word given a node.

Below is the C code for defining the struct (called a TrieNode):

```c

typedef enum {false, true} bool;

typedef struct TrieNode
{
	char c;
	struct TrieNode *parent;
	struct TrieNode **children;
	bool is_word;
} TrieNode;
```

Properly Creating and Destroying TrieNodes
------------------------------------------

Before we can begin building the prefix tree, first we must be able to correctly
create and destroy TrieNodes without any memory leaks. First, we will deal with 
creating TrieNodes. Below is a function to do so:

```c
struct TrieNode *create_trienode(char c, struct TrieNode *parent)
{
	struct TrieNode *node = malloc(sizeof(struct TrieNode));
	node->c = c;
	node->parent = parent;
	node->children = malloc(26*sizeof(struct TrieNode));
	node->is_word=false;
	return node;
}
```

This function returns a pointer to a TrieNode struct and takes in a character 
and a pointer to the parent node as arguments. It then allocates memory for the
new struct, which we assign to the variable `node`. It then sets the values of 
`char c` and `struct TrieNode *parent` according the the arguments. It then
allocates memory for the children array, assuming there are 26 letters in the 
alphabet. The size of this array can be changed to suit any alphabet. Lastly, we
assume that the node is not a word when it is created, so we set `is_word` to 
false, although this can be changed later in the program if need. We return the
pointer to the node that we allocated memory formed stored in the variable 
`node`.

Now that we can properly create a node, it is also important that we can destroy
it as well without and memory leaks. Below is a function to do that:

```c
void destroy_trienode(struct TrieNode *node)
{
	int i;

	if(node==NULL) 
	{
		return;
	}

	for(i=0; i<26; i++)
	{
		destroy_trienode(node->children[i]);
	}

	free(node->children);
	free(node);
	return;
}
```

There are a few important things happening in this function. First, we can see 
that the function is of type void and takes in a pointer to a TrieNode struct as
an argument. This is because C is a pass-by-value language, so passing in the
actual struct would not affect the struct outside of this function. The first 
thing we need to do in this function is check if the pointer is NULL, as we 
cannot destroy NULL. If it is null, we simply return as there is nothing else to
do. Next, wen need to recursively destroy all of the node's children before we 
can destroy the node as there will be no way to access them once the current 
node is gone.  Lastly, we free the children array and then the node itself.
