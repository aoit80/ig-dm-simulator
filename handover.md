# Instagram DM風チャットアプリ 引き継ぎメモ

## プロジェクト概要

TikTok動画制作用のInstagram DM風チャットシミュレーター。
台本を読み込んでセリフを1つずつ表示し、スクリーンショットを自動撮影するWebアプリ。

---

## ファイル構成

- `instagram-dm-app.html` — 単一HTMLファイル（vanilla JS + Tailwind CDN + html2canvas）

---

## 使用用途・運用想定

- 複数人が使用する（GitHub Pages でホスティング済み）
- **共有URL：`https://aoit80.github.io/ig-dm-simulator/`**
- **リポジトリ：`https://github.com/aoit80/ig-dm-simulator`**
- スマホ（iPhone Chrome）がメイン操作環境
- PC（Chrome）でも動作確認済み
- `file://`での直接開封は不可（localStorageがセキュリティブロックされる）

---

## 確定済みデザイン仕様

### UIテーマ
- **DM画面**：Instagramライトモード再現
  - 背景：`#ffffff`
  - ヘッダー・ステータスバー：`#ffffff`、区切り線：`#dbdbdb`
  - 相手の吹き出し：`#efefef`（テキスト黒）
  - 自分の吹き出し：`linear-gradient(135deg, #9B30D9, #C13DC7)`（紫グラデーション・テキスト白）
  - タイムスタンプ・既読：`#8E8E93`
- **操作パネル（ドロワー・FAB・モーダル）**：ダークテーマ（`#1a1a1a`系）

### レイアウト（案B：下部ドロワー型）
- DM画面がフル画面表示
- 右下FABボタン（☰）でドロワーを引き出す
- ドロワー内タブ4つ：メッセージ追加 / 台本 / 撮影 / 設定

---

## 実装済み機能

### メッセージ管理
- 相手／自分トグルで送信者切り替えてメッセージ追加
- 長押し（500ms）で編集メニュー表示（iOS対応）
- PC：右クリックで編集メニュー
- 既読クリックでON/OFF切り替え（DOM直接更新）
- メッセージ編集（テキスト変更のみ、DOM直接更新）
- localStorageに自動保存（キー：`ig-dm-v2`）

### 台本読み込み
- 絵文字プレフィックス構文：
  - `🤓` `👩‍💼` → 相手（partner）
  - `👤` `👨‍💻` `👩‍💻` → 自分（me）
- スキップ行：`---` `~~~` `心の声` `LINE：` `PR：` `#` で始まる行
- 読み込み後にlocalStorage保存・全再描画

### スクショ自動化（メイン機能）
- 撮影モード開始：全セリフが吹き出し枠のみ（テキスト非表示）になり、1枚目だけ表示
- 「📸 撮影して次へ」を押すたびに現在の状態を**1080×1920px PNG**で保存
- 次のセリフのテキストが解放され、画面上部に自動スクロール
- 全セリフ完了で自動終了
- 撮影中はFABボタン・DM入力フッターを非表示
- 出力ファイル名：`ig-dm-001.png`、`ig-dm-002.png`…

### 設定
- 相手名のカスタマイズ（localStorage保存、キー：`ig-dm-name`）

---

## 既知の注意点・バグ対策

### iOSでの注意点
- `contextmenu`イベントがiOSでは発火しないため長押し（touchstart 500ms）で代替
- `closeDrawer()`のCSSトランジション（0.3s）完了後に撮影モード処理を実行（`setTimeout(350ms)`）
- `requestAnimationFrame()`でDOMレンダリング完了後にスクロール

### JavaScriptの注意点
- `const`関数は定義前に参照するとReferenceErrorになる（hoistingされない）
  - イベントリスナーは必ず関数定義の後に登録する
- メッセージIDの型が`number`と`string`で混在しうるため`String(id)`に統一して比較
- 既読切り替え・編集後は`renderAll()`を避けDOM直接更新（パフォーマンス・状態保持のため）

### CSSの注意点
- `@import`は`<style>`タグ内に書くとエラーになるため`<link>`タグで読み込む
- Tailwind CDNは本番用途非推奨（警告が出るが動作には影響なし）

---

## 今後やりたいこと（未実装）

- 発言者変更・メッセージ削除・任意位置への挿入
- タイムスタンプの編集UI
- 撮影完了後のプレビューギャラリー

---

## 技術スタック

| 項目 | 内容 |
|---|---|
| HTML/CSS/JS | vanilla（単一ファイル） |
| CSSフレームワーク | Tailwind CSS（CDN） |
| スクリーンショット | html2canvas 1.4.1 |
| フォント | Inter（Google Fonts） |
| ストレージ | localStorage |
| ホスティング予定 | GitHub Pages |
