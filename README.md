# MapLibre Non-BMP Glyph Test

MapLibre GL JS で、BMP（基本多言語面, U+0000〜U+FFFF）の外にある文字（サロゲートペア文字）を
自前のグリフ PBF で正しく描画できるかを確認するための検証用ビューアです。

青森県上北郡東北町の町字データを地図上にラベル表示し、BMP 外の漢字を含む地名
「字𠏹沢」（`𠏹` = U+203F9）が描画されるかを確認します。

## 構成

```
viewer/
├── index.html                  # エントリーポイント
├── main.js                     # 地図の初期化・クリック時のポップアップ表示
├── styles/
│   └── style.json              # MapLibre スタイル定義
├── geojson/
│   └── tohokumachi.geojson     # 東北町の町字ポイントデータ（264 地物）
└── fonts/
    └── Noto Sans JP Regular/   # グリフ PBF（404 ファイル）
        ├── 0-255.pbf
        ├── ...
        └── 131840-132095.pbf   # U+203F9（𠏹）を含む範囲
```

## 仕組み

- **グリフ**: `style.json` の `glyphs` に `fonts/{fontstack}/{range}.pbf` を指定し、
  ローカルの PBF を読み込みます。
  - BMP: U+0000〜U+FFFF の全 256 範囲
  - BMP 外: Noto Sans JP に収録されている文字を含む 148 範囲のみ
- **ローカル描画の無効化**: `main.js` で `localIdeographFontFamily: false` を指定し、
  CJK 文字をブラウザのローカルフォントで描画せず、必ず PBF のグリフを使うようにしています。
- **背景地図**: OpenStreetMap のラスタタイルを透過度 0.3 で表示します。
- **ラベル**: GeoJSON の `name` プロパティを `machi-aza` レイヤー（symbol）で表示します（ズーム 10 以上）。
- **地物の確認**: 地図上のラベルをクリックすると、その地物のプロパティをポップアップで表示します。

初期表示位置は「字𠏹沢」付近（経度 141.217875, 緯度 40.784197, ズーム 15）です。

### 確認ポイント

- 「字𠏹沢」の `𠏹` が豆腐（□）や欠けにならず表示されること
- ブラウザの開発者ツールのネットワークタブで `131840-132095.pbf` が読み込まれていること

## 依存ライブラリ

CDN から読み込むため、インストールは不要です（インターネット接続が必要）。

| ライブラリ | 読み込み元 |
| --- | --- |
| MapLibre GL JS v6 | `https://unpkg.com/maplibre-gl@6/` |

## クレジット

- 地図: © [OpenStreetMap contributors](https://www.openstreetmap.org/copyright)
- 住所データ: [Geolonia 住所データツール v2](https://github.com/geolonia/japanese-addresses-v2) のデータを一部改変
- フォント: [Noto Sans JP](https://fonts.google.com/noto/specimen/Noto+Sans+JP)（SIL Open Font License 1.1）
