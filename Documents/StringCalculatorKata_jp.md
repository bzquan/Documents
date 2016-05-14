# 文字列電卓形(String Calculator Kata)
Kata(形)とは、TDD開発手法にて練習問題を繰り返して解くことにより、TDDの考え方をより深く身につけることです。本文では、TDDにて、文字列電卓のプログラム作成して見ます。 文字列電卓機能としては、区切り文字で区切られた数値の合計を計算します。 下記のGherkin記述手法にて仕様を記述します。

``` c++ 

    Feature: 文字列電卓
    使用者として、区切り文字で区切られた数字について、その数字の合計がほしい。

    Scenario Outline: 数字文字列の合計  
    区切り文字としては、「+」、「,」または「;」が可能である。但し、異なる区切り文字の混在は不可である。

    Given <数字文字列>がある
    When 数字を合計する
    Then 期待結果は<合計>であること

    Examples:
     |数字文字列   |合計|
     |            |0  |
     |18          |18 |
     |1 + 2 + 3   |6  |
     |4, 5, 6     |15 |
     |7; 8; 9     |24 |
     |1, 2; 3     |0  |
     |a1 + a2 + a3|0  |
```

まずは、初期状態のGoogle testケースを作成します。

``` c++ 

    TEST_F(StringCalculatorTest, 初期状態)
    {
        // Given
        StringCalculator calculator; 
    
	    // When
        int 合計結果 = calculator.CalculateSum();

	    // Then
        ASSERT_EQ(0, 合計結果);
    }
```

次は最小限のStringCalculatorを作成します。

``` c++ 

    class StringCalculator
    {
    public:
        int CalculateSum() { return 0; }
    };
```

これで、初期状態ケースが成功します。 続けて、数字一つのケースを作成します。

``` c++ 

    TEST_F(StringCalculatorTest, 数字一個)
    {
        // Given
        calculator.Input("18");

        // When
        int 合計結果 = calculator.CalculateSum();

        // Then
        ASSERT_EQ(18, 合計結果);
    }
```

StringCalculatorにInput関数を追加します。

``` c++ 

    class StringCalculator
    {
    public:
        int CalculateSum() { return 0; }
        void Input(const std::string input) { m_inputString = input; }

    private:
        string  m_inputString;
    };
```

これで、コンパイルは通るが「数字一個」ケースは失敗するはずです。 次は、「数字一個」ケースが成功するように、StringCalculatorを作成します。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        return std::stoi(m_inputString);
    }

```

「数字一個」ケースは成功するが、「初期状態」は失敗してしまいます。問題原因はstd::stoiは空文字の場合、例外を発生させるためです。改修後のコードは下記です。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        if (m_inputString.empty())
           return 0;
        else
           return std::stoi(m_inputString);
    }

```

これで、「数字一個」ケースも成功します。 続けて、区切り文字が「+」場合に対応します。

``` c++ 

    TEST_F(StringCalculatorTest, 区切り文字がプラス記号)
    {
        // Given
        calculator.Input("1 + 2 + 3");

        // When
        int 合計結果 = calculator.CalculateSum();

        // Then
        ASSERT_EQ(6, 合計結果);
    }
```

まだ未実装なので、このケースは失敗するはずです。この問題を解決するためには、

1. "1 + 2 + 3"を"1"、"2"、"3"に分解する
2. stoiにて個々の文字列形式の数字を数字に変換する
3. 数字を合計する

この考え方により、CalculateSumを下記に改修する。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        if (m_inputString.empty())
           return 0;
        else
        {
            // 文字列の分解
            std::istringstream iss(m_inputString);
            std::string tmp;
            std::vector<string> splitted_str;
            while (getline(iss, tmp, '+'))
                splitted_str.push_back(tmp);

            // 合計
            int sum = 0;
            for (std::string& str_num : splitted_str)
            {
                sum += std::stoi(str_num);
            }

            return sum;
        }
    }

```

これで、「区切り文字がプラス記号」ケースも成功します。 ここからは、リファクタリング(Refactoring)を行い、コードをきれいにします。まずは、Split関数を抽出して(Extract method)、文字列の分解を行います。
``` c++ 

    int StringCalculator::CalculateSum()
    {
        if (m_inputString.empty())
           return 0;
        else
        {
           // 文字列の分解
           std::vector<std::string> splitted_str = Split(m_inputString, '+');

           // 合計
           int sum = 0;
           for (std::string& str_num : splitted_str)
           {
               sum += std::stoi(str_num);
           }

           return sum;
        }
    }

    std::vector<std::string> StringCalculator::Split(const std::string &str, char delim)
    {
        std::istringstream iss(str);
        std::string tmp;
        std::vector<std::string> splitted_str;
        while (getline(iss, tmp, delim))
            splitted_str.push_back(tmp);
    
        return splitted_str;
    }
```

リファクタリング後、すべての試験ケースを実行して、リファクタリング時にミスがなかったことを確認します。続けて、合計関数を抽出します。

``` c++ 

    int StringCalculator::CalculateSum(std::vector<std::string>& nums)
    {
        int sum = 0;
        for (std::string& str_num : nums)
        {
            sum += std::stoi(str_num);
        }

        return sum;
    }
```

このリファクタリングにより、StringCalculator::CalculateSum()が下記のようになります。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        if (m_inputString.empty())
           return 0;
        else
        {
           std::vector<std::string> splitted_str = Split(m_inputString, '+');
           return CalculateSum(splitted_str);
        }
    }
```

実は、StringCalculator::CalculateSum(vector<string>& nums)が既に空文字列のケースにも対応していましたので、if (m_inputString.empty())は余分な処理になっています。再度リファクタリングを実施して、余分なコードを削除します。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        std::vector<std::string> splitted_str = Split(m_inputString, '+');
        return CalculateSum(splitted_str);
    }
```

続く => [BDD](https://github.com/bzquan/Documents/blob/master/Documents/StringCalculatorKata2_jp.md)