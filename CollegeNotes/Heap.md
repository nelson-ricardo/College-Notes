Functions Implemented:
1. Heapify
2. Insertion
3. Deletion
4. Heap Sort
5. Percolate Up/Down

## Boilerplate Code
```c
#include <stdio.h>
#include <stdlib.h>
#include <assert.h>

// Macros
#define PARENT(x) (((x) - 1) / 2)
#define RIGHT(x) ((x) * 2 + 2)
#define LEFT(x) ((x) * 2 + 1)

typedef struct Heap Heap;

struct Heap {
	int size, cap;
	int *arr;
};

Heap * createHeap() {
	// Instantiate
	Heap * res;

	// Allocate
	res = (Heap *) malloc(sizeof(Heap));

	// Initialize
	res->size = 0;
	res->cap = 10;
	res->arr = (int *) malloc(sizeof(int) * res->cap);

	// return heap address
	return res;
}

void swap(Heap * h, int firstIndex, int secondIndex) {
	int tmp = h->arr[firstIndex];
	h->arr[firstIndex] = h->arr[secondIndex];
	h->arr[secondIndex] = tmp;
}

void append(Heap * h, int data) {
	// If full
	if(h->size == h->cap) {
		// Expand
		h->cap *= 2;
		h->arr = (int *) realloc(h->arr, sizeof(int) * h->cap);
	}

	// Add value to the list
	h->arr[h->size++] = data;
}

void empty(Heap * h) {
	return !(h->size);
}
```

## Percolate Up
```c
// Function to move a value up in the heap based on priority
void percolateUp(Heap * h, int index) {
	// While the node has a parent with a lower priority than itself
	while(index != 0 && h->arr[index] > h->arr[PARENT(index)]) {
		// Swap the index and its parent
		swap(h, PARENT(index), index);

		// Move the parent's index
		index = PARENT(index);
	}
}
```

## Percolate Down
```c
// Function to move a value down in the heap based on priority
void percolateDown(Heap * h, int index) {
	// While the node has a higher priority
	while((RIGHT(index) < h->size && h->arr[RIGHT(index)] > h->arr[index]) || 
	(LEFT(index) < h->size && h->arr[LEFT(index)] > h->arr[index])) {
		// Store the index of the best
		int bestIndex = LEFT(index);

		// Update best to right if right is better
		if(RIGHT(index) < index->size && 
		h->arr[LEFT(index)] < h->arr[RIGHT(index)]) {
			bestIndex = RIGHT(index);
		}

		// Swap with best index
		swap(h, index, bestIndex);

		// Move to the best indexed child
		index = bestIndex;
	}
}
```

## Heap Insert
```c
void heapInsert(Heap * h, int data) {
	// Append value to list
	append(h, data);

	// Move the value up if necessary
	percolateUp(h, h->size - 1);
}
```

## Heap Remove
```c
void heapRemove(Heap * h) {
	assert(h->size > 0);

	// Swap the last value and the root
	swap(h, 0, h->size - 1);

	// Shrink the array by 1 (removes the old root)
	h->size--;

	// percolate the root down
	percolateDown(h, 0);

}
```

## Heapify
```c
void heapify(Heap * h) {
	// start at bottom and work towards top
	for(int i = h->size - 1; i >=0; i--) {
		// Perc down all values
		percolateDown(h, i);
	}
}
```

## Heap Sort
```c
// Function in O(Nlog(N)) time sort an array using a heap
void heapSort(Heap * h) {
	// [OPTIONAL] Shink the array to the number of values in array
	h->cap = h->size;
	h->arr = (int *) realloc(h->arr, sizeof(int) * h->cap);

	// Turn array into heap
	heapify(h);

	// "Remove" all value
	while(!empty(h)) {
		heapRemove(h); // value is actually moved to back of array
	}

	// Restore size of array
	h->size = h->cap;

}
```