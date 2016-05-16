# Search
Basic graph searching (Depth first)
```prolog
arc(Node, Next) :- % Something that relates Node to Next.
search(Node) :- goal(Node).
search(Node) :- arc(Node, Next), search(Next).
```
* Nondeterminism if `arc/2` has multiple solutions
    * Choose the best one (A*, Best first, etc)
* Computation eliminates the non-determinism
* Can bound the number of calls to `arc`, the number of search iterations.

## Frontier Search
* With a **graph**, **start nodes** and **goal nodes**, incrementally explore paths from start nodes, hoping to reach goal nodes.
* Maintain a **frontier** of paths from start that have been explored.
* Search is complete once frontier hits a goal.
* How the frontier expands (how the child nodes of the current frontier are inserted to the frontier) can vary, defines the **search strategy**


```ruby
frontier := { s : s is a start node }
until frontier.empty
    select and remove path <n_0, ... ,n_k> from frontier;
    if goal(n_k)
        return <n_0, ... , n_k>;
    end

    for each neighbor n of n_k;
        add <n_0, ... , n_k, n> to frontier
    end
end
```
## Search Strategies

```prolog
search(Start) :- frontier_search([Start]).

frontier_search([Node|_]) :- goal(Node).
frontier_search([Node|Rest]) :-
    findall(Next, arc(Node, Next), Children),
    add_to_frontier(Children, Rest, New),  % add the neighbours to the frontier
    frontier_search(New).
```

The strategy depends on how `add_to_frontier/3` is defined:

### Depth-first,
* Frontier is a stack; select the last element added to frontier
* If frontier is $[p_1, p_2, \cdots]$, all paths from $p_1$ are explored before _any_ of $p_2i$.

* Empty the `Children` before adding the `New`:
    ```prolog
    add_to_frontier([], Rest, Rest).
    add_to_frontier([H|T], Rest, [H, New]) :- add_to_frontier(T, Rest, New)
    ```

* Not guaranteed to halt, graph may have cycle or be infitnite.
* Space $O(n)$ in size of current path.
* Unconstrained by goal until it stumbles on it

### Breadth-first
* Frontier is a queue; select earliest element.
* If frontier is $[p_1, p_2, \cdots, p_r]$, neighbours of$p_1$ are explored _after_ all of of $p_2, ... p_r$.
* empty the `New` before adding the `Children`:
    ```prolog
    add_to_frontier(Children, [], Children).
    add_to_frontier(Children, [H|T], Children) add_to_frontier(Children, T, New).
    ```

* **branching factor (BF)** ($b$) : number of neighbours a node has
* with a finite BF, breadth first _will_ find a solution if it exists.
* guaranteed to find path with fewest arcs
* Time $O(b^n)$, exponential in path length ($n$)
* Space $O(b^n)$, exponential in path length ($n$)

### Bounded Depth First
* Add an iteration-counting term:
    ```prolog
    bounded_search(Node, _) :- goal(Node).
    bounded_search(Node, s(B)) :- bounded_search(B).
    ```

### Iterative deepening
* BDFS with a variable bound:
    ```prolog
    iterative_deepening(Node) :- bound(B), bs(Next, B).

    bound(0).
    bound(s(B)) :- bound(B). % easily adjustable
    ```

### Lowest Cost First
* Associate a cost with each arc. Then the cost of a path from $p_1$ to $p_n$ is the sum of all the costs of each of the path's nodes.
* Select the path with the lowest cost, will find the lowest-cost path.
* Frontier is a _priority queue ordered by cost_
* Reduces to **Breadth First** when the costs are all the same.

## Heuristic Search
* Don't ignore the goal, keep your eyes on the prize; introduce **heuristics** to the search.
* Define $h(n)$ to estimate the cost of the path from $n$ to the goal node.
* Only use easily computable information
* Define $h(\langle n_0, \cdots, n_k \rangle)$ to be $h(n_k)$
* An underestimate, if there's no path to the goal whose length is less than $h(n)$.
### $h(n)$ examples
Depends on the problem domain.

* On the Euclidian plane, could use $||n g||$, where $g$ is the nearest goal node
* _**If deriving from a Knowlege Base**_, use the **number of atoms in the query**
* If we're routing between locations, could use $\text{distance to a goal} \div \text{maximum speed we can go}$

### Best First
* Pick the path whose end is closest to the goal, addording to $h$.
* Gets a path of minimal $h$ value
* Frontier is a **priority queue ordered by $h$**.
* Space is exponential in path length.
* Not guaranteed to find a solution, even if there is one.
* Doesn't always find shortest path.

### Heuristic Best First
* Order a node's neighbours by $h$ _before_ pushing them on to the frontier
* The subtrees are developed locally, but it still explores all paths from the node before moving on to the rest of the frontier
* Linear space in path length
* Does _not_ always find a solution

## <span style="color:red">A* Search</span>
* Use both the path cost _and_ $h$.
* With $\text{cost}(p)$ the cost of the path p, and $h(p)$ the estimate of the end of $p$ to the goal,  let $f(p) = \text{cost}(p) + h(p)$
* `start ---cost(p)--> n ---h(n)--> goal`
* Lowest cost first _and_ best-first.
* Frontier is a **priority queue ordered by $f$**.
* Always selects node on the frontier with lowest estimated distance from start to goal, when constrained to go through that node.

### A* Admissibility
* Always finds a solution if it exists, as long as:
    1. Branching factor is finite
    2. All of the arc costs are greater than 0. ($\exists \epsilon > 0 : \forall \text{arc cost } c, c > \epsilon$)
    3. $h(n)$ is an _underestimate_ of the shortest path from $n$ to a goal

#### Proof of admissibility
Say A* picks a path $p$ to the goal. This is _always_ the shortest path:
Suppose $p'$ also on the frontier. $p$ was chosen before $p'$, and $h(p) = 0$ (it's already at the goal), so we know $\text{cost}(p) \leq \text{cost}(p') + h(p')$.

Now $h$ is an underestimae, so $\text{cost}(p') + h(p') \leq \text{cost}(p'')$, $\forall p''$ from $p'$ to the goal.

Which means $\text{cost}(p) \leq \text{cost}(p'')$ , $\forall p''$ from $p'$ to the goal.

* There's always an element of an optimal solution on the frontier, as by the abstract algorithm, there's an initial part of every path to the goal.
* A* will halt, as the minimum $g$-value keeps increasing and tends to infinity.
    - TODO: What on earth is the $g$ value? $h$ value?


