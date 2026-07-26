# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository. <!-- markdownlint-disable-line MD013 -->

## リポジトリの概要

コードベースではなくMarkdown文書が主体の、職務経歴書（レジュメ）を管理する個人リポジトリ。
textlint/markdownlintによる文章チェックと、Claude CodeのSkill/Subagentによるレジュメ作成ワークフローが中心。

## コマンド

パッケージマネージャーは **pnpm** のみを使用する（**npmは使用しない**）。
lintコマンドと設定は `package.json` / `.textlintrc.json` / `.markdownlint.jsonc` を参照。
lintの対象は `resume.md` のみ（`docs/`・`drafts/`・`.claude/` などは対象外。理由は [README.md](./README.md) を参照）。

## アーキテクチャ：一次情報源とレジュメ作成の分離

このリポジトリの中心的な設計は、**「事実の記録」と「レジュメの文章」を明確に分離する**こと。

- **`docs/` 配下が唯一の一次情報源。** ルールは [`docs/README.md`](./docs/README.md) に明記された
  一本のみ：「`docs/` にない実績は、レジュメに書かない」。ファイル構成も同ファイルを参照。
- **リポジトリ直下の `resume.md` は一次情報源ではない。** 以前に書かれた古いドラフトであり、
  骨格とMarkdownの体裁のみを参考にし、記載内容の根拠には使わない。
- `docs/projects/_template.md` の①〜⑦は、レジュメの案件記述の素材になる。
  レジュメ側（`.claude/skills/resume-build/assets/resume-template.md`）では3階層で書く。
  ①②を案件単位の `#### 概要 / #### チームおよび担当` に、③（自分が使った技術とその使い方）は
  専用セクションを設けず概要や `【打ち手・取り組み】` へ織り込み、④を開発単位の見出し（`■`）に、
  ⑤⑥⑦を各単位の `【課題】【打ち手・取り組み】【成果】` として**開発単位ごとに再編成する**
  （反省・学びは `#### 振り返り` に、反省点→原因→行動（次があるならどうするか）の流れで書く。
  学びは独立させず「次の行動」へ溶かす）。「プロジェクト詳細」という中間見出しは作らない。
  対応の詳細は resume-template.md 冒頭のコメントを参照。どちらかを変更したら、もう片方も揃える。

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
- ドラフトの文章スタイル（常体・改行の入れ方・プロジェクト詳細2000字以内・リストの使いどころ・
  造語や固有名詞の表記）は `.claude/skills/resume-build/references/writing-style.md` が正。
  `/resume-build` と `/resume-revise` の両方がこれを参照する。ユーザーから文章表現の指摘を受けたら、
  ドラフトを直すだけでなくこのファイルに追記して再発を防ぐ。
- 壁打ちの質問文例は `.claude/skills/resume-interview/references/questioning-guide.md` にまとまっている。

## プロフィール欄（上記フローの外）

`/self-profile` は上記4ステップとは**独立した単発のスキル**で、転職サービスのプロフィール欄
（今後身につけたい技術 / パフォーマンスを出せる環境 / 他人から評価されるポイント）を
`profile/self-profile.md` に書き出す。バージョンは採番せず上書きし、レビュー用Subagentも起動しない。
「`docs/` にない実績は書かない」は職務経歴のルールなので、この3項目には適用しない
（`docs/` は材料として読むが、上限にはならない）。
