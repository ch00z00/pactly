# Technology Stack

> 出典: `CLAUDE.md`(2026-09-25 時点)。コードはまだ存在しないため、本ファイルは「方針として決まっていること」と「未決定事項」を区別して記載する。
> **未決定事項を AI が勝手に決めて実装してはならない。** 候補を 2〜3 案比較して提示し、開発者の決定を ADR に記録してから着手する。

## アーキテクチャ

- フロントエンド(Next.js)とバックエンド(NestJS)を**独立したサービス**として分離する
  - ビジネスロジックを Next.js の API Routes / Route Handlers / Server Actions に寄せない
- API 契約は **OpenAPI 定義を単一の真実源(Single Source of Truth)** とする

```
NestJS (@nestjs/swagger) ──生成──▶ OpenAPI 定義
                                     │
                                     ▼ openapi-typescript
                               TypeScript 型
                                     │
                                     ▼ openapi-fetch
                         Next.js 側の型安全な API クライアント
```

## 技術スタック(決定済み)

| レイヤー | 技術 |
| --- | --- |
| フロントエンド | Next.js 16(App Router)/ React 19 / TypeScript 5 |
| バックエンド | NestJS(TypeScript) |
| DB | PostgreSQL |
| API 契約 | `@nestjs/swagger` → `openapi-typescript` → `openapi-fetch` |
| 認証・権限 | RBAC を自前で設計する(既存ライブラリに丸投げしない) |
| インフラ | AWS |
| AI 機能 | LLM API(契約書レビュー・経費自動仕訳) |

## 開発ツール・品質管理(決定済み)

| 用途 | 技術 |
| --- | --- |
| Lint | ESLint(Flat Config) |
| Format | Prettier |
| Git hooks | Husky |
| CI/CD | GitHub Actions(テスト自動実行・デプロイパイプライン) |
| 依存関係の更新 | Renovate |
| タスク管理 | GitHub Issues / GitHub Projects |

## 未決定事項(ADR 候補)

| 項目 | 候補 | 備考 |
| --- | --- | --- |
| ORM / クエリビルダ | Prisma / Drizzle | `CLAUDE.md` で両論併記 |
| AWS 実行環境 | App Runner / ECS Fargate | 「シンプル構成」が前提 |
| リポジトリ構成 | モノレポ(pnpm workspaces / Turborepo 等)/ 複数リポジトリ | OpenAPI 型の共有方法に直結する |
| パッケージマネージャ | npm / pnpm / yarn / bun | リポジトリ構成とセットで決める |
| Node.js バージョン | — | `.nvmrc` / `engines` で固定する |
| 認証方式 | セッション / JWT、パスワード / OAuth / パスキー 等 | 認証(誰か)と認可(RBAC: 何ができるか)は分けて設計する |
| テストフレームワーク | Jest / Vitest、E2E に Playwright 等 | NestJS の標準は Jest |
| LLM プロバイダ・モデル | — | レシート画像を扱うため、画像入力対応が要件になる |
| レシート画像の保存先 | S3 等 | 個人情報を含むため、保存期間とアクセス制御を設計する |
| ローカル開発環境 | Docker Compose(PostgreSQL)等 | — |

## よく使うコマンド

まだアプリケーションコードがないため未定義。パッケージマネージャとリポジトリ構成が決まり次第ここに追記する。

## 環境変数

未定義。運用方針のみ決まっている。

- `.env` / `.env.*` は `.gitignore` で除外済み
- 必要な変数は `.env.example` にキー名だけを記載してコミットする(値は書かない)
- API キーや DB 認証情報は steering / ADR / コードに直接書かない

## ポート

未定義。**Next.js と NestJS はどちらも既定ポートが 3000 で衝突する**ので、構成を決める際にあわせて割り当てること。
