<meta http-equiv="refresh" content="2">

# Knowlege Representation and Reasoning
## Representation and Reasoning System
### Definitions
* **Formal Language** : legal sentences
* **Semantics** : meaning of the symbols
* **Reasoning theory/proof procedure** _nondeterministic_ specification for how to produce and answer

### Implementation
* **Language Parser** : Sentences $\rightarrow$ Data Structures
* **Reasoning Procedure** : implementation of reasoning theory, search strategey
    * Does _not_ reflect semantics (It's a symbol-system manipluation)

### Datalog
**propositional definite clause** : one of these?

* variable : starts with upper case
* constant : starts with lower case, or is a numeral
* predicate symbol : stars with lower case
* term : variable or a constant
* **atomic symbol (atom)** : $p$ or $p(t_1, \cdots, t_n)$, with $p$ a predicate and each $t_i$ is a term.
* **definite clause** :  $a \leftarrow b_1 \land \cdots \land b_m$
* **query** :  $? b1 \land \cdots \land bm$
* **knowlege base** :  set of definite clauses

## Semantics
Meaning of sentences in a language

### Interpretation
What is in the world, symbol-to-real-things-and-relations correspondence

Triple $I = \langle D, \phi, \pi \rangle$

* **D** : Nonempty domain set. Elements are _individuals_
* **$\phi$** : mapping each constant to an individual. A constant $c$ denotes an individual $\phi(c)$
* **$\pi$** : maps each to $n$-ary predicate symbol a relation
    * ie $\pi : D^n \mapsto \{\text{True}, \text{False}\}$

#### Notes
* $D$ can be actual real things, not confined to being in a computer.
* $\pi(p)$ specifies truthiness for the predicate symbol $p$, for each $n$-tuple of individuals
* if $p$ has no arguments, then $\pi(p)$ either True or False.

### Truth in Interpretation
* $c$ _denotes in I_ the individual $\phi(c)$.
* **Ground** (variable free) atom $p(t_1, \cdots, t_n)$, in **Interpretation $I$** is
    * **True** if $\pi(p)(t'_1, \cdots, t'_n) = \text{True}$
    * **False** if $\pi(p)(t'_1, \cdots, t'_n) = \text{False}$
        * Where $t_i$ denotes $t'_i$ in interpretation $I$
* Ground clause $h \leftarrow b_1 \land \cdots  b_m$ is **False in $I$** if $h$ is False in $I$ and each $b_i$ is True in $I$.
    * Otherwise it's $True$ in $I$.

### Models, Logical Consequence
* A Knowledge Base $KB$ is True in interpretation $I$ iff every clause in KB is True in interpretation $I$ iff every clause in $KB$ is True in I.
* a **model** is an interpretation in which all clauses are True
* $g$ is a **logical consequence** of $KB$ ($KB \models g$), if $g$ is True in every models of KB.
    - ($KB \models g$) if there's no $I$ such that $KB$ is True $\land$ $g$ is False.

#### For Users
1. Come up with an **intended interpretation** $I$
    * the problem domain
2. Pick constants for the relevant individuals
    * e.g. `shibe` for your pet
3. Pick a predicate symbol for the relations
    *  `is_dog` to denote a constant's individual being a dog
4. Tell it things that are True in $I$
    *  build up the knowlege base by **axiomatizing the domain** )
    * `is_dog(X) :- barks(X).`{.prolog}
    * `barks(shibe).`{.prolog}, etc.
5. Ask it things
    * `? is_dog(shibe)` $\rightarrow$ `yes`{.prolog}.
6. Now if $KB \models g$, then $g$ must be True in $I$
    * Your pet must indeed be a dog.

#### For Computers
* Knows nothing about the interpretation, only $KB$.
    * What's a dog? What is barks?
* _Can_ determine if some $g$ is an LC of $KB$, so if $KB \models g$, then it's True in $I$.
* If $\neg (KB \models g)$, then $\exists$ some Interpretation in which $g$ is False. This could be the intended interpretation I.

## Proofs
* **Proof** : mechanical derivation that formula _follows_ from KB.
    * **$KB \vdash g$** : $g$ can be derived from $KB$.
    * **$KB \models g$** : $g$ is true in _all models_ of $KB$.
* **soundness** : $KB \vdash g \Rightarrow KB \models g$
* **completeness** : $KB \models g \Rightarrow KB \vdash g$

### Bottom Up
A _rule of derivarion_, modus ponens _generalisé_.

* Given $h \leftarrow b_1 \land \cdots \land b_m$ in the KB, if each $b_i$ has been derived, then $h$ van be derived.
* **Forward chaining** on the clause. Also covers $m = 0$.

#### Procedure
If $g \in C$ at the end, then $KB \vdash g$.

```
C := {}
until (no more clauses can be selected) {
    select clause h <- b_1 && ... && b_n such that
        b_i in C for all i
        and h not in C ;
    C := C union {h};
}
```
#### Proof of Soundness:
$KB \vdash g \Rightarrow KB \models g$

* Suppose $\exists g : KB \vdash g \land \neg (KB \models g)$.
* Let $h$ be the first atom added to C, which is not true in every model of $KB$.
* Suppose $h$ is not true in model $I$ of $KB$.
* There must be some clause $h \leftarrow b_1 \land \cdots \land b_m$
* Now each $b_i$ is True in $I$. $h$ is False in $I$, so the clause is False in $I$.
* So $I$ can't be a model of $KB$.
* This is a contradiction, so no such $g$ exists.


##### Fixed point
$C$, at the end, is a **fixed point**.

Now if we let $I$ be the interpretation such that every element of the _fixed point_ is True, and every other atom is False, then $I$ is a model of $KB$.

* Suppose $h \leftarrow b_1 \land \cdots \land b_m \in KB$ is False in I. Then $h$ is False, and each $b_i$ is true in $I$. So we can, by the method, add $h$ to $C$.
* This contradicts $C$ being a fixed point.
* The $I$ is a **minimal model**.


#### Proof of Completeness
* $KB \models g \Rightarrow KB \vdash g$.
* Suppose $KB \models g$. Then $g$ is true in all models of $KB$. Thus $g$ is true in the minimal model.
* Thus $g$ is generated by the bottom up algorithm. 
* Thus $KB \vdash g$.

### Top Down

