#Generic Merge Sort

This program implements a generic merge sort algorithm in C. It is a sorting algorithm that works with arrays of any data type, using a user-provided comparison function to determine the sorting order. The code achieves generality by using function pointers and void* pointers, allowing it to handle data of any type.
Key Features and Details
Generic Merge Sort:

The mergeSort function and its helper function merge work with void* arrays, which are type-agnostic.
The size of each element (size_t size) and a user-defined comparison function (compare_func cmp) are passed as arguments to handle different data types.
Merge Function:

The merge function is responsible for combining two sorted subarrays into a single sorted array.
It dynamically allocates memory for temporary arrays (L and R) to hold the left and right subarrays.
It uses memcpy to copy and merge the data back into the main array.
Comparison Function:

A custom comparison function (compareInt) is defined for integers. It can be replaced with other comparison functions to handle different data types (e.g., strings, floating-point numbers).
Dynamic Memory Management:

Memory for temporary arrays is allocated using malloc and freed after use to prevent memory leaks.
Ease of Testing:

The program includes a printIntArray function to display the contents of integer arrays, helping with visualization of results.
Driver Code:

The main function demonstrates the merge sort with an example array of integers, printing the array before and after sorting.
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

// Comparison function type
typedef int (*compare_func)(const void*, const void*);

// Merges two subarrays of arr[].
// First subarray is arr[l..m]
// Second subarray is arr[m+1..r]
void merge(void* arr, int l, int m, int r, size_t size, compare_func cmp) {
    int n1 = m - l + 1;
    int n2 = r - m;

    // Create temp arrays
    void* L = malloc(n1 * size);
    void* R = malloc(n2 * size);

    // Check for memory allocation failure
    if (L == NULL || R == NULL) {
        fprintf(stderr, "Memory allocation failed\n");
        exit(EXIT_FAILURE);
    }

    // Copy data to temp arrays L[] and R[]
    memcpy(L, (char*)arr + l * size, n1 * size);
    memcpy(R, (char*)arr + (m + 1) * size, n2 * size);

    // Merge the temp arrays back into arr[l..r]
    int i = 0; // Initial index of first subarray
    int j = 0; // Initial index of second subarray
    int k = l; // Initial index of merged subarray

    while (i < n1 && j < n2) {
        if (cmp((char*)L + i * size, (char*)R + j * size) <= 0) {
            memcpy((char*)arr + k * size, (char*)L + i * size, size);
            i++;
        } else {
            memcpy((char*)arr + k * size, (char*)R + j * size, size);
            j++;
        }
        k++;
    }

    // Copy the remaining elements of L[], if there are any
    while (i < n1) {
        memcpy((char*)arr + k * size, (char*)L + i * size, size);
        i++;
        k++;
    }

    // Copy the remaining elements of R[], if there are any
    while (j < n2) {
        memcpy((char*)arr + k * size, (char*)R + j * size, size);
        j++;
        k++;
    }

    // Free allocated memory
    free(L);
    free(R);
}

// Function to perform merge sort
void mergeSort(void* arr, int l, int r, size_t size, compare_func cmp) {
    if (l < r) {
        int m = l + (r - l) / 2;

        mergeSort(arr, l, m, size, cmp);
        mergeSort(arr, m + 1, r, size, cmp);
        merge(arr, l, m, r, size, cmp);
    }
}

// Function to print an array of integers
void printIntArray(int* arr, int size) {
    for (int i = 0; i < size; i++)
        printf("%d ", arr[i]);
    printf("\n");
}

// Comparison function for integers
int compareInt(const void* a, const void* b) {
    return (*(int*)a - *(int*)b);
}

// Driver code
int main() {
    int arr[] = {12, 11, 13, 5, 6, 7};
    int arr_size = sizeof(arr) / sizeof(arr[0]);

    printf("Given array is \n");
    printIntArray(arr, arr_size);

    mergeSort(arr, 0, arr_size - 1, sizeof(int), compareInt);

    printf("\nSorted array is \n");
    printIntArray(arr, arr_size);

    return 0;
}
