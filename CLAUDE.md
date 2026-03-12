# Paper Survey Bot

このリポジトリは医学論文の週次自動サーベイシステムです。

## 動作ルール
- PubMed E-utilities API（https://eutils.ncbi.nlm.nih.gov/entrez/eutils/）を使って論文を検索する
- 検索は `esearch.fcgi` と `efetch.fcgi` を組み合わせる
- 結果は `reports/YYYY-MM-DD.md` に Markdown 形式で保存する
- 既存レポートと重複する論文は除外する
- 出力は全て日本語（論文タイトル・ジャーナル名は英語原文のまま）
