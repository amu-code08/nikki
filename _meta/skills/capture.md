---
name: capture
description: "Append the user's message verbatim to today's daily note under daily/untagged/, creating it from the template when it does not exist. Use when the user invokes $capture, or asks to jot something down into today's daily — typically from a phone."
---

`$capture <本文>` で送られた本文を、**その日の daily に逐語のまま追記する。** 携帯から Claude Code を触っているときに、思いついたことをそのまま vault へ落とすための経路。

エージェントは**書き写す配管であって、書き手ではない。** ここで AI の文章が daily に入った瞬間、「daily に書いてあることは人間が書いたことだ」という保証(INV-05)が消え、vault のどの行も信用できなくなる。

## 手順

1. 今日の日付 `YYYY-MM-DD`(ローカル時刻)を決める。人間が日付を明示した場合だけそれに従う。
2. `daily/untagged/YYYY-MM-DD.md` があればそれが書き込み先。手順4へ。
3. 無い場合:
   - `daily/tagged/YYYY-MM-DD.md` がある → `daily/untagged/` へ戻し、frontmatter の `tagged:` だけを削除する。本文が増えた以上そのノートは処理済みではない。`tags:` は消さない。戻したことを報告に含める。`slices/YYYY-MM-DD--*.md` が既にある場合、次の `$tag-dailies` は上書きを避けてこのノートを飛ばすので、それも併せて伝える。
   - どちらにも無い → `_templates/daily.md` をそのままコピーして `daily/untagged/YYYY-MM-DD.md` を作る。テンプレの中身は変えない。
4. 本文を `## todo` 見出しの**直前**に挿入する。ファイル末尾に足さない(`## todo` 配下に散文が入ると `$tag-dailies` の空判定と断片化が壊れる)。
   - 既に散文がある場合は、その最後の行との間に空行を1つ置いてから挿入する。
   - `## todo` 見出しが無いノートでは、frontmatter より後ろの本文末尾に追記する。
5. 書き込み後にノートを再読し、YAML/frontmatter が壊れていないことを確認する。
6. 報告する。ファイルパス、新規作成か追記か、追記した行数。**追記した本文を要約して返さない。**

## 逐語であること

- 送られた本文を**一字も変えずに**書く。要約・言い換え・整形・翻訳・誤字修正をしない。
- 時刻・見出し・箇条書き記号・引用符を**足さない。** 人間が書いていない文字を1つも入れない。
- 複数行が送られた場合は改行の位置もそのまま保つ。
- `IDEA:` `Q:` の行頭マーカーは人間が書いたとおりに残す。付けるのも外すのもエージェントの仕事ではない。
- 本文が空の `$capture` では何も書かず、その旨だけ返す。空のノートを作らない。

## 制約

- **既存の行を編集・並べ替え・削除しない。** この skill が daily に対してできるのは追記だけである。チェックボックスの状態にも触らない。
- タグを付けない。`tagged:` を書かない。タグ付けは `$tag-dailies` の仕事であり、capture した直後は未処理であることが正しい。
- `slices/` を作らない・触らない。
- **1回の呼び出しで1回だけ追記する。** 呼ばれていない発言、会話の他の部分、自分の返答を daily に入れない。`$capture` と書かれていない発言は capture ではない(INV-06 の pull 型を保つため)。
- 追記した内容にインサイダー情報(MNPI)や勤務先の機密にあたりうる記述があれば、**書いたうえで**チャットで報告する。判定基準は `AGENTS.md` の「Sensitive content」。書き換えない、書くのを拒まない(FAIL-35)。携帯から送ったものがそのままリモートへ向かうので、ここが最後の関所になる。
- `daily/` の下に `untagged/` `tagged/` 以外のフォルダを作らない。日付以外の名前のノートを置かない(FAIL-01)。
