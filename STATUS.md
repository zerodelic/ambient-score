# ambient-score

## 状態

active（公開準備完了・共有フェーズ）。v2.7.2はCodex再々レビューで公開前ブロッカーなし承認済み。GitHub Pagesで公開中。技術タスク・配布物・ドキュメントとも完成。あとは本人が任意のタイミングで共有するのみ。

## 次の一手

まずFacebook友人限定でライブURLを共有し反応を見る(本人作業・任意)。Vector登録は「反応を見る」目的には不向きと判断し優先度を下げた(やるなら二次的な掲載先)。反応で不具合/要望が出たら次の改善へ。

- 公開URL(アプリ): https://zerodelic.github.io/ambient-score/ambient-score.html
- 技術解説: https://zerodelic.github.io/ambient-score/docs/audio-primer.html
- リポジトリ: https://github.com/zerodelic/ambient-score

## メモ

- Codexレビュー完結: 初回(P1×2/P2×2/P3)→2巡目(レポート版数・ファイルログ列固定・静寂スコア)→再々レビューで全解消・公開前ブロッカーなしと承認(対象86a5b4a / 確認時HEAD 23d137b)。Vector登録OKの判断を得た。

- v2.7.2 = 公開前レビュー対応。修正内容と検証:
  - [P1] メモ欄DOM XSS: sendMemo() を escHtml() 経由に。ライブ検証で `<img src=x onerror=alert(1)>` が要素化されず文字列表示(imgElementsCreated=0)を確認。
  - [P1] 声の占有率を「測定時間中の割合」に統一: 静寂・背景音フレームも分母(totalFrameCount)に加算(3087行)、voiceFrameCountはisVoice時のみ(3162行)。if/else排他で二重計上なし。静粛スコアも同分母を自動参照。VAD判定(重み42/25/18/15・しきい値0.52/0.35・活動ゲート6dB)は無変更でv2.7.1挙動維持。
  - [P2] CSVエスケープ強化: 共通関数 csvCell()（式インジェクション対策＋"の""化＋クォート囲み）をログ/リアルタイム/サマリー/履歴CSVで一貫使用。数値列は素の数値のまま。`a,b`/`a"b`/`=1+1`/` @SUM(A1:A2)` をNodeと実ページ両方で検証済み。
  - [P2] 平均音量の説明をエネルギー平均に修正(README・UIツールチップ)。
  - [P3] 画面表示の版数/更新日をREADMEと統一(v2.7.2 / 2026-08-29)。
- v2.7.2 再レビュー(2巡目)追加対応 [commit 86a5b4a]:
  - HTMLレポートのフッターを v2.7.2 に更新＋旧製品名「騒音周波数アナライザー」→「Ambient Score 1」。
  - リアルタイムファイルログの列を保存開始時に固定(_fileCols)。ヘッダーと追記行で同一列セット→保存中のチェック変更でも列ズレなし。実ページでヘッダー列数=行列数を確認。
  - 静寂/背景音の分岐でも updateSilenceScore() を呼び、静かな環境でもライブ静粛スコアが更新される(静寂模擬で --- →更新を確認)。
  - JS構文チェックok、HTML内にv2.7.1・旧製品名の残存なし、zip再生成で反映確認。
  - JS構文チェック通過(inline script 1: ok)、http配信での読み込みコンソールエラーなし。
- 品質ゲート両端クリア: ①ファン誤検知 68.7%→1.7%(偽陽性) ②カフェ実測(2026-08-29, 332行)で人の声89.2%・過検知0件(偽陰性)。※占有率の分母定義変更(v2.7.2)により、今後の占有率は測定時間ベースで従来より低めに出る点に注意。
- リリース物: README現状化・LICENSE(MIT, 2026 zerodelic)・配布zip dist/ambient-score-v2.7.2.zip(html+README+LICENSE同梱、dist/は.gitignore)。
- リポジトリ整理: 文脈メモは CLAUDE.md を単一の正とし AGENTS.md はポインタ化(両方track)。.claude/launch.json は共有(track)、他の.claude個人設定は.gitignoreで除外。docs/review/ にCodexレビュー文書とレビュー運用メモ(REVIEW_PROCESS.md)を追加。
- 公開導線: README冒頭に「▶ オンラインで試す」「📖 技術解説」リンク、リポジトリAbout欄(homepage)にアプリURLを設定。GitHub Pagesはmain push で自動デプロイ。
- 技術解説ドキュメント: docs/audio-primer.html を追加(Web Audio/周波数/フォルマント隆起/倍音/自己相関/2つのゲートを実装コードに紐づけて図解、単体HTML・両テーマ対応)。Claudeアーティファクト版も別途あり。
- 本命=変調ゲート(死蔵のamScore/pitchVariability配線・3〜8Hz変調で"不明確"を"騒音"へ寄せる)は"あると尚良い"改善。リリース障害ではない。
- 運用: コード修正は必ずレビューア事前レビュー→適用→テスターの流れ(本人ルール)。
