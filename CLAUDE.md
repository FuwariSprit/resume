# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository. <!-- markdownlint-disable-line MD013 -->

## リポジトリの概要

コードベースではなくMarkdown文書が主体の、職務経歴書（レジュメ）を管理する個人リポジトリ。
textlint/markdownlintによる文章チェックと、Claude CodeのSkill/Subagentによるレジュメ作成ワークフローが中心。
コマンド一覧とワークフローの全体像は [README.md](./README.md) を参照。

## コマンド

パッケージマネージャーは **pnpm** のみを使用する（**npmは使用しない**）。
lintコマンドと設定は `package.json` / `.textlintrc.json` / `.markdownlint.jsonc` を参照。
lintの対象は `resume.md` のみ（`docs/`・`drafts/`・`.claude/` などは対象外）。

## 中心となる設計：一次情報源とレジュメ作成の分離

**「事実の記録」と「レジュメの文章」を分ける。** ルールは
[`docs/README.md`](./docs/README.md) の一本のみ：「`docs/` にない実績は、レジュメに書かない」。
リポジトリ直下の `resume.md` は一次情報源ではない（過去に書かれた古い版）。

## ルールの正本

**同じルールを2箇所に書かない。** 直すときは正本だけを直し、他からは参照する。

| ルールの領域 | 正本 |
| --- | --- |
| 一次情報源の扱い、docs/ の構成 | [`docs/README.md`](./docs/README.md) |
| 案件メモの項目（①〜⑦） | [`docs/projects/_template.md`](./docs/projects/_template.md) |
| レジュメの骨格（雛形）と ①〜⑦ の対応 | `.claude/skills/resume-build/assets/resume-template.md` |
| 案件の構造の規約、文体・字数・造語・表記 | `.claude/skills/resume-build/references/writing-style.md` |
| キャリアビジョン欄の構成 | `.claude/skills/resume-build/references/career-vision-structure.md` |
| 壁打ちの質問文例 | `.claude/skills/resume-interview/references/questioning-guide.md` |
| レビュー4観点の共通作法・担当境界 | `.claude/skills/resume-review/references/review-protocol.md` |
| コマンド一覧、4観点の担当表 | [README.md](./README.md) |

## ワークフローの不変条件

上記の正本に書き場所がない、リポジトリ固有の約束事。

- `drafts/` のドラフトは**上書きせず**バージョンを増やす（`resume-v1.md` → `resume-v2.md`）。
  後から差分で何を直したかを追える設計。
- `/resume-review` は `.claude/agents/` の4つのSubagentを**同一メッセージ内で並列起動**する。
- `/resume-revise` はレビュー結果を一括反映せず、**AskUserQuestionでユーザーに採否を確認してから**
  新しいバージョンを作る。採否の記録は `drafts/reviews/vN-decisions.md` に残す。
- ユーザーから文章表現の指摘を受けたら、ドラフトを直すだけでなく `writing-style.md` に
  追記して再発を防ぐ。
- `/self-profile` は上記フローの外にある単発のスキル。「`docs/` にない実績は書かない」は
  職務経歴のルールなので、プロフィール欄の3項目には適用しない。
