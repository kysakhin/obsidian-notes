# Valid anagram (242)

### description
Given two strings `s` and `t`, return `true` if `t` is an anagram of `s`, and `false` otherwise.

### solution
store the characters and their counts in a hashmap. iterate through each character in the string s and string t and store it in their respective hasmaps. return true if both hashmaps are equal
![](https://blog-pictures.vercel.app/anagram1.png)

# Binary search (704)
not gonna type this out

# Flood Fill (733)
### description
You are given an image represented by an `m x n` grid of integers `image`, where `image[i][j]` represents the pixel value of the image. You are also given three integers `sr`, `sc`, and `color`. Your task is to perform a **flood fill** on the image starting from the pixel `image[sr][sc]`.

To perform a **flood fill**:

1. Begin with the starting pixel and change its color to `color`.
2. Perform the same process for each pixel that is **directly adjacent** (pixels that share a side with the original pixel, either horizontally or vertically) and shares the **same color** as the starting pixel.
3. Keep **repeating** this process by checking neighboring pixels of the _updated_ pixels and modifying their color if it matches the original color of the starting pixel.
4. The process **stops** when there are **no more** adjacent pixels of the original color to update.

Return the **modified** image after performing the flood fill.

check out [leetcode](https://leetcode.com/problems/flood-fill/description/) if it is too confusing. 

### solution
we can try to get the size of the image, i.e mxn. we can perform recursion / dfs to search. since constraints are minimal, recursion stack is safe. first we identify the starting pixel. if that pixel is already the color of the target, then we simply return. after that we must check for the neighboring pixels. if it is of the same color then we change that. but also we must also ensure that it doesnt repeat iteration to the same cell. and also handle boundary checks and perform recursion accordingly. \
![](https://blog-pictures.vercel.app/floodfill1.png)\
in our dfs function, we call each function recursively, considering that current cell is not at the boundary.

# lowest common ancestor of a binary tree
### description
Given a binary search tree (BST), find the lowest common ancestor (LCA) node of two given nodes in the BST.

According to the [definition of LCA on Wikipedia](https://en.wikipedia.org/wiki/Lowest_common_ancestor): “The lowest common ancestor is defined between two nodes `p` and `q` as the lowest node in `T` that has both `p` and `q` as descendants (where we allow **a node to be a descendant of itself**).”

### solution
the feature of bst is that the left child is smaller than the root and the right child is greater than the root. we can take use of this simple logic. so we can use recursion for this job. if both the given nodes are lesser than the root node, then we can simply do a recursion call to the left sub-tree. and if both are greater, then perform a recursion call to the right sub-tree
![](https://blog-pictures.vercel.app/lowancest1.png)

# Balanced binary tree (110)
### description
Given a binary tree, determine if it is **height-balanced** .

### solution
according to this problem, a height-balanced tree is a binary tree in which the depth of the two subtrees of every node never differs by more than one. a binary tree is just naturally more recursion friendly. so we are gonna be using recursion for this. having a recursive function that returns an int, we can pretty easily find the depth at any given point. and using a recursive dfs is a lot easier. so we make a new function that returns int and that performs dfs and it accepts Tree node pointers. \
so first we shall implement a base condition. if the given pointer is NULL, then we simply return 0. then we initiate a variable to store the left height for that given node, and call the dfs function with the current_node->left. and the same with right aswell. and right after each function we try to early check if the value of the height ends up as -1, then we just return out of the function with -1. and for each recursion call, we also try to find the absolute difference between the left depth and right depth. if it is greater than 1, then simply return -1. else return 1 + whichever is the greater height (either left or right) \
![](https://blog-pictures.vercel.app/balancedbst1.png)

# 