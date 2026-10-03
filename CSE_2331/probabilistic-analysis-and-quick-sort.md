# Probabilistic Analysis & Quick Sort

## Introduction

This technical note summarize the tool and logic to use in probabilistic analysis.

## Type of probabilistic analysis

There are 4 different versions about the probabilistic analysis

- Random if
  - Recursion
  - Non recursion
- Random for
  - only loop
  - loop out of loop
- Random-sized recursive call ( i.e. Math line problem )
- Loop that randomly terminate

### **1. Random if**

### 1.1 Recursion Random if

```text
loop
    k = Random(n)
    if Boolean condition on k then
        Make recursive call
    else
        Make recursive call
    end if
```

For example

```java
function Function1(A[], n) {
    k = random(n)
    if (k mod 4 = 1) {
        x = x + Function1(A[], n/2)
    } else {
        x = x + Function1(A[], n/3)
    }
}
```

We can use the following fomula:

$$
ET(n)=\Pr(A)\,*\,E(T(n)\,|\,A)\,+\,\Pr(Not\,A)\,*\,E(T(n)\,|\,Not\;A)
$$

From the fomula, we can easily get that

$$
ET(n)=\frac{1}{4}\,*\,T(\,\frac{n}{2}\,)\,+\,(1\,-\,\frac{1}{4})\,*\,T(\,\frac{n}{3}\,)
$$

2.


```text
1. k = Random(n)
2. Loop that runs f(k) times
```

$$
ET(n)=\sum_{q=1}^{n}\Pr(k=q)t(k=q)
$$

### Reference List

1. *"CSE2331_Types_of_Probabilistic_Problems"* Created by Professor Painter.
2. *"Quick Sort Worksheet AU26"* Created by Professor Painter.
3. *"CSE2331_QuickSort_and_Probabilistic_Analysis_Homework"* Created by Professor Painter.
