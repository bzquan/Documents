# How to remove elements from a range collection

Suppose you have a range of elements and you want to remove elements matching a certain
condition. Here we can use so-called _remove-erase-idiom_. You can remove all empty strings from a string vector.

``` c++   
    #include <vector>   
    #include <sstream>

    void removeEmptyStrings(vector<string>& strings)
    {
        auto it = remove_if(begin(strings), end(strings),
                [](const string& str){ return str.empty(); });
        // Erase the removed elements.
        strings.erase(it, end(strings));
    }
 ```


