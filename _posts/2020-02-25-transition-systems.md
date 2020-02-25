---
layout: post
published: true
title: Transition Systems
date: '2020-02-25'
---
# Motivation
Transition systems appear in many facets of system modelling; they give an intuitive formalism to the operation of a system, namely functions on state.

# Formalisms
## What is a state?
## Definition

The following tuple characterizes a labelled tranisition system (LTS):
	
  $$(S, i, L, \mathit{Tran})$$,

where
* S: set of states
* i: initial state
* L: set of labels that specify the possible set of actions between two states
* $$\mathit{Tran} \subset S \times L \times S$$: is the *transition relation*
  * This is the set of transitions that occurred in the system
  * *Tran* describes the behaviour of the LTS
  * A transition $$s \xrightarrow{\a} s'$$ between two states, *s, s'*, is described by $$(s, a, s') \in \mathit{Tran}$$

# Example

# Sources
* [Models for Concurrency - Glynn Winskel and Mogens Nielsen](https://dl.acm.org/doi/10.5555/218623.218630)