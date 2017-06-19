---
published: true
---
I describe here a patch I've made to SearchTreeSampler - an approximate SAT model counter for CNF formulas by Stefano Ermon.

The patch leverages the uniform solution sampling to compute the entropy of a formula, which I'll describe below.

# Preliminaries
