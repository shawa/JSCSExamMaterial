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
* Go back from a query to see if it's a logical consequence of the KB.
* **Answer Clause** $\alpha$ is $\text{Yes} \leftarrow a_1 \land \cdots \land c_m$
* **SLD Resolution** of $\alpha$ with atom $a_i$ is $\alpha$ with $a_i$ substituted for the clauses of $a_i$

* **Answer** is an answer clause with $m = 0$, i.e. $\text{Yes} \leftarrow$.
* **derivation** is a sequence of answer clauses, $\gamma_0, \gamma_1, \cdots, \gamma_n$
    * Each $\gamma_i$ is the resolution of $\gamma_{i-1}$ with some clause in the KB.
    * $\gamma_n$ is the 'final' answer.


#### Procedure
To solve $ac$.
```ruby
ac := "yes <- q_1 && ... && q_k"
until ac is an answer (i.e. until ac matches "yes <-"
    select conjunct a_i from the body of a_c;
    choose C from the KB that has a_i at its head;
    replace a_i in body of ac with body of C;
end
```

There's nondeterminism in the choice of C here:

* **Don't care** : If one doesn't lead to solution, none of the others will.
* **Don't know** : If one choice doesn't lead to the solution, others might.

## Knowlege Representation
* How to represent _"Coco is a Shiba Inu"_
* Could do something like `shibe(coco)`
    * "Who are the shibes?" easy to solve
* Or `breed(coco, shibe)`
    * "What breed is Coco"
    * "What dogs are the Shiba Inu"
    * ~~"What property is "Shiba Inu?"~~ It's a `breed`, but we can't resolve this easily.
* Solution: `prop(coco, breed, shibe)`. Now we can solve it all with the usual strategies. Called **object-attribute-value** representation.
    * `prop(coco, is_a, shibe)`
    * `prop(coco, shibe, true)`
* **Reification** : translating a scenario into object.

### Frames
This can all be brought into a **frame**, collection of attribute-value pairs:
```perl
[ owned_by = craig
, deliver_to = ming
, model = lemon_laptop_1000 ...  ]
```
etc.

### Relations
* **Primitive knowlege** : defined explicity
* **Derived knowlege** : defined by rules

With an `is_a` attribute, we can do **property inheritance**. Every individual in a class has $n$ for some attribute $p$.

No reason not to allow **multiple inheritance**, where an object is a member of multiple classes. There can be conflicts, for example if both classes define a different default for some property (_multiple inheritance problem_).

Associate most general class with an attribute, don't add properties willy-nilly, and axiomatize in the causal direction.

## Complete Knowlege Assumption (CKA)
> Any fact not listed in a Knowlege Base is False

The definite clause system is **monotonic**, that is, if we add a clause, it doesn't cause other clauses to be false. (It doesn't invalidate previous conclusions)

Adding the CKA, the system is **non monotonic**; we _can_ invalidate a conclusion by adding more clauses.

### Example
```prolog
student(mary).
student(john).
student(ying).
```
* Under the CKA, this means  $\text{student}(X) \iff X = \text{mary} \lor X = \text{john} \lor X = \text{ying}$
* So to prove that $\neg \text{student}(\text{alan})$, you need $\text{alan} \neq \text{mary} \land \text{alan} \neq \text{john} \land \text{alan} \neq \text{ying}$
    * Need unique names assumption

### Clarke Completion
```prolog
mem(X, [X|T]).
mem(X, [H|T]) :- mem (X, T).
```

becomes
```prolog
mem(X, Y) <=> (eT Y == [X | T]) or
              (eH eT T == [H | T] and mem(X, T)).
```
_Where `e` is $\exists$_

* Completion of every predicate, and equality/inequality axioms.
* If $p$ in the KB is defined by no clauses, then it completes to $p \leftrightarrow \text{False}$. i.e. $\neg p$.
* **Negation as Failure** : Interpret negations in clause bodies. $\sim p$ means $p$ is False under CKA.

#### Bottom up Negation as Failure proof Procedure
```
C = {};
until no more selections possible
    either
        select "h <- b1 or ... or b_n" in KB such that
            bi in C for all i, and h not in C;
        C = C union {h}
    or
        select h such that
            for each "h <- b1 or ... or b_n" in KB
                either
                    exists b_i such that ~b_i in C
                or
                    exists b_i such that b_i = ~g, and g in C
                end
        C = C union {~h}
    end
end
```

* If this procedure fails, then $\neg a$ can be concluded.
* Say we have $a \leftarrow b_1, \cdots, a \leftarrow b_n,$, need all of them to fail. It needs to be _finite_ however, like $p \leftarrow p$ is not decidable with bottom up.
    * Halting problem means these things are undetectable in the general case too!

* If trying to NAF a query that has unbound variables, the NAF must be **delayed** until the variable is bound, otherwise it **flounders**.

## Integrity Constraints
* $\text{false} \leftarrow a_1 \land \cdots \land \a_k$
    * With $a_i$ atoms, $\text{false}$ an atom that's False in all interpretations.
* **Horn Clauses** are either definite clauses or integrity constraints.
* $\neg \alpha$ is a formula which is
    * True in $I$ if $\alpha$ is False in $I$ and
    * False in $I$ if $\alpha$ is True in $I$

Suppose we have KB

* $\text{false} \leftarrow a \land b$
* $a \leftarrow c$
* $b \leftarrow c$
    * Then $KB \models \neg c$

Can also be **disjuctive conclusions**:

* $\text{false} \leftarrow a \land b$
* $a \leftarrow c$
* $b \leftarrow d$
    * Then $KB \models \neg c \lor \neg d$

### Questions and Answers
* **assumable** : an atom whose negation is acceptably included in the (disjuntive answer)
    * Hand-wavily 'assumably' true
* **conflict** : a set of assumables which imply _False_ for a KB
* **minimal conflict** : a conflict with no strict subsets that are also conflicts
* **consistency-based diagnosis** : set of assumables that has one element in each conflict
* **minimal diagnosis** : no strict subset is a diagnosis

### Bottom Up Conflict Finding
* **conclusion** : a pair $\langle a, A \rangle$ with $a$ an atom, and $A$ a set of assumbables which imply $a$.


## Rules and Consistency
* $g$ is $KB$-**consistent** if it's true in some model of $KB$.
