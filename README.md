# 4d-tips-print-form
セレクションの印刷にPrint formを使用するサンプル

4D標準の印刷コマンドは 1. ワンコマンドで完結することに主眼が置かれたものと 2. コマンドを逐次的に発行することで細かく内容を制御できることに重点が置かれたものに分類することができます。

シンプル系

* PRINT LABEL
* PRINT RECORD
* PRINT SELECTION

自由系

* Print form
* Print object

並び替えとブレークレベルを上手に組み合わせれば，PRINT SELECTIONでも複雑なレポートを印刷することができますが，ヘッダー・フッターの内容を制御したい場合，あるいは途中で改ページを強制したい場合には，Print formのほうが便利かもしれません。

###Print formでセレクションを印刷する

[sample.pdf](https://github.com/4D-JP/4d-tips-print-form/blob/master/sample.pdf)

ポイント

改ページのタイミングを決定するため，余白関連のコマンドを活用する

* [GET PRINTABLE MARGIN](http://doc.4d.com/4dv15r/help/command/ja/page711.html)
* [GET PRINTABLE AREA](http://doc.4d.com/4dv15r/help/command/ja/page703.html)
* [Get printed height](http://doc.4d.com/4dv15r/help/command/ja/page702.html)

用紙を制御するためにプリントジョブ関連のコマンドを活用する

* [PAGE BREAK](http://doc.4d.com/4dv15r/help/command/ja/page6.html)
* [OPEN PRINTING JOB](http://doc.4d.com/4dv15r/help/command/ja/page995.html)
* [CLOSE PRINTING JOB](http://doc.4d.com/4dv15r/help/command/ja/page996.html)

```
ALL RECORDS([製品])
ORDER BY([製品];[製品]製品ID;>)

SET PRINT PREVIEW(True)
SET PRINT OPTION(Hide printing progress option;1)

GET PRINTABLE MARGIN($left;$top;$right;$bottom)
GET PRINTABLE AREA($printableHeight)

OPEN PRINTING JOB

For ($i;1;Records in selection([製品]))

  If ($i#1)
    PAGE BREAK(>)
  End if 

  $height:=Print form([製品];"帳票";Form header)
  $height:=Print form([製品];"帳票";Form detail)

  RELATE MANY([製品]ID)
  ORDER BY([パーツ];[パーツ]パーツID;>)

  For ($j;1;Records in selection([パーツ]))
    If ($j=1)
      $height:=Print form([パーツ];"帳票";Form header)
    Else 
      If (Get printed height>($printableHeight-$height-$bottom))
        PAGE BREAK(>)
      End if 
    End if 
    $height:=Print form([パーツ];"帳票";Form detail)
    NEXT RECORD([パーツ])
  End for 
  NEXT RECORD([製品])
End for 

CLOSE PRINTING JOB
```
