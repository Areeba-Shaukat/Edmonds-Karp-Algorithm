# Edmonds-Karp-Algorithm
from collections import deque
def bfs(capacity, source, sink, parent):
    """
    Perform BFS to find an augmenting path in the residual graph.
    Updates the parent array to store the path.

    :param capacity: 2D list representing the residual capacities of the graph
    :param source: Source node
    :param sink: Sink node
    :param parent: List to store the path
    :return: The bottleneck capacity of the found path, or 0 if no path is found
    """
    n = len(capacity)
    visited = [False] * n
    queue = deque([(source, float('inf'))])  # (node, flow so far)
    visited[source] = True
    parent[source] = -1

    while queue:
        current, flow = queue.popleft()

        for next_node in range(n):
            if not visited[next_node] and capacity[current][next_node] > 0:
                parent[next_node] = current
                new_flow = min(flow, capacity[current][next_node])
                if next_node == sink:
                    return new_flow  # Return bottleneck capacity
                queue.append((next_node, new_flow))
                visited[next_node] = True
    return 0  # No augmenting path found

def edmonds_karp(graph, source, sink):
    """
    Implements the Edmonds-Karp algorithm to find the maximum flow.
    :param graph: 2D list representing the capacity of the graph
    :param source: Source node
    :param sink: Sink node
    :return: Maximum flow from source to sink
    """
    n = len(graph)
    capacity = [row[:] for row in graph]  # Create a copy of the graph for residual capacities
    max_flow = 0
    parent = [-1] * n
    while (flow := bfs(capacity, source, sink, parent)) > 0:
        max_flow += flow
        current = sink
        while current != source:
            prev = parent[current]
            capacity[prev][current] -= flow  # Forward flow
            capacity[current][prev] += flow  # Reverse flow
            current = prev
    return max_flow
# Example Usage
if __name__ == "__main__":
    # Number of nodes (vertices)
    num_nodes = 6
    # Graph represented as an adjacency matrix (capacity[u][v] = capacity of edge u->v)
    capacity_graph = [[0, 3, 7, 0, 0, 0],  # 0 -> 1, 0 -> 2
                      [0, 0, 0, 3, 4, 0],  # 1 -> 3, 1 -> 4
                      [0, 5, 0, 3, 0, 0], # 2 -> 1, 2 -> 4
                      [0, 0, 0, 0, 3, 2],  # 3 -> 4, 3 -> 5
                      [0, 0, 0, 0, 0, 6],  # 4 -> 5
                      [0, 0, 0, 0, 0, 0]]   # Sink
    source_node = 0  # Source
    sink_node = 5    # Sink
    max_flow = edmonds_karp(capacity_graph, source_node, sink_node)
    print(f"Maximum Flow: {max_flow}")
Output:
Maximum Flow: 8
