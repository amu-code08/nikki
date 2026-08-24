---
name: resurface-ideas
description: "List explicit IDEA: and Q: entries across daily notes without editing files. Use when the user wants to resurface small ideas, unresolved questions, or forgotten marked notes."
---

daily の行頭マーカー行(小さいアイデア・未解決の問い)を横断回収し、チャットに一覧する(pull型)。「書いたのに忘れて消えていく着想」の拾い直し用。

## マーカー規約

- `IDEA:` — あとで拾い直したい着想
- `Q:` — 未解決の問い
- 行頭に書く。付けるかは常に任意。
- マーカーはこの2つのみ。増やす場合は人間が本 skill と仕様書を更新する。

1. 対象範囲を決める(デフォルト:全 daily。または人間が指定した期間)。
2. `rg -n '^IDEA:|^Q:' daily` で該当行だけを集める(再帰検索なので `untagged/` `tagged/` の両方が入る)。本文を広く読まない。
3. 新しい順、または近い話題ごとにまとめて提示する。各項目に出典 `[[YYYY-MM-DD]]` を添える。パスは書かない(ノートが `daily/untagged/` から `daily/tagged/` へ移ってもリンクが切れないため)。
4. デフォルトでは消化済み判定をしない。人間が「permanent 化済み・消化済みを除いて」と指定した時のみ、該当ノートを読んで「済みかも」と注記する。除外・削除はしない。
5. チャットで返す。ファイルは作らない。permanent 化は人間が行う。

- 走査対象は `daily/` だけ(`untagged/` `tagged/` の両方)。`slices/` は daily の行をそのまま持つ派生層なので、含めると同じ着想を2回拾う。
- 派生物を vault に書き込まず、本文を編集しない。
- マーカーの無い行を推測でアイデアとして拾わない。規約導入前の過去ノートを扱う依頼は、この skill ではなく通常の読解で答える(`AGENTS.md` の Answering from notes)。
