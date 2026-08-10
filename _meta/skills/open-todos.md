---
name: open-todos
description: "Collect incomplete checkbox items from daily notes into a chat-only current view. Use when the user asks to see open todos, unfinished tasks, or unchecked items across daily notes."
---

daily note 群を横断し、未完のチェックボックス `- [ ]` を集めてチャットに一覧する(pull型)。タスク管理ではなく「見落とし防止の現在ビュー」。

1. 対象範囲を決める(デフォルト:全 daily。または人間が指定した期間 / taxonomy タグ)。
2. 各 daily の未完行 `- [ ]` を集める。完了 `- [x]` は除外する。本文が空のチェックボックス行(テンプレートの `- [ ]` のみの行)は無視する。
3. 新しい日付順、または話題タグでグルーピングして提示する。各項目に出典日付 `[[daily/YYYY-MM-DD]]` を添え、期限記法(例 `📅 2026-07-01`)があれば近い順に並べてよい。
4. チャットで返す。集計 note を作らない。

- 派生物を vault に書き込まない。
- チェック状態を変えない。完了マークは人間が行う。
- 重複・消化済みらしき項目は「重複/済みかも」と指摘するに留め、削除しない。
