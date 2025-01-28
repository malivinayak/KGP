```
def solution(memory, queries):
    results = []
    block_ids = [0] * len(memory)
    id_counter = 1
    
    for op, val in queries:
        if op == 0:  # alloc
            start = -1
            for i in range(0, len(memory) - val + 1, 8):
                if all(memory[i + j] == 0 for j in range(val)):
                    start = i
                    for j in range(val):
                        memory[i + j] = 1
                        block_ids[i + j] = id_counter
                    id_counter += 1
                    break
            results.append(start)
        else:  # erase
            count = sum(1 for i in range(len(memory)) if block_ids[i] == val)
            if count > 0:
                for i in range(len(memory)):
                    if block_ids[i] == val:
                        memory[i] = 0
                        block_ids[i] = 0
            results.append(count if count > 0 else -1)
            
    return results
```
