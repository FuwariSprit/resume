# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository. <!-- markdownlint-disable-line MD013 -->

## リポジトリの概要

コードベースではなくMarkdown文書が主体の、職務経歴書（レジュメ）を管理する個人リポジトリ。
textlint/markdownlintによる文章チェックと、Claude CodeのSkill/Subagentによるレジュメ作成ワークフローが中心。

## コマンド

パッケージマネージャーは **pnpm** のみを使用する（**npmは使用しない**）。
lintコマンドと設定は `package.json` / `.textlintrc.json` / `.markdownlint.jsonc` を参照。
`drafts/` と `.claude/` はlint対象外（理由は [README.md](./README.md) を参照）。

## アーキテクチャ：一次情報源とレジュメ作成の分離

このリポジトリの中心的な設計は、**「事実の記録」と「レジュメの文章」を明確に分離する**こと。

- **`docs/` 配下が唯一の一次情報源。** ルールは [`docs/README.md`](./docs/README.md) に明記された
  一本のみ：「`docs/` にない実績は、レジュメに書かない」。ファイル構成も同ファイルを参照。
- **リポジトリ直下の `resume.md` は一次情報源ではない。** 以前に書かれた古いドラフトであり、
  骨格とMarkdownの体裁のみを参考にし、記載内容の根拠には使わない。
- `docs/projects/_template.md` の①〜⑦の観点は、`.claude/skills/resume-build/assets/resume-template.md`
  の「プロジェクト詳細」と一対一で対応している。どちらかを変更したら、もう片方も揃える。

## Skill / Subagent によるレジュメ作成ワークフロー

転職ドラフト（job-draft.jp）向けのレジュメを、壁打ち→作成→レビュー→修正の4ステップで作る
（コマンド一覧は [README.md](./README.md) を参照）。特定1社の求人票への最適化ではなく、
複数企業が読む前提の内容にする。

- `drafts/` のドラフトは**上書きせず**バージョンを増やしていく（`resume-v1.md` → `resume-v2.md`）。
  後から差分で何を直したかを追える設計。
- `/resume-review` は `.claude/agents/` の4つのSubagentを**同一メッセージ内で並列起動**する
 （担当の一覧はREADME.mdの表を参照）。QAとFDの境界は「`docs/` を見ないと言えないか」で
  切り分けている — 表現の良し悪しはQA、資料と食い違っているかはFDが担当するため、
  同じ箇所が両方から別理由で指摘されることがある（重複ではない）。
- `/resume-revise` はレビュー結果を一括反映せず、**AskUserQuestionでユーザーに採否を確認してから**
  新しいバージョンを作る。採否の記録は `drafts/reviews/vN-decisions.md` に残す。
- 壁打ちの質問文例は `.claude/skills/resume-interview/references/questioning-guide.md` にまとまっている。
