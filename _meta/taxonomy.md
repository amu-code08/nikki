---
type: meta
updated: 2026-07-20
---
# Tag Taxonomy

運用ルール:
- `- [x]` = 許可タグ(tag-dailies が使用可)。`- [ ]` = 未承認候補。tag-dailies が新タグを必要と判断したときは、候補を処理中に人間へ提示して承認・却下を確認する。
- タグの追加・統合・改名の**決定は常に人間**。新タグは人間の明示承認後にのみ skill が許可タグとして追加できる。
- 粒度の原則:何にでも付くタグを作らない。「実際にその話題を書いた日にだけ付く」程度を保つ。
- 改名・統合は grep/sed の機械置換で既存ノートに反映する(LLM で本文を再読しない)。
- 新タグは前向き適用がデフォルト。過去ノートへの遡及タグ付けは人間の明示依頼時のみ(期間指定つき)。

## タグ

- [x] finance/equity-research — インターンの株式調査・決算短信モデル化・モデル更新・GSAI
- [x] finance/equity-research/regional-banks — 地銀の財務・NII・金利環境(equity-research の下位テーマ)
- [x] finance/stablecoin — 円ステーブルコイン・規制・決済
- [x] career/path — キャリア観・EA vs IB・HK研修・ネットワーキング・職業観
- [x] school/grad-research — 修士研究・mmWave HAR・PCF-PointTube・研究室MTG・発表
- [x] system/pkm — この外部記憶システム自体の設計・運用(Obsidian vault の構造・skill・taxonomy)
- [x] productivity/excel — Excel・ショートカット・モデリング効率化・Copilot in Excel
- [x] productivity/obsidian — Obsidian の操作・ショートカット・ツール効率化
- [x] productivity/ai — AIツールの使い方・ワークフロー設計(orchestrator/subagent・AIでのデータ収集等)
- [x] life/reflection — 日々の気づき・思考の癖・雑感
- [x] life/relationships — 家族・友人・恋人との距離感・消耗しない関わり方
- [x] life/health — 酒さ/V-beam・運動・睡眠など身体のこと
- [x] life/beauty — 美容院・スタイル・ロールモデル
- [x] productivity/github - githubを使った効率化、スマホPC連携のテーマ
