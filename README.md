# resume

職務経歴書を管理するリポジトリです。GitHub Pages等での公開は行いません。

## 構成

- [`resume.md`](./resume.md) — 職務経歴書の本体
- [`docs/projects/`](./docs/projects/) — 案件ごとの詳細メモ（`resume.md` の概要欄を書く際の下書き・裏取り用）
- [`docs/skills.md`](./docs/skills.md) — テクニカルスキルの棚卸し
- [`docs/activities.md`](./docs/activities.md) — 登壇資料・執筆記事などのリンク集

## セットアップ

パッケージマネージャーは [pnpm](https://pnpm.io/) を使用します（npmは使用しません）。

```sh
pnpm install
```

## 文法・誤字脱字のチェック

[textlint](https://textlint.github.io/) と
[markdownlint](https://github.com/DavidAnson/markdownlint) でMarkdownの文章・表記をチェックします。

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

1. 案件の詳細を思い出しながら `docs/projects/` にメモを書く（下書きなので体裁は気にしない）
2. メモをもとに `resume.md` の該当セクションを整える
3. `pnpm lint` で文章をチェックし、指摘があれば修正する
