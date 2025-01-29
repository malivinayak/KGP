```
from collections import defaultdict

def has_overlap(c1, c2):
    return abs(c1[0] - c2[0]) <= 2 and abs(c1[1] - c2[1]) <= 2

def solution(centers):
    n = len(centers)
    seen_pairs = set()
    grid = defaultdict(set)
    
    # Map coordinates to indices to handle duplicates
    coord_to_indices = defaultdict(list)
    for i, coord in enumerate(centers):
        coord_tuple = tuple(coord)
        coord_to_indices[coord_tuple].append(i)
    
    # For each coordinate
    for i in range(n):
        x, y = centers[i]
        # Get grid cell
        cell_x, cell_y = x // 2, y // 2
        
        # Check neighboring cells
        for dx in [-1, 0, 1]:
            for dy in [-1, 0, 1]:
                cell = (cell_x + dx, cell_y + dy)
                # Check crystals in this cell
                for j in grid[cell]:
                    if i != j and has_overlap(centers[i], centers[j]):
                        pair = tuple(sorted([i, j]))
                        seen_pairs.add(pair)
        
        # Add current crystal to its cell
        grid[(cell_x, cell_y)].add(i)
    
    # Add pairs for same coordinates
    for indices in coord_to_indices.values():
        if len(indices) > 1:
            for i in range(len(indices)):
                for j in range(i + 1, len(indices)):
                    pair = tuple(sorted([indices[i], indices[j]]))
                    seen_pairs.add(pair)
    
    return len(seen_pairs)
```
