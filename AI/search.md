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
## Prolog Examples

```prolog
search(Start) :- frontier_search([Start]).

frontier_search([Node|_]) :- goal(Node).
frontier_search([Node|Rest]) :-
    findall(Next, arc(Node, Next), Children),
    add_to_frontier(Children, Rest, New),  % add the neighbours to the frontier
    frontier_search(New).
```

The strategy depends on how `add_to_frontier/3` is defined:

Depth-first, empty the `Children` before adding the `New`:
```prolog
add_to_frontier([], Rest, Rest).
add_to_frontier([H|T], Rest, [H, New]) :- add_to_frontier(T, Rest, New)
```

Breadth-first, empty the `New` before adding the `Children`:
```prolog
add_to_frontier(Children, [], Children).
add_to_frontier(Children, [H|T], Children) add_to_frontier(Children, T, New). 
```

Bounded Depth First by adding an iteration-counting term:
```prolog
bounded_search(Node, _) :- goal(Node).
bounded_search(Node, s(B)) :- bounded_search(B).
```

Iterative deepening is depth first with a variable bound:
```prolog
iterative_deepening(Node) :- bound(B), bs(Next, B).

bound(0).
bound(s(B)) :- bound(B). % easily adjustable
```
