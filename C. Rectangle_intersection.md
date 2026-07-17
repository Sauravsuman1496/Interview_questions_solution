## Idea

Instead of checking whether the rectangles overlap, I check whether they are completely separated. There are only four ways they can be separated: A can be completely left, completely right, completely above, or completely below B. If any one of these conditions is true, I return false. If none of them are true, then the rectangles must overlap or touch each other, so I return true.

This approach is very efficient because it only performs four comparisons, so the time complexity is O(1).

## Code

```cpp
#include <iostream>
using namespace std;

struct Rectangle
{
    int left;
    int right;
    int top;
    int bottom;
};

bool isIntersect(Rectangle A, Rectangle B)
{
    // A is left of B
    if (A.right < B.left)
        return false;

    // A is right of B
    if (A.left > B.right)
        return false;

    // A is above B
    if (A.bottom < B.top)
        return false;

    // A is below B
    if (A.top > B.bottom)
        return false;

    return true;
}

int main()
{
    Rectangle A = {2, 8, 2, 8};
    Rectangle B = {6, 12, 4, 10};

    if (isIntersect(A, B))
        cout << "Rectangles Intersect";
    else
        cout << "Rectangles Do Not Intersect";

    return 0;
}
```

**Time Complexity:** O(1)
