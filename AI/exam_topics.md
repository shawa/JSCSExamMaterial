# Background
* [x] Agent acting intelligently in environment (sword)
    * [x] SSH and relevance to sword
* [x] Church-Turing thesis and relevance to AI
* [x] If CTT true, what does this imply to mechanical computation reducing to graph search?
* [x] Symbol-system hypothesis, doubts that it's true?
* [x] Non determinism, relevance to sword
    * [x] Don't care vs. don't know non determinism
* [x] Halting problem and CTT
* [x] Solve the halting problem?
* [x] Cantor's theorem
* [x] HP and deriving a Prolog answer
    * [x] P is finite automaton, D finite strings
* [ ] Unifier, when is it most general unifier
    * [ ] 2 expressions with more than 1 general unifier
* [x] Turing Machine
* [x] SAT
* [x] P vs NP

# Knowlege Representation
* [x] Logical Consequence
* [x] Proof Procedure (PP)
    * [x] Bottom up
    * [x] Top down
    * [x] Soundness
    * [x] Simplest Sound Proof procedure
    * Soundness/Completeness trade-off
    * Prolog's over 0-ary terms complete
    * Not always possible to derive sound & complete PP
* [x] Equality rule, make Prolog loop
* [x] Negation as failure
* [x] Proving a negative
* Search procedure to find logical consequences (`goal`, `arc`, `add_to_frontier`), with KB encoded as a list `[a, b, c]` $=$ `a :- b, c.`{.prolog}.
    * Suggest cost, heuristic function for that `A*` search.
* `prove` prop in prolog
    * Prove in terms of a CSP
* [x] Top down vs bottom up LC approach?
* [x] Find conflicts, which ones are minimal.
* [x] Complete Knowledge assumption.
    * [x] Non-monotonicity
    * [x] Apply it to KB, what do we 'get'
* [x] Translate KB to
    * [x] Object Attribute Value triple
    * [ ] Semantic Network
    * [x] Frames
* [x] Definite Clause
* [x] Propositional Clause
* `lc` proposition in prolog.
    * Integrity Constraint, Horn Clause
* [x] Define KB interpretation
* [x] Consistency


# Search
* [ ] Frontier Search variable depending on `add_to_frontier`{.prolog}
    * [ ] How to define to get depth-first search? (DFS)
        * [ ] Bounded DFS
    * [ ] How to define to get breadth-first search? (DFS)
* [ ] For frontier search can reduce frontier to single node, not list of nodes.
* [ ] Nondeterministic FSM is `Trans, Final`, $Trans \in Q \times \Sigma \times Q$, $Final$ list of accepting states. Define `accept/3(Trans, Final, String)`{.prolog} to accept a string.
* [ ] Show this is a search procedure with arc/goal.
* [ ] What are cost, heuristic estimates, relevance to FSM thing.
* [ ] NTM more complicated than FSM, what are the other lists
* [ ] 3 Conditions sufficient to guarantee A* to be admissible.
    * [ ] Show each one is not _necessary_ for A* to be admissible.
    * [ ]  Met by LC Search?
* [ ] A* search predicates (other than `goal`, `arc`).
    * [ ] What is a Constraint Satisfaction Prob, outline search strategy. A* suitable?
* [ ] Zebra puzzle
    * [ ] Generate and Test
    * [ ] Incremental Instantiation
    * [ ] Graph depth bound
