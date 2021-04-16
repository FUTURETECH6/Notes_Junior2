# Parse Tree & Abstract Syntax Tree

## Abstract syntax tree

[compiler construction - What's the difference between parse tree and AST? - Stack Overflow](https://stackoverflow.com/questions/5026517/whats-the-difference-between-parse-tree-and-ast)

# Ambiguity

## Ambiguity grammars

Two basic methods :

1. a rule: that specifies in each ambiguous case which of the parse trees (or syntax trees) is the correct one. Such a rule is called a **disambiguating** rule. 
    * The advantage: it corrects the ambiguity without changing (and possibly complicating) the grammar. 
    * The disadvantage: the syntactic structure of the language is no longer given by the grammar alone.
2. change the grammar into a form that forces the construction of the correct parse tree.  

 in either method we must first decide which of the trees in an ambiguous case is the correct one. 