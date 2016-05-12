# 文字列電卓形(String Calculator Kata) - BDD
本節では、BDD(Behavior Driven Development)にて、文字列電卓のプログラム作成します。 文字列電卓機能としては、区切り文字で区切られた数値の合計を計算します。 Gherkin記述手法にて仕様を記述します。この仕様は、[文字列電卓形](https://github.com/bzquan/Documents/blob/master/Documents/StringCalculatorKata_jp.md)での仕様と同じです。

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

[Cucumber/C++](https://github.com/bzquan/CucumberCpp)にて実行可能な仕様を生成すると、下記コードが生成されます。

![Feature](https://github.com/bzquan/Documents/blob/master/Images/StringCalculator_feature.jpg)

``` c++ 

    文字列電卓_Steps.h/cpp
    文字列電卓_TestModel.h/cpp
    文字列電卓_Feature.cpp
```

文字列電卓_Steps.cppには下記シナリオ手順(Step)関数が含まれます。

``` c++ 

    // <数字文字列>がある
    void 文字列電卓_Steps::数字文字列がある(GherkinRow& row)
    {
    PendingStep(L"数字文字列がある");
    }

    // 数字を合計する
    void 文字列電卓_Steps::数字を合計する()
    {
        PendingStep(L"数字を合計する");
    }

    // 期待結果は<合計>であること
    void 文字列電卓_Steps::期待結果は合計であること(GherkinRow& row)
    {
        PendingStep(L"期待結果は合計であること");
    }

```

文字列電卓_TestModelの責務はシナリオStepの実行を行います。そのため、Step関数では、「文字列電卓_TestModel」にStepの処理を委託します。自動生成された文字列電卓_TestModelは下記のように、枠だけのクラスです。

``` c++ 

    //文字列電卓_TestModel
    class 文字列電卓_TestModel : public AbstractTestModel
    {
    public:
        void SetUp(){}
        void TearDown(){}
        void VerifyExpectations() override{}
    };
```

BDDの上記コードは、[TestCaseModel](https://github.com/bzquan/CucumberCpp/tree/master/Example/SimpleStringCalculator/src/TestCaseModel)ライブラリを使用するので、プロジェクトにTestCaseModelコードを追加する必要があります。まずは、文字列電卓_StepsのStep関数を下記のように実装します。

``` c++ 

    // <数字文字列>がある
    void 文字列電卓_Steps::数字文字列がある(GherkinRow& row)
    {
        model.Input(row[L"数字文字列"].strValue());
    }

    // 数字を合計する
    void 文字列電卓_Steps::数字を合計する()
    {
        model.CalculateSum();
    }

    // 期待結果は<合計>であること
    void 文字列電卓_Steps::期待結果は合計であること(GherkinRow& row)
    {
        model.ExpectedSum(row[L"合計"].intValue());
    }
```

次は、文字列電卓_TestModelクラスを実装します。

``` c++ 

    void 文字列電卓_TestModel::Input(std::string input)
    {
        m_Calculator.Input(input);
    }

    void 文字列電卓_TestModel::CalculateSum()
    {
        m_ActualSum = m_Calculator.CalculateSum();
    }

    void 文字列電卓_TestModel::ExpectedSum(int sum)
    {
        ASSERT_EQ(sum, m_ActualSum);
    }

```

これで、シナリオの実装が完了しました。今までの開発により、「1 + 2 + 3」ケースまで成功しました。ところが、「4, 5, 6」と「7; 8; 9」ケースはまだ成功できていません。その理由は、CalculateSum()関数で区切り文字を「+」に固定したためでした。 「4, 5, 6」と「7; 8; 9」ケースを成功させるため、入力された文字列に使用された区切り文字を見つける関数GetSeparatorを作成します。

``` c++ 

    char StringCalculator::GetSeparator(const std::string& input)
    {
        if (IsSeprator(input, '+'))
            return '+';
        else if (IsSeprator(input, ','))
            return ',';
        else if (IsSeprator(input, ';'))
            return ';';
        else
            return '\0';
    }
``` 

従いまして、CalculateSum関数も改修します。これで、最後の二項目以外、すべての試験項目が成功します。

``` c++
 
    int StringCalculator::CalculateSum()
    {
        char delim = GetSeparator(m_inputString);
        std::vector<std::string> nums = Split(m_inputString, delim);
        int sum = CalculateSum(nums);

        return sum;
    }
``` 

最後の二項目が失敗した理由は、std::stoiの引数に数字文字列として渡された文字列には、実は数字以外の文字列が含まれたため、std::stoiから例外が発生しました。「1, 2; 3」の場合、「,」が区切り文字として判定されたので、「2; 3」が一つの数字文字列になりました。「a1 + a2 + a3」の場合は、区切り文字が「+」でありましたので、a1、a2、a3が数字文字列として、std::stoi関数に渡されました。

この問題を解決するため、std::stoiを使用する前に、引数が数字またはスペースかをチェックするように改修しましょう。完成後のコードは下記のようです。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        char delim = GetSeparator(m_inputString);
        std::vector<std::string> nums = Split(m_inputString, delim);
        int sum = CalculateSum(nums);

        return sum;
    }

    char StringCalculator::GetSeparator(const std::string& input)
    {
        if (IsSeprator(input, '+'))
            return '+';
        else if (IsSeprator(input, ','))
            return ',';
        else if (IsSeprator(input, ';'))
            return ';';
        else
            return '\0';
    }

    std::vector<std::string> StringCalculator::Split(const std::string &str, char delim)
    {
        std::stringstream iss(str);
        std::string tmp;
        std::vector<std::string> splitted_str;
        while (getline(iss, tmp, delim))
            splitted_str.push_back(tmp);
    
        return splitted_str;
    }

    int StringCalculator::CalculateSum(std::vector<std::string>& nums)
    {
        int sum = 0;
        for (std::string& str : nums)
        {
            if (IsAllDigit(str))
                sum += std::stoi(str);
            else
            {
                sum = 0;
                break;
            }
        }

        return sum;
    }

    bool StringCalculator::IsSeprator(const std::string& input, char ch)
    {
        return std::string::npos != input.find(ch);
    }

    bool StringCalculator::IsAllDigit(const std::string& str)
    {
        if (str.length() == 0) return false;
        for (char ch : str)
        {
            bool is_digit = (ch >= '0') && (ch <= '9');
            bool is_space = (ch == ' ');
            if (!is_digit && !is_space) return false;
        }
        return true;
    }
```

続く => [Google Mockの使用](https://github.com/bzquan/Documents/blob/master/Documents/StringCalculatorKata3_jp.md)