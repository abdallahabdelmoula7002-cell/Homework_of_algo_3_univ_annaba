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
gcc -std=c11 -Wall -Wextra -o sorting.exe hmw_code.c
.\sorting.exe
```

The results of section 7 were measured **without optimization** (default GCC options, no `-O2`). With `-O2` the program is faster, but the comparison between the algorithms stays the same.

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
- **Timer precision:** `clock()` is precise to about 1 millisecond on Windows. Because I average 3 runs, the times are multiples of 0.333 ms (for example 0.333, 0.667, 1.000). A time of `0.000 ms` does not mean the sort takes no time: it means it is shorter than the precision of the timer.
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
The results come from option 6 and option 7 of the program. I ran the tests in two sessions: the **random** and **already sorted** tables come from the first session, the **reverse sorted**, **many duplicates** and **big array** tables come from the second session.
The file `comparison_results.csv` contains the table of the last run of option 6 (many duplicates). The other tables are copied from the terminal.

### 7.1 Random data

| Size | Bubble | Selection | Insertion | Merge | Quick |
|---:|---:|---:|---:|---:|---:|
| 1 000 | 1.333 | 0.667 | 0.667 | 0.000 | 0.000 |
| 5 000 | 35.000 | 23.333 | 11.333 | 0.333 | 0.333 |
| 10 000 | 151.667 | 91.000 | 46.667 | 1.000 | 1.000 |
| 50 000 | 5 536.667 | 2 288.000 | 1 179.333 | 5.667 | 5.000 |
| 100 000 | skipped | skipped | skipped | 12.333 | 10.667 |

**Extra memory:** Bubble, Selection, Insertion and Quick Sort = 0 KB. Merge Sort (same for all types of data):

| Size | 1 000 | 5 000 | 10 000 | 50 000 | 100 000 |
|---|---:|---:|---:|---:|---:|
| Merge Sort (KB) | 3.91 | 19.53 | 39.06 | 195.31 | 390.62 |

### 7.2 Already sorted data

| Size | Bubble | Selection | Insertion | Merge | Quick |
|---:|---:|---:|---:|---:|---:|
| 1 000 | 0.000 | 1.333 | 0.000 | 0.333 | 0.000 |
| 5 000 | 0.000 | 23.333 | 0.000 | 0.333 | 0.000 |
| 10 000 | 0.000 | 95.667 | 0.000 | 0.333 | 0.333 |
| 50 000 | 0.333 | 2 256.333 | 0.000 | 2.333 | 2.000 |
| 100 000 | skipped | skipped | skipped | 5.333 | 4.000 |

### 7.3 Reverse sorted data

| Size | Bubble | Selection | Insertion | Merge | Quick |
|---:|---:|---:|---:|---:|---:|
| 1 000 | 2.333 | 1.667 | 1.667 | 0.000 | 0.333 |
| 5 000 | 77.667 | 44.667 | 61.667 | 0.667 | 0.333 |
| 10 000 | 359.000 | 198.667 | 198.667 | 0.667 | 0.667 |
| 50 000 | 8 676.667 | 4 418.333 | 5 243.000 | 12.000 | 5.333 |
| 100 000 | skipped | skipped | skipped | 10.000 | 6.000 |

### 7.4 Data with many duplicates

| Size | Bubble | Selection | Insertion | Merge | Quick |
|---:|---:|---:|---:|---:|---:|
| 1 000 | 3.000 | 3.333 | 1.667 | 0.333 | 0.333 |
| 5 000 | 96.333 | 66.667 | 36.333 | 1.333 | 2.333 |
| 10 000 | 349.667 | 199.000 | 75.333 | 2.667 | 6.667 |
| 50 000 | 11 786.333 | 4 807.000 | 2 336.667 | 7.333 | 113.667 |
| 100 000 | skipped | skipped | skipped | 24.333 | 334.667 |

### 7.5 Big array example (option 7)

100 000 random integers, seed 42:

| Algorithm | Size | Time (ms) | Extra memory (KB) |
|---|---:|---:|---:|
| Bubble Sort | 10 000 | 329.000 | 0.00 |
| Selection Sort | 10 000 | 223.000 | 0.00 |
| Insertion Sort | 10 000 | 120.333 | 0.00 |
| Merge Sort | 100 000 | 36.333 | 390.62 |
| Quick Sort | 100 000 | 31.333 | 0.00 |

## 8. Critical analysis

- **Do the experiments match the theory?** Yes. On random data, when the size goes from 10 000 to 50 000 (5 times bigger), the time of Selection Sort is multiplied by 25 (91 ms → 2 288 ms) and the time of Insertion Sort by 25 (46.7 ms → 1 179 ms). This is the O(n²) behaviour (5² = 25). Bubble Sort is multiplied by about 36 (151.7 ms → 5 537 ms), a little more than 25. Merge Sort and Quick Sort are multiplied by only about 5 (1 ms → 5 or 6 ms), which is the O(n log n) behaviour.
- **Memory:** Merge Sort uses exactly 4 bytes per element: 195.31 KB for 50 000 elements and 390.62 KB for 100 000 elements. This is the O(n) of the theory. The other algorithms sort in place and need no extra array.
- **Which algorithm is the best?** For big arrays, **Merge Sort** and **Quick Sort** are far better than the three simple algorithms: they sort 50 000 elements in a few milliseconds, while Bubble Sort needs several seconds. On random, sorted and reverse sorted data they are close to each other, and Quick Sort is usually a little faster (for example 6.0 ms against 10.0 ms on reverse sorted data of size 100 000). Their order can change from one run to another, because the times are only a few milliseconds and the timer precision is 1 ms.
- **Simple algorithms:**
  - **Insertion Sort** is the best of the three on random data: about 2 times faster than Selection Sort and about 5 times faster than Bubble Sort (size 50 000).
  - On **already sorted** data, Bubble Sort (with the early-exit flag) and Insertion Sort are almost instant (0.000 to 0.333 ms for 50 000 elements): this is their O(n) best case.
  - On **reverse sorted** data, Bubble Sort and Insertion Sort are at their worst case: at size 50 000, Bubble Sort takes 8 677 ms (against 5 537 ms on random data) and Insertion Sort takes 5 243 ms (about 4 times more than on random data, 1 179 ms).
  - **Selection Sort** always makes about n²/2 comparisons, whatever the data. Its time is the same on random and sorted data (2 288 ms and 2 256 ms at size 50 000). On reverse sorted and duplicates data it was about 2 times higher (4 418 ms and 4 807 ms). I did not study the exact reason; it is probably linked to how often the minimum changes during the search, which depends on the data.
- **Many duplicates:** this is the weak point of my Quick Sort. Elements equal to the pivot all go to the same side of the partition, so the two parts are not balanced. At 100 000 elements, Quick Sort takes 334.7 ms on duplicates against 10.7 ms on random data, and it is about 14 times slower than Merge Sort (24.3 ms). Merge Sort is not affected by the type of data. A way to improve Quick Sort would be a three-way partition (smaller / equal / bigger).
- **When to use which algorithm:**
  - small or almost sorted array: Insertion Sort;
  - big array: Quick Sort, or Merge Sort if a guaranteed O(n log n) time is needed (for example with many duplicates) and the extra memory is acceptable;
  - Bubble Sort and Selection Sort are only useful to learn.
- **Limits of my measurements:** the times are not perfectly stable, because I ran the tests on a laptop with other programs open and `clock()` has a precision of 1 ms. When I repeated some tests in two sessions, short times changed a lot (Merge Sort in the big array example: 19.3 ms in the first session and 36.3 ms in the second) and Bubble Sort on reverse sorted data of size 50 000 took 4 015 ms in the first session and 8 677 ms in the second. For this reason the **trends** (growth by about 25 for the quadratic algorithms, Merge Sort and Quick Sort much faster, the effect of sorted and reverse sorted data, the problem of Quick Sort with duplicates) are reliable, but the exact values must be read with care.
- **Problems I faced:**
  - the standard C libraries cannot give the memory of one function directly, so I count the memory allocated with `malloc` myself (see section 5);
  - `clock()` has a precision of about 1 ms, so very short times are not precise;
  - the three quadratic algorithms are too slow for 100 000 elements, so I limited them;
  - Quick Sort can have a very deep recursion, so I recurse on the smaller part only.

## 9. Files of the repository

| File | Content |
|---|---|
| `hmw_code.c` | The whole program: the 5 algorithms, the measurement, the menu and the comparison |
| `README.md` | This report |
| `.gitignore` | Files that Git must not upload (compiled programs, editor settings) |
| `comparison_results.csv` | Results of the last run of option 6 |
