# Introduction

toptreeを実装する記事やぞ

## toptree

簡単に言うとLinkCutTreeの上位互換  700行あります.(解説するのしんどいけど... 700行そんなに多くないっていう声も合ったので気にせず実装してね)

## 実装方針

はじめから`struct toptree`と書く, templateを使う, 最適化をしようとすると簡単に破滅します. いつものライブラリに対する綺麗な書き方はしないほうがいいです...  
この記事では, しません(`struct node`とかその他の関数をグローバルにおいたりします)

ここで, 僕のtoptree(self-adjusting toptree)をおいておきます. これを実装します.

[No.902 Query ζone - yukicoder](https://yukicoder.me/problems/no/902)

```cpp
{{#include ../toptree.cpp}}
```

