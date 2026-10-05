# Project Guidelines

## Overview

勤怠管理アプリの実装・保守における、Copilot 向けのプロジェクト指示書です。
実務環境を想定した開発運用体制を前提に、仕様・データ設計・画面設計・テストの整合性が取れた実装を継続します。

## Tech Stack

| 項目 | 内容 |
|---|---|
| 言語 | PHP 8.5 |
| フレームワーク | Laravel 12 |
| 認証 | Laravel Fortify（ヘッドレス） |
| データベース | MySQL 8.4 |
| 開発環境 | Docker / Laravel Sail |
| CI | GitHub Actions |
| フロントエンド | Blade + Tailwind CSS + Vite |
| テスト | PHPUnit |

## Architecture

- 想定業務は小規模飲食店の勤怠管理
- 想定従業員数は約 10 名、管理者は店長 1 名
- まずは紙タイムカードや Excel 管理の置き換えを目指す
- 画面設計より先にデータ設計と権限制御を固める
- タイムゾーンは JST 固定
- 認証は Laravel Fortify を用い、ログイン・登録の View は独自 Blade で実装する
- `main` と `develop` は PR 必須・CI 必須の保護ブランチとして扱う

## Build and Test

```bash
# 起動
./vendor/bin/sail up -d

# テスト実行
./vendor/bin/sail artisan test

# カバレッジ確認
./vendor/bin/sail artisan test --coverage

# フロントエンドビルド
./vendor/bin/sail npm run build
```

## Testing Conventions

- ローカルと CI で同じ手順で動かせることを優先する
- テストは CI で必ず実行する
- Feature テストは業務フロー・権限制御・画面遷移を主に確認する
- Unit テストはモデル、ポリシー、バリデーションなどの単位で確認する
- テストデータは Factory を使い、意図がわかる最小限のデータを用意する
- 未認証・権限不足・境界値・異常系を優先して追加する

## Conventions

- README はプロジェクト概要と利用環境、前提条件は `docs/project-prerequisites.md` に集約する
- ER 図、画面遷移図、テスト仕様書、CI 設計メモを実装前にそろえる
- 実装の順序は認証とロール分岐、勤怠記録、休憩と日跨ぎ、修正申請と承認、月次集計と CSV 出力の順を基本とする
- ブランチは `feature/*`, `fix/*`, `docs/*`, `ci/*` を `develop` に集約し、`develop` から `main` へ PR で反映する
- コミットメッセージは Conventional Commits 形式を基本とする

## Branch Strategy

ブランチは以下の構成で運用する。`main` と `develop` への直 push は禁止する。

```text
main        # 本番コード。CI パス済みの develop からのみマージ
└── develop # 開発中の統合ブランチ
      ├── feature/*  # 新機能の追加
      ├── fix/*      # バグ修正
      ├── docs/*     # ドキュメント更新
      └── ci/*       # CI 設定の変更
```

### PR フロー

```text
feature/xxx → develop（PR・CI パス必須）→ マージ
develop     → main（PR・CI パス必須）  → マージ
```

### ブランチ名の例

```text
feature/add-attendance-record
feature/add-break-history
fix/login-redirect
docs/update-prerequisites
ci/add-github-actions-check
```

## Commit Message Format

コミットメッセージは以下の形式に従う（Conventional Commits 準拠）。

```text
<型>(スコープ): タイトル

本文（任意）

フッター（任意）
```

### 型の一覧

| 型 | 用途 |
|---|---|
| `feat` | 新機能の追加 |
| `fix` | バグ修正 |
| `test` | テストの追加・修正 |
| `refactor` | 機能変更を伴わないリファクタリング |
| `style` | コードスタイル・フォーマットの修正 |
| `docs` | ドキュメント・コメントの変更 |
| `chore` | ビルド設定・依存関係など雑務 |
| `ci` | CI 設定の変更 |

### 例

```text
feat(attendance): 打刻一覧を追加する
```

```text
fix(auth): ログイン後の遷移先を修正する
```

```text
test(attendance): 日跨ぎ勤務の境界値テストを追加する
```

## Key Files

- `docs/project-prerequisites.md` — 実装前に固める前提条件と必須ドキュメント
- `docs/er-conceptual.drawio` — 概念 ER 図
- `docs/er-logical.drawio` — 論理 ER 図
- `docs/er-physical.drawio` — 物理 ER 図
- `docs/screen-flow.html` — 画面遷移図
- `README.md` — プロジェクト概要と利用環境
- `.github/workflows/ci.yml` — CI 実行定義