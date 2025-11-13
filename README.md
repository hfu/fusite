# fusite

[hfu/fusi](https://github.com/hfu/fusi) で生産された WebP Terrarium タイルを MapLibre GL JS で可視化するサイトです。

## デモ

GitHub Pages で公開予定: `https://hfu.github.io/fusite/`

## 機能

- **3D地形表示**: 標高データを立体的に可視化
- **陰影表示**: hillshade レイヤーによる地形の陰影表現  
- **Globe Control**: 地球儀モードとメルカトル図法の切り替え
- **地形強調スライダー**: 地形の高低を調整可能

## データソース

- 標高タイル: https://tunnel.optgeo.org/martin/fusi/{z}/{x}/{y}
- エンコーディング: Terrarium 形式 (512x512 WebP)
- ズーム範囲: 0-16

## 謝辞

このプロジェクトは [Mapterhorn](https://github.com/mapterhorn/mapterhorn) の手法とビジュアライゼーションに触発されました。

## ライセンス

データ: 測量法に基づく国土地理院長承認（使用）R 6JHs 133

コード: CC0 1.0 Universal (LICENSE ファイルを参照)
