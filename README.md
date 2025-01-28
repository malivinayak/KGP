```
class SegmentTree:
    def __init__(self, n):
        self.n = n
        self.tree = [0] * (4 * n)  # Segment tree array
    
    def update(self, idx, value, node=1, start=0, end=None):
        if end is None:
            end = self.n - 1
        
        # Update a single index
        if start == end:
            self.tree[node] = value
        else:
            mid = (start + end) // 2
            if idx <= mid:
                self.update(idx, value, node * 2, start, mid)
            else:
                self.update(idx, value, node * 2 + 1, mid + 1, end)
            
            # Update the current node based on child nodes
            self.tree[node] = self.tree[node * 2] + self.tree[node * 2 + 1]
    
    def query(self, l, r, node=1, start=0, end=None):
        if end is None:
            end = self.n - 1

        # If the range is completely outside
        if r < start or l > end:
            return 0

        # If the range is completely inside
        if l <= start and end <= r:
            return self.tree[node]

        # Partially inside
        mid = (start + end) // 2
        left_query = self.query(l, r, node * 2, start, mid)
        right_query = self.query(l, r, node * 2 + 1, mid + 1, end)
        return left_query + right_query


def process_operations(operations):
    # Determine the bounds of the number line
    MAX_COORD = 10**6  # Assuming the range of coordinates is reasonable
    offset = MAX_COORD  # Offset to handle negative coordinates
    seg_tree = SegmentTree(2 * MAX_COORD + 1)  # Segment tree to cover [-MAX_COORD, MAX_COORD]
    
    result = []

    for operation in operations:
        if operation[0] == 1:
            # Add obstacle at coordinate x
            x = operation[1] + offset
            seg_tree.update(x, 1)  # Mark the obstacle as present
        elif operation[0] == 2:
            # Check if block of `size` can end immediately before `x`
            x, size = operation[1] + offset, operation[2]
            start = x - size
            # Query the range [start, x-1] for any obstacles
            if seg_tree.query(start, x - 1) > 0:
                result.append("0")
            else:
                result.append("1")

    return "".join(result)
```
