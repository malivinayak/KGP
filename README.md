```
def blur_image(image, radius):
    if not image or not image[0]:
        return image
    
    rows, cols = len(image), len(image[0])
    result = [[0] * cols for _ in range(rows)]
    
    def get_neighbors(i, j):
        neighbors = []
        for k in range(max(0, i - radius), min(rows, i + radius + 1)):
            for l in range(max(0, j - radius), min(cols, j + radius + 1)):
                if abs(i - k) <= radius and abs(j - l) <= radius:
                    if k != i or l != j: 
                        neighbors.append(image[k][l])
        return neighbors
    
    for i in range(rows):
        for j in range(cols):
            neighbors = get_neighbors(i, j)
            
            if not neighbors:
                result[i][j] = image[i][j]
            else:
                neighbors_mean = sum(neighbors) // len(neighbors)
                result[i][j] = (image[i][j] + neighbors_mean) // 2
    
    return result
```
