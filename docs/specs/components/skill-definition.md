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

AIエージェントが仕様書を解析・認知展開・図解化し、ポータルおよび個別ポータブルHTMLを生成するためのスキル指示書およびセクション構成プロトコルの定義正本。

---

## 1. 責務と境界

<!-- @spec:component-skill-definition-scope -->

| 区分 | 対象範囲・境界契約 (`MUST` / `MUST NOT`) |
| :--- | :--- |
| **対象範囲（単一責務）** | 動作モード判定（ポータル / 単一）、認知展開プロトコル（Key Takeaways, Before/After対比, 具象シナリオトレース）、全仕様書へのMermaid図解必須化、レスポンシブ横スクロールラッパー（`.table-responsive`）の適用、共通ヘッダーナビ（`.global-nav`）の構築、元仕様とのトレーサビリティ管理、およびHTML安全エスケーププロトコルの提供 (**MUST**)。 |
| **対象外（非責務・委譲先）** | HTML/CSSレンダリングエンジンの実装（ブラウザに委任）、画像バイナリデータの生成（インラインSVGまたはMermaid描画に委任）、外部Webサーバーへのデプロイ処理 (**MUST NOT**)。 |

---

## 2. 公開インターフェース ＆ データ構造

<!-- @spec:component-skill-definition-interface -->

内部プロンプト推論プロセスは記載せず、エージェントスキルの呼び出しIFおよび生成ドキュメントの構成スキーマを定義する (**MUST**)。

| 公開シンボル / エンドポイント | 入力（型・値域制約） | 出力（戻り値 / レスポンス型） | 事前条件・事後条件 |
| :--- | :--- | :--- | :--- |
| `/spec-to-readable-html [target] [lang]` | `target: string` (対象ファイル/ディレクトリ、省略時は既定: `docs/specs`)、`lang: string` (省略可能、既定: `"ja"`) | ポータル（`docs/specs-html/index.html` ＋ 仕様書群HTML）または指定仕様書HTML群 | **事前**: `docs/specs/spec-router.md` または指定対象ファイルが読み込み可能であること。<br>**事後**: `template.html` をベースとした自己完結HTML群が生成されること。 |
| ポータルモード (`index.html`) | `docs/specs/*` 全体仕様書解析結果、DAGネットワーク構造 | ポータルHTML (`docs/specs-html/index.html`) | **事前**: `docs/specs/spec-router.md` が存在するか全体変換指示であること。<br>**事後**: Hero、DAG全体図、ロール別読書ルート、仕様カタログ表を含むこと。 |
| 個別仕様書 HTML 認知展開構造 | 個別仕様書解析結果データ | 認知展開フローに準拠したHTMLブロック群 | **事前**: 仕様の要約・再編ではなく認知展開（Unpack）が適用されていること。<br>**事後**: Key Takeaways、必須Mermaid図解、Before/After対比、契約表、具象シナリオを含むこと。 |

---

## 3. ふるまい ＆ 異常系契約

<!-- @spec:component-skill-definition-contract -->

| 区分 | トリガー・入力条件 | 期待動作・送出エラー (`MUST`) | 状態不変・副作用保証 |
| :---: | :--- | :--- | :--- |
| **正常系 (Case 1-1)** | 対象指定なし（省略） かつ `docs/specs/spec-router.md` が存在 | ポータルモードとして動作し、`docs/specs/*` 配下の全仕様書を `docs/specs-html/*` 配下へ一括出力する (**MUST**)。 | ポータルトップ `docs/specs-html/index.html` と各仕様書HTMLを同一相対階層へ配置し、現在地アクティブな `.global-nav` を含める。 |
| **正常系 (Case 1-2)** | 対象指定あり（ファイル指定） かつ `docs/specs/spec-router.md` が存在 | 指定仕様書変換として動作し、指定ファイルを `docs/specs-html/*` 内の同一相対階層（ミラーリング）へ出力する (**MUST**)。 | 指定外ファイルの破壊・上書きを行わず、既存ポータルナビゲーションとの相対リンク整合性を維持する。 |
| **正常系** | 構造化表や RFC 2119 契約（`MUST`/`MUST NOT`）の解析 | 単なる転記を行わず、Key Takeaways、Before/After比較カード、具象シナリオ実例へと認知展開する (**MUST**)。 | 元仕様書の規範キーワード（MUST/SHOULD）およびAPI識別子の意味を改変しない。 |
| **正常系** | すべての仕様書ページの生成 | 最低1つの Mermaid 図解（構造、多重防御、状態遷移、パイプライン等）を必ず配置する (**MUST**)。 | 図解のない仕様書ページを生成しない (**MUST NOT**)。 |
| **正常系** | テーブル要素の出力 | すべての表を `.table-responsive` でラップする (**MUST**)。 | 横スクロール可能とし、幅狭画面でのレイアウト崩れを防ぐ。 |
| **異常系/対話 (Case 2-1)** | 対象指定なし（省略） かつ `docs/specs/spec-router.md` が存在しない | 処理を中断し、ユーザーに対象ファイル（またはディレクトリ）および出力先ディレクトリの指定を要求する (**MUST**)。その際、出力先候補を1件のみ提案する（既定提案: `docs/specs-html`） (**MUST**)。 | 未指定状態での推測による自律ファイル生成・書き込みを行わない (**MUST NOT**)。 |
| **異常系/対話 (Case 2-2)** | 対象指定あり（ファイル指定） かつ `docs/specs/spec-router.md` が存在しない | 処理を中断し、ユーザーに出力先ディレクトリの指定を要求する (**MUST**)。その際、出力先候補を1件のみ提案する（提案例: 指定ファイルの親横 `specs-html`） (**MUST**)。 | 出力先未承認のまま任意パスへ直接書き込みを行わない (**MUST NOT**)。 |
| **異常系** | 仕様書に未定義の曖昧な仕様や推論シナリオ | 独断で確定事実化せず、`[Inferred]` または `[Example]` バッジを明記する (**MUST**)。 | 未確証の仕様を確定事実（Confirmed）として出力しない (**MUST NOT**)。 |
| **異常系** | 入力テキストに `<script>` 等の危険タグ混入 | HTML特殊文字（`&`, `<`, `>`, `"`, `'`）をエスケープして安全なテキストノードとして出力する (**MUST**)。 | 悪意あるスクリプトの実行を防止する (**MUST NOT**)。 |

---

## 4. 受入条件 ＆ 検証基準

<!-- @spec:component-skill-definition-verification -->

| 条件ID | シナリオ（前提・実行操作） | 期待される検証結果 (`MUST`) | 検証テストファイル / ケース |
| :--- | :--- | :--- | :--- |
| `AC-SKILL-01` | 仕様書を入力してHTMLを生成 | 冒頭に `.key-takeaway`（3箇条まとめ）が配置されていること (**MUST**)。 | 生成HTML文字列検査 |
| `AC-SKILL-02` | `MUST` / `MUST NOT` 等の制約仕様を入力 | `.comparison-grid`（Before/After対比カード）が出力されていること (**MUST**)。 | 生成HTML文字列検査 |
| `AC-SKILL-03` | 抽象的な型やフローを含む仕様を入力 | `.scenario-box`（具体例トレース）に `[Inferred]` または `[Example]` バッジが付与されて出力されること (**MUST**)。 | 生成HTML文字列検査 |
| `AC-SKILL-04` | 任意の仕様書ページを出力 | 最低1つの `.diagram-container > .mermaid`（Mermaid図解）が配置されていること (**MUST**)。 | 生成HTML文字列検査 |
| `AC-SKILL-05` | ポータルモードでの変換実行 | `docs/specs-html/index.html` が生成され、共通ナビゲーション `.global-nav` で相互リンクが確立されていること (**MUST**)。 | ファイル存在・相対リンク検証 |
| `AC-SKILL-06` | すべてのテーブルの配置 | 表が `<div class="table-responsive">` でラップされていること (**MUST**)。 | 生成HTML構造検査 |
| `AC-SKILL-07` | Case 1-1（対象未指定 かつ `docs/specs/spec-router.md` 存在） | `docs/specs/*` の全仕様書が `docs/specs-html/*` へ一括ポータル変換されること (**MUST**)。 | スキル動作・出力パス検証 |
| `AC-SKILL-08` | Case 1-2（対象指定あり かつ `docs/specs/spec-router.md` 存在） | 指定ファイルが `docs/specs-html/*` 配下の元階層維持（ミラーリング）で出力されること (**MUST**)。 | スキル動作・出力パス検証 |
| `AC-SKILL-09` | Case 2-1（対象未指定 かつ `docs/specs/spec-router.md` 不存在） | 対象・出力先の指定が要求され、出力先候補が1件提案（`docs/specs-html`）されること (**MUST**)。 | 対話プロトコル検査 |
| `AC-SKILL-10` | Case 2-2（対象指定あり かつ `docs/specs/spec-router.md` 不存在） | 出力先の指定が要求され、出力先候補が1件提案されること (**MUST**)。 | 対話プロトコル検査 |
