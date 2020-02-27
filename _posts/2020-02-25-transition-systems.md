---
layout: post
published: true
title: Transition Systems
date: '2020-02-25'
---
# Motivation
Transition systems appear in many facets of system modelling; they give an intuitive formalism to the operation of a system, namely functions on state.

# Formalisms
## Definitions

**<u>Definition 1: Labelled Transition System (LTS)</u>**:

The following tuple characterizes a LTS:
	
  $$(S, i, L, \mathit{Tran})$$,

where
* S: set of states
  * What is a state?:
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

$$\mathit{Tran_{*}} = \mathit{Tran} \cup \{(s, *, s) \: | \: s \in S\}$$

## Operations on Transition Systems
**<u>Definition 5</u>**: Restriction:

Let transition system $$T' = (S, i, L', \mathit{Tran'})$$ and $$L \subseteq L'$$. *Restriction* $$T' \upharpoonright \lambda$$ results in the transition system $$T = \{(s, \alpha, t) \in \mathit{Tran'} \: | \: \alpha \in L \}$$

where $$\lambda : L \hookrightarrow L'$$ takes label, $$\alpha$$, in $$L$$ to label, $$\alpha$$, in $$L'$$.

In some process languages, such as Milner's CCS, communication is performed via input and output channels between process that are connected via ports. The restriction operation limits communication to a set of ports. In transition systems, restriction removes a set of transitions from its labelling set.

**<u>Definition 6</u>**: Relabelling

**<u>Definition 7</u>**: Product

**<u>Definition 8</u>**: Composition

**<u>Definition 9</u>**: Sum

**<u>Definition 10</u>**: Prefixing


# Example

# Sources
[1] [Models for Concurrency - Glynn Winskel and Mogens Nielsen](https://dl.acm.org/doi/10.5555/218623.218630)
