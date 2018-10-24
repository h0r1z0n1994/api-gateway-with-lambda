# api-gateway-with-lambda
ハンズオン用LambdaのAPI Gateway

## バックエンドでLambda関数を作成する

+ Lambdaコンソールに移動する

+ 「関数の作成」を選択します

+ 「一から作成」を選択

+ 「名前」ボックスに任意の名前を設定

例）test-api-gateway-with-lambda-horizono

+ 「既存のロール」は「lambda-basic-execution」を設定

+ 以下の Lambda 関数をコピーし、Lambda コンソールのコードエディターに貼り付けます

```
console.log('Loading the Calc function');

exports.handler = function(event, context, callback) {
    console.log('Received event:', JSON.stringify(event, null, 2));
    if (event.a === undefined || event.b === undefined || event.op === undefined) {
        callback("400 Invalid Input");
    }
    
    var res = {};
    res.a = Number(event.a);
    res.b = Number(event.b);
    res.op = event.op;
    
    if (isNaN(event.a) || isNaN(event.b)) {
        callback("400 Invalid Operand");
    }

    switch(event.op)
    {
        case "+":
        case "add":
            res.c = res.a + res.b;
            break;
        case "-":
        case "sub":
            res.c = res.a - res.b;
            break;
        case "*":
        case "mul":
            res.c = res.a * res.b;
            break;
        case "/":
        case "div":
            res.c = res.b===0 ? NaN : Number(event.a) / Number(event.b);
            break;
        default:
            callback("400 Invalid Operator");
            break;
    }
    callback(null, res);
};
```

+ 「保存」を選択肢、関数の作成を終了します


## Lambda関数のAPIリソースを作成する

+ API Gateway コンソール

+ 「新しいAPI」を選択

+ 任意のAPI名を指定

例）calc-lambda-horizono

+ 「アクション」から「リソースの作成」を選択

+ 「リソース名」に「calc」、「リソースパス」に「calc」を指定

+ 「リソースの作成」を選択

+ 「アクション」から「メソッドの作成」を選択

+ 「GET」を作成

+ /calc -GET-「セットアップ」ペインで以下の操作を行います。

+ 「Lambda関数」ボックスに先程作成したLambda関数名を指定します。

+ 「保存」を選択

+ 「リソース」ペインから「GET」を選択

+ 「メソッドリクエスト」を選択

+ 「URLクエリ文字パラメータ」を選択

+ 「クエリ文字列の追加」を選択

+ 「operand1」「operand2」 「operator」を追加し、必須にチェックを入れる

+ 「統合リクエスト」を選択

+ 「マッピングテンプレート」を選択

+ 「マッピングテンプレートの追加」を選択

+ 「Content-Type」に「application/json」を入力し、保存

+ マッピングテンプレートエディタに下記を貼り付け、保存

```
{
    "a":  "$input.params('operand1')",
    "b":  "$input.params('operand2')", 
    "op": "$input.params('operator')"   
}
```

+ 「テスト」を選択

+ 「クエリ文字列」に「operand1=1&operand2=2&operator=+」を入力

+ 「テスト」を選択

+ 「レスポンス本文」に下記が出力されることを確認する

```
{
  "a": 1,
  "b": 2,
  "op": "+",
  "c": 3
}
```
