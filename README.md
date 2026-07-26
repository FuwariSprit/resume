# resume

職務経歴書を管理するリポジトリです。GitHub Pages等での公開は行いません。

## 構成

- [`resume.md`](./resume.md) — 職務経歴書の本体
- [`docs/`](./docs/) — 職務経歴の**一次情報源**。ここにない実績はレジュメに書きません
  - [`docs/projects/`](./docs/projects/) — 案件ごとの棚卸し
  - [`docs/skills.md`](./docs/skills.md) — テクニカルスキルの棚卸し
  - [`docs/activities.md`](./docs/activities.md) — 登壇資料・執筆記事などのリンク集
  - [`docs/career-vision.md`](./docs/career-vision.md) — キャリアビジョン
- `drafts/` — レジュメのドラフト（バージョンごとに採番）とレビュー結果
- `profile/` — 転職サービスのプロフィール欄に貼るテキスト（レジュメ本体とは別管理）

## セットアップ

パッケージマネージャーは [pnpm](https://pnpm.io/) を使用します（npmは使用しません）。

```sh
pnpm install
```

## 文法・誤字脱字のチェック

[textlint](https://textlint.github.io/) と
[markdownlint](https://github.com/DavidAnson/markdownlint) でMarkdownの文章・表記をチェックします。
チェック対象は [`resume.md`](./resume.md) のみです（`docs/`・`drafts/`・`.claude/` などは対象外）。

```sh
# チェックのみ
pnpm lint

# 自動修正可能な指摘を修正
pnpm lint:fix
```

textlintのルールは [textlint-rule-preset-smarthr][preset-smarthr] を土台に、
日本語の校正に有用な追加ルールを組み合わせています。設定は [`.textlintrc.json`](./.textlintrc.json) を参照してください。

[preset-smarthr]: https://www.npmjs.com/package/textlint-rule-preset-smarthr

Pull Request作成時にはGitHub Actionsで自動的にlintが実行されます（[`.github/workflows/lint.yml`](./.github/workflows/lint.yml)）。

### エディタ連携（推奨）

VSCodeを使用している場合、次の拡張機能を入れるとリアルタイムでチェック結果が表示されます。

- [textlint](https://marketplace.visualstudio.com/items?itemName=3w36zj6.textlint)
- [markdownlint](https://marketplace.visualstudio.com/items?itemName=DavidAnson.vscode-markdownlint)

## 職務経歴書を更新する際の流れ

転職ドラフト（[job-draft.jp](https://job-draft.jp/)）に提出するレジュメを想定した、
Claude CodeのSkillによる4ステップのワークフローを用意しています。
特定1社の求人票に向けた最適化ではなく、複数企業が読む前提の内容を作ります。

```text
/resume-interview  壁打ちして docs/ に一次情報源を書き溜める
       ↓
/resume-build      docs/ を元に drafts/resume-v1.md を生成
       ↓
/resume-review     4観点のサブエージェントが drafts/reviews/v1-*.md に指摘を出す
       ↓
/resume-revise     どの指摘を反映するか確認し、drafts/resume-v2.md を生成
```

| ステップ | Skill | やること |
| --- | --- | --- |
| 1. 壁打ち | `/resume-interview [対象]` | STAR法で経験を棚卸しし、`docs/` に保存する |
| 2. 作成 | `/resume-build` | 一次情報源だけを使ってドラフトを組み立てる |
| 3. レビュー | `/resume-review [バージョン]` | 観点ごとに分かれた4エージェントが並列でレビューする |
| 4. 修正 | `/resume-revise [バージョン]` | 指摘の採否を確認し、新しいバージョンを作る |

レビューの観点は4つに分かれており、各エージェントは担当外の観点に踏み込みません。

| 観点 | エージェント | 見るもの |
| --- | --- | --- |
| アンチパターン（AP） | `resume-antipattern-reviewer` | レジュメのアンチパターン8種の検出 |
| 企業側評価軸（QA） | `resume-quality-reviewer` | 企業が判断に必要な要素が書けているか |
| キャリアビジョン（CV） | `resume-vision-reviewer` | 野望欄の書き方 |
| 事実整合性・網羅性（FD） | `resume-fidelity-reviewer` | `docs/` との突き合わせ（誇張・捏造と、記載漏れ） |

3〜4は繰り返せます。ドラフトは上書きせず `resume-v1.md` → `resume-v2.md` と採番するので、
どのレビューで何を直したかを後から追えます。

内容が固まったら `resume.md` に反映し、`pnpm lint` で文章をチェックしてください
（lintの対象は `resume.md` のみなので、反映時にはじめてチェックがかかります）。

判断の基準や書き方のルールは [`docs/README.md`](./docs/README.md) を参照してください。

## プロフィール欄を作る（上記フローとは独立）

転職サービスのプロフィール欄に入力する次の3項目は、レジュメ本体とは別のスキルで作ります。

| Skill | 出力 | 内容 |
| --- | --- | --- |
| `/self-profile [対象]` | `profile/self-profile.md` | 今後身につけたい技術 / パフォーマンスを出せる環境 / 他人から評価されるポイント |

上の4ステップと違い、単発で完結します。バージョンは採番せず上書き更新し、
レビュー用のSubagentも起動しません。レジュメ本体ほど分量がなく、
レビューと改訂のループを回す必要がないためです。
