---
layout: post
date: 2025-02-06
categories: [Java, Algorithms]
tags: [Java Algorithms]
title: Binary Search

---
Binary Search is one of the most fundamental and efficient searching algorithms, often covered in introductory computer science courses. It is used to find an element in a sorted array or list in logarithmic time. This makes Binary Search highly efficient compared to linear search algorithms, especially when dealing with large datasets. In this blog post, we’ll explore how Binary Search works and implement it in Java.

Here is example implementation of binary search in Java:
```java
public class BinarySearch {

    public static int binarySearch(int[] array, int target) {
        int low = 0;
        int high = array.length - 1;

        while (low <= high) {
            int mid = (low + high) / 2;

            if (array[mid] == target) {
                return mid; // Target value found
            } else if (array[mid] < target) {
                low = mid + 1; // Search in the right half
            } else {
                high = mid - 1; // Search in the left half
            }
        }

        return -1; // Target value not found
    }
```
Binary Search works by repeatedly dividing the search interval in half.

- The binarySearch method takes two arguments: the sorted array and the target value to search for.
- The low variable keeps track of the lower bound of the search interval, and the high variable keeps track of the upper bound.
- The while loop continues as long as the lower bound is less than or equal to the upper bound.
- Inside the loop, the mid variable is calculated as the middle index of the interval.
- If the element at the middle index matches the target value, the method returns the middle index.
- If the element at the middle index is less than the target value, the search continues in the right half of the interval by updating the low variable to mid + 1.
- If the element at the middle index is greater than the target value, the search continues in the left half of the interval by updating the high variable to mid - 1.
- If the loop finishes without finding the target value, the method returns -1.


## Key requirements for Binary Search:
- The array or list must be **sorted**.
- The algorithm reduces the search space in each step, thus performing in **O(log n)** time complexity.

