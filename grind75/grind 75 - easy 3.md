# Ransom Note (383)
### description
Given two strings `ransomNote` and `magazine`, return `true` _if_ `ransomNote` _can be constructed by using the letters from_ `magazine` _and_ `false` _otherwise_.

Each letter in `magazine` can only be used once in `ransomNote`.

### solution
very easy. just use a hashmap to store the characters and their occurrence from the magazine string. and while iterating through the ransomNote string, remove each character one by one and if a particular character's score becomes 0, then just return false. \
![](https://blog-pictures.vercel.app/ransomnote1.png)

# Climbing stairs (70)
### description
You are climbing a staircase. It takes `n` steps to reach the top.

Each time you can either climb `1` or `2` steps. In how many distinct ways can you climb to the top?

### solution
fibonacci series. and not using recursion since it exceeds time limit. \
![](https://blog-pictures.vercel.app/climbingstairs1.png)

# Longest Palindrome (409)
### description
Given a string `s` which consists of lowercase or uppercase letters, return the length of the **longest**

**palindrome**

 that can be built with those letters.

Letters are **case sensitive**, for example, `"Aa"` is not considered a palindrome.

### solution
so we iterate through the string and keep a hashmap of the characters and their frequencies. and then iterate through the hashmap and for each character that has an even frequency, we add it to the final answer. and for a character that has an odd frequency, we can do two things
1. add the even part only. for example if the frequency is 3, then add 2. omit the odd part
2. and we can afford the whole odd frequency, but only once

to achieve this we keep a key or a marker for knowing if an odd numbered frequency value has been added or not. if it is not added yet, then add the 1 and change the key. if it is already added, then dont do anything more. \
![](https://blog-pictures.vercel.app/longestpalindrome.png)

# Reverse Linked List (206)
### description
Given the `head` of a singly linked list, reverse the list, and return _the reversed list_.

### solution
pretty simple. just use a stack. to hold only the linked list values. with my solution im only changing the values of the linked list and not actually reversing it with respect to the pointers. this gets the job done but its not really correct. but to actually reverse, we would need to make a pretty big array of structures using malloc but leetcode did not let me do that so im just making a stack of int values to store the values only. and since i am doing in c, i just need one extra traversal for getting the exact size of the stack i need to store. \
![](https://blog-pictures.vercel.app/revll.png)

# Majority Element (169)
### description
Given an array `nums` of size `n`, return _the majority element_.

The majority element is the element that appears more than `⌊n / 2⌋` times. You may assume that the majority element always exists in the array.

### solution
create a dictionary with each element and its frequency. return the max key corresponding to the max value. \
![](https://blog-pictures.vercel.app/majele.png)

# Add Binary (67)
### description
Given two binary strings `a` and `b`, return _their sum as a binary string_.

### solution
ive come up with a solution using c, but python also should work the same way. i use string + pointer arithmetic to write this solution. we first traverse to the last of each string one by one and keep a track of the size while doing it. and then create a variable to store the max length of the string. and then create another string with that same size + 2 (one to handle carry forwards and another for the null pointer). and then keep updating the result string from the back. at the end, if the pointer of the result string ends up at 0 and not -1, this means we have an extra char at the beginning of the string, which we had created to handle carry forward, but wasn't used. so we can just use pointer arithmetic to update the first position of the string to the next position. \
![](https://blog-pictures.vercel.app/addbin.png)

# 