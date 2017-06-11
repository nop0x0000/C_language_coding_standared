ヘッダファイルの書き方
======================

参考
----

技術評論社 C言語入門書の次に読む本 改訂新版

ヘッダファイルの目的
--------------------

ヘッダファイルはモジュールを使用するために *外部に公開される* 関数や構造体、マクロなどを記述する。

モジュールの使用者はそのモジュールのヘッダファイルをインクルードすることで、
そのモジュールを使用できるようになる。

ヘッダファイルはそのモジュールをどう使うか、どのような機能が有るかを使用者に伝える役割もあるため、
そのモジュールの使用者から見ると、 *モジュールのユーザマニュアル兼仕様書となる* 。

そのため、モジュールの使用者はヘッダファイルを見てどのような機能が使えるかのみを気にすれば良く、
モジュールのソースファイル(.cファイル)の中身は気にする必要が無くなる。

このように、モジュールを使用するのに必要な関数・定数・型のみをヘッダファイルに記述し、
非公開ものは記述しないことで、モジュールのカプセル化を行い、モジュール間の関係を疎結合にする。

ヘッダファイルに記述する項目
----------------------------

* 外部に公開するマクロの定義(関数型マクロの定義)
* 外部に公開する定数の定義( ``#define`` や ``enum`` による定義)
* 外部に公開する構造体、共用体の定義
* 外部に公開する型の定義( ``typedef`` による型の定義)
* グローバル関数のプロトタイプ宣言
* グローバル変数の ``extern`` 宣言
* このモジュールを使用するために必要な他のヘッダファイルのインクルード(例えば関数の引数や戻り値に ``bool`` を用いている場合、 ``stdbool.h`` のインクルードが必要)

インクルードガードについて
--------------------------

ヘッダファイルの先頭と末尾には下記のようなインクルードガードを必ず記述すること。

.. code-block:: c

   #ifndef SAMPLE_H // マクロ「SAMPLE_H」はできればUUIDを使用する
   #define SAMPLE_H
   
   // ヘッダファイルの内容をここに記述
   
   #endif // Include guard

なぜインクルードガードが必要か
------------------------------

``#include`` は指定されたファイルの内容をプリプロセスでその場所にそのまま展開するため
インクルードガード無しのヘッダファイルを複数箇所でインクルードすると、
全く同じ記述が複数箇所に埋め込まれるため、多重定義となる。

インクルードガードはヘッダファイルを複数箇所でインクルードしたときに
多重定義でエラーとなるのを回避するために記述する。

ヘッダファイルによるモジュールの記述例
--------------------------------------

.. code-block:: c
   :caption: HeaderFileExample.h
   :linenos:

   #ifndef 95eb19a0-e338-4d2a-899f-ad081db253d6 // Include guard
   #define 95eb19a0-e338-4d2a-899f-ad081db253d6
   
   // このモジュールを使用するのに必要なモジュールのインクルード
   // この場合、公開する関数の引数と戻り値でboolとuint16_tを使用しているため
   // 以下の2つをインクルードする
   #include <stdbool.h>
   #include <stdint.h>
   
   // 公開する関数型マクロ
   #define BITSET(DATA, BIT) (DATA = DATA | (1<<BIT))
   #define BITCLEAR(DATA, BIT) (DATA = DATA & ~(1<<BIT))
   
   // 公開するオブジェクトマクロ(定数)
   #define CONSTANT_NUM (1234)
   
   // 公開する構造体
   typedef struct
   {
       unsigned char year;
       unsigned char month;
       unsigned char day;
       unsigned char hour;
       unsigned char minute;
       unsigned char second;
   } TDateTime;
   
   // 公開する共用体
   typedef union
   {
       uint16_t bit16;
       struct
       {
           uint8_t byte0;
           uint8_t byte1;
       } Byte;
   } UBit16;
   
   // 公開する列挙体
   typedef enum
   {
       Day_Monday,
       Day_Tuesday,
       Day_Wednesday,
       Day_Thursday,
       Day_Friday,
       Day_Sunday,
   } EDay;
   
   // 公開するグローバル関数のプロトタイプ
   bool Sample_ReturnsBoolFunction(void);
   void Sample_UintArgFunction(uint16_t value);
   // constによって引数の構造体の中身が変更されないことを明記する
   uint32_t Sample_GetSecondsOfYear(const TDateTime * dateTime);
   
   // 公開する変数のextern宣言(実態は対応するソースファイルに記述)
   extern uint32_t SecondsOfDay;
   
   #endif // Include guard

.. code-block:: c
   :caption: HeaderFileExample.c
   :linenos:

   // モジュールのヘッダファイルを必ずソースファイルの先頭でインクルードすること
   // 関数のプロトタイプをソースファイルに取り込み、マクロや定数を使用できるようにするため。
   // また、ヘッダファイルでextern宣言されている変数の実態と紐付けされることをコンパイラに知らせるため。
   #include "sample.h"
   
   // ソース内のローカル関数で使用するモジュールのインクルード
   #include <stdio.h>
   
   // ファイル外から参照しないローカル変数はヘッダファイルに記述しない。
   // staticを付けて静的グローバル変数とすることで、ファイル外から使用できなくする。
   static int32_t counter = 0;
   
   // ファイル外から参照しないローカル関数はヘッダファイルに記述しない。
   // staticを付けて静的関数とすることで、ファイル外から使用できなくする。
   static char * localFunctionReturnsStr(void)
   {
       return "Test string\n";
   }
   
   // グローバル変数の実態はソースファイルに記述すること。
   // ヘッダファイルにextern宣言を書いているため、
   // 他のモジュールはこのモジュールのヘッダをインクルードすることで
   // この変数を使用できるようになる。
   uint32_t SecondOfDay
   
   // グローバル関数の実態
   // ヘッダファイルにプロトタイプ宣言を書いているため、
   // 他のモジュールはこのモジュールのヘッダをインクルードすることで
   // この関数を使用できるようになる。
   bool Sample_ReturnsBoolFunction(void)
   {
       if(/*何かしらの条件分*/)
           return false;
   
       // 何かしらの処理
       return true;
   }
   
   // グローバル関数の実態
   void Sample_UintArgFunction(uint16_t value)
   {
       // 何かしらの処理
   }
   
   // グローバル関数の実装
   uint32_t Sample_GetSecondsOfYear(const TDateTime * dateTime)
   {
       uint32_t secondOfDay;
       // 何かしらの処理
       return secondsOfDay;
  
   }
