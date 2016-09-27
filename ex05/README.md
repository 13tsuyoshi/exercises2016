# 課題5 (2016/9/26)
課題1で書いた線形補間のコードを fitted value iteration で使ってみる．

* 提出方法：課題1のリポジトリにファイルを「プッシュ」する．
* 締め切り：10月3日 (月)

A Simple Optimal Growth Model の
[Implementation 2](http://lectures.quantecon.org/jl/optgrowth.html#implementation-2)
のコードで `Interpolations` を使わずにそれを自分の線形補間のコードで置き換え，
[この図](http://lectures.quantecon.org/_images/vfi_intro_dp.png)や[この図](http://lectures.quantecon.org/_images/vfi_intro_dp2.png)を描いてみる．

* quant-econ のコードを notebook にコピーして

  ```jl
  using Interpolations
  ```

  を

  ```jl
  include("lin_interp.jl")
  ```

  で置き換える．
  (ファイル名は自分のものに合わせて適切に書き換える．)

* 線形補間関数を定義している

  ```jl
  Aw = scale(interpolate(w, BSpline(Linear()), OnGrid()), g.grid)
  ```

  の行を

  ```jl
  Aw = my_lin_interp(g.grid, w)
  ```

  に置き換える．
  (これも自分の仕様に合わせる．)

* われわれの仕様では `Aw` は `[]` ではなく `()` で呼び出すようにしたので，
  `Aw[g.f(k) - c]` を `Aw(g.f(k) - c)` に置き換える．

* 上の方にある `main` 関数を参考にして，図を表示するコードを書く．
  * `GrowthModel` インスタンスは `gm = GrowthModel()` で生成する
    (引数を空にするとデフォルトの値が使われる)．
  * `grid` は `gm` の中に保持されていて `gm.grid` でアクセスできる．
  * `beta` は `gm.bet`．
  * `alpha` は別個に定義しないといけない．


## ゼミ生の成果物

* [Notebook リスト](notebooks.md)
