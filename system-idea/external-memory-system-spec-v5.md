# Obsidian 外部記憶システム 仕様書 v5.3

## 0. 変更履歴

**v5.3(2026-08-10)**:ディレクトリ構造の整理。
- **新規ノートの入口を分離**:Obsidian の `newFileFolderPath` が `daily/untagged` を指していたため、
  デイリー以外の新規ノートまで daily パイプラインに流入し、`daily/tagged/` に非日付ノートが
  混入していた。受け皿を `inbox/` に分離し、`daily/` は日付ノート専用にした。
- ラッパー階層 `Documents/agent-vault/agent-vault/` を廃止し、vault を1段上へ平坦化。
- `daily/tagged/` にあった WebX 報告 3 枚(人間執筆)を `permanent/` へ移動。
- 本文のない空デイリー 2 枚を §8 に従い削除。
- §2 の構成図を実体(tagged / untagged 分割)に同期。§5 に delegate を追記。git init を実行。

**v5.2(2026-07-20)**:Codex 移行。
- skill の正本を Codex が自動発見する `.agents/skills/*/SKILL.md` に移した。旧 `skills/*.md` の重複原本と、廃止済み push 型の v4 仕様書は削除。
- 運用は「daily を書く → 必要時に tag-dailies / extract 等の pull 型 skill を呼ぶ」のみ。scheduled task と morning brief は使わない。

**v5.1(2026-07-03)**:実運用2週間のレビューを反映。
- **正の所在を宣言**:skill の挙動は `.agents/skills/*/SKILL.md`、タグ語彙は `_meta/taxonomy.md` が常に正。本仕様書に skill 本文を二重掲載しない(v5 で仕様と実体が乖離した反省)。
- taxonomy をチェックボックス承認形式に変更(候補→許可のコピペ移動を廃止)。
- 抽出の完全性保証:未タグの daily は常にスキャン対象(タグ付けラグによる取りこぼし防止)。
- `references/` 新設:残す価値のある AI 生成物の隔離置き場(permanent の純化)。
- 保全(バックアップ・版管理)の節を追加。
- テンプレート由来の残骸ノート対策。

**v5**:v1〜v4 の push 型自己改善ループ(毎朝 brief 生成+ラベル学習)を全廃し、
「思ったことを書き留めて忘れないための、信頼できる外部記憶」に目的を再定義。
機能は3つ:**選択的タグ付け / オンデマンド抽出 / 統制語彙(taxonomy)**。
push 型をやめ **pull 型(呼ばれた時だけ働く)** にする。

---

## 1. 設計思想

```
L1 資料層(準不可侵)  daily note / permanent note   ← 本文は人間のみ。AI は frontmatter の tags / tagged のみ追記可
L2 モデル層(承認制)  _meta/taxonomy.md             ← 変更の決定は常に人間
L3 派生層            なし                           ← AI生成物で残したいものだけ references/ に隔離
L4 エンジン(交換可)  .agents/skills/(tag-dailies / extract / open-todos / resurface-ideas / delegate)
```

不変の原則:
- 人間が書くのは daily だけ。混ぜて書いてよい(複数トピックを1ファイルに)。
- 派生物を貯めない。抽出はチャットで受け取る。例外として、残す価値があると人間が判断した
  AI 生成物のみ references/ へ(タグ付け・抽出のデフォルト対象外)。
- **permanent/ は人間が書いた・編集したノートのみ**。AI 生成のまま残すものは references/ へ。
- permanent 化・taxonomy 変更は常に人間の承認を経る。
- 出力は日本語。technical 語は英語可。
- scheduled task(push 型の自動実行)は使わない。新しい用途が生まれたら pull 型の skill を足して対応する。

---

## 2. フォルダ構成

```
vault/                            # = Documents/agent-vault/(ラッパー階層は v5.3 で廃止)
├── daily/                        # 唯一の入力面。人間が自由記述。日付ノートのみを置く
│   ├── untagged/YYYY-MM-DD.md    # 未処理。Obsidian の新規デイリーはここに作られる
│   └── tagged/YYYY-MM-DD.md      # tag-dailies 処理済み
├── inbox/                        # デイリー以外の新規ノートの受け皿(未整理)
├── permanent/                    # 人間が昇格を決めたノートのみ
├── references/                   # AI生成物の保存庫(タグ付け・抽出のデフォルト対象外)
├── .agents/skills/               # Codex が発見する engine 定義 ← skill の挙動はここが常に正
│   ├── tag-dailies/SKILL.md
│   ├── extract/SKILL.md
│   ├── open-todos/SKILL.md
│   ├── resurface-ideas/SKILL.md
│   └── delegate/SKILL.md
├── .codex/agents/                # executor / executor-deep のサブエージェント定義
├── system-idea/                  # 本仕様書(設計思想のみ。skill 本文は書かない)
├── _templates/daily.md
├── _meta/taxonomy.md             # 統制語彙 ← タグはここが常に正
└── AGENTS.md                     # エージェントが最初に読む運用ルール
```

## 3. 正の所在(Single Source of Truth)

- **skill の挙動 = `.agents/skills/*/SKILL.md` が正**。本仕様書には役割の1行要約しか書かない。
- **タグ語彙 = `_meta/taxonomy.md` が正**。本仕様書にタグ一覧を掲載しない。
- 本仕様書が持つのは設計思想・構成・運用フローのみ。実体と食い違ったら**仕様書側を直す**。

---

## 4. taxonomy(統制語彙)の運用

形式と運用ルールは `_meta/taxonomy.md` 冒頭に記載。要点:

- `- [x]` = 許可タグ、`- [ ]` = 候補。**承認=人間がチェックを付ける、却下=行削除**。
  節間のコピペ移動は存在しない(移動ミスを構造的に排除)。
- skill は候補行の追記のみ。追加・統合・改名の決定は常に人間。
- 改名・統合の既存ノート反映は grep/sed の**機械置換**(LLM で本文を再読しない=トークン消費ほぼゼロ)。
- 新タグは前向き適用がデフォルト。遡及タグ付けは人間の明示依頼時のみ(期間指定つき)。
- 保留中の候補は tag-dailies 実行時に**1件だけ**人間に問われて消化される
  (pull 型でも承認作業が自然に回るための仕掛け)。
- 粒度の原則:何にでも付くタグを作らない。

---

## 5. skills(エンジン)

各 skill の仕様は `.agents/skills/*/SKILL.md` を参照。役割のみ:

- **tag-dailies**:未処理(`tagged:` 無し)の daily に許可タグを選択的に付与。バッチ・任意起動。
- **extract**:問いに対しタグ+期間で絞った daily を読んでチャットで答える。
  **未タグの daily は絞り込みに関係なく必ずスキャン**(完全性保証)。派生物は作らない。
- **open-todos**:未完チェックボックスの横断ビュー。ファイルは作らない。
- **resurface-ideas**:行頭マーカー `IDEA:` / `Q:` の行を grep で横断回収して提示
  (小さいアイデアの拾い直し。本文の LLM 読解なし)。
- **delegate**:セッションを計画/実行の分業モードに切り替える(`.codex/agents/` の
  executor / executor-deep へ委譲)。ノートを操作する skill ではなく、作業の進め方を変える skill。

pull 型の原則:すべて人間が呼んだ時だけ動く。「小さいアイデアの再浮上」のような
新しい欲求が出たら、scheduled task ではなく skill を1枚足して対応する。

---

## 6. 容量とスケール

- テキストのみなら容量は実質問題にならない(年間数 MB)。膨張源の派生物は生成しない。
  画像・PDF は vault に貯めず外部リンク or 別管理。
- **スケール対策**:タグ絞り込み後の対象が目安 30 枚を超えたら、期間の併用を必須にする。
  同一タグが恒常的に太る場合は、核を permanent に圧縮して daily を読む頻度を下げる。

## 7. 保全

「信頼できる外部記憶」の前提はデータが壊れないこと。

- **版管理**:vault を git 管理する。`.gitignore` は配置済み。
  init は 2026-08-10 に vault 直下(`Documents/agent-vault/`)で実行済み。
  以後のコミットは週1の手動で十分(`git add -A` → `git commit`)。
- Obsidian の File Recovery(コアプラグイン)を有効にしておく。
- AI による L1 への書き込みは frontmatter の `tags` / `tagged` 追記に限定(本文不可侵)。

## 8. テンプレートと空の日

- テンプレートに placeholder 風の文(例:「積み残し:〔…〕」)を書かない。
  LLM が本文として誤読し、抽出を汚すため。
- 何も書かなかった日のファイルは残さない(削除してよい)。空のまま残っても
  open-todos は本文の無いチェックボックス行を無視する。

---

## 9. 運用フロー(完成形)

- **書く**:daily に普段どおり、複数トピックを混ぜて自由記述。これだけが習慣。
  任意で行頭マーカー:拾い直したい着想は `IDEA:`、未解決の問いは `Q:`(resurface-ideas が回収)。
- **デイリー以外を書いた**:`inbox/` に落ちる。溜めずに、人間が編集して残すなら permanent、
  AI 生成のまま残すなら references へ振り分ける。`daily/` には日付ノート以外を置かない。
- **タグ付け**:思い出した時に tag-dailies を起動。冒頭で taxonomy の保留判断を1件だけ聞かれる。
- **抽出**:聞きたい時にチャット/skill で問う。残すかは人間が判断
  (人間が編集して残す→permanent、AI 生成のまま残す→references)。
- **permanent 化・taxonomy 更新**:常に人間。
- **コミット**:週1目安で git commit(手動)。

## 10. 将来スコープ

- **携帯からの capture 経路**(同期):アイデアの取りこぼしは現状最大の入口の穴だが、
  本人判断で保留中。それまでは「思いついたら daily に書く」に一元化する。
- ~~「小さいアイデアの再浮上」用の pull skill~~:resurface-ideas として実装済み(2026-07-13)。
