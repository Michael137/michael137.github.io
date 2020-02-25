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
	
    $$(S, i, L, Tran)$$,

where
* S: set of states
* i: initial state
* L: set of labels that specify the possible set of actions between two states
* $$Tran \subset S \times L \times S$$: is the *transition relation*
  * This is the set of transitions that occurred in the system
  * *Tran* describes the behaviour of the LTS

# Example