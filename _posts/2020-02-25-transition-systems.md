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
* $$\mathit{Tran} \subset S \times L \times S$$: is the *transition relation*
  * This is the set of transitions that occurred in the system
  * *Tran* describes the behaviour of the LTS
  * A transition $$s \xrightarrow{\alpha} s'$$ between two states is described by: $$(s, a, s') \in \mathit{Tran}$$
  * A transition $$s \xrightarrow{v} s'$$ where $$v = \alpha_{1}\alpha_{2} \dots \alpha_{n}$$ describes a series of transitions of the form:
  
  $$s \xrightarrow{\alpha_{1}} s_{1} \xrightarrow{\alpha_{2}} \dots \xrightarrow{\alpha_{n}} s_{n}$$

**<u>Definition 2: Reachability</u>**:

# Example

# Sources
* [Models for Concurrency - Glynn Winskel and Mogens Nielsen](https://dl.acm.org/doi/10.5555/218623.218630)
