py-toposort
===========

This Python 2.5 module computes a topological sorting (http://en.wikipedia.org/wiki/Topological_sorting)
of a directed graph.

function **toposort** (edges[,extra_nodes][,non_loop_cycles_are_errors][,loops_are_errors][,loop_report][,cycle_report])

Computes a topological sorting of a directed graph.

The topological sorting is an ordering of the graph's nodes such that,
for every edge (u -> v), u comes before v.
The implementation is hardened against loops and arbitrary cycles and
can handle isolated nodes and complete (sub)graphs. In these
degenerated cases the computed ordering is obviously not correct
(as there is no correct one), but it will contain all nodes and the nodes
not participating in cycles will be ordered correctly.
The detected cycles can be reported, but it is not guaranteed that every
cycle is reported (this does not mean a cycle can ever go undetected).
Note that the node objects must be hashable and properly compare by
identity with the ``is`` operator.

The basic algorithm is a depth-first search along the reversed
edges, starting from nodes that only have incoming edges.
With this kind of traversal, the result ordering is added to whenever
a recursion frame ends.
Additionally we employ cycle detection by tracking which nodes have
already been visited *within the same recursion tree path*.
This mechanism can both detect cycles and determine the participating
nodes. To do this robustly and not run into infinite recursion we globally
track which edges have been traversed so far and skip those later.

Args:
    * edges: the directed edges of the graph (an iterable of iterables).
      The first two elements of each edges element are interpreted as
      directed edge (1st elem -> 2nd elem).
    * extra_nodes: if specified, an iterable with any additional (or
      duplicated) nodes. This is entirely optional and this sequence does
      not have to include all or any nodes specified in the edges,
      but if used, this function can include in the sorting those nodes
      without incoming or outgoing edges (i.e., isolated nodes,
      with nodes of degree 2 with a loop also considered isolated).
    * non_loop_cycles_are_errors: if specified and True, non-loop cycles are
      detected and a CycleDetectedError is raised. Normally, edges
      completing a cycle in a traveral are ignored.
      Since there exists no topological ordering for a graph with cyles
      the returning sequence is not correct, but it will contain all nodes
      and it will be correct for the nodes that do not make up cycles.
      Note that this argument has no effect on the handling of loops which
      are ignored by default.
    * loops_are_errors: if specified and True, loops are detected
      and a LoopDetectedError is raised. Normally, loops are ignored.
    * loop_report: if specified this argument should be a list that any
      detected nodes with loops are appended to.
    * cycle_report: if specified this argument should be a list that any
      detected cycles are appended to (as a list of nodes for each cycle).
      Note that there is no guarantee that all cycles in the graph will be
      reported.

Returns:
    A list containing all nodes in topological order or, if no such order exists,
    one that comes reasonably close.

Raises:
    * LoopDetectedError: if loops_are_errors is True and a loop is detected.
      If the loop_report argument was specified and this exception is
      raised, the report will contain the loop.
    * CycleDetectedError: if non_loop_cycles_are_errors is True and a
      cycle is detected. If the cycle_report argument was specified and
      this exception is raised, the report will contain the cycle.
