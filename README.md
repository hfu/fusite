# fusite

[hfu/fusi](https://github.com/hfu/fusi) で生産された WebP Terrarium 標高タイルを MapLibre GL JS で可視化するビューアサイトです。

## Demo

GitHub Pages で公開中：

- **[index.html](https://hfu.github.io/fusite/)** - 国土地理院最適化ベクトルタイル (bvmap) オーバーレイ版
- **[contour.html](https://hfu.github.io/fusite/contour.html)** - 動的等高線生成デモ
- **[osm.html](https://hfu.github.io/fusite/osm.html)** - OpenStreetMap オーバーレイ版（地形1.5倍誇張）

## 機能

- **3D 地形表示**: 国土地理院の標高データを立体的に可視化
- **ヒルシェード**: 地形の陰影を表現し、立体感を強調
- **シンプルな構成**: 最小限のコードで Terrarium エンコーディングを正確に扱う

## 技術仕様

### データソース

- **標高タイル**: `https://tunnel.optgeo.org/martin/fusi/{z}/{x}/{y}`
- **エンコーディング**: Terrarium 形式
- **タイルサイズ**: 512×512 WebP
- **ズーム範囲**: 0-16

### Terrarium エンコーディング

fusi は標高データを Terrarium 形式でエンコードしています：

```text
標高 (m) = (R * 256 + G + B / 256) - 32768
```

- NODATA 領域は海面高度（0m）として扱われ、RGB(128, 0, 0) に変換されます
- 最小値: -32768m（RGB: 0, 0, 0）
- 最大値: +32767.99609375m（RGB: 255, 255, 255）

### MapLibre GL JS 設定

```javascript
{
  type: 'raster-dem',
  tiles: ['https://tunnel.optgeo.org/martin/fusi/{z}/{x}/{y}'],
  encoding: 'terrarium',
  tileSize: 512,
  minzoom: 0,
  maxzoom: 16
}
```

## 開発

### ローカルでの実行

```bash
# シンプルな HTTP サーバーで起動
cd docs
python3 -m http.server 8000
# ブラウザで http://localhost:8000 を開く
```

### ファイル構成

```text
fusite/
├── README.md          # このファイル
├── LICENSE            # CC0 1.0 Universal
└── docs/              # GitHub Pages 公開ディレクトリ
    ├── index.html     # メインビューア
    └── style.css      # スタイルシート
```

## 謝辞

このプロジェクトは以下のプロジェクトに触発されました：

- [Mapterhorn](https://github.com/mapterhorn/mapterhorn): Terrarium エンコーディングにおける NODATA 処理のアプローチとビジュアライゼーション手法
- [optimal_bvmap](https://github.com/gsi-cyberjapan/optimal_bvmap): 国土地理院最適化ベクトルタイルのレイヤー構成とスタイル定義を参照

## データライセンス

標高データ: 国土地理院  
測量法に基づく国土地理院長承認（使用）R 6JHs 133

## コードライセンス

CC0 1.0 Universal（パブリックドメイン）  
詳細は [LICENSE](LICENSE) ファイルを参照してください。

## 等高線表示 (Contour) の追加について

標準の Terrarium 標高タイルから動的に等高線を生成するには、`maplibre-contour` ライブラリを利用できます。高ズームで細かい間隔を要求すると計算コストが上がるため、ズーム別に閾値 (thresholds) を変えるのが推奨です。

### 推奨初期設定

| Zoom | 閾値 (m) | 目的 |
|------|----------|------|
| ≤12  | 500      | 大域的な地形把握 |
| 13-14| 100, 500 | 中レベルの主要・副等高線 |
| ≥15  | 20, 100, 500 | 詳細表示 |

### 例: `docs/contour.html` での利用コード抜粋

```html
<script src="https://unpkg.com/maplibre-gl@5.7.0/dist/maplibre-gl.js"></script>
<link href="https://unpkg.com/maplibre-gl@5.7.0/dist/maplibre-gl.css" rel="stylesheet" />
<script src="https://unpkg.com/maplibre-contour@0.0.5/dist/index.min.js"></script>
<script>
const demSource = new mlcontour.DemSource({
  url: 'https://tunnel.optgeo.org/martin/fusi/{z}/{x}/{y}',
  encoding: 'terrarium',
  tileSize: 512,
  maxzoom: 16,
  worker: true
});
demSource.setupMaplibre(maplibregl);
const map = new maplibregl.Map({
  container: 'map',
  zoom: 11,
  center: [138.7292, 35.3628],
  style: {
    version: 8,
    sources: {
      hillshadeSource: {
        type: 'raster-dem',
        tiles: [demSource.sharedDemProtocolUrl],
        encoding: 'terrarium',
        tileSize: 512,
        maxzoom: 17
      },
      contourSource: {
        type: 'vector',
        tiles: [
          demSource.contourProtocolUrl({
            thresholds: { 12: [500], 13: [100,500], 15: [20,100,500] },
            elevationKey: 'ele',
            levelKey: 'level',
            contourLayer: 'contours',
            buffer: 1,
            overzoom: 2
          })
        ],
        maxzoom: 17
      }
    },
    layers: [
      {
        id: 'hillshade', type: 'hillshade', source: 'hillshadeSource',
        paint: {
          'hillshade-exaggeration': 0.2,
          'hillshade-highlight-color': 'rgb(255,255,235)',
          'hillshade-shadow-color': 'rgb(110,120,130)'
        }
      },
      {
        id: 'contours', type: 'line', source: 'contourSource', 'source-layer': 'contours',
        paint: {
          'line-color': 'rgb(215,151,60)',
          'line-width': ['match', ['get','level'], 1, 1, 0.5]
        }
      }
    ]
  }
});
</script>
```

### 注意点

- 計算負荷の高い細密閾値 (例: 5m 間隔) は高ズーム限定にする。
- NODATA (海面) 域が 0m として連続している場合、等高線が密集または断線しうるためソース側の補間品質が重要。
- 主要等高線と補助等高線を `level` 属性で線幅差別化することで視認性を維持。

### 今後の拡張案

- ラベル付与: 主要線のみ `symbol` レイヤーで標高値表示。
- ベクター事前生成: 広域利用や多数閾値が必要なら pipeline 化し PMTiles に。
- シェーダ拡張: 光沢向上のため multi-directional hillshade をカスタムレイヤーで実装。


