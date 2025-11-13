# fusi 標高タイル可視化サイト

このサイトは [hfu/fusi](https://github.com/hfu/fusi) で生産された WebP Terrarium タイルを MapLibre GL JS で可視化します。

## 特徴

- **3D地形表示**: 標高データを立体的に可視化
- **陰影表示**: hillshade レイヤーによる地形の陰影表現
- **Globe Control**: 地球儀モードとメルカトル図法の切り替え
- **地形強調スライダー**: 地形の高低を強調表示
- **インタラクティブな操作**: ピッチ、方位、ズームの自由な操作

## データソース

- **標高タイル**: https://tunnel.optgeo.org/martin/fusi/{z}/{x}/{y}
- **エンコーディング**: Terrarium 形式
- **タイルサイズ**: 512x512 ピクセル
- **ズーム範囲**: 0-16

## 技術スタック

- **地図ライブラリ**: [MapLibre GL JS](https://maplibre.org/) v5.6.0
- **タイル形式**: WebP (Lossless)
- **エンコーディング**: Terrarium (mapterhorn 互換)
- **背景地図**: OpenStreetMap

## データのライセンス

測量法に基づく国土地理院長承認（使用）R 6JHs 133

国土地理院が提供する標高データを使用しています。

## 謝辞

このプロジェクトは [Mapterhorn](https://github.com/mapterhorn/mapterhorn) の手法とビジュアライゼーションに触発されました。Mapterhorn チームの先駆的な取り組みに深く感謝します。

## ローカルでの実行

このサイトは静的 HTML/CSS/JavaScript で構成されており、ウェブサーバーで配信できます：

```bash
# Python の HTTP サーバーを使用
cd docs
python3 -m http.server 8000

# または Node.js の http-server を使用
npx http-server docs -p 8000
```

ブラウザで http://localhost:8000 を開いてください。

## GitHub Pages での公開

このリポジトリの設定で GitHub Pages を有効にし、ソースを `docs` ディレクトリに設定してください：

1. リポジトリの Settings → Pages に移動
2. Source を "Deploy from a branch" に設定
3. Branch を `main` (または `master`) / `docs` に設定
4. Save をクリック

数分後、サイトが `https://<username>.github.io/<repository>/` で公開されます。
