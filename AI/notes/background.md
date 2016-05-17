# Background on Computation/AI
## Agent acting intelligently in its own environment ($\dagger$)
* **Intelligent Action** : An Intelligent Agent makes choices towards realising its goals
    * Actions appropriate for goals
    * Flexibile to changing environments & goals
    * Learns from Experience
    * Appropriate choices for limitations, finite computation

> An agent is intelligent insofar as it makes choices that
> lead to its goals being realized. SAT is all about satisfying
> goals expressed as Boolean formulas (by choosing 0 or 1 as
> values for Boolean variables in the formulas). SAT is known
> to be in NP; it is not known whether or not SAT is in P
> (which by Cobham's thesis, would make it feasibly computable).
> SAT is in P iff P=NP (i.e. non-determinism does not
> change what is feasibly computable).

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

$P$ is "clearly" $\subseteq NP$ as all DTMS can just be an NTM with only one triple in its $\delta$ relation (in which $\delta$ then defines a function).

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

* $\dagger$ : An intellgient agent must _make progress towards its goals_, if it's not making progress toward its goal, then it's not going to halt. Hence there's no general way to determine if a particular intellgent agent will reach its goal.

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
