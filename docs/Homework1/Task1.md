# Task 1: Drawing Single-Color Triangles
## Method
The high-level idea of drawing triangles comes in two steps:

1. **Bounding Box**: Find the bounding box of the triangle, which is the smallest rectangle that contains the triangle. This is done by finding the minimum and maximum x and y coordinates of the triangle.

2. **Rasterization**: For each pixel in the bounding box, check if it is inside the triangle. If it is, color it with the triangle's color.

## Implementation

### Bounding Box

Given the three vertices of the triangle, finding x limits and y limits is straightforward. However, the limits are not necessarily integers. In order to ensure the bounding box fully covers the triangle, we need to take the ceiling and floor of the limits. To elaborate, the x minimum and y minimum should be rounded down when finding the boundary pixels, vice versa.

```cpp
    // Step 1: Find the bounding box of the triangle
    // The bounding box should at least cover the triangle, so for the left side, we take the floor of the minimum x coordinate of the triangle
    int bounding_box_x_min = (int)floor(min(x0, min(x1, x2)));
    // For the right side, we take the ceiling of the maximum x coordinate of the triangle
    int bounding_box_x_max = (int)ceil(max(x0, max(x1, x2)));
    // The same implementation for the y coordinate--floor for the bottom and ceiling for the top
    int bounding_box_y_min = (int)floor(min(y0, min(y1, y2)));
    int bounding_box_y_max = (int)ceil(max(y0, max(y1, y2)));
```

### Rasterization

Implicit line equations are implemented to determind whether a sample position is inside or on the edge of the triangle:

$$
L(x,y) = -(x - x_0)dX + (y - y_0)dY
$$

If $P_0(x_0,y_0)$ and $P_1(x_1,y_1)$ are two points on the line, then $dX = x_1 - x_0$, $dY = y_1 - y_0$.

If $L$ yields all positive results for the three edge, then the sample position is considered insde the triangle. When $L = 0$, the sample position is on the edge. Both situation the sample position should be colored.

Though the above method, whether each pixel's color could be determind through a one-time iteration.

```cpp
    // Step 2: Iterate through all the pixels in the bounding box
    // Initializing variables
    int current_x_position, current_y_position;
    float line01_delta_x = x1 - x0;
    float line01_delta_y = y1 - y0;
    float line12_delta_x = x2 - x1;
    float line12_delta_y = y2 - y1;
    float line20_delta_x = x0 - x2;
    float line20_delta_y = y0 - y2;
    float line01_result, line12_result, line20_result;
    // iterate through lines
    for (current_y_position = bounding_box_y_min; current_y_position < bounding_box_y_max; current_y_position++)
    {
        // iterate through pixels in the line
        for (current_x_position = bounding_box_x_min; current_x_position < bounding_box_x_max; current_x_position++)
        {
          // check if the pixel is inside the triangle. If so, fill the corresponding buffer
          // note when calculating line results, we should use the CENTER of the pixel as position
          line01_result = - ((float)current_x_position + 0.5 - x0) * line01_delta_y + ((float)current_y_position + 0.5 - y0) * line01_delta_x;
          line12_result = - ((float)current_x_position + 0.5 - x1) * line12_delta_y + ((float)current_y_position + 0.5 - y1) * line12_delta_x;
          line20_result = - ((float)current_x_position + 0.5 - x2) * line20_delta_y + ((float)current_y_position + 0.5 - y2) * line20_delta_x;

          if (line01_result >= 0. && line12_result >= 0. && line20_result >= 0.)
          {
            fill_pixel(current_x_position,current_y_position,color);
          }
        }
    }
```

## Results
The following image is the rasterized result of `basic/test4.svg`:
![Result of test4.svg](../images/hw1/hw1task1.png)

