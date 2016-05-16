<meta http-equiv="refresh" content="5">

# Background on Computation/AI
## Agent acting intelligently in its own environment ($\dagger$)
* Actions appropriate for goals
* Flexibile to changing environments & goals
* Learns from Experience
* Appropriate choices for limitations, finite computation

### Symbol-System Hypothesis
> A physical symbol system has the necessary and sufficient means for general intelligent action

* Necessity: Anything capable of intelligent action is a physical symbol system
* Sufficiency: Any (sufficiently sophisticated) PSS is capable of intelligent action
* Reasoning is Symbol Manipulation
* Symbols are `1` and `0` of computers, in which case it only means intelligence can be digitized

#### Doubts
* The brain is not merely a computer, _computation_ is not a complete model for intelligence

## Church Turing Thesis and its relevance to AI
* Any symbol manipulation possible on a Turing Machine
* Combined with _SSH_, any Intelligent Action possible on a Turing Machine
* Computation turns into Graph Search (express problem as a graph)

## Turing machine
A theoretical machine to reason about computation. Reads and writes symbols to and from a tape ($\dagger$'s environment).
A 6-tuple of:

* **$\Sigma$** : Alphabet of Symbols
* **$Q$** : Set of possible internal states
* **$Q_0 \in Q$** : Initial State
* **$\epsilon \in \Sigma$** : Blank symbol
* **$A$** : Accepting/Final States
* **$\delta \subseteq (Q \setminus  A \times \Sigma) \times (Q \times \Sigma \times \{L, R\})$** : Relation on State-Symbol pairs, mapping to State-Symbol-Left/Right Movement

Example: $(q, \sigma), (r, \alpha, L)$ would enocde, if in state $q$ and a $\sigma$ is read, move to state $r$, write an $\alpha$ to the tape, and move Left.

* **Deterministic Turing Machine (DTM)** has one State-Symbol-Left/Right triple per State-Symbol pair (it's a function)
* **Non-Deterministic Turing Machine (NTM)** can have more than on S-S-LR per S-S pair.

* $\dagger$: The environment is the tape, and the agent is the TM. If the TM reaches a state in $A$, then the action of the agent has been complete/solution to given problem found.

## Non-determinism
* Turing machine defines next state depending on current state, read symbol. An NTM has more than one S-S-LR triple per S-S pair.
* With computation as graph search

    ```prolog
    search(Node) :- goal(Node).
    search(Node) :- arc(Node, Next), search(Next).
    ```
    there may exist more than one $Next$ for some $Node$.

* **Don't know (Prolog does this)** : If one choice doesn't lead to solution another might
    * Choose
* **Don't care (Paralog)** : If one selection doesn't lead do a solution, no point in trying others
    * select

$\dagger$: Problem may be a non-deterministic one, and so needs an NTM to implement the Intelligent Agent to solve it feasibly.

## $P$ vs $NP$
### Cobham's Thesis
Feasible computation is defined as being solved by a Deterministic Turing Machine in Polynomial time (it's in $P$).

$$P :=  \{\text{problems solved by a Deterministic Turing Machine in Polynomial Time} \}$$

$$NP:= \{\text{problems solved by a Non-deterministic Turing Machine in Polynomial Time} \}$$

$P$ is clearly $\subseteq NP$ as all DTMS can just be an NTM with only one triple in its $\delta$ relation (in which $\delta$ then defines a function).

## SAT
For some boolean expression $\phi$, of variables $x_1, x_2, \cdots, x_n$, find an assignment makes $\phi$ evaluate to True.
Checking that it's True in P is easy, linear in $n$.
Finding is hard due to non-determinism, many assignment to check.

* **Cook-Levin Theorem** says $\text{SAT} \in P \iff P = NP$.
* **CSAT** : $\phi$ is a _conjunction_ of _clauses_ where a _clause_ is a disjunction of _literals_ and a literal is either a non-negated variable $x_i$ (positive) or a negated variable $\neg x_i$ (negative).
* **k-SAT** : Says each clause has $k$ literals
* **3-SAT** : Says each clause has $3$ literals. Is as hard as SAT, but 2-SAT is in P.
    * **horn-SAT** : A conjunction of _horn_ clause s, where a horn clause has at most 1 positive literal. Linear.

## Halting Problem
Given a program $P$, and data $D$ return 1 if $P$ halts on $D$, otherwise 0 (if it loops indefinitely).
It is undecidable.

### Proof (by contadiction)
* Assume for contadiction's sake $\exists$ program $halt(P, D)$ that returns 1 iff P halts on D, otherwise 0.
* Now construct a new program/string $Z$
    ```ruby
    def Z(String x)
        if halt(x, x) then
             loop forever
        else
            halt
        end
    end
    ```
    and run it on itself, i.e. `Z(Z)`. There are 2 cases:
    1. $Z$ halts on $Z$. Then the call to `Halt(Z, Z)` will return `True`, so $Z$ loops forever on $Z$.
        - Contradiction
    2. $Z$ loops forever on $Z$.  Then the call to `Halt(Z, Z)` returns `False`, so $Z$ halts on $Z$
        - Contradiction.
* Conclude that $halt$ cannot exist, so there is no general method to decide if some $P$ will halt on some $D$.

Prolog consequence is that there's no general algorithm to detect loops caused by KBs such as
```prolog
p :- q.
q :- p.

a :- a.
```
etc.

## Church-Turing Thesis
> A function is effectively calculable if its values can be found by some purely mechanical process.

### Halting Problem implications:
Can define functions which are not computable.

* `busy_beaver(n)`: given a TM with $n$ possible states, how many symbols can it write before halting when run with no input?

Can't get an upper bound on this without solving the halting problem. Since _CTT_ says you can compute anything on a _TM_ this is uncomputable by any method.

## Cantor's Theorem
$\forall \omega, \left|2^\omega \right| > \left|\omega\right|$

TODO: Prove this

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
