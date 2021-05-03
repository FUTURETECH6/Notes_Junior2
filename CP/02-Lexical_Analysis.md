```
[abcd]
[a-z]
[a-z A-Z]
```

用code表示DFA和NFA

1. 纯流程，顺序结构
2. 嵌套，第一层state，第二层input
3. 二维数组表示DFA的transition table

# Regex to DFA

## Thompson's construction(Regex -> NFA)

![](assets/image-20210312125218866.png)

## the Subset Construction(NFA -> DFA)

中文课本P48

ε闭包

## DFA最小化

### 1

![](assets/image-20210423104118020.png)

### 2

<img src="assets/image-20210425211924213.png" style="zoom:67%;" />

<img src="assets/image-20210425211932636.png" style="zoom:67%;" />

### 3

<img src="assets/image-20210425212151740.png" style="zoom: 67%;" />

<img src="assets/image-20210425211945795.png" alt="image-20210425211945795" style="zoom:67%;" />