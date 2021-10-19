# Parse Tree & Abstract Syntax Tree

==看作业==

## Parse Tree

一个Parse Tree只与一个Leftmost Derivation相对应

## Abstract syntax tree

一个抽象语法树可以对应多个推导过程

[compiler construction - What's the difference between parse tree and AST? - Stack Overflow](https://stackoverflow.com/questions/5026517/whats-the-difference-between-parse-tree-and-ast)

# Ambiguity

## Ambiguity grammars

可生成带有两个不同分析树（语法树也一样的？不一样）的串（L(G)）的文法称作二义性文法（ambiguous grammar）。



造成二义性的原因是：文法中没有体现出结合率和优先级。



Two basic methods :

1. a rule: that specifies in each ambiguous case which of the parse trees (or syntax trees) is the correct one. Such a rule is called a **disambiguating** rule.
    * The advantage: it corrects the ambiguity without changing (and possibly complicating) the grammar.
    * The disadvantage: the syntactic structure of the language is no longer given by the grammar alone.
2. change the grammar into a form that forces the construction of the correct parse tree.

 in either method we must first decide which of the trees in an ambiguous case is the correct one.



**Leftmost Derivations**：指它的每一步中最左的非终结符都要被替换的推导。（左边还有非终结符的时候，不能去推导右边的东西

---

### Chapter 3 上下文无关文法(CFG)及分析

##### 语法树

语法树的结构在很大程度上依赖于语言特定的语法结构。这种树通常被定义为动态数据结构，该结构中的每个节点都由一个记录组成，而这个记录的域包括了编译后面过程所需的特性。

##### 推导(Derivation)

推导是在文法规则的右边进行选择的一个结构名字替换序列。 推导以一个结构名字开始并以记号符号串结束。在推导的每一个步骤中，使用来自文法规则的选择每一次生成一个替换。  

![image-20200513234015228](../../CP/Res/YCJ_Notes/assets/image-20200513234015228.png)

由推导从起始符号中得到的所有记号符号的串集是被表达式的文法定义的语言。

$L(G)=\{s|exp=>*s\}$

##### 例1：考虑下面语句的极为简化的文法

![image-20200513234617894](../../CP/Res/YCJ_Notes/assets/image-20200513234617894.png)

推导产生的串有：

![image-20200513234625939](../../CP/Res/YCJ_Notes/assets/image-20200513234625939.png)

##### 例2：考虑下列文法

![image-20200514220956367](../../CP/Res/YCJ_Notes/assets/image-20200514220956367.png)

![image-20200514221029763](../../CP/Res/YCJ_Notes/assets/image-20200514221029763.png)

#### 3.3 分析树与抽象语法树

##### 最左推导

![image-20200514221425482](../../CP/Res/YCJ_Notes/assets/image-20200514221425482.png)

#### 3.4 二义性

文法有可能允许一个串有多于一个的分析树。

例如在前面作为标准示例的简单整型算术文法中 ：

![image-20200514222223225](../../CP/Res/YCJ_Notes/assets/image-20200514222223225.png)

可生成带有两个不同分析树的串的文法称作二义性文法***（ambiguous grammar）***

##### 二义性产生的原因

- 优先级
- 结合律

##### 解决二义性的方法

- 设置规则
- 修改文法

##### 例：运算文法

![image-20200514222823407](../../CP/Res/YCJ_Notes/assets/image-20200514222823407.png)

之所以不采取$exp~~addop~~exp$是因为算符两边的递归都允许每一边匹配推导（因此也在分析树和语法树）中的算符重复，会产生二义性。

##### 左递归会产生左结合

##### 悬挂else问题

![image-20200514223115271](../../CP/Res/YCJ_Notes/assets/image-20200514223115271.png)

语法树1：

![image-20200514223152646](../../CP/Res/YCJ_Notes/assets/image-20200514223152646.png)

语法树2：

![image-20200514223201237](../../CP/Res/YCJ_Notes/assets/image-20200514223201237.png)

修改后的文法：

![image-20200514223230613](../../CP/Res/YCJ_Notes/assets/image-20200514223230613.png)

#### 3.5 EBNF和语法图

##### 闭包

![image-20200514224527520](../../CP/Res/YCJ_Notes/assets/image-20200514224527520.png)

![image-20200514224555666](../../CP/Res/YCJ_Notes/assets/image-20200514224555666.png)

##### 选择

![image-20200514224616082](../../CP/Res/YCJ_Notes/assets/image-20200514224616082.png)









