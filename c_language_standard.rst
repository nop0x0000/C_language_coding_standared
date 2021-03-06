C言語の規格について
===================

基本的にC99を使用する。

積極的理由
----------

* コメントに ``//`` を使用でき、コメントのネストができる。( ``/* */`` では入れ子になったコメントはできない)
* ``bool`` 型を使用でき、処理の意味を明確化できる。
* ``inline`` キーワードを使用でき、短い処理を ``#define`` によるマクロより安全に記述できる。
* 暗黙的関数呼び出しによる問題を回避できる。( :doc:`../bug_and_bad_practice_report/implicit_function_calling` を参照)
* 変数宣言をブロックの先頭以外でもできるため、処理に使用する変数が近くで宣言でき、読みやすい。
* ``stdint.h`` によりビット幅を明確に指定した変数を使用でき、より安全なプログラムを作成できる。

消極的理由
----------

* 積極的理由に記述したことが出来ない
* C11はマイコン用コンパラでのサポートが執筆時点(2016年3月)で十分ではない。
