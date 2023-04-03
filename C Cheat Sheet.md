p:: [[Programação|Programming]] [[C]]
tags:: #C #lowlevel #programação💻 

# C Cheat Sheet
Data: 2022-12-30 09:56
Last Modified: `=dateformat(this.file.mtime, "yyyy-MM-dd - HH:mm")`

----
## Variables and Types
Main C Types are:

| Type name                | C Type |
| ------------------------ | ------ |
| Integer                  | int    |
| Floating Point           | float  |
| Character                | char   |
| String (pointer to char) | char * |

>[!note] 
Strings in C are array of chars.

### Variable declaration and assignment
>[!note] Definitions
> **Variable declaration**: The association of a place in memory to a given name.
> **Variable assignment**: When a value is given to a existing variable.
> **Variable initialization**: When a value is assigned to a freshly declared variable.

A variable can not be re-declared in the same scope neither with equal or different types.
```C
int main(void) {
	int a = 0;
	int a = 1; // Error.
	float a = 2.5; // Error
}
```

However, it is ok to define a variable with the same name in another scope.
```C
int main(void) {
    int i = 0;
    // a "i" variable is used, but it's declaration is inside the for-loop scope.
    for (int i = 0; i < 4; i++) {
        printf("first loop %d\n", i);
    }
    // The second for-loop does not conflict with the first one.
    for (int i = 0; i < 5; i++) {
        printf("second loop %d\n", i);
    }
    // This variable i is a reference to the "i" defined in the current scope.
    printf("%d", i);
}
```

Or:
```C
int main(void) {
    int a = 0;
    for (int i = 0; i < 2; i++) {
        int a = i;
    }
    printf("%d", a); // 0
    // OBS: The "a" variable defined inside the for disapears after the scope ends.
}
```

>[!important]
> Variables that are not initialized (i.e. have been declared but not had a value assigned to them) hold indeterminate values (_trash_). This value can be 0 or any other integer and can vary from run to run.

>[!info] Assignment operator return value
> The assignment operator (`=`) returns the value being assigned, e.g. `int a = 2` returns `2`.

Example:
```C
int a = 2;
int b;
int c = (b = a);
printf("%d%d\n", b, c); // 2 2
```
### Boolean types
Traditionally, boolean types in C are defined as integers. `0` means `false` and everything else is `true`. However, there are more modern alternatives, like the `stdbool`.

```C
#include <stdio.h>
#include <stdbool.h>

int main(void) {
    bool is_working = false;

    if (is_working) {
        printf("Oh Yes!\n");
    } else {
        printf("Oh no!\n");
    }
}
```

### Ternary operator
```C
// condition ? value_assigned_if_true : value_assigned_if_false
int y = (x > 3) ? 20 : 25;

// Equivalente to
int y;
if (x > 3) {
	y = 20;
} else {
	y = 25;
}
```

### Pre and Post increment and decrement
```C
// Evaluate the expression, then increment
int i = 1;
int j = 1 + i++;
printf("%d, %d\n", i, j); // 2, 2

// Increments, then evaluate expression
int a = 1;
int b = 1 + ++a;
printf("%d, %d\n", a, b); // 2, 3
```

### Comma operator
```C
// Multiple declarations and assignments in the same line
int x = 10, y = 15;
printf("%d %d\n", x, y);

// Use more than one loop variable
for (int i = 0, j = 0; i < 100; i++, j++) {
	printf("i = %d | j = %d\n", i, j);
}
```

### sizeof operator
It returns the size of a type in bytes.
```C
typedef struct Car {
    int wheels;
    int doors;
    float price;
} Car;


int main(void) {
    int x = 10, y = 15;
    printf("%d %d\n", x, y);
    printf("size of x: %zu, size of y: %zu", sizeof x, sizeof y); // 4, 4 (size of the type)
	
    Car car;
    car.wheels = 6;
    car.doors = 2;
    car.price = 4000.0;
    printf("size of car struct: %zu\n", sizeof car); // 12
}
```

>[!note] Type of `sizeof` return
> The `sizeof` operator returns a value of type `size_t`, which is normally used to return counts in C.
> - To print this value, use `%zu` (or `%zd` if negative).

>[!important]
> The `sizeof` is a _compile time_ operation!

## Flow control - Decision and Loops
### If-else statement
```C
int i = 2;

if (i > 2) {
	printf("i is greater than 2.\n");
} else if (i == 2) {
	printf("i is equal to 2.\n");
} else {
	printf("i is lesser than 2.\n")
} // i is equal to 2.
```

### Switch
```C
int wheels = 2;
switch (wheels) {
	case 0:
		printf("it is an ufo!\n");
		break;
	case 1:
		printf("That is a monocycle.\n");
		break;
	case 2:
		printf("Joining the hells angels.\n");
		break;
	case 3:
		printf("Joining the geriatric hells angels.\n");
		break;
	case 4:
		printf("I'm in my muns car, vrum vrum.\n");
		break;
	default:
		printf("biblically accurate vehicle\n");
		break;
}
```
### While
Executes code _while_ the specified conditions is **true**.
```C
int keep_going = 1;

while (keep_going) {
	
	// do something

	if (condition) {
		keep_going = 0;
	}
}

// Infinite loop
while (1) {
	// do something
}
```

The `do-while` loop executes its block statement at least one time.
```C
int keep_going = 0;

// while
while (keep_going) {
	printf("mochi mochi!");
} // no output

// do-while

do {
	printf("ohayou gozaimasu!");
} while (keep_going); // ohayou gozaimasu
```

### For
Loop structure used when the number of iterations are known.
```C
// While
int i = 0;
while (i < 5) {
	printf("while loop. i = %d\n", i);
	i++;
}

// Equivalent for
for (int i = 0; i < 5; i++) {
	printf("for loop. i = %d\n", i);
}
```

Trivia:
```C
// Infinite for loop
for (;;) {
	printf("OH mY GAHD!\n");
}

// Multi iterator parameters for-loop
for (int i = 0, j = 90; i < 1000 || j > 0; i++, j--) {
	printf("i = %d, j = %d\n", i, j);
}
```

## Functions
Functions are units of code that can be called. Functions can accept _parameters_ and can return a value. Functions are called by their name followed by parenthesis.

>[!info] Some definitions 
> **Parameters**: Variables defined at function declaration that belongs to the function scope.
> **Arguments**: Values passed (or _copied_) to the parameters at function call.
> - Most of the time, an argument is passed to the parameter as a copy, which means a new value is stored in memory. Some times, however, the argument is passed by reference, meaning that the parameters references the same place in memory in which the argument is stored.

```C
// function blueprint:
//
// return_type function_name(parameters_type parameters, ...) {
//     function body...
//     return something;
// }
#include <stdio.h>

int add_1(int n) {
	return n + 1;
}

int main(void) {
	int i = 2;
	int j = add_1(i);
	printf("value of j = %d\n", j); // 3
}
```

>[!important]
> For a function to be called, i.e. function_name(parameter), it must be defined before the calling point.

### Function Prototypes
Function prototypes are used to **pre-define** a function before the calling point, and prevent _implicit declaration errors_, even though the actual definition is below the calling point.

```C
int get_1(void); // Function Prototype

int add_1(int i) {
    return i + get_1(); // calling point
}

// actual definition
int get_1(void) {
    return 1;
}

int main(void)
{
    int i = 2;
    int j = plus_1(i);
    printf("j = %d\n", j); // 3
}
```

>[!warning] Empty parameters list
> When a function receives no parameters, it is best practice to use the `void` keyword between the parenthesis. **This is even more important for function prototypes** (BEEJ, 2022).

## Pointers
**A pointer, or _pointer variable_, is a variable that holds the address to another variable that is stored in memory.**

To evaluate the address of a variable (_**a pointer to that variable**_) in C, the `&` operator is used.
```C
int x = 2;
printf("pointer to x: %p", &x); // some hexadecimal code (e.g. 0x7fff538e308c)
```

>[!note] Hexadecimal
> The hexadecimal system is a numeric system of base 16 (instead of 10) and uses the chars from 0-9 and A-F.

### Pointer types
The pointer types are defined with an `*` after the type of the variable which the pointer points to.
```C
int x = 2;
int *px = &x;
printf("x: %d\n", x);
printf("pointer to x: %p\n", px);
```

### Dereferencing
**Dereferencing** means to get the actual value of the variable a pointer points to, so it can me used or modified. This is done by using the _indirection operator_, i.e. `*poiner_name`.
```C
int x = 2;
int *px = &x;

printf("x: %d\n", x); // 2
*px = 3;
printf("x: %d\n", x); // 3
```

>[!note]
>`*px` is also called an alias of `x`;

### Passing a pointer to a function
This is useful if a function needs to modify more than one variable.
```C
#include <stdio.h>

// A int* (pointer to int) is passed.
void add_1(int *n) {
    *n += 1; // Dereferencing
}

int main(void) {
    int x = 2;
    printf("x: %d\n", x); // 2
    add_1(&x);            // Passing the address of x to add_1(), i.e. the pointer to x.
    printf("x: %d\n", x); // 3
}
```

### The NULL pointer
The NULL pointer does not point to anything.
```C
int *p = NULL;
printf("%p\n", p); // (nil)

// A NULL pointer can be checked.
if (p == NULL) {
	printf("This pointer does not point to anything!");
}
```

>[!warning] Not initialized pointers
>A pointer that was not initialized points to _**garbage**_, it DOES NOT equal to NULL.

### Pointer miscellaneous
One line definitions
```C
int *a, *b, *c; // all are pointers
int d, *e, f; // only e is a pointer, d and f are regular int
```

`sizeof` pointers
```C
int a = 2;
int *q = &a;
printf("size of the q pointer: %zu\n", sizeof q);
printf("size of the dereferenced pointer q (i.e. the size of a, an int): %d\n", sizeof *q);
```
## Arrays
**_Arrays are just a sequence of pointers that points to the next element_**. Arrays must be defined with a known and fixed length.
```C
int arr[4];
printf("size of arr: %zu\n", sizeof arr); //16

// Assigning values
arr[0] = 12;
arr[1] = 27;
arr[2] = 42;
arr[3] = 89;

// Retrieving values
printf("value of arr stored in index 2: %d\n", arr[2]);
```

### Length of an array
The information is not stored by default and require a not so useful hack to be evaluated.
```C
int arr[5];
size_t arr_len = (sizeof arr) / (sizeof(int));
printf("length of arr: %zu\n", arr_len); // 5
```

>[!warning] Limitations
> This does not work outside the scope where the array was defined, e.g. if the array is passed to a function (as the only thing passed to a function is the pointer to the first element).
> 
>**Question**: _If the array must have a fixed length, the length should be known on the scope where the array is defined. So what is the point of this_? 
> 
### Array initialization
Can be done in many ways:
```C
// Given the size and the same number of items.
int arr[5] = {5, 3, 4, 8, 6}; // 5 3 4 8 6

// Given the size and lesser number of items (the rest populated by zeros)
int arr[20] = {1, 5, 8, 7, 7, 9}; // 1 5 8 7 7 9 0 0 0 0 0 0 0 0 0 0 0 0 0 0
int arr[20] = {0}; // 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0

// Given the size and values for specific positions (the rest is populated by zeros)
int arr[10] = {[2] = 5, [6] = 10} // 0 0 5 0 0 0 10 0 0 0

// Giver only the values (the size is equal to the number os items)
int arr[] = {1, 1, 1, 8, 9, 8, 7, 7}; // 1 1 1 8 9 8 7 7
size_t size = (sizeof arr / sizeof(int)); // 8
```

>[!warning] Limitations
>- A number of values greater than the specified size is not allowed.
>- The given size (between square brackets) can not be specified by a variable (**define directives work, though**).

>[!danger] Out of bound indexes
> In C it is possible to access array indexes that were not defined. This is results in _undefined behavior_, which means the program is unpredictable at runtime, **AVOID**!

### Array notation
When an array is defined using the array notation (`int arr[SIZE];`), the result is a value that **_cannot be modified after definition_**. Which means that values cannot be assigned directly to it. 
```C
int arr[5] = {1, 2, 3, 4, 5};
int another_arr[5] = arr; // ERROR: Expression must be a modifiable lvalue.
```

>[!note]
>This means that an array cannot be copied by assigning it to another array type, because the second one is not modifiable, i.e. cannot receive new values.

However, as an array is evaluated to a pointer to the first element of the collection, it can be assigned to a pointer type. 
```C
int arr[5] = {1, 2, 3, 4, 5};
int *another_arr = arr;
```

>[!warning]
> The pointer is in fact being copied, but `arr` and `another_arr` points to the same position in memory, therefore this is not a true copy of the array, meaning that any modification made to `another_arr` will impact `arr`.

>[!info] Conclusion
> - An array, defined with the array notation (`int arr[SIZE];`), **cannot have values assigned directly to it** after definition (_it is not modifiable_.
> - However, **the pointer to the first element of the array can be copied to other pointers (of the same type)**.
>  
>  Both are **mutable**, tho.
### Multidimensional arrays
```C
// Definition + initialization (do this by nesting arrays)
int matrix[2][5] = {
	{0, 4, 4, 8, 9},
	{1, 1, 1, 7, 6}
};

// Looping over it
for (int row = 0; row < 2; row++) {
	for (int col = 0; col < 5; col++) {
		printf("%d ", matrix[row][col]);
	}
	printf("\n");
}
// 0 4 4 8 9 
// 1 1 1 7 6

// Alternatively:
int matrix[3][3] = {[0][2] = 1, [1][1] = 1, [2][0] = 1};
// 0 0 1 
// 0 1 0 
// 1 0 0 
```

### Arrays and pointers
In C, it is possible to assign an **array type** to a **pointer type**. In doing so, the defined pointer will be a pointer to the first element of the array.
```C
int a[5] = {1, 2, 3, 4, 5};
int *p;

// Equivalent
p = a;
p = &a[0];

// Dereferencing p or a, results in the first element of the array.
printf("%d\n", *a); // 1
printf("%d\n", *p); // 1
```

>[!note]
> The array variable in _isolation_ (without square brackets - `a`) **is equal to the pointer to the its element**, what enables the equivalent behavior above.

### Passing arrays to functions
There are 3 ways to pass one-dimensional arrays to functions. And they are all the same.
```C
#include <stdio.h>

void print_double(int *arr, int len)   // Pointer notation (Preferred way!)
void print_double(int arr[], int len)  // or Array notation
void print_double(int arr[5], int len) // or Array notation with size
{
    for (int i = 0; i < len; i++) 
    {
        printf("%d, ", 2 * arr[i]);
    }
}

int main(void)
{
    int a[5] = {1, 2, 3, 4, 5};
    print_double(a, 5); 
}
```

### Altering arrays inside functions
```C
#include <stdio.h>

void triple_it(int *arr, int len) 
{
    for (int i = 0; i < len; i++) 
    {
        arr[i] = arr[i] * 3;
    }
}


int main(void)
{
    int a[5] = {1, 2, 3, 4, 5};
    
    triple_it(a, 5);

    for (int i = 0; i < 5; i++) 
    {
        printf("%d, ", 2 * a[i]);
    } 
    // 6, 12, 18, 24, 30
}
```

### Passing multidimensional arrays
The difference is that C needs to know all the dimensions, _except the first one_.
```C
#include <stdio.h>

void print_matrix2(int mat[][3]) // Array notation
void print_matrix(int (*mat)[3]) // Pointer notation
{
    for (int row = 0; row < 2; row++) 
    {
        for (int col = 0; col < 3; col++) 
        {
            printf("%d ", mat[row][col]);
        }
        printf("\n");
    }
}

int main(void)
{
    int matrix[2][3] = {
        {3, 2, 1},
        {0, 7, 0}
    };
    print_matrix(matrix);
    // 3 2 1 
	// 0 7 0 
}
```

## Strings
String literals (require double quotes)
```C
"Python is awesome!\n"
"Nim is fun!"
```

String variable definition
```C
// Pointer notation
char *s = "HENLON EBURINHAN!\n";

// Array notation
char s_2[] = "HOW ARE U. FINE THANK U.\n";

printf("%s%s", s, s_2);
// HENLON EBURINHAN!
// HOW ARE U. FINE THANK U.
```

Loop over a string
```C
char *s = "HENLON EBURINHAN!"; // or
char s[17] = "HENLON EBURINHAN!";

for (int i = 0; i < 17; i++)
{
	printf("%c, ", s[i]);
} 
// H, E, N, L, O, N,  , E, B, U, R, I, N, H, A, N, !, 
```

>[!danger] Pointer vs Array notation for Strings
> Pointer notation results in an immutable string, while Array notation creates a mutable copy of a string.

```C
char *s = "HENLON EBURINHAN!";
s[0] = 'Z'; // RUNTIME ERROR! -> SIGSEGV (Adress boundary error)

char s_2[17] = "HENLON EBURINHAN!";
s_2[0] = 'Z';

printf("%s\n", s_2); // ZENLON EBURINHAN!
```

### String assignment 
Strings behave differently if defined with _pointer notation_ and _array notation_. 
- Array notation: **Cannot be modified after definition**, but is mutable.
- Pointer notation: **Can be modified after definition**, but is not mutable.  

```c
// WRONG: Defining with array notation and then modifying it.
char s[100];
s = "OH MMY GAHD!";

// CORRECT: Defining with array notation and the mutating it.
char s[100] = "OH MMY GAHD!";
s[2] = '7';


// CORRECT: Defining with pointer notation and then modifying it.
char *s;
s = "OH MMY GAHD!";

// ALSO CORRECT
char s[100] = "OH MMY GAHD!";

// WRONG: Defining with array notation and then mutating it.
char s[100] = "OH MMY GAHD!";
s[2] = '7';
```
Reference: https://stackoverflow.com/a/37225315/8736974

>[!important] Modifying vs Mutating
> - **Mutating**: Assigning a new value _to one item inside_ an array or string (`s[2] = 't'`)
> - **Modifying**: Assigning a new value to the **whole** array or string.
### String length
To get the string length it is necessary to use the `string.h` header.
```C
#include <stdio.h>
#include <string.h>

void main(void)
{
    char *s = "OH MY GAAH!";
    printf("string \"%s\" has %zu characters.", s, strlen(s));
    // string "OH MY GAAH!" has 11 characters.
}
```

### String termination
C uses a terminator character to indicate the end of a string. That is the **NUL character**, or `\0`, which is added to the end of every string.
```C
#include <stdio.h>

size_t get_str_size(char *s)
{
    int size = 0;
    for (int i = 0; i < 1000000; i++)
    {
        if (s[i] == '\0')
        {
            break;
        }
        size++;
    }
    return size;
}

// This is the book solution. Less dumb.
size_t get_str_size_less_dumb(char *s)
{
    int size = 0;
    while (s[size] != '\0')
    {
        size++;
    }
    return size;
}

int main(void)
{
    char *s = "I WISH I WERE A BIRD!";
    size_t s_size = get_str_size(s);
    size_t s_size = get_str_size_less_dumb(s);
    printf("string \"%s\" has %zu characters.", s, s_size);
    // string "I WISH I WERE A BIRD!" has 21 characters.
}
```

### Copy of a String
>[!warning]
>Strings can not be copied using the `=` operator, it only assigns the pointer to the first element of a string to a pointer type.

```C
#include <stdio.h>
#include <string.h>

int main(void)
{
    char s[] = "I WISH I WERE A BIRD!";
    char *s2;

	// This assings the pointer to the first element of the string s to the pointer type s2.
    s2 = s;
    s[0] = 'Z';
    printf("%s\n", s2); // Z WISH I WERE A BIRD!
}
```

>[!warning]
>The result is two pointers that point to the same string, so modifications will affect both, which may be unexpected. 

To truly copy a string (a byte at a time), the `strcpy()` function from `string.h` is needed.
```C
#include <stdio.h>
#include <string.h>

int main(void)
{
    char s[] = "I WISH I WERE A BIRD!";
    char s2[100];
    strcpy(s2, s);
    s[0] = 'Z';
    printf("%s\n", s2); // I WISH I WERE A BIRD!
}
```

Manually copying a string:
```C
#include <stdio.h>
#include <string.h>

void mystrcpy_pointer(char *src, char *dest)
{
    int size = 0;
    while (*(src + size) != '\0')
    {
        *(dest + size) = *(src + size);
        size++;
    }
}

void mystrcpy(char *src, char *dest)
{
    int size = 0;
    while (src[size] != '\0')
    {
        dest[size] = src[size];
        size++;
    }
}

int main(void)
{
    char s[] = "I WISH I WERE A BIRD!";
    char s2[100];
    mystrcpy(s, s2);
    s[0] = 'Z';
    printf("%s\n", s2); // I WISH I WERE A BIRD!
}
```

## Struct
A struct is a pseudo-class with only data members and not methods. The definition of a struct is done by:
```C
struct Book
{
    int pages;
    int publication_year;
    char *publisher;
    char *title;
};
```

>[!important]
> This creates a new type called `struct Book` and not just `Book`.

Struct initialization is done using the `.` notation.
```C
#include <stdio.h>

struct Book
{
    int pages;
    int publication_year;
    char *publisher;
    char *title;
};

int main(void)
{
	// Option 1
    struct Book sicp;
    sicp.pages = 200;
    sicp.publication_year = 1996;
    sicp.publisher = "MIT Press";
    sicp.title = "Structure and Interpretation of Computer Programs";
    printf("Book: %s (%d), by the %s\n", sicp.title, sicp.publication_year, sicp.publisher);
	
	// Option 2: Requires the same order.
	struct Book atbs = {
        179,
        2015,
        "No Starch Press",
        "Automate the boring stuff with Python"
	};
    printf("Book: %s (%d), by the %s\n", atbs.title, atbs.publication_year, atbs.publisher);

	// Option 3: Does not require the same order.
	struct Book fp = {
        .publication_year = 2015,
        .title = "Fluent Python",
        .publisher = "OReilly Media",
        .pages = 800,
    };
    printf("Book: %s (%d), by the %s\n", fp.title, fp.publication_year, fp.publisher);
}
// Book: Structure and Interpretation of Computer Programs (1996), by the MIT Press
// Book: Automate the boring stuff with Python (2015), by the No Starch Press
// Book: Fluent Python (2015), by the OReilly Media
```

>[!warning]
> Non initialized fields default to `0` in Options 2 and 3. In Option 1, if no value is assign to a particular field, it will hold _garbage_.

### Passing structs to functions
There are two options:
1. Pass a copy of the struct.
2. Pass a pointer to the struct (**_Often preferred_**).

To pass a struct as a pointer:
```C
#include <stdio.h>

struct Book
{
    int pages;
    int publication_year;
    char *publisher;
    char *title;
    int rating;
};

void set_rating(struct Book *book, int rating)
{
    // book.rating = rating;  ERROR: the pointer book does not have a rating field
    // *book.rating = rating; ERROR: the dereference operator does not work on book.rating
    (*book).rating = rating; // First book is dereferenced and then the . notation is used.
}

int main(void)
{
    struct Book sicp;
    sicp.pages = 200;
    sicp.publication_year = 1996;
    sicp.publisher = "MIT Press";
    sicp.title = "Structure and Interpretation of Computer Programs";
    set_rating(&sicp, 5);

    printf("%d Star book: %s (%d), by the %s\n",
           sicp.rating, sicp.title, sicp.publication_year, sicp.publisher);
}
// 5 Star book: Structure and Interpretation of Computer Programs (1996), by the MIT Press
```

Alternative: **The arrow notation**
```C
#include <stdio.h>

struct Book
{
    int pages;
    int publication_year;
    char *publisher;
    char *title;
    int rating;
};

void set_rating(struct Book *book, int rating)
{
	// The arrow notation is used to access a struct field inside its pointer.
	book->rating = rating;
}

int main(void)
{
    struct Book sicp;
    sicp.pages = 200;
    sicp.publication_year = 1996;
    sicp.publisher = "MIT Press";
    sicp.title = "Structure and Interpretation of Computer Programs";
    set_rating(&sicp, 5);

    printf("%d Star book: %s (%d), by the %s\n",
           sicp.rating, sicp.title, sicp.publication_year, sicp.publisher);
}
// 5 Star book: Structure and Interpretation of Computer Programs (1996), by the MIT Press
```

### Returning structs
```C
#include <stdio.h>

struct Book
{
    int pages;
    int publication_year;
    char *publisher;
    char *title;
    int rating;
};

// Passing struct as a copy
struct Book set_rating_copy(struct Book book, int rating)
{
    book.rating = rating;
    return book;
}

int main(void)
{
    struct Book fp = {
        .publication_year = 2015,
        .title = "Fluent Python",
        .publisher = "OReilly Media",
        .pages = 800};

    struct Book fp_cp = set_rating_copy(fp, 3);
    printf("%d Star book: %s (%d), by the %s\n",
           fp_cp.rating, fp_cp.title, fp_cp.publication_year, fp_cp.publisher);
}
// 3 Star book: Fluent Python (2015), by the OReilly Media
```

>[!tip] Comparing structs
> To compare two structs it is required to compare **_each of its fields_**.

>[!note] Terminology on copy
>- **Deep copy**: When the paths to all the fields on the struct are copied.
>- **Shallow copy**: When only the surface contents are copied (including pointers, instead of following to the content it points to).

## File IO: Text
Data is passed to the program by _streams_, being files one case type of _stream_. This _streams_ are represented in C by the `FILE*` data type. The most common _streams_ are:

| `FILE*`  | Descriptions                                                                      |
| -------- | --------------------------------------------------------------------------------- |
| `stdin`  | Standard input (keyboard)                                                         |
| `stdout` | Standard Output (screen)                                                          |
| `stderr` | Standard Error (screen). It is used to send error messages identifiable by the OS |

### Reading from text file
```C
// hello.txt
//
// HENLO EBURINHAN!


// Define the variable that holds the stream
FILE *file;

// fopen opens and then returns the stream referring to a given file
file = fopen("hello.txt", "r");

// fgetc returns the next character in the file
int c = fgetc(file);
printf("%c\n", c); // H

// fclose closes the stream
fclose(file);
```

>[!note]
>The `fopen()` function returns `NULL` when an error is found (e.g. the file does not exist).

### EOF
It is a value returned by `fgetc()` when there is no more characters do read from a file.
```C
FILE *file = fopen("hello.txt", "r");

int c;
while ((c = fgetc(file)) != EOF)
{
	printf("%c", c);
}
fclose(file);
```

### Read text file line by line
This is possible with `fgets()`.
```C
// fgets(string, sizeof string, stream)
FILE *file = fopen("lorem.txt", "r");

// string buffer long enough to hold one line in the file
char line[1000];

while (fgets(line, sizeof line, file) != NULL)
{
	printf("%s", line);
}
fclose(file);
```

### Reading text line by line (formatted)
```C
// anime.txt
//
// nobara jujutsu_kaisen 16
// kaguya kaguya_sama_love_is_war 17
// yor spy_x_family 27
// kobayashi kobayashi_dragon_maid 25
// nazuna yofukashi_no_uta 40

FILE *file = fopen("anime.txt", "r");
int age;
char name[60];
char show[200];
while (fscanf(file, "%s %s %d", name, show, &age) != EOF)
{
	printf("Name: %s - Show: %s - Age %d\n", name, show, age);
}
fclose(file);
// Name: nobara - Show: jujutsu_kaisen - Age 16
// Name: kaguya - Show: kaguya_sama_love_is_war - Age 17
// Name: yor - Show: spy_x_family - Age 27
// Name: kobayashi - Show: kobayashi_dragon_maid - Age 25
// Name: nazuna - Show: yofukashi_no_uta - Age 40
```

### Writing to file
There are equivalent functions to write text to file:
- `fputc()`: Writes a char;
- `fputs()`: Writes a whole string;
- `fprintf()`: Writes formatted text.
```C
// Get file stream in "Write" mode
FILE *output_file = fopen("output.txt", "w");

fputc('W', output_file);
fputc('\n', output_file);

fputs("HENLON EBURINHAN!\n", output_file);

char *name = "Arthurpmrs";
fprintf(output_file, "My name is: %s.", name);

// W
// HENLON EBURINHAN!
// My name is: Arthurpmrs.
```

## File IO: Binary
The main difference is that the `fopen()` modes get a extra `b`, like `rb` or `wb`.

### Writing bytes
It can be accomplished by using `fwrite()`.
```C
// fwrite(data, size of data type, number of elements, stream)
FILE *f = fopen("bData.bin", "wb");
unsigned char bytes[6] = {12, 244, 78, 77, 4, 11};

fwrite(bytes, sizeof(char), 6, f);
fclose(f);
```

>[!note] unsigned types
> The types `unsigned int` and `unsigned char` represents integers greater of equal to 0 (without sign).
> 
> - Reference: https://www.ime.usp.br/~pf/algoritmos/aulas/int.html

### Reading bytes
```C
// freads(dest variable address, size of data type, amount of elements, stream)
FILE *f = fopen("bData.bin", "rb");
unsigned char c;

while (fread(&c, sizeof(char), 1, f) > 0)
{
	printf("%d ", c);
}
```

>[!important]
> `freads()` returns 0 when the end of file is reached.

## typedef
It is used to alias types with other names, like:
```C
typedef int Inteiro;

// Here, Inteiro behaves just like int.
int main(void)
{
    Inteiro x = 2;
    printf("%d\n", x); // 2 - No Errors
}
```

### Typedef for structs
Using `typedef` it is possible to avoid the need for `struct Car car`
```C
#include <stdio.h>

// Option 1
struct Car
{
    char *name;
    int wheels;
    int doors;
};
typedef struct Car Car;

// Option 2
typedef struct Car
{
    char *name;
    int wheels;
    int doors;
} Car;

// Option 3: Annonymous structs
typedef struct
{
    char *name;
    int wheels;
    int doors;
} Car;


int main(void)
{
    Car car;
    car.name = "Volvo";
    car.wheels = 4;
    car.doors = 2;
}
```

## Pointer Arithmetic
### Adding ints to pointers
Adding 1 to a pointer to a type results in a pointer to the next element of that type stored in memory. This can be useful to to loop over a array or string without using array notation.
```C
int values[] = {1, 2, 3, 4, 5, 6, 7, 100};
size_t size = sizeof(values) / sizeof(int);

for (int i = 0; i < size; i++)
{
	printf("%d: %d\n", i, *(values + i));
}
// 0: 1
// 1: 2
// 2: 3
// 3: 4
// 4: 5
// 5: 6
// 6: 7
// 7: 100
```

### Subtracting ints from pointers
```C
int values[] = {1, 2, 3, 4, 5, 6, 7, 100};
size_t size = sizeof(values) / sizeof(int);

for (int i = 0; i < size; i++)
{
	printf("%d: %d\n", i, *(values + size - 1 - i));
}
// 0: 100
// 1: 7
// 2: 6
// 3: 5
// 4: 4
// 5: 3
// 6: 2
// 7: 1
```

### Subtracting two pointers
This can be useful to know how many items is there between to pointers. Like the length of a string
```C
char *s = "Arthur";
char *f = s;
int i = 0;
while (*f != '\0')
{
	f = (s + i);
	i++;
}
printf("length of %s is %d", s, f - s); // length of Arthur is 6
```

>[!danger]
>Pointer subtraction only works when both pointers point to the same array.

### Array and pointer equivalence
```C
a[b] == *(a + b)
```

These are equivalent:
```C
// Array notation
for (int i = 0; i < size; i++)
{
	printf("%d: %d\n", i, arr[i]);
}

// Pointer notation
for (int i = 0; i < size; i++)
{
	printf("%d: %d\n", i, *(arr + i));
}

// Also, Pointers can be modified (Arrays cant).
for (int i = 0; i < size; i++)
{
	printf("%d: %d\n", i, *(arr++));
}
```

### void Pointers
It points to a value of unknown type. It is mainly used when a function expects another function as arguments (a.k.a _**callback functions**_) and the calling function must not know about the types of the called function, in order to be generic. 
```C
#include <stdio.h>

// This is a function that sorts things
// sort_it() must not know the type of collection
// Neither the types required by fun (callback function)
void sort_it(void *collection, int size, void (*fun)(void *, int))
{
    fun(collection, size);
}

// This is a function that sorts arrays
// It is passed and called by sort_it()
void bubble_sort_arr(void *a, int size)
{
	// Convert the void pointer passed by sort_it() to the
	// specialized pointer type.
    int *arr = a;
    for (int i = 0; i < (size - 1); i++)
    {
        for (int j = 0; j < (size - 1 - i); j++)
        {
            if (arr[j] > arr[j + 1])
            {
                int aux = arr[j];
                arr[j] = arr[j + 1];
                arr[j + 1] = aux;
            }
        }
    }
}

// This is a function that sorts arrays
// It is passed and called by sort_it()
void bubble_sort_str(void *s, int size)
{
    char *str = s;
    for (int i = 0; i < (size - 1); i++)
    {
        for (int j = 0; j < (size - 1 - i); j++)
        {
            if (*(str + j) > *(str + j + 1))
            {
                int aux = *(str + j);
                *(str + j) = *(str + j + 1);
                *(str + j + 1) = aux;
            }
        }
    }
}

int main(void)
{
	// In this case, to be mutable, the string must be defined with array notation.
    char name[] = "Arthur";
    int videos[] = {10, 85, 55, 1, 45, 13};

    sort_it(name, 6, bubble_sort_str);
    sort_it(videos, 6, bubble_sort_arr);

    printf("%s\n", name);
    for (int i = 0; i < 6; i++)
    {
        printf("%d, ", *(videos + i));
    }
}
```

>[!note] Callback functions
> To pass a function to another function the following notation is required:
> `return_type (*fun_parameter_name)(param_type1, param_type2, ...)`
> 
> - Ref: https://en.wikipedia.org/wiki/Function_pointer#Example_in_C, https://stackoverflow.com/questions/9410/how-do-you-pass-a-function-as-a-parameter-in-c
## Manual Memory Allocation
Manual memory allocation refers to the action of reserving some bytes of memory for specific data that needs to persist across blocks, as opposed to local variables which are deallocated automatically.

>[!danger] Memory Leaks
>All manually allocated memory needs to be freed after it is no longer relevant. Otherwise, the program will use unnecessary resources, which is called a _memory leak_. 

To manually allocate and free memory, the `malloc()` and `free()` functions are used, respectively, both belonging to the `stdlib.h` header.
```C
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
	// The size of an int is allocated (returns a pointer)
    int *n = malloc(sizeof(int));

    *n = 2;
    printf("n = %d\n", *n); // n = 2

	// Memory is freed
    free(n);
    // n now points to nothing, so this yields undefined behavior
    printf("n = %d\n", *n); 
}
```

### Prevent errors
If the memory cannot be allocated, `malloc()` will return `NULL`.

```C
#include <stdio.h>
#include <stdlib.h>

int main(void)
{
    int *n;
    if ((n = malloc(sizeof(int))) == NULL)
    {
        printf("Error allocating an int.\n");
        return 0;
    }
    
    // OR
    int *n = malloc(sizeof(int));
    if (n == NULL)
    {
        printf("Error allocating an int.\n");
        return 0;
    }
    ...
}
```

### Allocating an array
```C
// This allocates memory for an array of 100 elements
int *arr = malloc(sizeof(int) * 100);

// Populate the array
for (int i = 0; i < 100; i++)
{
	if ((3 * i) % 9 == 0)
	{
		*(arr + i) = -1;
	}
	else
	{
		*(arr + i) = 3 * i;
	}
}
// Print the elements
for (int i = 0; i < 100; i++)
{
	printf("%d, ", *(arr + i));
}

// Memory deallocation
free(arr);
```

The `malloc()` function allocates the memory without initializing it. So this "spaces" will be filled with garbage. The `calloc()` function does the same thing as `malloc()` but initializes the spaces in memory to `0`. 
```C
// Allocate and initialize separately
int *arr1 = malloc(sizeof(int) * 100);
for (int i = 0; i < 100; i++)
{
	*(arr1 + i) = 0;
}

// Allocate and initialize automatically
int *arr2 = calloc(100, sizeof(int));
```

### Reallocating memory
https://stackoverflow.com/questions/62326230/how-to-create-an-array-without-knowing-the-size-in-c-language
https://stackoverflow.com/questions/3536153/c-dynamically-growing-array