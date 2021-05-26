[TOC]

# Stack-Based Runtime Env

## SB Env w/o Local Procedures

* Frame pointer or **fp**, a pointer to the current 《activation record》 to allow access to local variable.
    * control link or dynamic link, a point to a record of the immediately **preceding** activation.（也称作old fp，因为是fp的旧值）
* Stack pointer or **sp**, a point to the last location allocated on the call stack.（栈顶）



**Example1**

```c
#include <stdio.h>
int x, y;

int gcd(int u, int v) {
    return v == 0 ? u : gcd(v, u % v);
}

int  main() {
    scanf("%d%d", &x, &y);
    printf("%d\n", gcd(x, y));
    return 0;
}
```

<img src="assets/image-20210516160234217.png" style="zoom: 67%;" />

在每个新*活动记录*中，*控制链*指向先前活动记录的*控制链*。还请大家注意，fp指向当前*活动记录*的*控制链*，因此在下一个调用中当前的fp就会变成下一个*活动记录*的*控制链*了。



**Example2**

```c
int x = 2;
void g(int); /*prototype*/

void f(int n) {
    static int x = 1;
    g(n);
    x--;
}
void g(int m) {
    int y = m - 1;
    if (y > 0) {
        f(y);
        x--;
        g(y);
    }
}
int main() {
    g(x);
    return 0;
}
```

<img src="assets/image-20210516164927146.png" style="zoom: 67%;" />

Stack structure:

```
main
main g(2)            // 第一次
main g(2) f(1)
main g(2) f(1) g(0)  // 第二次
main g(2) f(1)
main g(2)
main g(2) g(1)       // 第三次
main
```

**Activation Tree**

<img src="assets/image-20210516172827834.png" style="zoom: 67%;" />

### Details

#### Access to Names

* parameters and local variable must be found <u>by offset from the current fp</u>.
* In most language, the offset can be statically computable by the compiler.

#### Calling Sequence

**enter**:

1. Compute the arguments and store them in their correct positions in the new activation record of the procedure;
2. Store the fp as the control link in the new activation record;（就相当于old fp）
3. Change the fp so that it points to the beginning of the new activation record;（fp := sp）
4. Store the return address in the new activation record;（存RA）
5. Perform a jump to the code of the procedure to be called.（jmp）

**exit**:

1. Copy the fp to the sp.
2. Load the control link into the fp.
3. Perform a jump to the return address.
4. Change the sp to pop the arguments.

#### Dealing with variable-length data

What vary? number of objs / size of each obj



2 examples

* The number of arguments in a call may vary from call to call
    * ex: printf
    * deal: pushing the arguments to a call <u>in reverse order</u> onto the runtime stack
        * The first parameter is always located at a fixed offset from the fp in the implementation described above.
* The size of an array parameter or a local array variable may vary from call to call
    * ex: Ada

#### Local Temporaries and Nested Declarations

**Local temporaries**

* `x[i] = (i + j) * (i / k + f(j))`
    * 3 partial results need to be saved across the call to `f(j)`
        * The address of `x[i]`;
        * The sum `i+j`
        * The quotient `i/k`;
* Deal
    * reg
    * stack
        * <img src="assets/image-20210516210540962.png" style="zoom: 67%;" />

**Nested Decl**

 ```c
 void p(int x, double y) {
     char a;
     int i;
 
     // ...
 
     {  // Block A
         double x;
         int j;
         // ...
     }
 
     // ...
 
     {  // Block B
         char *a;
         int k;
         // ...
     }
 
     // ...
 }
 ```

每个块可以视作一个基于上一层的global的局部procedure

* 进入块A：<img src="assets/image-20210517084757468.png" style="zoom:67%;" />
* 进入块B：<img src="assets/image-20210517084813427.png" style="zoom:67%;" />

## SB Env with Local Procedures

When local procedure declarations are permitted, there are maybe <u>non-local and non-global</u> reference to var.

Ex.

```pascal
program xx;

procedure p;
var n: integer;

    procedure q;
    begin
        (* a ref to `n` is now non-local and non-global,
           but it should be the p.n *)
    end; (* q end *)
    
    procedure r(n: integer);
    begin
        q;
    end; (* r end *)

begin
    n := 1;
    r(2);
end; (* p end *)

begin (* main *)
    p;
end;
```

<img src="assets/image-20210517145128261.png" style="zoom:67%;" />

**Sol**. Use Access Link

将一个称作访问链 (access link)的额外簿记信息添加到每个活动记录中。除了可以指向代表过程的定义环境而不是调用环境之外，访问链与控制链相似。

* Access link represents the <u>defining environment</u> of the procedure; access link is sometimes also called the static link.
* Control link represents the <u>calling environment</u> of the procedure.

<img src="assets/image-20210517150209618.png" style="zoom:67%;" />

\* p自己没有access link：因为p是个global procedure，所以p中的non-local ref一定是global的

### Details

#### ==Calling Sequence==

调用时将access link压入到fp前的stack，退出时用一个额外的量来修改sp以便删掉access link（？

## SB Env with Procedure Param

将procedure作为param传递（函数指针）时，必须将procedure的access link与procedure code的pointer一同传递。

# Dynamic Memory

