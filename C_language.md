## C language

### C语言创建内存

#### malloc()：

`malloc()` 函数用于在堆上分配指定数量的字节。它返回分配的内存块的指针，如果分配失败则返回 `NULL`。

语法为：`void* malloc(size_t size);` `size`表示要分配的内存大小，以字节为单位。

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int*)malloc(sizeof(int));  // 在堆上分配一个整数的内存
    if (p != NULL) {
        *p = 5;
        printf("Value: %d\n", *p);
        free(p);  // 释放分配的内存
    } else {
        printf("Memory allocation failed\n");
    }
    return 0;
}

```



#### calloc()：

`calloc()` 类似于 `malloc()`，但有两个区别：它接受两个参数，并且分配的内存会被自动初始化为零。

语法为：`void* calloc(size_t num, size_t size);` `num`元素的数量、`size`每个元素的大小，以字节为单位。

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int*)calloc(4, sizeof(int));  // 在堆上分配4个整数的内存并初始化为0
    if (p != NULL) {
        for (int i = 0; i < 4; i++) {
            printf("p[%d] = %d\n", i, p[i]);  // 输出每个元素的值，应为0
        }
        free(p);  // 释放分配的内存
    } else {
        printf("Memory allocation failed\n");
    }
    return 0;
}

```



#### realloc():

`realloc()` 用于更改之前分配的内存块的大小。它可以扩大或缩小已分配内存的大小，并返回新内存块的指针。

语法为：`void* realloc(void* ptr, size_t newSize); `  `ptr`：之前分配的内存块指针。`newSize`：新的内存大小，以字节为单位。

```c
#include <stdio.h>
#include <stdlib.h>

int main() {
    int *p = (int*)malloc(2 * sizeof(int));  // 初始分配2个整数的内存
    if (p != NULL) {
        p[0] = 1; p[1] = 2;
        int *newP = realloc(p, 4 * sizeof(int));  // 改变内存大小以容纳4个整数
        if (newP != NULL) {
            p = newP;
            p[2] = 3; p[3] = 4;
            for (int i = 0; i < 4; i++) {
                printf("p[%d] = %d\n", i, p[i]);
            }
            free(p);  // 释放分配的内存
        } else {
            free(p);  // 如果realloc失败，原始内存需要释放
            printf("Unable to realloc memory\n");
        }
    } else {
        printf("Initial memory allocation failed\n");
    }
    return 0;
}

```



### 内存释放-悬挂指针问题

当我们进行内存的分配后，需要将内存进行释放。使用free函数，语法为：`void free(void* ptr);` `ptr`：指向先前由动态内存分配函数分配的内存块的指针。

示例：

``` C
#include <stdio.h>
#include <stdlib.h>

int main() {
    // 使用malloc分配一个包含5个整数的数组
    int *array = (int*)malloc(5 * sizeof(int));
    
    if (array == NULL) {
        // 如果内存分配失败，则输出错误消息并退出程序
        fprintf(stderr, "Memory allocation failed\n");
        return 1;
    }

    // 初始化数组
    for (int i = 0; i < 5; i++) {
        array[i] = i;
    }

    // 打印数组内容
    for (int i = 0; i < 5; i++) {
        printf("%d ", array[i]);
    }
    printf("\n");

    // 释放数组占用的内存
    free(array);

    // 注意：释放后不应再使用array指针，可将其设为NULL防止悬挂指针问题
    array = NULL;

    return 0;
}

```

****

- **避免悬挂指针**：释放内存后，原来的指针变成了悬挂指针，这意味着它指向的是不再有效的内存区域。为了避免使用悬挂指针，最好在调用 `free()` 后将指针设置为 `NULL`。

- **重复释放**：尝试释放同一个内存块两次是危险的，会导致运行时错误。确保每个动态分配的内存块只被释放一次。
- **释放非动态分配的内存**：仅应释放通过 `malloc()`, `calloc()`, 或 `realloc()` 分配的内存。尝试释放其他内存可能导致未定义行为。



### 静态函数

#### 定义：

在C语言中，静态函数的概念涉及到函数的作用域和链接属性。通过使用关键字 `static` 定义的函数，这种函数具有文件作用域，**这意味着它只能在定义它的文件内部被访问和调用**。这种属性对于封装和模块化编程非常有用。

#### 特性：

- **局部可见性**：静态函数只在其定义的源文件中可见，其他源文件不能链接到这个函数。这有助于防止函数名称的冲突，并且可以在不同的文件中使用相同的函数名而不会互相干扰。
- **保持状态**：与静态变量类似，静态函数在程序的执行期间一直存在，但它的作用域限制在定义它的文件内。

#### 静态函数的用途：

- **封装**：静态函数通常用于封装仅在某个文件内部使用的功能。这样做不仅可以隐藏实现细节，还可以提高模块的独立性和可维护性。
- **避免命名冲突**：在大型项目中，可能会有许多辅助函数，它们在不同的文件中用于不同的目的。将这些函数声明为静态的可以防止同名函数之间的链接错误和命名冲突。
- **优化**：编译器可能会对静态函数进行更优化的处理，因为编译器知道这些函数只在一个文件中被调用，这可能有助于函数调用的内联处理和其他性能优化。

#### 示例：

```C
#include <stdio.h>

// 静态函数声明
static void displayMessage() {
    printf("Hello, I am a static function.\n");
}

int main() {
    displayMessage();  // 调用静态函数
    return 0;
}

// 尝试从另一个文件调用 displayMessage() 将导致编译错误

```



