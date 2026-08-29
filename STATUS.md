# ambient-score

## 状態

active（公開直前）。Codex公開前レビュー(P1×2/P2×2/P3)の指摘を全対応し v2.7.2 化。品質ゲートは両端クリア済み。あとは配布物の最終化とVector登録(本人作業)のみ。

## 次の一手

Codex側の再レビューに v2.7.2 の差分（コミット群）を回す。問題なければ dist/ambient-score-v2.7.2.zip をVectorに登録・送信する(本人作業)。

## メモ

- v2.7.2 = 公開前レビュー対応。修正内容と検証:
  - [P1] メモ欄DOM XSS: sendMemo() を escHtml() 経由に。ライブ検証で `<img src=x onerror=alert(1)>` が要素化されず文字列表示(imgElementsCreated=0)を確認。
  - [P1] 声の占有率を「測定時間中の割合」に統一: 静寂・背景音フレームも分母(totalFrameCount)に加算(3087行)、voiceFrameCountはisVoice時のみ(3162行)。if/else排他で二重計上なし。静粛スコアも同分母を自動参照。VAD判定(重み42/25/18/15・しきい値0.52/0.35・活動ゲート6dB)は無変更でv2.7.1挙動維持。
  - [P2] CSVエスケープ強化: 共通関数 csvCell()（式インジェクション対策＋"の""化＋クォート囲み）をログ/リアルタイム/サマリー/履歴CSVで一貫使用。数値列は素の数値のまま。`a,b`/`a"b`/`=1+1`/` @SUM(A1:A2)` をNodeと実ページ両方で検証済み。
  - [P2] 平均音量の説明をエネルギー平均に修正(README・UIツールチップ)。
  - [P3] 画面表示の版数/更新日をREADMEと統一(v2.7.2 / 2026-08-29)。
  - JS構文チェック通過(inline script 1: ok)、http配信での読み込みコンソールエラーなし。
- 品質ゲート両端クリア: ①ファン誤検知 68.7%→1.7%(偽陽性) ②カフェ実測(2026-08-29, 332行)で人の声89.2%・過検知0件(偽陰性)。※占有率の分母定義変更(v2.7.2)により、今後の占有率は測定時間ベースで従来より低めに出る点に注意。
- リリース物: README現状化・LICENSE(MIT, 2026 zerodelic)・配布zip dist/ambient-score-v2.7.2.zip(html+README+LICENSE同梱、dist/は.gitignore)。
- リポジトリ整理: 文脈メモは CLAUDE.md を単一の正とし AGENTS.md はポインタ化(両方track)。.claude/launch.json は共有(track)、他の.claude個人設定は.gitignoreで除外。docs/review/ にCodexレビュー文書を追加。
- 本命=変調ゲート(死蔵のamScore/pitchVariability配線・3〜8Hz変調で"不明確"を"騒音"へ寄せる)は"あると尚良い"改善。リリース障害ではない。
- 運用: コード修正は必ずレビューア事前レビュー→適用→テスターの流れ(本人ルール)。
