---
name: tag-dailies
description: "Apply approved taxonomy tags and a tagged: marker to Markdown notes in daily/untagged/, then move reviewed notes to daily/tagged/. Use when the user asks to tag daily notes, process untagged dailies, or re-tag a specified time range in this vault."
---

`daily/untagged/` の Markdown ノートを読み、`_meta/taxonomy.md` の許可タグ(`- [x]` 行)から該当するものを frontmatter の `tags:` に付与する。レビュー済みのノートには frontmatter の `tagged:` で印を付け、検証後に同じファイル名のまま `daily/tagged/` へ移動する。配置先ディレクトリを未処理・処理済みの一次情報とする。

1. `_meta/taxonomy.md` を読む。チェック済み `- [x]` の行だけが許可タグであり、未チェック `- [ ]` は未承認候補である。
2. デフォルトでは `daily/untagged/` 内の Markdown ノートだけを対象にする。`tagged:` 行の有無で対象を判定しない。人間が期間指定または再タグ付けを指定した場合は、指定範囲の `daily/tagged/` 内のノートを対象にし、移動はしない。別のパスを明示された場合は、その指定を優先する。
3. 対象ノートだけを読み、実際に書かれている内容に該当する許可タグを選択的に付ける。既存の `tags:` は残す。
4. 許可タグでは適切に表現できず、新しいタグを付ける価値がある場合は、処理中にユーザーへ候補を1件ずつ提示して承認・却下を尋ねる。候補タグ名、短い説明、該当ノートで必要な理由を示し、回答を得るまでそのノートの処理を完了しない。
5. ユーザーが承認した場合だけ、taxonomy に許可済みの `- [x]` タグとして追加し、そのノートに付ける。却下された場合はタグも taxonomy の変更も行わず、既存の許可タグだけで処理を続ける。未チェック候補を見つけた場合も、使う前に同じ確認を行う。
6. タグの有無に関わらず、レビューした各ノートの frontmatter に `tagged: <実行日 YYYY-MM-DD>` を加える。frontmatter がないノートには、本文を変えずにファイル先頭へ inline 形式の frontmatter を作る。
7. 変更後のノートを再読して YAML/frontmatter を確認する。問題がなければ、`daily/untagged/<ファイル名>` から `daily/tagged/<ファイル名>` へ移動する。移動先に同名ファイルがある場合は上書きせず、ユーザーに確認する。

## 制約

- taxonomy の許可タグを、この skill の判断で削除・改名・チェック付けしない。ユーザーが処理中に明示承認した新タグだけを許可済みに追加できる。
- 本文、チェック状態、タスク本文を編集しない。frontmatter の `tags` と `tagged:` のみ追記する。
- 状態タグ(todo / done など)を作らない。タスク本文が話題を含む場合のみ、その話題の許可タグをノートに付けてよい。
- 改名・統合の既存ノートへの反映は、明示依頼時だけ frontmatter に対する機械的な置換で行う。新タグの過去ノートへの遡及も明示依頼時だけ行う。
- frontmatter は inline 形式(`tags: [type/daily, finance/equity-research]`)を正とする。Obsidian が block 形式に書き換えたものは直さない。
- 判断に迷うタグは付けない。本文インラインの `#hashtag` は使わない。
- デフォルト処理では `daily/tagged/` を探索しない。`daily/untagged/` から移動する前にタグ付けと frontmatter の検証を完了する。
