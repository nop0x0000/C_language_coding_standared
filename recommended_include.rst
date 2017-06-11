======================
推奨されるインクルード
======================

.. list-table::
   :header-rows: 1

   * - ヘッダ
     - 内容
   * - stdint.h
     - 固定長の標準型を使用できるようにする。
   * - stdbool.h
     - bool型を使用できるようにする。
   * - stddef.h
     - NULLやsize_t, ptrdiff_tなど共通の型を使用できるようにする。
   * - inttypes.h
     - stdint.hで定義された型をprintfやscanfで使用するための変換指定子や最大幅整数型(intmax_t)のための関数を使用できるようにする。
