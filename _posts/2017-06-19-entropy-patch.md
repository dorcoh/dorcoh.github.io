---
published: true
---
I describe here a patch I've made to SearchTreeSampler - an approximate SAT model counter by [Stefano Ermon](https://cs.stanford.edu/~ermon/).

The patch leverages the uniform solution sampling method to compute the entropy of a formula, a new property of formula which I'll define below.

# Preliminaries

## SAT

Let \\( X_1 ,... X_n \\) be boolean variables

A boolean formula \\( \varphi \\) is said to be in CNF if it's a logical conjunction of a set of clauses \\( C_1,...,C_n \\), where each clause \\( C \\) is a logical disjunction of a set of literals (could be a variable or it's negation). A clause for example: \\( (x_1 \vee \neg x_2) \\)

SAT problem is defined as deciding whether there exists an assignment that satisfies \\( \varphi \\)

An example: \\( \varphi=(x_1 \vee \neg x_2 \vee x_3)\wedge(x_4 \vee \neg x_1)\wedge(x_2 \vee \neg x_3) \\)

Satisfying assignment: \\( \{ x_1=1,x_2=1,x_3=1,x_4=1 \} \\)

\\( \Longrightarrow \varphi \\) is SATISFIABLE
