---
spec_id: component-skill-definition
title: スキル定義 ＆ プロンプトプロトコル
status: stable
target_files:
  - spec-to-readable-html/SKILL.md
  - spec-to-readable-html/references/html-output-template.md
dependencies:
  - core-rules
  - domain-rules
  - system-architecture
  - tech-stack
  - component-html-template
---
# スキル定義 ＆ プロンプトプロトコル

<!-- @spec:component-skill-definition -->

AIエージェントが仕様書を解析・再編・図解化し、ポータブルHTMLを生成するためのスキル指示書およびセクション構成プロトコルの定義正本。

---

## 1. 責務と境界

<!-- @spec:component-skill-definition-scope -->

| 区分 | 対象範囲・境界契約 (`MUST` / `MUST NOT`) |
| :--- | :--- |
| **対象範囲（単一責務）** | 仕様書の構文解析、要件・制約・API・データフローの抽出、読者に応じた論理構造の再編、適切な図解（フロー・シーケンス・アーキテクチャ・ER図）の選定、元仕様とのトレーサビリティ管理、およびHTML安全エスケーププロトコルの提供 (**MUST**)。 |
| **対象外（非責務・委譲先）** | HTML/CSSレンダリングエンジンの実装（ブラウザに委任）、画像バイナリデータの生成（インラインSVGまたはMermaid描画に委任）、外部Webサーバーへのデプロイ処理 (**MUST NOT**)。 |

---

## 2. 公開インターフェース ＆ データ構造

<!-- @spec:component-skill-definition-interface -->

内部プロンプト推論プロセスは記載せず、エージェントスキルの呼び出しIFおよび生成ドキュメントの構成スキーマを定義する (**MUST**)。

| 公開シンボル / エンドポイント | 入力（型・値域制約） | 出力（戻り値 / レスポンス型） | 事前条件・事後条件 |
| :--- | :--- | :--- | :--- |
| `/spec-to-readable-html [lang]` | `lang: string` (省略可能、既定: `"ja"`)、仕様書コンテキスト (Markdown / Plain Text) | ポータブル HTML ドキュメント (`.html` / `.spec.html`) | **事前**: 対象仕様書が読み込み可能であること。<br>**事後**: `template.html` をベースとした単一自己完結HTMLが生成されること。 |
| `html-output-template` 構造仕様 | 仕様書解析結果データオブジェクト | 定型11セクション構成のHTMLブロック群 | **事前**: 仕様の要約・再編が完了していること。<br>**事後**: 空白セクションを含まず、目次およびトレーサビリティ表が包含されること。 |

---

## 3. ふるまい ＆ 異常系契約

<!-- @spec:component-skill-definition-contract -->

| 区分 | トリガー・入力条件 | 期待動作・送出エラー (`MUST`) | 状態不変・副作用保証 |
| :---: | :--- | :--- | :--- |
| **正常系** | 有効な仕様書入力の変換要求 | 要約・論理再編を行い、図解・表・バッジ・トレーサビリティを付与したHTMLを出力する (**MUST**)。 | 元仕様書の規範キーワード（MUST/SHOULD）およびAPI識別子の意味を改変しない。 |
| **正常系** | 言語引数 `lang` の指定 | 指定された言語（`ja`, `en` 等）に合わせた見出し・ラベル・バッジで出力する (**MUST**)。 | コード識別子・APIパス・ステータス定数は言語変更せず原文を維持する。 |
| **異常系** | 仕様書に未定義の曖昧な仕様が存在 | 独断で推測補完せず、`Open Questions` セクションまたは `Assumption` バッジとして明示する (**MUST**)。 | 未確証の仕様を確定事実（Confirmed）として出力しない (**MUST NOT**)。 |
| **異常系** | 入力テキストに `<script>` 等の危険タグ混入 | HTML特殊文字（`&`, `<`, `>`, `"`, `'`）をエスケープして安全なテキストノードとして出力する (**MUST**)。 | 悪意あるスクリプトの実行を防止する (**MUST NOT**)。 |

---

## 4. 受入条件 ＆ 検証基準

<!-- @spec:component-skill-definition-verification -->

| 条件ID | シナリオ（前提・実行操作） | 期待される検証結果 (`MUST`) | 検証テストファイル / ケース |
| :--- | :--- | :--- | :--- |
| `AC-SKILL-01` | `MUST` / `SHOULD` などの要件キーワードを含む仕様書を入力 | 生成HTML内の要件表に `.badge-must` / `.badge-should` が正確に割り振られること (**MUST**)。 | 目視確認および生成HTML文字列検査 |
| `AC-SKILL-02` | 推論・補足情報を含む仕様書を入力 | 該当項目に `.badge-inferred` または `.badge-assumption` が付与されること (**MUST**)。 | 目視確認および生成HTML文字列検査 |
| `AC-SKILL-03` | 特殊記号（小なり/大なり記号）を含む仕様テキストを入力 | 生成HTMLの要素・属性値において `&lt;` や `&gt;` にエスケープされていること (**MUST**)。 | 目視確認およびHTML構文検査 |
| `AC-SKILL-04` | オフライン/完全自己完結出力の要求 | 外部Mermaid CDNへのスクリプト参照を含まず、インラインSVGでダイアグラムが出力されること (**MUST**)。 | 生成HTML内の `script[src]` 存在検査 |
