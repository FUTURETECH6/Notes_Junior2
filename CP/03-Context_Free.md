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