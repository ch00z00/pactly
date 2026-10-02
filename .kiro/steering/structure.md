# Project Structure

> 2026-09-25 時点。アプリケーションコードは未作成。ディレクトリ構成・命名規則・レイヤー分割は**アーキテクチャの意思決定**にあたるため、開発者が決定して ADR に記録してから本ファイルに反映する。

## 現在のルート構成

```
pactly/
├── .kiro/
│   └── steering/        # AI 向けのプロジェクト文脈(product / tech / structure)
├── docs/
│   ├── adr/             # 技術選定・設計判断の記録
│   └── retro/           # 週次の振り返り(KPT + 改善の数値効果)
├── .gitignore
└── CLAUDE.md            # プロジェクト概要・AI との役割分担ルール(真実源)
```

## ドキュメント規約(決定済み)

### ADR — `docs/adr/NNNN-<title>.md`

- 新しい設計判断が発生するたびに作成する
- `NNNN` は 0001 からの連番、`<title>` は kebab-case(例: `0001-orm-selection.md`)
- 構成: **背景 → 検討した選択肢 → 決定 → トレードオフ**

### 振り返り — `docs/retro/YYYY-WW.md`

- 週次で作成する(例: `2026-39.md`)
- 構成: KPT(Keep / Problem / Try)。改善提案 → 次週実行 → 効果測定(**数値**)のログを残す

### Spec — `.kiro/specs/<feature>/`

- 機能ごとに requirements → design → tasks の順で作成し、各フェーズで開発者が承認する

## Git 運用(決定済み)

- デフォルトブランチ: `main`(リモート: `github.com:ch00z00/pactly`)
- 作業は feature ブランチで行い、PR 経由で `main` にマージする
- コミット前に「このコードで何を聞かれても答えられるか」を自問し、説明できない実装は採用しない

## コード構成(未決定)

以下は ADR で決定する。決まるまで AI は具体的な構成を前提にしたコードを生成しない。

- リポジトリ構成(例: `apps/web` + `apps/api` + `packages/api-client` のモノレポにするか)
- NestJS のモジュール分割の単位(機能単位 / ドメイン単位)とレイヤー分割(Controller / Service / Repository 等)
- ドメインロジックの置き場所と、ORM への依存をどこまで閉じ込めるか
- OpenAPI 生成物(スキーマ・型)の配置と生成タイミング(CI で差分チェックするか)
- Next.js App Router のディレクトリ規約(Route Groups、Server / Client Component の境界)
- ファイル命名規則・import 順序・パスエイリアス
