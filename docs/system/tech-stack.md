---
spec_id: tech-stack
title: 技術スタック ＆ 開発検証環境
status: stable
target_files:
  - "**/*"
dependencies:
  - core-rules
  - system-architecture
---
# 技術スタック ＆ 開発検証環境

<!-- @spec:tech-stack -->

採用技術スタック、ディレクトリ構成、標準検証コマンド、および開発ワークフローの定義正本。

---

## 1. 技術スタック ＆ 動作環境

<!-- @spec:runtime-stack -->

| 分類 | 採用技術・バージョン | 用途・選定制約 (`MUST` / `SHOULD`) |
| :--- | :--- | :--- |
| **言語 / ランタイム** | HTML5 / CSS3 / JavaScript (ES5/ES6) | 単一ポータブルHTMLの組版および図拡大・ズーム・パン機能の実装 (`MUST`) |
| **仕様・プロンプト規格** | Agent Skills (SKILL.md) / Markdown | AIコーディングアシスタント向け推論プロトコルおよび出力ガイドの記述 (`MUST`) |
| **検証スクリプト環境** | Python 3.11+ | AIDD仕様整合性の決定論的静的検証（`verify_specs.py`）の実行 (`MUST`) |
| **外部ライブラリ / CDN** | Mermaid.js (既定: CDN参照) | フローチャート・シーケンス・ER・状態図の動的描画（オフライン時はSVGへ置換） (`SHOULD`) |

---

## 2. ディレクトリ構成マップ

<!-- @spec:directory-structure -->

| パス | 役割・配置対象 |
| :--- | :--- |
| `AGENTS.md` | メタ行動規範（Layer 0・不変） |
| `docs/spec-router.md` | ルーティング索引（Layer -・自動生成） |
| `docs/core-rules.md` | システム憲法（Layer 1・不変） |
| `docs/system/*.md` | システム全体設計（Layer 2・業務資産） |
| `docs/components/*.md` | 個別仕様正本（Layer 3・`_template.md` は不変、他は業務資産） |
| `.agents/skills/` | 手順・スキル運用（(Tools)・不変） |
| `spec-to-readable-html/` | エージェントスキル本体（`SKILL.md`, `README.md`, `LICENSE`, `references/`） |

---

## 3. 標準検証コマンド ＆ 開発ワークフロー

<!-- @spec:verification-commands -->

コードまたは仕様の変更後、報告前に必ず以下の全検証を実行し、合格基準を満たすこと (**MUST**)。

| 区分 | 実行コマンド / 採用スキル | 合格基準 |
| :--- | :--- | :--- |
| **仕様書整合性** | `python .agents/skills/spec-verification/scripts/verify_specs.py --strict` | エラー `0` 件 (Exit Code `0`) |
| **構文・文字コード検証** | UTF-8 エンコード・改行コード LF 検証 | 不正な文字化け・改行不整合なし (Exit Code `0`) |
| **静的整合性** | `target_files` 実在確認・DAG循環参照検知 | 欠落ファイル `0` 件・循環参照 `0` 件 (Exit Code `0`) |
| **開発ワークフロー** | `.agents/skills/local-workflow` (ローカル完結) | ワークフロー規定の DoD 完全充足 |
