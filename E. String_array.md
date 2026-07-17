## Code

```cpp
#include <iostream>
#include <cstring>
using namespace std;

class StringArray
{
private:
    char **arr;
    size_t size;

public:
    // Constructor
    StringArray(size_t s)
    {
        size = s;
        arr = new char*[size];
        for(size_t i = 0; i < size; i++)
        {
            arr[i] = new char[1];
            arr[i][0] = '\0';
        }
    }

    // Set String
    void setString(size_t index, const char *str)
    {
        if(index >= size)
        {
            cout << "Invalid Index\n";
            return;
        }
        if(str == nullptr) {
            cout << "Null Pointer Received\n";
            return;
        }
        delete[] arr[index];
        arr[index] = new char[strlen(str) + 1];
        strcpy(arr[index], str);
    }

    // Get String
    const char* getString(size_t index) const
    {
        if(index >= size)
        {
            cout << "Invalid Index\n";
            return nullptr;
        }
        return arr[index];
    }

    // Destructor
    ~StringArray()
    {
        for(size_t i = 0; i < size; i++)
        {
            delete[] arr[i];
        }
        delete[] arr;
    }
};

int main()
{
    StringArray names(3);
    names.setString(0,"Saurav");
    names.setString(1,"Rahul");
    names.setString(2,"Rohit");

    cout << names.getString(0) << endl;
    cout << names.getString(1) << endl;
    cout << names.getString(2) << endl;

    return 0;
}
```

## Explanation

**1. Destructor / Memory Cleanup**
The destructor deletes each individual string element (`Saurav`, `Rahul`, `Rohit`), then deletes the array of pointers itself. This releases all dynamically allocated memory and prevents memory leaks.

**2. Null Pointer Handling**
A check `if(str == nullptr) return;` was added in `setString()` to handle the case where a `nullptr` is passed. Without this check, dereferencing/using the null pointer would cause undefined behavior.

**3. Shallow Copy Problem**
If the object is copied using the default copy constructor (`StringArray b = a;`), C++ performs a **shallow copy** — both objects end up pointing to the same underlying memory. For example:

```cpp
int main()
{
    StringArray a(2);
    a.setString(0, "Saurav");
    StringArray b = a;
}
```

When `main()` ends, `b` is destroyed first and deletes `"Saurav"`. Then when `a` is destroyed, it tries to `delete[]` the same memory again — this causes **undefined behavior due to double deletion**. Also, after `b` frees the memory, the pointers inside `a` become **dangling pointers**, since they still point to memory that no longer exists.

This happens because the default copy constructor performs a shallow copy (copies the pointer values, not the data they point to).

### Fix: Custom Deep-Copy Constructor

```cpp
StringArray(const StringArray& source)
{
    size = source.size;
    arr = new char*[size];
    for(size_t i = 0; i < size; i++)
    {
        arr[i] = new char[strlen(source.arr[i]) + 1];
        strcpy(arr[i], source.arr[i]);
    }
}
```

This allocates fresh memory for the new object and copies the actual string contents, so each object owns its own independent memory and double-deletion / dangling pointer issues are avoided.
