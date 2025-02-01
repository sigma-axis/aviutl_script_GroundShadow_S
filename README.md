# GroundShadow_S AviUtl スクリプト

影を立体的に地面へ落とすアニメーション効果．地面だけでなく壁や天井に影を落としたような使い方もできます．

[ダウンロードはこちら．](https://github.com/sigma-axis/aviutl_script_GroundShadow_S/releases) [紹介動画．](https://www.nicovideo.jp/watch/sm44602570)

![動作デモ](https://github.com/user-attachments/assets/9512ddc9-2efe-40b0-8239-e76e292539a9)

![色んな影の落とし方](https://github.com/user-attachments/assets/8323b397-022e-4af5-abb1-9ed3bd059dbc)

## 動作要件

- AviUtl 1.10 (1.00 でも動作するはずだが 1.10 推奨)

  http://spring-fragrance.mints.ne.jp/aviutl

- 拡張編集 0.92

  - 0.93rc1 でも動作するはずだが未確認 / 非推奨．

- **(推奨)** patch.aul (謎さうなフォーク版)

  https://github.com/nazonoSAUNA/patch.aul

  アンカー位置の認識がずれる原因が 1 つ減ります．
  - 設定ファイル `patch.aul.json` で `"switch"` 以下の `"lua"` と `"lua.getvalue"` を `true` (初期値) にしてください．

## 導入方法

以下のフォルダのいずれかに `GroundShadow_S.anm` のファイルをコピーしてください．

1. `exedit.auf` のあるフォルダにある `script` フォルダ
1. (1) のフォルダにある任意の名前のフォルダ

## パラメタの説明

### `地面角度`

影を落とす先の「地面」の角度を指定します．カメラ視点を基準として次のように決まります:

|負の値|`0`|正の値|
|:---:|:---:|:---:|
|下り坂|水平|上り坂|

単位は度数法で最小値は `-180`, 最大値は `180`, 初期値は `0`.

- [`左右に射影`](#左右に射影) が ON の場合は，また解釈が変わってきます．

### `光源角度` / `光源傾斜`

光の入射角を指定します．`光源角度` で上下方向が，`光源傾斜` で左右方向が，カメラ視点を基準として次のように決まります:

|パラメタ|負の値|`0`|正の値|
|:---:|:---:|:---:|:---:|
|`光源角度`|上から下|手前から奥への水平方向|下から上|
|`光源傾斜`|右から左|左右にまっすぐ|左から右|

`光源角度` の単位は度数法で最小値は `-180`, 最大値は `180`, 初期値は `-45`.

- `光源角度` が $\pm 90\degree$ を超えると，影が手前側に落ちるようになります．

  ![前後からの光源](https://github.com/user-attachments/assets/119f196b-21a8-4b4f-861b-872c2c220baa)

- 下から上への光は，「天井に影を落とす」ような表現に使えます．

  ![天井へ影を落とす](https://github.com/user-attachments/assets/3a466847-0874-4bd8-83ec-6157d59be6a1)

`光源傾斜` は傾斜量 (角度の正接 ($\tan$ 関数) の値) の % 単位で，最小値は `-800` (約 $-82.87\degree$), 最大値は `800` (約 $82.87\degree$), 初期値は `0`.

- [`左右に射影`](#左右に射影) が ON の場合は，また解釈が変わってきます．

### `濃さ`

影の不透明度を % 単位で指定します．最小値は `0`, 最大値は `100`, 初期値は `50`.

### `左右に射影`

影を「地面」に落とすのではなく，「壁」に落としたように描画します．

![左右に影を落とす](https://github.com/user-attachments/assets/3796f7cc-a5ad-4999-82cb-c49f88418cb6)

ON だと[`地面角度`](#地面角度) と [`光源角度`, `光源傾斜`](#光源角度--光源傾斜) の解釈が変化します:

- `地面角度` は壁の角度として次のように解釈されます:

  |負の値|`0`|正の値|
  |:---:|:---:|:---:|
  |左手前から右奥へ伸びる壁|カメラ視線と平行|右手前から左奥へ伸びる壁|

- `光源角度` は光の左右入射角として次のように解釈されます:

  |負の値|`0`|正の値|
  |:---:|:---:|:---:|
  |左から右|手前から奥へ左右にまっすぐ|右から左|

- `光源傾斜` は光の上下入射角として次のように解釈されます:

  |負の値|`0`|正の値|
  |:---:|:---:|:---:|
  |上から下|上下に水平方向|下から上|

初期値は OFF.

### `影色`

影の色を指定します．初期値は `0x000000` (黒).

### `影色強さ`

[`影色`](#影色) を適用する強さを % 単位で指定します．`0` だと元オブジェクトと同じ色で影を描画します．初期値は `100`.

### `影拡散`

影のぼかし幅をピクセル単位で指定します．初期値は `20`.

### `影位置移動`

影の描画位置を平行移動で動かせます．`{<X移動量>, <Y移動量>}` の書式でピクセル単位で記述します．初期値は `{0,0}` (移動なし).

### `影の範囲`

[`地面位置`](#地面位置) で指定した $y$ 座標（[`左右に射影`](#左右に射影) が ON の場合は $x$ 座標）に近い部分のみを影の描画対象にします．描画対象の範囲を `地面位置` からのピクセル単位の距離で指定．負数を指定すると現在オブジェクト全てが描画対象になります．初期値は `-1`.

### `影先端ぼかし`

影の上下端（[`左右に射影`](#左右に射影) が ON の場合は左右端）が薄くなっているように描画します．薄くなる範囲を影先端からのピクセル単位の距離で指定．初期値は `0`.

### `地面位置`

影を落とす「地面」の位置を指定します．ここで指定した座標を通り，[`地面角度`](#地面角度) で指定した角度の平面に影を落とします．AviUtl のメインウィンドウ上にあるアンカーをドラッグすることでも調整できます．

- オブジェクトの下端にアンカーを置くことで「地面に立っている」ような表現ができます．下端よりさらに下に置くと「地面から浮いている」ように見えます．

- オブジェクトの上端にアンカーを置くことで「天井に影が落ちている」ような表現もできます．

- 鉛直な「地面」([`地面角度`](#地面角度) が `90`) に対し，[`地面位置`](#地面位置) を $z$ 方向に移動させると影の見た目の大きさを拡大・縮小できます．

  ![浮いた影](https://github.com/user-attachments/assets/a931e155-c54b-4218-ab54-bd5bd9aeb8b9)

オブジェクトの中心からの相対座標で `{<X座標>, <Y座標>, <Z座標>}` とピクセル単位で記述します．初期値は `{0,200,0}`.

- 緑のアンカー線を操作することで指定できます．

- オブジェクトに拡大率や回転などが設定されていた場合，アンカー位置を正しく取得できない場合があります．

### `カメラ位置`

立体射影を計算する際の，カメラ位置の $x, y$ 座標を指定します．

オブジェクトの中心からの相対座標で `{<X座標>, <Y座標>}` とピクセル単位で記述します．初期値は `{0,-200}`.

- 青いアンカー線を操作することで指定できます．

- オブジェクトに拡大率や回転などが設定されていた場合，アンカー位置を正しく取得できない場合があります．

### `カメラ距離`

立体射影を計算する際の，カメラとオブジェクトとの間の距離をピクセル単位で指定します．

- 小さくすると立体感が強くなり，遠くの影がさらに小さく，近くの影はさらに大きくなります．
- 大きくすると立体感が弱くなり，遠くの影と近くの影の大きさに違いが出にくくなります．

最小値は `1`, 初期値は `1024`.

### `視野幅(%)`

立体射影を計算する際の，カメラの視野角を調整します．単位は % で，視野角の正接 ($\tan$ 関数の値) の割合で指定します．

- 大きくすると立体感が強くなり，遠くの影がさらに小さく，近くの影はさらに大きくなります．
- 小さくすると立体感が弱くなり，遠くの影と近くの影の大きさに違いが出にくくなります．

最小値は `0`, 初期値は `100`.

### `前景透明度(%)`

影の元となるオブジェクトの透明度を % 単位で指定します．最小値は `0`, 最大値は `100`, 初期値は `0`.

### `PI`

パラメタインジェクション (parameter injection) です．初期値は `nil`. テーブル型を指定すると `obj.check0` や `obj.track0` などの代替値として使用されます．また，任意のスクリプトコードを実行する記述領域にもなります．

```lua
{
  [0] = check0, -- boolean 型 で "左右に射影" の項目を上書き，または nil. 0 を false, 0 以外を true 扱いとして number 型も可能．
  [1] = track0, -- number 型で "地面角度" の項目を上書き，または nil.
  [2] = track1, -- number 型で "光源角度" の項目を上書き，または nil.
  [3] = track2, -- number 型で "光源傾斜" の項目を上書き，または nil.
  [4] = track3, -- number 型で "濃さ" の項目を上書き，または nil.
}
```


## TIPS

1.  [`地面角度`](#地面角度) と [`光源角度`](#光源角度--光源傾斜) が平行に近い場合，影が極端に大きくなることがあります．その場合オブジェクトの画像最大サイズに達してしまうことがあるので注意してください (後続のフィルタ効果が意図通りにかからないことがある).

1.  画面中央にカメラがあるようにしたい場合，[`カメラ位置`](#カメラ位置) に `{-obj.x,-obj.y}` と記述してください．

    ただし「拡大率」やオブジェクトの $z$ 座標がデフォルトでない場合などではカメラ位置がずれることがあります．

1.  [`地面位置`](#地面位置) や [`カメラ位置`](#カメラ位置) の画面上の位置を固定したうえでオブジェクトを移動したい場合，`GroundShadow_S` の*上に*フィルタ効果の `座標` を追加してそれを動かしてください．

1.  [`視野幅(%)`](#視野幅) で `0` を指定すると[直投影](https://ja.wikipedia.org/wiki/%E7%9B%B4%E6%8A%95%E5%BD%B1) ([orthographic projection](https://en.wikipedia.org/wiki/Orthographic_projection)) な描画ができます．この場合 [`カメラ位置`](#カメラ位置) のパラメタは影響しなくなります．

1.  テキストエディタで `GroundShadow_S.anm` を開くと冒頭付近にファイルバージョンが付記されています．

    ```lua
    --
    -- VERSION: v1.00
    --
    ```


## 改版履歴

- **v1.00** (2025-02-01)

  - 初版．


## ライセンス

このプログラムの利用・改変・再頒布等に関しては MIT ライセンスに従うものとします．

---

The MIT License (MIT)

Copyright (C) 2025 sigma-axis

Permission is hereby granted, free of charge, to any person obtaining a copy of this software and associated documentation files (the “Software”), to deal in the Software without restriction, including without limitation the rights to use, copy, modify, merge, publish, distribute, sublicense, and/or sell copies of the Software, and to permit persons to whom the Software is furnished to do so, subject to the following conditions:

The above copyright notice and this permission notice shall be included in all copies or substantial portions of the Software.

THE SOFTWARE IS PROVIDED “AS IS”, WITHOUT WARRANTY OF ANY KIND, EXPRESS OR IMPLIED, INCLUDING BUT NOT LIMITED TO THE WARRANTIES OF MERCHANTABILITY, FITNESS FOR A PARTICULAR PURPOSE AND NONINFRINGEMENT. IN NO EVENT SHALL THE AUTHORS OR COPYRIGHT HOLDERS BE LIABLE FOR ANY CLAIM, DAMAGES OR OTHER LIABILITY, WHETHER IN AN ACTION OF CONTRACT, TORT OR OTHERWISE, ARISING FROM, OUT OF OR IN CONNECTION WITH THE SOFTWARE OR THE USE OR OTHER DEALINGS IN THE SOFTWARE.

https://mit-license.org/


#  連絡・バグ報告

- GitHub: https://github.com/sigma-axis
- Twitter: https://x.com/sigma_axis
- nicovideo: https://www.nicovideo.jp/user/51492481
- Misskey.io: https://misskey.io/@sigma_axis
- Bluesky: https://bsky.app/profile/sigma-axis.bsky.social
