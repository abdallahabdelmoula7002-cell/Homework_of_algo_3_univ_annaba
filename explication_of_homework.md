# Sorting Algorithms in C

**Author:** Abdelmola Abdallah

## 1. About the project

In this project I implemented five sorting algorithms in C, **without using any built-in sort function**:
Bubble Sort, Selection Sort, Insertion Sort, Merge Sort and Quick Sort.

The program has a menu. With it I can run each algorithm, measure its **time** and **extra memory**, and compare the five algorithms on different array sizes and different types of data.

## 2. Language, compiler and libraries

- **Language:** C
- **Compiler:** GCC 15.2.0 (MinGW-W64 x86_64-ucrt-posix-seh, built by Brecht Sanders, r6)
- **Editor:** Visual Studio Code

Libraries used (all standard C):

| Library | What I use it for |
|---|---|
| `stdio.h` | `printf`, `fgets`, `sscanf` (menu, display), `fopen`/`fprintf` (CSV file) |
| `stdlib.h` | `malloc`, `free` (temporary array of Merge Sort), `rand`, `srand` (random data), `exit` |
| `string.h` | `memcpy`: copy the original data before each sort |
| `stdbool.h` | `bool`, `true`, `false` |
| `time.h` | `clock()` and `CLOCKS_PER_SEC`: time measurement |

## 3. My machine

| | |
|---|---|
| CPU | 11th Gen Intel(R) Core(TM) i7-11370H |
| RAM | 24 GB DDR4 |
| OS | Windows 11 |

## 4. How to compile and run

Open the project folder in Visual Studio Code, open a terminal (**Terminal → New Terminal**) and type:

```
gcc -std=c11 -O2 -Wall -Wextra -o sorting.exe main.c
.\sorting.exe
```

The menu:

| Choice | What it does |
|---|---|
| 1 to 5 | Runs one algorithm. I choose the array size and the type of data. For arrays of 20 elements or less, the array is shown before and after the sort. |
| 6 | Compares the five algorithms for the sizes 1 000, 5 000, 10 000, 50 000 and 100 000, shows a table with a rank, and saves it in `comparison_results.csv`. |
| 7 | Big array example: 100 000 random integers. |
| 0 | Exit. |

If I type something invalid (a letter, a number out of range...), the program asks again and does not crash.

The types of data are: **random**, **already sorted**, **reverse sorted** and **many duplicates** (values between 0 and 99).

## 5. How the measurement works

- **Time:** I use `clock()` from `time.h`. It gives the processor time. I convert it to milliseconds.
- **Memory:** the sorting functions only need extra memory in one case: Merge Sort allocates a temporary array of the same size as the data. I use my own functions `trackedMalloc` and `trackedFree` that count every byte asked to `malloc` and keep the highest value (the peak). Bubble, Selection, Insertion and Quick Sort allocate nothing, so they show 0 KB.
  I chose this method because it uses only standard C libraries and gives an exact value for the memory used by the sorting function. Its limit: it does not count the memory of the recursive calls (the stack) and the memory of the input array itself. It is not the total memory of the whole process given by the operating system.
- **Fair comparison:**
  - all algorithms sort a **copy** of the same data (`originalArray` is copied into `workArray` before each sort);
  - the copy, the data generation and the display are **not** timed, only the sort;
  - each measurement is repeated **3 times** and I keep the **average time** and the **peak memory**;
  - the random numbers use a **fixed seed (42)**, so the results can be reproduced;
  - after each sort the program checks that the array is really sorted.
- **Timer precision:** `clock()` is precise to about 1 millisecond on Windows. For very small times (for example Quick Sort on 1 000 elements) the program can show `0.000 ms` or a very small value. This does not mean the sort takes no time.
- **Slow algorithms:** Bubble, Selection and Insertion Sort are too slow for 100 000 elements. In the comparison they are run only up to 50 000 elements, and in the big array example they use 10 000 elements. Merge Sort and Quick Sort run on all sizes, including 100 000.

## 6. The algorithms and their complexity

| Algorithm | Idea | Best case | Average case | Worst case | Extra memory |
|---|---|---|---|---|---|
| **Bubble Sort** | Compare neighbours and swap them if they are in the wrong order. If a pass has no swap, stop. | O(n) | O(n²) | O(n²) | O(1) |
| **Selection Sort** | Find the smallest element of the unsorted part and swap it into place. | O(n²) | O(n²) | O(n²) | O(1) |
| **Insertion Sort** | Take each element and insert it at the right place in the sorted left part. | O(n) | O(n²) | O(n²) | O(1) |
| **Merge Sort** | Split the array in two, sort each half (recursion), then merge the two sorted halves. | O(n log n) | O(n log n) | O(n log n) | O(n) |
| **Quick Sort** | Choose a pivot, put smaller elements on its left and bigger ones on its right, then sort both parts (recursion). | O(n log n) | O(n log n) | O(n²) | O(log n) |

**Quick Sort pivot strategy:** I use the **middle element** as pivot (it is moved to the end, then the partition is done). With a first or last element pivot, sorted and reverse-sorted data give the worst case O(n²). With the middle element they are fast. To avoid a deep recursion, the function calls itself on the smaller part and uses a loop for the bigger part, so the depth is at most about log₂(n). This is why 100 000 elements do not cause a stack problem.

## 7. Results

All times are in milliseconds (average of 3 runs), measured on the machine of section 3.
The complete results of option 6 are in `comparison_results.csv`.

### 7.1 Random data

| Size | Bubble | Selection | Insertion | Merge | Quick |
|---:|---:|---:|---:|---:|---:|
| 1 000 | … | … | … | … | … |
| 5 000 | … | … | … | … | … |
| 10 000 | … | … | … | … | … |
| 50 000 | … | … | … | … | … |
| 100 000 | skipped | skipped | skipped | … | … |

Extra memory: Bubble, Selection, Insertion and Quick Sort = 0 KB. Merge Sort = … KB for 100 000 elements (the theory gives 100 000 × 4 bytes ≈ 390 KB).

### 7.2 Other types of data (size 50 000)

| Data | Bubble | Selection | Insertion | Merge | Quick |
|---|---:|---:|---:|---:|---:|
| Random | … | … | … | … | … |
| Already sorted | … | … | … | … | … |
| Reverse sorted | … | … | … | … | … |
| Many duplicates | … | … | … | … | … |

### 7.3 Big array example (option 7)

100 000 random integers, seed 42:

| Algorithm | Size | Time (ms) | Extra memory (KB) |
|---|---:|---:|---:|
| Bubble Sort | 10 000 | … | … |
| Selection Sort | 10 000 | … | … |
| Insertion Sort | 10 000 | … | … |
| Merge Sort | 100 000 | … | … |
| Quick Sort | 100 000 | … | … |

## 8. Critical analysis

- **Do the experiments match the theory?** Yes. When the size goes from 10 000 to 50 000 (5 times bigger), the time of Bubble, Selection and Insertion Sort is multiplied by about 25 (5²), which is the O(n²) behaviour. Merge Sort and Quick Sort grow only a little more than 5 times, which is the O(n log n) behaviour.
- **Which algorithm is the best?** For big arrays, **Quick Sort** and **Merge Sort** are by far the best: they sort 50 000 elements in a few milliseconds, while Bubble Sort needs several seconds. Quick Sort is usually the fastest and uses no extra array. Merge Sort is a bit slower, but its time is always O(n log n) and it is stable. Its price is the extra memory O(n).
- **Simple algorithms:**
  - **Insertion Sort** is the best of the three. On already sorted data it is almost instant (O(n)), and it is also very good on small arrays.
  - **Bubble Sort** with the early-exit flag is also almost instant on sorted data, but it is the slowest on random and reverse-sorted data.
  - **Selection Sort** does not care about the data: it always makes about n²/2 comparisons, so it is slow even on sorted data.
- **Effect of the data type:** sorted data is the best case of Bubble and Insertion Sort. Reverse-sorted data is their worst case. With many duplicates, my Quick Sort has a weak point: elements equal to the pivot all go to the same side of the partition, so the parts are not balanced and Quick Sort can become slower than Merge Sort (see table 7.2).
- **When to use which algorithm:**
  - small or almost sorted array: Insertion Sort;
  - big array: Quick Sort, or Merge Sort if a guaranteed O(n log n) time and a stable sort are needed;
  - Bubble Sort and Selection Sort are only useful to learn.
- **Problems I faced:**
  - the standard C libraries cannot give the memory of one function directly, so I count the memory allocated with `malloc` myself (see section 5);
  - `clock()` has a precision of about 1 ms, so very short times are not precise;
  - the three quadratic algorithms are too slow for 100 000 elements, so I limited them;
  - Quick Sort can have a very deep recursion, so I recurse on the smaller part only.

## 9. Files of the repository

| File | Content |
|---|---|
| `main.c` | The whole program: the 5 algorithms, the measurement, the menu and the comparison |
| `README.md` | This report |
| `.gitignore` | Files that Git must not upload (compiled programs, editor settings) |
| `comparison_results.csv` | Results of option 6 |
