---
name: extract
description: "Answer a user's question by reading daily and permanent notes selected by tags and time range. Use for retrieval, reflection, or synthesis from this Obsidian vault when a chat answer—not a new note—is wanted."
---

人間の問いに対し、tags で絞り込んだ daily note 群を読んで答える。

1. 問いから対象タグと期間を特定する。曖昧なら人間に1つだけ確認する。期間指定がなく対象ノートが多い場合(目安30枚超)は、読む前に期間で絞るよう確認する。
2. 対象期間内で frontmatter に `tagged:` が無い daily は、タグ絞り込みに関係なく全てスキャン対象に加える。タグ付けの遅延で抽出が不完全にならないためである。
3. 該当する daily を読む。タグはノート単位なので、問いに関係する段落だけを使う。
4. 答えはチャットで返す。新ファイルを作らない。permanent note 化が妥当な核は提案に留め、作成は人間が行う。

## 対象範囲

- 対象は `daily/` と `permanent/`。`references/` は人間が明示的に含める場合のみ読む。

## タスク行の扱い

- `- [ ]` / `- [x]` は思考ではなくタスク。記憶・思考についての問いでは原則として無視し、prose の記述だけで答える。
- 問いがタスクや進捗についてなら `$open-todos` を使う(またはその観点で答える)。

- 派生物を vault に書き込まない。
- 励まし・評価で水増しせず、観察と整理を返す。
- 自己の思考の癖を扱う場合も、決めつけず「日記にこう繰り返し現れる」という観測ベースで述べる。
