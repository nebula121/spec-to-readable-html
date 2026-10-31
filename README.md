# spec-to-readable-html

Markdown / テキストの仕様書を、**認知展開（Cognitive Unpacking）・図解・トレーサビリティ付きの「人間向けビジュアル HTML ドキュメント」**に変換する Agent Skill プロジェクトです。
[nebula121/AIDD_Template](https://github.com/nebula121/AIDD_Template) の 4層CQRS仕様書体系に最適化された Human Read-Only View 生成エンジンとして動作します。

---

## 1. 仕様書体系 (AIDD / SDD)

本プロジェクトの開発・運用および仕様正本は AIDD（AI-Driven Development）体系に準拠して管理されています。
全仕様書のルーティングおよび依存グラフは以下の索引を参照してください：

- **仕様書ルーティングマップ**: [docs/specs/spec-router.md](docs/specs/spec-router.md)
- **コア不可侵ルール**: [docs/specs/core-rules.md](docs/specs/core-rules.md)
- **システム概要 ＆ スコープ**: [docs/specs/system/overview.md](docs/specs/system/overview.md)
- **システムアーキテクチャ ＆ コンポーネント構成**: [docs/specs/system/architecture.md](docs/specs/system/architecture.md)
- **スキル定義正本**: [docs/specs/components/skill-definition.md](docs/specs/components/skill-definition.md)
- **HTMLテンプレート正本**: [docs/specs/components/html-template.md](docs/specs/components/html-template.md)

---

## 2. スキル本体 ＆ ドキュメント

エージェントスキルの機能詳細、インストール方法、利用手順、およびテンプレートのカスタマイズ方法については、以下のドキュメントを参照してください：

- **スキル概要・利用方法**: [spec-to-readable-html/README.md](spec-to-readable-html/README.md)
- **スキル実行指示書**: [spec-to-readable-html/SKILL.md](spec-to-readable-html/SKILL.md)
- **HTML出力テンプレートガイド**: [spec-to-readable-html/references/html-output-template.md](spec-to-readable-html/references/html-output-template.md)
- **HTMLベーステンプレート**: [spec-to-readable-html/references/template.html](spec-to-readable-html/references/template.html)

---

## 3. ライセンス

[MIT](./spec-to-readable-html/LICENSE) © 2026 KeMezz
