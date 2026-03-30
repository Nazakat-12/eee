1️⃣ Single-Point Crossover
def single_point(p1, p2):
    point = random.randint(1, len(p1)-1)
    c1 = p1[:point] + p2[point:]
    c2 = p2[:point] + p1[point:]
    return c1, c2
2️⃣ Two-Point Crossover
def two_point(p1, p2):
    pt1 = random.randint(1, len(p1)-2)
    pt2 = random.randint(pt1+1, len(p1)-1)

    c1 = p1[:pt1] + p2[pt1:pt2] + p1[pt2:]
    c2 = p2[:pt1] + p1[pt1:pt2] + p2[pt2:]

    return c1, c2
3️⃣ Uniform Crossover
def uniform_crossover(p1, p2):
    child = []
    for i in range(len(p1)):
        if random.random() < 0.5:
            child.append(p1[i])
        else:
            child.append(p2[i])
    return child
4️⃣ PMX (For TSP / N-Queens Permutation)
def pmx(p1, p2):
    size = len(p1)
    pt1 = random.randint(0, size-2)
    pt2 = random.randint(pt1+1, size-1)

    child = [-1]*size
    child[pt1:pt2] = p1[pt1:pt2]

    for i in range(pt1, pt2):
        if p2[i] not in child:
            val = p2[i]
            pos = i
            while True:
                val2 = p1[pos]
                pos = p2.index(val2)
                if child[pos] == -1:
                    child[pos] = val
                    break

    for i in range(size):
        if child[i] == -1:
            child[i] = p2[i]

    return child
5️⃣ Ordered Crossover (OX)
def ordered_crossover(p1, p2):
    size = len(p1)
    pt1 = random.randint(0, size-2)
    pt2 = random.randint(pt1+1, size-1)

    child = [-1]*size
    child[pt1:pt2] = p1[pt1:pt2]

    pointer = pt2
    for gene in p2:
        if gene not in child:
            if pointer >= size:
                pointer = 0
            child[pointer] = gene
            pointer += 1

    return child
6️⃣ Cyclic Crossover
def cyclic_crossover(p1, p2):
    size = len(p1)
    child = [-1]*size

    index = 0
    while child[index] == -1:
        child[index] = p1[index]
        index = p1.index(p2[index])

    for i in range(size):
        if child[i] == -1:
            child[i] = p2[i]

    return child
🔥 🔥 SELECTION METHODS (CODE ONLY)
1️⃣ Roulette Wheel
def roulette_selection(pop, fitness):
    total = sum(fitness)
    pick = random.uniform(0, total)

    current = 0
    for i in range(len(pop)):
        current += fitness[i]
        if current > pick:
            return pop[i]
2️⃣ Tournament Selection
def tournament_selection(pop, fitness, k=3):
    selected = random.sample(list(zip(pop, fitness)), k)
    selected.sort(key=lambda x: x[1], reverse=True)
    return selected[0][0]
3️⃣ Rank-Based Selection
def rank_selection(pop, fitness):
    sorted_pop = [x for _, x in sorted(zip(fitness, pop))]
    ranks = list(range(1, len(pop)+1))
    total = sum(ranks)

    pick = random.uniform(0, total)
    current = 0
    for i in range(len(sorted_pop)):
        current += ranks[i]
        if current > pick:
            return sorted_pop[i]
4️⃣ Elitism
def elitism(pop, fitness):
    best_index = fitness.index(max(fitness))
    return pop[best_index]
🔥 🔥 FITNESS FUNCTIONS (CODE ONLY)
1️⃣ N-Queens
def fitness_nqueens(board):
    conflicts = 0
    n = len(board)

    for i in range(n):
        for j in range(i+1, n):
            if abs(board[i] - board[j]) == abs(i - j):
                conflicts += 1

    return -conflicts
2️⃣ 8-Puzzle
def fitness_8puzzle(state, goal):
    misplaced = 0
    for i in range(len(state)):
        if state[i] != goal[i]:
            misplaced += 1
    return -misplaced
3️⃣ Maze (Distance to Goal)
def fitness_maze(position, goal):
    return -(abs(position[0] - goal[0]) + abs(position[1] - goal[1]))
4️⃣ Chess (Simple Material Count)
def fitness_chess(board):
    values = {'P':1,'N':3,'B':3,'R':5,'Q':9,'K':0}
    score = 0
    for piece in board:
        if piece.isupper():
            score += values[piece]
        else:
            score -= values[piece.upper()]
    return score
    If Problem = TSP (Very Important)

Chromosome:

[0, 2, 1, 3]

Meaning order of cities.

Fitness:

def fitness(route):
    distance = 0
    for i in range(len(route)-1):
        distance += matrix[route[i]][route[i+1]]

    distance += matrix[route[-1]][route[0]]

    return -distance
    _______________________________
    A*
    import heapq

def astar(grid, start, goal):
    rows, cols = len(grid), len(grid[0])

    def heuristic(a, b):
        return abs(a[0]-b[0]) + abs(a[1]-b[1])

    pq = []
    heapq.heappush(pq, (0, start))

    g_cost = {start: 0}
    visited = set()

    while pq:
        f, current = heapq.heappop(pq)

        if current == goal:
            return g_cost[current]

        if current in visited:
            continue

        visited.add(current)

        r, c = current

        for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
            nr, nc = r+dr, c+dc
            neighbor = (nr, nc)

            if 0 <= nr < rows and 0 <= nc < cols:
                if grid[nr][nc] != 1:

                    new_g = g_cost[current] + 1

                    if neighbor not in g_cost or new_g < g_cost[neighbor]:
                        g_cost[neighbor] = new_g
                        f_cost = new_g + heuristic(neighbor, goal)
                        heapq.heappush(pq, (f_cost, neighbor))

    return None
    _________________________________________________________________
    import heapq

def greedy(grid, start, goal):
    rows, cols = len(grid), len(grid[0])

    def heuristic(a, b):
        return abs(a[0]-b[0]) + abs(a[1]-b[1])

    pq = []
    heapq.heappush(pq, (heuristic(start, goal), start))

    visited = set()

    while pq:
        h, current = heapq.heappop(pq)

        if current == goal:
            return True

        if current in visited:
            continue

        visited.add(current)

        r, c = current

        for dr, dc in [(1,0), (-1,0), (0,1), (0,-1)]:
            nr, nc = r+dr, c+dc

            if 0 <= nr < rows and 0 <= nc < cols:
                if grid[nr][nc] != 1:
                    heapq.heappush(pq, (heuristic((nr,nc), goal), (nr,nc)))

    return False
