# Pixel String Viewer

PixelArtAI experiments用の最小ローカルWebツールです。

## 起動

依存関係はありません。 `index.html` をブラウザで直接開くだけで動作します。

## 機能

- 1行目のフォーマット指定子に従ってPixel文字列を解析
- Pixel文字列を画像としてプレビュー
- テキストでパレット番号とRGB/RGBAを編集
- 元PixelサイズのPNGとして保存

## フォーマット

### matrix-char

1文字を1Pixelとして扱います。

```text
@format matrix-char
0000
0110
0110
0000
```

### matrix-csv

カンマ区切りの値を1Pixelとして扱います。10以上のパレット番号などにも利用できます。

```text
@format matrix-csv
0,0,0,0
0,10,10,0
0,10,10,0
0,0,0,0
```

## パレット

```text
0 = 0,0,0,0
1 = 0,0,0,255
2 = 255,0,0,255
3 = 255,255,255,255
```

RGBだけを指定した場合は alpha=255 として扱います。
