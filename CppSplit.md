# Split string

Using std::istringstream we can easily split a string to a string vector.

``` c++  
    #include <vector>   
    #include <sstream>

    std::vector<std::string> Split(const std::string &str, char delim)
    {
        std::istringstream iss(str);
        std::string tmp;
        std::vector<string> splitted_str;
        while (getline(iss, tmp, delim))
            splitted_str.push_back(tmp);
    
        return splitted_str;
    }
 ```


