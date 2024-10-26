# Two sum (classic)(1)
### statement
Given an array of integers `nums` and an integer `target`, return indices of the two numbers such that they add up to `target`_.

You may assume that each input would have **_exactly_ one solution**, and you may not use the _same_ element twice.

You can return the answer in any order.

### solution
the first thought would be to use two for loops. but it has a time complexity of O(n^2). we can rather iterate through the array and storing each element of the array into a dictionary, with the element as the key and its index as the value. while iterating, at each step we can also find the *complement* or the *needed value* for each iteration and if that value exists we can just simply return the value of that key and the current position. else, just add that into the dictionary.
![](https://blog-pictures.vercel.app/twosum1.png)
if no such numbers were found, then return an empty list.
time complexity: O(n)

# Valid parenthesis (20)
### description
Given a string `s` containing just the characters `'('`, `')'`, `'{'`, `'}'`, `'['` and `']'`, determine if the input string is valid.

An input string is valid if:

1. Open brackets must be closed by the same type of brackets.
2. Open brackets must be closed in the correct order.
3. Every close bracket has a corresponding open bracket of the same type.


### solution
obviously we need to use stack to handle this. thankfully python has negative indexing and hence there is no need to keep track of top. if we find an opening brace, then we can simply append it into our stack. but for closing brace, we need to check if the corresponding opening brace is present at the top of the stack.
![](https://blog-pictures.vercel.app/validparenth1.png)
for each closing brace, we are also checking if the stack is empty aswell.
at the end, if our stack becomes empty, then all the opening braces has a corresponding closing brace and it is properly arranged. and hence the stack becomes empty. and we can just return true.


# Merge two sorted lists (21)
### description
You are given the heads of two sorted linked lists `list1` and `list2`.

Merge the two lists into one **sorted** list. The list should be made by splicing together the nodes of the first two lists.

Return _the head of the merged linked list_.

### solution
since this uses linked lists, i prefer to use C. but the logic is just the same. there are two approaches, recursion and iterative approach. \

#### recursion
first we assign some base conditions, like if list1 is empty, then just return list2 pointer and vice-versa. 
we check condition if the current value of the list1 is greater or lesser than list2 and then update the next pointer with recursion. but when doing the recursion we call it with the next value of the selected list. so that it can also do its thing and compare. \
when either one of the lists reaches its end, i.e at NULL, then the base condition triggers, that returns the non-empty list and this value will be stored in the previous recursion call's `list->next`. once that is resolved, it will return that particular list's current pointer. 
![](https://blog-pictures.vercel.app/merge1.png)

here's a visual representation of whats happening, 
![](https://blog-pictures.vercel.app/merge2.png) 

please do mind that, by `returned values` i am referring to the line after `list->next`. each recursion return passes the lower value back up the stack connecting it to the previous smaller node found in the previous call.
this would mean `2000->1000->2010->1010->2020->1010`