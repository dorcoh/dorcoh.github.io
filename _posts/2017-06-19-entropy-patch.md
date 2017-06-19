---
published: true
---
I describe here a patch I've made to SearchTreeSampler - an approximate SAT model counter by [Stefano Ermon](https://cs.stanford.edu/~ermon/).

The patch leverages the uniform solution sampling method to compute the entropy of CNF formula, a new property of SAT formulas which I'll define below.

# Preliminaries

## SAT

Let \\( X_1 ,... X_n \\) be boolean variables

A boolean formula \\( \varphi \\) is said to be in CNF if it's a logical conjunction of a set of clauses \\( C_1,...,C_n \\), where each clause \\( C \\) is a logical disjunction of a set of literals (a base element that could be a variable or it's negation). A clause for example: \\( (x_1 \vee \neg x_2) \\)

SAT problem is defined as deciding whether there exists an assignment that satisfies \\( \varphi \\)

An example: \\( \varphi=(x_1 \vee \neg x_2 \vee x_3)\wedge(x_4 \vee \neg x_1)\wedge(x_2 \vee \neg x_3) \\)

Satisfying assignment: \\( \{ x_1=1,x_2=1,x_3=1,x_4=1 \} \\)

\\( \Longrightarrow \varphi \\) is SATISFIABLE

## Model counting

Let \\( V \\) be the set of boolean variables of \\( \varphi \\) and let $$ \sum $$ be the set of all possible assignments to these variables

An assignment \\( \sigma \in \sum \\) is a mapping that assigns a value in \\( \{0,1\} \\) to each variable in \\( V \\)

Define the weight \\( w(\sigma) \\) to be 1 if \\( \varphi \\) is satisfied by \\( \sigma \\) and 0 otherwise

In this context $$  W=\sum_{\sigma \in \sum}^{} w(\sigma)=\#(\varphi)  $$ is the actual number of solutions to  \\( \varphi \\)

## Entropy

Let $$ \varphi $$ be a propositional CNF formula, $$ var(\varphi) $$ its set of variables and $$ lit(\varphi)$ its set of literals. 

If $$ \varphi $$ is SATISFIABLE, we denote by $$ r(l) $$, for $$ l \in lit(\varphi) $$, the ratio of solutions to $$ \varphi $$ that satisfy $$ l $$. Hence for all $$ v \in var(\varphi) $$ , it holds that $$ r(v) + r(\bar v) = 1 $$

The entropy of a variable $$ v \in V $$ is defined by:

$$ e(v) = -r(v)logr(v) -r(\bar v)logr(\bar v) $$ 

_The entropy of a satisfiable formula is the average entropy of its variables_

Motivation: Since model counting is a $$ #P $$ problem entropy is hard to compute and requires $$ | var(\varphi) | $$ calls to a model counter

# STS and patch explained

## Hashing and optimization

STS, Search tree sampler is an approximate model counter designed by Stefano Ermon. It uses hashing and optimization technique in order to count solutions. Briefly, in the context of model counting, hashing means that on each 'level' the algorithm explores, we shrink the configuration space. Optimization means using a SAT  solver as an oracle to tell the algorithm if still solutions exist (after shrinking). This technique is also used in probablistic inference problems.

Specifically STS works by sampling uniform (controlled by a parameter) solutions. I took advantage of this mechanism and on each run of the algorithm I recorded those uniform solutions, in order to cheaply approximate the entropy, with only one run of STS instead of $$ n $$ runs - as the size of the formula. Computing the entropy requires to compute $$ r(v) $$ for each literal, $$ r(v) $$ is the ratio of solutions that the literal $$ v $$ appears in, from all the formula solutions. So technically if we have a decent amount of uniform solutions, we can approximate the variables entropy.

## Patch explained

STS is built on top of Minisat, the algorithm is implemented on `Main.cc`

In the following I'll describe my patch:

First I added the needed variables

```cpp
  std::vector<int> varSolCountPos; // counter for pos
  std::vector<int> varSolCountNeg; // counter for neg
  std::vector<double> rvPos;		 // vector for r(v)
  std::vector<double> rvNeg;
  std::vector<double> ev;			 // vector for e(v)
  std::vector<double> entropy;	 // vector for formula entropy for each sample
```




