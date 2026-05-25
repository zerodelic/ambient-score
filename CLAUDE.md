# Ambient Score プロジェクト — Claude向け文脈メモ

## プロジェクト概要

ブラウザだけで動作するリアルタイム環境音分析・スコアリングツール群。
マイクから取得した音をFFT解析し、オフィス・在宅・カフェなどの音環境を可視化・評価する。

---

## アプリ一覧

| アプリID | 名前 | 状態 | 説明 |
|---------|------|------|------|
| **AS1** | Ambient Score 1 | ✅ 開発中 (v2.3.9) | 単一HTMLファイル、FFT+ヒューリスティックVAD |
| **AA1** | Ambient AI 1 | 🔍 調査・設計中 | TensorFlow.js + YAMNet による高精度音識別 |
| **未定** | ログ解析アプリ | 💭 構想中 | AS1/AA1のCSVログを可視化・分析 |

---

## AS1 (Ambient Score 1) — 現状

- **ファイル**: `ambient-score.html`（単一HTMLファイル、依存ライブラリなし）
- **GitHub Pages**: `https://zerodelic.github.io/ambient-score/ambient-score.html`
- **最新バージョン**: v2.3.9
- **開発方針**: 1ファイルで動作・オフライン対応・PCとモバイル(iOS Chrome)両対応

### 主要な技術的決定事項

- **VAD**: フォルマント隆起42% + ピッチ25% + 倍音18% + 声帯域比率15% の重み付き合算
- **BGMと声判定の分離**: v2.3.8でBGM検出と声判定閾値の連動を廃止（声検出精度優先）
- **モバイル対応**: max-width:720px でグラフタブ切り替えUI（スペクトラム/時系列/JoyPlot）
- **iOS getUserMedia**: 3段階フォールバック（sampleRate付き → sampleRateなし → audio:true）
- **AudioContext iOS対応**: getUserMedia後にresume()を明示呼び出し
- **デバイス間の測定値差**: iOS ChromeでもNS/AGC/EC OFFを確認済み。差はマイクハードウェアの感度差。キャリブレーション機能は要検討。

### ファイル同期

作業ファイル（iCloud）→ リポジトリへのコピーが必要：
```
cp "/Users/hiroshi.onozawa/Library/Mobile Documents/com~apple~CloudDocs/400_Claude/010_code/001_sample/ambient-score.html" \
   "/Users/hiroshi.onozawa/Projects/ambient-score/ambient-score.html"
```

---

## AA1 (Ambient AI 1) — 構想

### コンセプト

AS1の改良ではなく、AIを活用した**別アプリ**として開発する。
単一HTMLファイルのメリットは捨てて精度向上を優先。

### 技術選定（暫定）

- **モデル**: YAMNet（Google、TensorFlow.js版）
- **動作環境**: ブラウザ（クライアントサイド推論、サーバー不要）
- **主要活用クラス**:
  - 声・会話: Speech(0), Conversation(2), Whispering(12)
  - 静寂: Silence(494)
  - 空調・機械: Mechanical fan(406), Air conditioning(407)
  - 環境ノイズ: Noise(507), Environmental noise(508)
  - 場所コンテキスト: Inside small room(500), Inside large room(501)

### YAMNet 仕様メモ

| 項目 | 値 |
|------|----|
| アーキテクチャ | MobileNet V1 |
| パラメータ数 | 370万（float32で約14MB） |
| 入力 | 16kHz モノラル、0.96秒フレーム |
| 出力 | 521クラスの確率スコア |
| 精度（全クラス平均mAP） | 0.306 |
| TF.js版 | TF Hub で公開済み |
| レイテンシ | 最小約0.96秒（AS1の300msより遅い） |
| 注意 | 入力は16kHz必要 → AudioContext(48kHz)からリサンプリング要 |

### プロトタイプ実装済み（aa1-prototype.html）

- TF.js + YAMNet（CDN・TF Hub）でブラウザ動作を確認
- 推論時間：**約40ms以下**（1秒サイクルの4%負荷）
- Silence / Speech / Music の識別を概ね確認
- 可視化：メルスペクトログラム・主要クラス時系列・パフォーマンスグラフ・サマリーレポート
- FFTスペクトラム・JoyPlotの追加は**見送り**（メルスペクトログラムで代替可能）

### 次のステップ

1. iPhoneでの動作確認（モデルロード時間・推論速度）
2. 実オフィス環境での識別精度検証（AS1との比較）
3. 検証結果を踏まえてAA1本格設計に進む

---

## 議事メモ

詳細な議論の記録は `docs/discussion/` フォルダを参照。
