# Paper Survey Bot

このリポジトリは小児科全般の週次自動論文サーベイシステムです。

## 動作ルール
- PubMed E-utilities API（https://eutils.ncbi.nlm.nih.gov/entrez/eutils/）を使って論文を検索する
- 検索は `esearch.fcgi` と `efetch.fcgi` を組み合わせる
- OA情報の取得には Europe PMC API を使用する
- 結果は `reports/YYYY-MM-DD/` フォルダに保存する（report.md, survey.json, OA論文PDF）
- 既存レポートと重複する論文（PMIDまたはDOIで判定）は除外する
- `state/survey-state.json` で既報PMID・ドメイン別検索日を管理し、3種の重複排除を行う
- 出力は全て日本語（論文タイトル・ジャーナル名は英語原文のまま）

## ディレクトリ構成
- `prompts/` — サーベイプロンプト（検索手順・出力フォーマット）
- `config/` — ドメイン設定・ジャーナルリスト
- `state/` — サーベイ状態管理（既報PMID、ドメイン別検索日）
- `reports/YYYY-MM-DD/` — 週次レポート出力先（report.md, survey.json, *.pdf）
- `docs/` — ドメイン定義仕様書

## 設定ファイル
- `config/specialty.json` — 診療科、ドメイン定義（A〜K）、検索クエリ、選定基準
- `config/journals.json` — 対象ジャーナルリスト（Tier 1〜3）

## 状態管理
- `state/survey-state.json` — 既報PMID辞書、ドメイン別最終検索日、統計情報
  - Gitで管理。状態破損時は `git revert` で復旧可能

## 重複排除の3パターン
1. **クエリ間重複**: 同一論文が複数ドメインにマッチ → PMID単位でセッション内重複排除
2. **週間重複**: PubMedインデックス日ラグ → stateファイルの既報PMIDで排除
3. **ニッチ領域の枯渇**: 新着が少ない領域 → ドメイン別last_searchedで日付フィルタ厳密化

## ドメイン構成（11領域・34サブドメイン）
| ドメイン | 領域 | サブドメイン数 |
|----------|------|---------------|
| A | 小児腎臓病 | 4 |
| B | 小児血液・腫瘍 | 5 |
| C | 小児感染症 | 4 |
| D | 小児救急・集中治療 | 2 |
| E | 新生児科・周産期 | 2 |
| F | 小児アレルギー・呼吸器・免疫 | 3 |
| G | 小児神経・発達 | 3 |
| H | 小児内分泌・代謝 | 3 |
| I | 小児消化器 | 2 |
| J | 小児循環器 | 2 |
| K | 小児特有の横断的テーマ | 4 |

詳細: `docs/domain-specification.md`

## 診療科・ドメインの変更方法
1. `config/specialty.json` の `specialty`, `role_description`, `domains` を変更
2. `config/journals.json` の Tier 2/3 ジャーナルを対象診療科に変更
3. `state/survey-state.json` の `domains` を新ドメインに合わせて初期化
4. プロンプト（`prompts/survey-prompt.md`）の変更は不要
