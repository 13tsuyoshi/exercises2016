# 課題1 (2016/5/2)
線形補間 (linear interpolation) の関数を返すコードを書いてみる．

目標は[この節](http://quant-econ.net/jl/optgrowth.html#fitted-value-iteration)の図を描く[コード](https://github.com/QuantEcon/QuantEcon.applications/blob/master/optgrowth/linapprox.jl)の[この行](https://github.com/QuantEcon/QuantEcon.applications/blob/master/optgrowth/linapprox.jl#L7)を置き換えられるようなプログラムを書くこと．

* 提出方法：GitHub のリポジトリにファイルを「プッシュ」する．
* 中間締め切り：5月9日 (月)
* 最終締め切り：5月21日 (土)

与えられた1次元配列 `grid`, `vals` (ともに同じ長さ `n` とする) に対して，
`(grid[1], vals[1])`, ..., `(grid[n], vals[n])` を結ぶ折れ線グラフの関数を得たい．
(`grid` の要素は小さい順にソートされているとする．)

たとえば，以下のように「関数を返す関数」を書いてみる．

```julia
function my_lin_interp(grid, vals)
    function func(x)
        ...
    end

    return func
end
```

(ほんとはこれは筋が悪く，type で書いた方がよい．それはまた後で考える．)

最初は `grid` の要素が2つ (区間が1つ) だけのケースを想定して書いてみる．
たとえば：

```julia
grid = [1, 2]
vals = [2, 0]
f = my_lin_interp(grid, vals)

f(1.25)
# 1.5 が返ってくるはず
```

* [Arrays](http://quant-econ.net/jl/julia_by_example.html#arrays)
* [User-Defined Functions](http://quant-econ.net/jl/julia_by_example.html#user-defined-functions)

一般の n 要素のケースにおいては，`x` がどの区間に入るのかを調べないといけない．
それには
[`searchsortedfirst`](http://docs.julialang.org/en/release-0.4/stdlib/sort/#Base.searchsortedfirst)
あるいは
[`searchsortedlast`](http://docs.julialang.org/en/release-0.4/stdlib/sort/#Base.searchsortedlast)
を使う．

(`grid` の範囲外の値が `x` として入力されたときの動作は自分で適宜決める．)


## 拡張・改良

次は

* 引数 `x` が Vector のときへの対応 (universal function 化)
* type としての実装

をやってみる．

### Vector 入力への対応

[Types, Methods and Performance](http://quant-econ.net/jl/types_methods.html) の章の

* [User Defined Methods](http://quant-econ.net/jl/types_methods.html#user-defined-methods)
* [Exercise 2](http://quant-econ.net/jl/types_methods.html#exercise-2)

を参考にする．

```julia
julia> f([1.25, 1.5])
2-element Array{Float64,1}:
 1.5
 1.0
```

のように `x` が Vector として与えられたら要素それぞれに対する `y` の値を Vector で返すようにしたい．

### Type として実装

[Immutable Composite Type](http://docs.julialang.org/en/release-0.4/manual/types/#immutable-composite-types)
を使ってみる．

```julia
immutable MyLinInterp
    grid
    vals
end
```

`MyLinInterp` タイプに対して `call` メソッドを定義することで，

```
grid = [1, 2]
vals = [2, 0]
f = MyLinInterp(grid, vals)

f(1.25)
```

のように使えるようになる．

`call` メソッドを定義するには以下のように書く：

```julia
function Base.call(f::MyLinInterp, x)
    ...
end
```

この場合も，`x` がスカラーのケースとベクトルのケースについて定義しておけば，それぞれ入力に対して動作を変えることができる．
