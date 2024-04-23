## Selection Sort
```c
void selection_sort(int *arr, int len) {
	// Repeatedly...
	for(int i = 0; i < len; i++) {
		// find the smallest value in the array...
		int smallest = i;
		for(int j = i; j < len; j++) {
			if(arr[j] < arr[smallest]) {
				smallest = j;
			}
		}
		// and replace it with the first value in the array
		int tmp = arr[smallest];
		arr[smallest] = arr[i];
		arr[i] = tmp;
	}
}
```

## Quick Sort
```c
void quick_sort(int * arr, int len) {
	// base case
	if (len <= 1) {
		return;
	}

	// split the array based on the pivot (arr[0])
	int fptr = 1; // front of the array
	int bptr = len - 1; // back of the array
	for(int i = 1; i < len; i++) {
		if(arr[0] < arr[fptr]) {
			// swap arr[fptr] with arr[bptr] & decrease val of bptr
			int tmp = arr[fptr];
			arr[fptr] = arr[bptr];
			arr[bptr] = tmp;
			bptr--;
		} else { // increase the val of fptr
			fptr++;
		}
	}

	// move pivot into location
	int tmp = arr[0];
	arr[0] = arr[fptr - 1];
	arr[fptr - 1] = tmp;

	// sort the two parts
	quick_sort(arr, fptr - 1);
	quick_sort(arr + fptr, len - fptr);
}
```

## Merge Sort
```c
void merge_sort(int * arr, int len) {
	// Base Case
	if(len <= 1) {
		return;
	}

	// Sort the two halves
	merge_sort(arr, len / 2);
	merge_sort(arr + len / 2, len - (len / 2));

	// Merge the two halves into a temp array
	int * tmp = (int *) malloc(sizeof(int) * len);
	int fptr = 0;
	int bptr = len / 2;
	for(int i = 0; i < len; i++) {
		if(fptr == len / 2 || (bptr != len && arr[bptr] < arr[fptr])) {
			tmp[i] = arr[bptr];
			bptr++;
		} else {
			tmp[i] = arr[fptr];
			fptr++;
		}
	}

	for(int i = 0; i < len; i++) {
		arr[i] = tmp[i];
	}

	free(tmp);
}
```

## Insertion Sort
```c
void insertion_sort(int * arr, int len) {
	// "Insert" the values one at a time
	for(int i = 0; i < len; i++) {
		// swap to the sorted position
		int j = i;
		while(j && arr[j] < arr[j - 1]) {
			int tmp = arr[j];
			arr[j] = arr[j - 1];
			arr[j - 1] = tmp;

			// Move to the new position with value
			j--;
		} 
	}
}
```

## Bubble Sort
```c
void bubble_sort(int * arr, int len) {
	// start the last swap at the end
	int last = len;

	// "Insert" the values one at a time
	for(int i = 0; i < len; i++) {
		int cur = 0;

		// swap to the sorted position
		for(int j = 0; j + 1 < last; j++) {
			if(arr[j] > arr[j + 1]) {
				cur = j + 1;
				int tmp = arr[j];
				arr[j] = arr[j + 1];
				arr[j + 1] = tmp;
			}
		}
		// Update the position for the last swap
		last = cur;
	}
}
```

## Heap Sort
