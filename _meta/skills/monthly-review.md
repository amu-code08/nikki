---
name: monthly-review
description: "Compare one month's daily notes against the note tagged type/monthly-goals and report progress, evidence, and untracked effort in chat. Use when the user asks for a monthly review, how a month went against their goals, or what they actually spent a month on."
---

指定された月について、`type/monthly-goals` タグの付いたノート(現在 `permanent/motivation/Monthly Goals.md`)の目標と、その月の daily に実際に書かれていたことを突き合わせ、**チャットで**返す。読み取り専用であり、ファイルを一切作らない・変更しない。

目的は進捗の採点ではなく、「立てた目標」と「実際に時間を使ったこと」のズレを見えるようにすること。

## 手順

1. 対象月を決める。引数があればその月(`YYYY-MM`)。無ければ当月。
2. `type/monthly-goals` のノートを読み、対象月の `## YYYY/MM` 見出し配下を取る。**評価対象は `- [ ]` と `- [x]` の行だけ**。素の `- ` は方向性であり、達成判定をしない。対象月の見出しが無い、または中身が空なら、その旨を伝えて手順4へ進む(タグ分布だけでも返す価値がある)。`type/north-star` のノートは長期の方向性であり、月次の達成判定には使わない。
3. 対象月の daily を集める。`daily/` にあるものは `tagged:` の有無に関わらず**すべて**見る。タグ付けが済んでいない日を落とさないため(完全性保証)。`_trash/` と `slices/` は見ない。`slices/` は daily の本文をそのまま持つ派生層なので、含めるとタグも記述も二重に数えることになる。
4. 各 daily の frontmatter からタグを数え、分布を出す。ここでは本文を読まない。未タグの枚数は `rg --files-without-match '^tagged:' daily` のようなファイル名だけを返す検索で数える。
5. 未達(`- [ ]`)の各項目について、本文から関連する記述を探す。該当した日付と、短い引用または要約を添える。**達成したと断定しない。**「8/27 に『あとは結論だけ』とある」のように観測で返し、判定は人間に委ねる。
6. タグ分布の上位のうち、対象月の目標に対応しないものを「目標に無かったが時間を使っていたこと」として挙げる。**ここがこの skill の主眼**であり、翌月の目標を書く材料になる。
7. 未達件数を伝え、繰り越し・削除・書き直しは Monthly Goals に直接どうぞ、と添えて終わる。

## 出力の形

```
2026/08 の Monthly Goals

- [ ] Finish writing the researh paper entirely
      言及 4 日(8/3, 8/8, 8/19, 8/27)。8/27「あとは結論だけ」
- [ ] FInish CFA book1
      言及なし

書いていたが目標に無かったもの
- system/pkm 9 日 ← 最多
- finance/equity-research 5 日

対象 21 枚(うち未タグ 2)。未達 2 件。
繰り越すか、落とすか、書き直すかは Monthly Goals に直接どうぞ。
```

対象枚数と、そのうち `tagged:` を持たない枚数を必ず示す。未タグの daily を含めたことが読み手に見えるようにするため。

## 制約

- **ファイルを作らない・書き換えない。** 結果は必ずチャットで返す(INV-02)。残す価値があると人間が判断した場合のみ、`references/` への保存を提案する。提案までで、作成はしない。
- **目標ノート2枚(`type/monthly-goals` / `type/north-star`)の目標文とチェック状態を変えない**(FAIL-29)。読むだけ。
- North Star の記述を達成判定の対象にしない。長期の方向性であって、完了するものではない(FAIL-30)。
- 達成/未達を断定しない。根拠となる日付と記述を示し、判定は人間に任せる。誤った「達成」は見逃しよりも高くつく。
- `_trash/` `references/` `_meta/` `slices/` を対象に含めない。
- 対象月の daily が0枚なら、その旨だけを返す。タグ分布や推測を作らない。
- 励ましや評価で水増ししない。観察と整理だけを返す。書けていない項目を責めない。
- 目標の文言を直さない(誤字を含めて人間のもの)。
