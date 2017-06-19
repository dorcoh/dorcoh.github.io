---
published: true
---
I describe here a patch I've made to SearchTreeSampler - an approximate SAT model counter for CNF formulas by Stefano Ermon.

The patch leverages the uniform solution sampling to compute the entropy of a formula, which I'll describe below.

# Preliminaries

SAT problem is defined as deciding whether there exists a solution for a propositional logic formula, in CNF format (a normal form).

$$ \varphi = 3 $$
