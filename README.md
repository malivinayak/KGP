```
def solution(memory, queries):
    n = len(memory)  # Length of the memory array
    id_counter = 1   # Atomic counter for assigning IDs
    id_map = {}      # Map to store {id: (start_index, block_length)}
    results = []     # To store the result of each query

    for query in queries:
        if query[0] == 0:  # Alloc type query
            x = query[1]
            found = False

            # Search for the first aligned position with x consecutive free units
            for i in range(0, n, 3):  # Start from aligned positions (divisible by 3)
                if i + x <= n and all(memory[j] == 0 for j in range(i, i + x)):
                    # Mark the memory block as occupied
                    for j in range(i, i + x):
                        memory[j] = id_counter
                    
                    # Store the mapping of id to its block
                    id_map[id_counter] = (i, x)
                    
                    # Add the starting index to results and increment ID counter
                    results.append(i)
                    id_counter += 1
                    found = True
                    break
            
            if not found:
                results.append(-1)

        elif query[0] == 1:  # Erase type query
            erase_id = query[1]

            if erase_id in id_map:
                start, length = id_map.pop(erase_id)  # Get the block info and remove the ID
                for j in range(start, start + length):
                    memory[j] = 0  # Free the block in memory
                results.append(length)  # Append the block length to results
            else:
                results.append(-1)

    return results
```
