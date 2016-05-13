# 文字列電卓形(String Calculator Kata) - Google Mock
本節では、[Google mock](https://github.com/google/googlemock)を使用したTDDとBDDを紹介します。通常、アプリケーションは複数のモジュールから構成されます。ところが、開発担当範囲がその一部のモジュールになる場合があります。この場合、担当外のモジュールをMock Object(疑似オブジェクト)にて代用することもあります。例えば、文字列電卓の場合、GUIがユーザにて入力された文字列を文字列電卓アプリケーションに通知し、アプリケーションがその合計結果をGUIに通知するとします。ここで、前節まで開発した文字列電卓StringCalculatorクラスが文字列電卓アプリケーションになります。まずは、Featureを下記のように改定します。いわゆる仕様変更です。

``` c++ 

    Feature: 文字列電卓Mock
    GUIとして、区切り文字で区切られた数字について、その数字の合計が通知されてほしい。

    Scenario Outline: 数字文字列の合計  
    区切り文字としては、「+」、「,」または「;」が可能である。但し、異なる区切り文字の混在は不可である。

    When <数字文字列>を入力する
    Then <合計>が通知されること

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

まず、StringCalculatorの使用者をIDisplayとして定義します。StringCalculatorは数字の合計を計算した後、IDisplay::Sum関数を呼び出し、その合計をIDisplayに通知します。

``` c++ 

    #pragma once

    class IDisplay
    {
    public:
        virtual ~IDisplay(){}
        virtual void Sum(int sum) = 0;
    };

```

下記はIDisplayのGoogle mockオブジェクトです。

``` c++ 

    #pragma once

    #include "gmock/gmock.h"
    #include "gtest/gtest.h"

    #include "IDisplay.h"

    using namespace testing;

    class MockDisplay : public IDisplay
    {
    public:
        MOCK_METHOD1(Sum, void(int sum));
    };

```

StringCalculatorがIDisplayに合計を通知できるようにするため、StringCalculatorを下記に変更します。つまり、ConstructorにてIDisplayオブジェクトを注入します。

``` c++ 

    class IDisplay;

    class StringCalculator
    {
    public:
        StringCalculator(IDisplay* pDisplay = nullptr) : m_pDisplay(pDisplay) {}
        void Input(const std::string input) { m_inputString = input; }
        int CalculateSum();

    private:
        ...

    private:
        IDisplay*    m_pDisplay;
        std::string  m_inputString;
};
```

「1 + 2 + 3」の合計は「6」の試験ケースを下記のように記述できます。

``` c++ 

    TEST_F(StringCalculatorTest, MockObjectの使用)
    {
        // Given
        MockDisplay mock;
        StringCalculator calc{ &mock };
        calc.Input("1 + 2 + 3");

        EXPECT_CALL(mock, Sum(6));  // 期待動作なので、実はThenに相当します

        // When
        calc.CalculateSum();

        // Then
    }
```

上記試験プログラムで、CalculateSum関数を呼び出す前に、mockに対しての期待動作EXPECT_CALL(mock, Sum(6))を設定しなければなりません。試験コードを理解しやすくするため、mockオブジェクトについての期待動作の設定処理をMockDisplayクラスに移動します。

``` c++ 

    class MockDisplay : public IDisplay
    {
    public:
        MOCK_METHOD1(Sum, void(int sum));

        void ExpectSum(int sum)
        {
            EXPECT_CALL(*this, Sum(sum));
        }
    };
``` 

これで、「MockObjectの使用」の試験項目を下記のように簡略できます。

``` c++
 
    TEST_F(StringCalculatorTest, MockObjectの使用)
    {
        // Given
        MockDisplay mock;
        StringCalculator calc{ &mock };
        calc.Input("1 + 2 + 3");

        mock.ExpectSum(6);   // ここが簡略されました。期待動作なので、実はThenに相当します

        // When
        calc.CalculateSum();

        // Then
    }
``` 

次は、StringCalculatorを下記のように変更します。これで、TEST_F(StringCalculatorTest, MockObjectの使用)が成功します。

``` c++ 

    int StringCalculator::CalculateSum()
    {
        char delim = GetSeparator(m_inputString);
        std::vector<std::string> nums = Split(m_inputString, delim);
        int sum = CalculateSum(nums);
        InformResult(sum);

        return sum;
    }

    void StringCalculator::InformResult(int sum)
    {
        if (m_pDisplay != nullptr) m_pDisplay->Sum(sum);
    }
```

上記の試験プログラムからわかるように、Google mockを使用したTDD形式の試験コードを組むのに手間が掛かるし、理解し易くでもありません。

続けて、Google mockを使用したBDDの実現方法を紹介します。[文字列電卓形-BDD](https://github.com/bzquan/Documents/blob/master/Documents/StringCalculatorKata2_jp.md)と同じく、Cucumber/C++にてGherkin Featureから実行可能な仕様を生成します。Stepの実装は下記のようです。

``` c++ 

    // <数字文字列>を入力する
    void 文字列電卓Mock_Steps::数字文字列を入力する(GherkinRow& row)
    {
        model.Input(row[L"数字文字列"].strValue());
    }

    // <合計>が通知されること[[mock]]
    void 文字列電卓Mock_Steps::合計が通知されること(GherkinRow& row)
    {
        model.ExpectedSum(row[L"合計"].intValue());
    }
```

Modelの実装は下記のようです。


``` c++
 
    class 文字列電卓Mock_TestModel : public AbstractTestModel
    {
    public:
        文字列電卓Mock_TestModel() : m_Calculator(&m_MockDisplay) {}

        void Input(string input);
        void ExpectedSum(int sum);

    private:
        MockDisplay      m_MockDisplay;
        StringCalculator m_Calculator;
        int m_ExpectedSum;
        int m_ActualSum;
    };

    void 文字列電卓Mock_TestModel::Input(string input)
    {
        m_Calculator.Input(input);
        m_ActualSum = m_Calculator.CalculateSum();  // 文字入力時、合計処理も行います
    }

    void 文字列電卓Mock_TestModel::ExpectedSum(int sum)
    {
        m_MockDisplay.ExpectSum(sum);
    }

```

残念ながら、上記の実装では文字列電卓Mock関連の試験項目がすべて失敗します。その理由は、m_MockDisplay.ExpectSum(sum)がm_Calculator.Inputとm_ActualSum = m_Calculator.CalculateSum()が実行された後、最後に実行されるので、MockオブジェクトへのEXPECT_CALL(*this, Sum(sum))の期待動作が満たされていません。この処理順序の問題解決のため、Gherkin featureを下記に改定します。ここで、Then文に[[mock]]属性を追加しました。このmock属性により、「Then <合計>が通知されること[[mock]]」が先に実行されるので、すべての試験ケースが成功になります。

``` c++ 

    Feature: 文字列電卓Mock
    GUIとして、区切り文字で区切られた数字について、その数字の合計が通知されてほしい。

    Scenario Outline: 数字文字列の合計  
    区切り文字としては、「+」、「,」または「;」が可能である。但し、異なる区切り文字の混在は不可である。

    When <数字文字列>を入力する
    Then <合計>が通知されること[[mock]]

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

補足ですが、Stepの関数名が下記のように変更されましたので、Stepコードの変更も必要です。

``` c++ 

    ... ...

    // <合計>が通知されること[[mock]] => 関数名の最後尾にmockが追加されました
    void 文字列電卓Mock_Steps::合計が通知されることmock(GherkinRow& row)
    {
        model.ExpectedSum(row[L"合計"].intValue());
    }
```

[サンプルコードはここから](https://github.com/bzquan/CucumberCpp/tree/master/Example/SimpleStringCalculatoMock)

おわり