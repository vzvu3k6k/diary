---
title: Rational を10進小数展開するアルゴリズムを実装した
tags: ruby
---

CRuby の [Feature #8850](https://bugs.ruby-lang.org/issues/8850) で提案されている Rational を10進小数展開する機能を実装してみた。
循環節の検出は、セルオートマトンの周期解を検出するときに使った手法を応用した。

この手法では、1桁ずつの展開 (標準展開) と2桁ずつの展開 (倍速展開) を同時に進め、両者の状態 (剰余) が等しくなったときに循環を見付けたことになる。

標準展開の末尾の位置と倍速展開の末尾の位置の差が循環節の長さに一致する。

循環節の先頭位置を見付けるには、標準展開と倍速展開のそれぞれの末尾から1桁ずつ前に戻りながら桁の値を比較していけば良い。
たとえば、循環を検出した時点で以下のような小数展開が得られたとしよう。

~~~~
123.4567891357924680123450987613579246801234
                       ^ 標準展開の末尾
                                           ^ 倍速展開の末尾
~~~~

標準展開の末尾と倍速展開の末尾にあるカーソルを両方1桁ずつ前に移動させながら、それぞれのカーソルが指す桁の値が等しくなくなるまで繰替えすと次のようになる。

~~~~
123.4567891357924680123450987613579246801234
         ^ 標準展開のカーソル
                             ^ 倍速展開のカーソル
~~~~

このときの標準展開のカーソルが指す桁の直後から循環節が開始し、倍速展開のカーソルが指す位置までが循環節になっている。

このアルゴリズムは循環を検出するまで循環節の桁数より多くの桁を小数展開するが、循環節の桁数の2倍以上は展開することはない。
必要な記憶領域は剰余2つ分使用し、2桁を展開するために2回の剰余を実行する。

[TODO] 循環節の先頭を探索するために二分探索が使えるので後で実装すること。
