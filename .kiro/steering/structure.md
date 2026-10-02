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

## リポジトリ構成(決定済み: [ADR 0001](../../docs/adr/0001-repository-structure.md))

pnpm workspaces によるモノレポを採用した。予定している構成は次のとおり(未作成)。

```
pactly/
├── apps/
│   ├── web/            # Next.js
│   └── api/            # NestJS
└── packages/
    ├── api-client/     # openapi-typescript の生成型 + openapi-fetch クライアント
    └── config/         # ESLint / tsconfig / Prettier の共有設定
```

### 境界のルール

- `apps/web` から `apps/api` のコードを直接 import しない。2つのアプリをつなぐのは `packages/api-client` だけにする
- 守れているかをツールで確認する
  - `apps/web` の `package.json` に `apps/api` を依存として書かない(pnpm の厳格な依存解決により、パッケージ名での import がエラーになる)
  - 相対パスでの import は ESLint の `no-restricted-imports`(または dependency-cruiser)で禁止し、CI で検知する
- API を変更したら、型の再生成とフロントエンドの修正を同じ PR に含める。CI で生成した型に差分がないかを検査する

## コード構成(未決定)

以下は ADR で決定する。決まるまで AI は具体的な構成を前提にしたコードを生成しない。

- NestJS のモジュール分割の単位(機能単位 / ドメイン単位)とレイヤー分割(Controller / Service / Repository 等)
- ドメインロジックの置き場所と、ORM への依存をどこまで閉じ込めるか
- OpenAPI 生成物(スキーマ・型)の配置と生成タイミング(CI で差分チェックするか)
- Next.js App Router のディレクトリ規約(Route Groups、Server / Client Component の境界)
- ファイル命名規則・import 順序・パスエイリアス
