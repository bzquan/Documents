# Small function - how small is small?

Small functions are considered to be more readable in general. How small is small for a function or method? First of all a function should do only one thing. Some people recommend numerical limits, like within 5 - 15 lines, or less than 30 lines. However these limits would bring question of why. The main principle to keep a function to do one thing is so called [Single Level of Abstraction Principle](http://principles-wiki.net/principles:single_level_of_abstraction).

__Single Level of Abstraction__  

- Compose methods out of calls ot other methods, each of which is at roughly the same level of abstraction - [From Implementation Patterns by Kent Beck](http://www.amazon.com/Implementation-Patterns-Kent-Beck/dp/0321413091/ref=sr_1_1?ie=UTF8&qid=1463270329&sr=8-1&keywords=Implementation+Patterns)
- In order to make sure our functions are doing "one thing", we need to make sure that the statements within a function are all at the same level of abstraction. - [From Clean code by Robert C. Martin](http://www.amazon.com/Clean-Code-Handbook-Software-Craftsmanship/dp/0132350882/ref=sr_1_3?ie=UTF8&qid=1463270329&sr=8-3&keywords=Implementation+Patterns)
- Each function should be written in terms of a single level of abstraction. - [From Principles Wiki](http://principles-wiki.net/principles:single_level_of_abstraction)

__Description__

All statements of a function should belong to the same level of abstraction. If there is a statement which belongs to a lower level of abstraction, it should go to a private function which comprises statements on this level. Doing so will result in smaller functions.

__Example1__(_from Implementation Patterns_)

``` c++ 

    void compute()
    {
        input();
        flags |= 0x0080;
        output();
    }
```

The statement "flags |= 0x0080" is not in the same level of abstraction. It shall be refactored to a function.

``` c++ 

    void compute()
    {
        input();
        setFlag();
        output();
    }

    void setFlag()
    {
        flags |= 0x0080;
    }
```

__Example2__

CalculateSum function sums numbers which are separated by '+' in a string, e.g. the result of "1 + 2 + 3" is 6.

``` c++ 

    int CalculateSum(string input_str)
    {
        std::vector<std::string> splitted_str = Split(input_str);

        // Sum numbers
        int sum = 0;
        for (std::string& str_num : splitted_str)
        {
            sum += std::stoi(str_num);
        }

        return sum;
    }

    std::vector<std::string> Split(const std::string &input_str)
    {
        std::istringstream iss(input_str);
        std::string tmp;
        std::vector<std::string> splitted_str;
        while (getline(iss, tmp, '+'))
            splitted_str.push_back(tmp);
    
        return splitted_str;
    }
```

It shall be refactored as following.

``` c++ 

    int CalculateSum(input_str)
    {
        std::vector<std::string> splitted_str = Split(input_str);
        return CalculateSum(splitted_str);
    }

    std::vector<std::string> Split(const std::string &input_str)
    {
        std::istringstream iss(input_str);
        std::string tmp;
        std::vector<std::string> splitted_str;
        while (getline(iss, tmp, '+'))
            splitted_str.push_back(tmp);
    
        return splitted_str;
    }

    int CalculateSum(std::vector<std::string>& splitted_str)
    {
        int sum = 0;
        for (std::string& str_num : splitted_str)
        {
            sum += std::stoi(str_num);
        }

        return sum;
    }
```
