---
name: commit
description: "Review the working tree, update AGENTS.md only if the diff made it stale, then stage, commit, and push to origin. Use when the user asks to commit, save changes to git, push the vault, or do the weekly vault commit."
---

作業ツリーの差分を読み、**その差分が `AGENTS.md` の記述を古くした場合に限り** `AGENTS.md` を直し、同じコミットに含めてからコミットし、`origin` へ push する。

構成が変わったのに運用ルールが古いまま残る状態を防ぐのが目的。**毎回 `AGENTS.md` を更新する skill ではない。**

## 手順

1. `git status --short` と `git diff HEAD` を読む。差分がなければ、`git status -sb` で未 push のコミットが無いかを見る。あればその一覧を示して push だけ行うか尋ね、承認されたら手順8へ飛ぶ。それも無ければその旨を報告して終了する。
2. **無関係な変更の分離**:今回の意図と無関係な変更(人間が Obsidian で行ったノートの追加・削除・移動など)が混ざっていないか確認する。混ざっていれば一覧で示し、同じコミットに含めるか分けるかを人間に決めさせる。勝手に `git add -A` しない。`daily/untagged/` から `daily/tagged/` への移動は `$tag-dailies` の通常の出力であり、無関係な変更ではない。
3. **機密の確認**:コミット対象の差分に、インサイダー情報(MNPI)や勤務先の機密にあたりうる記述が新しく含まれていないか見る。判定基準は `AGENTS.md` の「Sensitive content」。該当しそうなものがあれば、ファイル・行・なぜ該当しそうかを手順6の確認に併せて提示する。**見つけてもコミットや push を止めず、本文も書き換えない**(FAIL-35)。すでに人間が見て残すと決めたものを蒸し返さない。

   **この skill は同じ実行の中で push まで行う。** 一度リモートへ出たものは取り消せないので、ここが最後の関所であり、手順6の承認はその関所を通す判断でもある。だから懸念は承認を求める**前に**必ず提示する。
4. 下の「AGENTS.md 更新要否」に照らして判定する。判定の根拠は必ず**実際の差分**とし、推測で判断しない。
5. 更新が必要な場合のみ `AGENTS.md` を直す。差分に現れた事実だけを反映する。**新しい不変則(INV)や失敗モード(FAIL)を発明しない。** 節の追加・削除など構造的な変更が要るときは、その理由を添えて先に人間へ提案する。
6. **確認は1回だけ**:コミット対象ファイル、コミットメッセージ案、`AGENTS.md` の変更点(あれば)、手順3で見つかった懸念(あれば)、および**push 先(`origin` と対象ブランチ)**をまとめて提示し、承認を得る。この1回の承認がコミットと push の両方を覆う。
7. 承認後に `git add <対象>` → `git commit` を実行する。結果のコミットハッシュと件名を報告する。
8. `git push origin <現在のブランチ>` を実行する。成功したらリモートのブランチ名と、押し上げたコミット数を報告する。
   - **拒否された場合(non-fast-forward など)は、そこで止めて人間に報告する。** `--force` も `--force-with-lease` も使わない。fetch・merge・rebase のどれを選ぶかは人間の判断であり、この skill は決めない。コミットは既に手元にあるので、失われるものは何もない。
   - リモートが未設定、または現在のブランチに upstream が無い場合も、勝手に作らずに報告して終了する。

## AGENTS.md 更新要否

| 差分の種類 | 更新 | 対象節 |
|---|---|---|
| トップレベルディレクトリの追加・削除・リネーム | **要** | File routing / Write permissions |
| `_meta/skills/*.md` の追加・削除・リネーム | **要** | Vault skills(スキル表と発話ルーティング表) |
| 発見スタブの追加・削除(新ツールの導入・撤去) | **要** | File routing / Portability |
| 不変則・書き込み権限・失敗モードを変える決定 | **要** | Invariants / Write permissions / Failure modes |
| 既存 skill の**手順**だけの変更(役割は不変) | 不要 | Vault skills は役割1行しか持たないため |
| `_meta/taxonomy.md` のタグ増減 | **不要** | `AGENTS.md` はタグ一覧を持たない(正本は taxonomy.md) |
| 目標ノート2枚(`type/north-star` / `type/monthly-goals`)の**移動・リネーム・削除** | **要** | File routing。AGENTS.md がパスをヒントとして載せているため |
| `daily/` 配下のフォルダ構成の変更(`untagged/` `tagged/` の増減・改名) | **要** | File routing / Write permissions / Failure modes |
| `daily/` `inbox/` `permanent/` `references/` の中身 | **不要** | ノートの増減も `untagged/`→`tagged/` の移動も構成の変更ではない(上の2枚の中身の変更も不要) |
| `.obsidian/` `.gitignore` | 不要 | — |

判断に迷う差分は「不要」に倒し、迷った理由を人間に報告する。`AGENTS.md` の肥大化は乖離と同じくらい害がある。毎セッション読まれるファイルであることを忘れない。

変更履歴は `AGENTS.md` に書かない。履歴は git log が持つ。

## コミットメッセージ規約

既存履歴に合わせる。

- 件名は**英語・命令形・先頭大文字・末尾ピリオドなし**(例:`Drop stale tagged: markers from promoted permanent notes`)。
- 件名の後に空行、本文は**何をしたかではなく、なぜそうしたか**を書く。1行 ~78 文字で折る。
- 複数の独立した変更を含む場合は本文を `- ` の箇条書きにする。
- Conventional Commits の接頭辞(`feat:` 等)・絵文字・スコープ記法は使わない。
- エージェントが作成したコミットは、末尾に実行モデル名の `Co-Authored-By:` トレーラを付ける。

## 制約

- **push してよいのは、手順6の承認を得た現在のブランチだけである。** `--force` / `--force-with-lease` / `--all` / タグの push を使わない。他のブランチを push しない。既存のリモートブランチを消す・書き換える操作をしない。
- `git reset --hard` / `git checkout --` / `git clean` / 強制系オプションを使わない。作業ツリーの破棄が必要に見える場合は、実行せず人間に報告する。
- `--amend` を使わない。既存コミットを書き換えず、新しいコミットを作る。
- `--no-verify` などフックの迂回をしない。
- ノートの本文を編集しない。この skill が書いてよいのは `AGENTS.md` だけで、それも上表で「要」と判定された時のみ。
- コミット対象の選定を人間の承認なしに広げない(手順2)。
