---
published: true
---
I describe here a patch I've made to SearchTreeSampler - an approximate SAT model counter by [Stefano Ermon](https://cs.stanford.edu/~ermon/).

The patch leverages the uniform solution sampling method to compute the entropy of CNF formula, a new property of SAT formulas which I'll define below.

# Preliminaries

## SAT

Let \\( X_1 ,... X_n \\) be boolean variables

A boolean formula \\( \varphi \\) is said to be in CNF if it's a logical conjunction of a set of clauses \\( C_1,...,C_n \\), where each clause \\( C \\) is a logical disjunction of a set of literals (could be a variable or it's negation). A clause for example: \\( (x_1 \vee \neg x_2) \\)

SAT problem is defined as deciding whether there exists an assignment that satisfies \\( \varphi \\)

An example: \\( \varphi=(x_1 \vee \neg x_2 \vee x_3)\wedge(x_4 \vee \neg x_1)\wedge(x_2 \vee \neg x_3) \\)

Satisfying assignment: \\( \{ x_1=1,x_2=1,x_3=1,x_4=1 \} \\)

\\( \Longrightarrow \varphi \\) is SATISFIABLE

## Model counting

Let \\( V \\) be the set of boolean variables of \\( \varphi \\) and let $$ \sigma $$ be the set of all possible assignments to these variables

An assignment \\( \sigma \in \sum \\) is a mapping that assigns a value in \\( \{0,1\} \\) to each variable in \\( V \\)

Define the weight \\( w(\sigma) \\) to be 1 if \\( \varphi \\) is satisfied by \\( \sigma \\) and 0 otherwise

In this context $$  W=\sum_{\sigma \in \sum}^{} w(\sigma)=\#(\varphi)  $$ is the actual number of solutions to  \\( \varphi \\)

## Entropy


