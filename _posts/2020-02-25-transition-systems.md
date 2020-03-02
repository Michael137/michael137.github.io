---
layout: post
published: true
title: Transition Systems
date: '2020-02-25'
---
# Motivation
Transition systems appear in many facets of system modelling; they give an intuitive formalism to the operation of a system, namely functions on state.

Transition systems give a convienent model for reasoning about inifite-state systems. There is a fundamental relationship between semantics of programming languages and theory of transition systems. In a sense describe the operational semantics of process calculi such as CCS and CSP. Conversely, a transition system can be derived from the operational semantics of a language. The semantics of CCS allow for the relabelling, restriction and parallel composition of processes. Below is a summary of how these operatins can be described in terms of operations on transition systems.

Below is a summary of the definitions of transitions systems found in Winskel and Nielsen [1] together with their categorical implications.

# Formalisms
## Definitions

**<u>Definition 1: Labelled Transition System (LTS)</u>**:

The following tuple characterizes a LTS:
	
  $$(S, i, L, \mathit{Tran})$$,

where
* S: set of states
[comment]: <> (* What is a state?)
* i: initial state
* L: set of labels that specify the possible set of actions between two states
* $$\mathit{Tran} \subseteq S \times L \times S$$: is the *transition relation*
  * This is the set of transitions that occurred in the system
  * *Tran* describes the behaviour of the LTS
  * A transition $$s \xrightarrow{\alpha} s'$$ between two states is described by: $$(s, a, s') \in \mathit{Tran}$$
  * A transition $$s \xrightarrow{v} s'$$ where $$v = \alpha_{1}\alpha_{2} \dots \alpha_{n}$$ describes a series of transitions of the form:
  
  $$s \xrightarrow{\alpha_{1}} s_{1} \xrightarrow{\alpha_{2}} \dots \xrightarrow{\alpha_{n}} s_{n}$$
    * A sequence $$v = nil$$ denotes the non-existence of a transition between two states

**<u>Definition 2: Reachability</u>**: $$T = (S, i, L, \mathit{Tran})$$ is reachable iff for every label, $$\alpha$$, there is a transition, $$(s, a, s') \in \mathit{Tran}$$.

**<u>Definition 3: Acyclicity</u>**: $$T$$ is acyclic iff for all sequences of labels, $$v$$, if $$s \xrightarrow{v} s$$ then $$v = nil$$.

**<u>Definition 4: Idle transitions</u>**: An *idle transition* of a transition system, $$T = (S, i, L, \mathit{Tran})$$, is a transition, $$(s, *, s)$$, where $$s \in S$$. A transition system with idle transitions is defined as:

$$\mathit{Tran_{*}} = \mathit{Tran} \cup \{(s, *, s) \: \vert \: s \in S\}$$

## Operations on Transition Systems
**<u>Definition 5</u>**: Restriction

- Let transition system $$T' = (S, i, L', \mathit{Tran'})$$ and $$L \subseteq L'$$.
- *Restriction* $$T' \upharpoonright \lambda$$ results in the transition system $$T = \{(s, \alpha, t) \in \mathit{Tran'} \: \vert \: \alpha \in L \}$$

where $$\lambda : L \hookrightarrow L'$$ takes label, $$\alpha$$, in $$L$$ to label, $$\alpha$$, in $$L'$$.

In some process languages, such as Milner's CCS, communication is performed via input and output channels between processes that are connected via ports. The restriction operation limits communication to a set of ports. In transition systems, restriction removes a set of transitions from its labelling set.

**<u>Definition 6</u>**: Relabelling

- Let transition system $$T = (S, i, L, \mathit{Tran})$$
- Let $$\lambda : L \rightarrow L'$$ be a morphism between relabelling sets
- *Relabelling* $$T\{\lambda\}$$ results in the transition system $$T' = (S, i, L', \mathit{Tran'})$$

where $$\mathit{Tran'} = \{(s, \lambda(a), s') \: \vert \: (s, a, s') \in \mathit{Tran}\}$$

**<u>Definition 7</u>**: Product

- Let transition system $$T_0 = (S_0, i_0, L_0, \mathit{Tran_0})$$
- Let transition system $$T_1 = (S_1, i_1, L_1, \mathit{Tran_1})$$
- The *product* $$T_0 \times T_1 = (S, i, L, \mathit{Tran})$$

where

   - $$S = S_0 \times S_1$$ with $$i = (i_0, i_1)$$ and projections
      - $$\rho_0 : S_0 \times S_1 \rightarrow S_0$$
      - $$\rho_1 : S_0 \times S_1 \rightarrow S_1$$
   - $$L = L_0 \times_{*} L_1 = \{(a, *) \: \vert \: a \in L_0 \} \cup \{(*, b) \: \vert \: b \in L_1\} \cup \{(a,b) \: \vert \: a \in L_0, b \in L_1\}$$ and projections $$\pi_0$$, $$\pi_1$$
   - $$(s, a, s') \in \mathit{Tran_{*}} \iff (\rho_0(s), \pi_0(a), \rho_0(s')) \in \mathit{Tran_0*} \& (\rho_1(s), \pi_1(a), \rho_1(s')) \in \mathit{Tran_1*}$$

Synchronization in process languages occurs via parallel composition, e.g., by combining input channels of one process with the output channels of another. Below definition implies that the product on transition systems is a special case of parallel composition where all possible synchronizations are allowed.

**Example Product**
Given two transition systems, $$T_0$$ and $$T_1$$, with labelling sets (i.e., set of possible actions) $$\{a\}$$ and $$\{b\}$$ respectively:

$$T_0$$
![LTS1](/img/post0_lts_arr1.png){:height="120px" width="60px" .center-image}

$$T_1$$
![LTS2](/img/post0_lts_arr2.png){:height="120px" width="60px" .center-image}

Then,

- $$\{a\} \times_{*} \{b\} = \{(a,*), (a, b), (*, b)\}$$
- Diagrammatically the product is expressed as,

![LTS_PROD](/img/post0_lts_prod.png){:height="120px" width="150px" .center-image}

The transition $$(a, b)$$ represents a synchronized transition while transitions involving only a single label (e.g., $$(a, *)$$) represent a process performing an unsynchronized transition. As hinted at earlier, this type of parallel composition essentially allows all processes to perform all combinations of possible synchronized/unsynchronized transitions, which is not representative of real-world systems. However, combining the product on LTSes with restriction and relabelling can achieve more useful parallel compositions.

**<u>Definition 8</u>**: Parallel Composition

**<u>Definition 9</u>**: Sum

**<u>Definition 10</u>**: Prefixing


# Example

# Sources
[1] [Models for Concurrency - Glynn Winskel and Mogens Nielsen](https://dl.acm.org/doi/10.5555/218623.218630)
