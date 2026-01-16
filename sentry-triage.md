# Sentry 高優先度・未修正 Issue を収集して GitHub Issue 化（Devin 用指示書）

## 目的
- 優先度が高い & 未修正（未対応） の Sentry Issue を Devin に収集・整理させ、GitHub Issue を作成させる。
- Devin は 自動修正をしない。修正は 必ず人間の明示指示後 に行う。
- この Issue 本文は Devin の作業指示書。

## 実施範囲
- 対象：Sentry の Issue
- ゴール：Sentry Issue を トリアージ済みの GitHub Issue として起票（複数可）
- 禁止：コード修正、PR 作成、ブランチ作成、本番変更

## 対象
- Sentry Organization: `{{SENTRY_ORG}}`
- Sentry Project(s): `{{SENTRY_PROJECTS}}`
- Environment: `production`
- GitHub Repository: `{{GITHUB_REPO}}`

# Devin へのタスク

## Phase 1: Context Gathering（情報収集）
- [ ] Sentry で 高優先度 & 未修正 の Issue を検索し、上位 3 件を抽出
- [ ] 各 Issue について以下を整理
    - タイトル
    - Sentry Issue URL
    - firstSeen / lastSeen（タイムゾーン含む）
    - event count / affected users
    - environment / release
    - 代表的な stack trace（上位フレーム）
    - 発生頻度・スパイク傾向
- [ ] Devin MCP を使い、関連システム構成を把握
    - `ask_question(repoName, "...")`
    - `read_wiki_structure`
    - `read_wiki_contents`
- [ ] 調査対象が不明確な場合は、質問を箇条書きで 1 回だけ投稿して停止

## Phase 2: Investigation（調査）
- [ ] Sentry でイベント詳細を調査（stack trace / breadcrumbs / tags / contexts）
- [ ] 関連 Issue の横断確認（同一 release / endpoint / 例外型）
- [ ] 該当コードパスを特定・推定（ファイル/関数単位）
- [ ] 発生タイミングとデプロイ・変更履歴の相関を確認
- [ ] 事象のタイムラインを整理

## Phase 3: Root Cause Analysis（原因仮説）
- [ ] 収集した証拠を整理
- [ ] 強くあり得る原因を複数提示（以下を必須）
    - Hypothesis（何が起きたか）
    - Evidence（ログ・コード・時系列）
    - Confidence（High / Medium / Low）
    - Affected Code（パス / 行番号）
    - Suggested Fix（方針のみ、実装しない）
- [ ] smart friend に仮説を検証させ、抜け漏れを確認（必須）

## GitHub Issue 起票ルール（Devin 用）
- 原則 Sentry Issue : GitHub Issue = 1 : 1
- GitHub Issue に必ず含める：
    - Sentry Issue URL
    - 影響範囲（頻度 / ユーザ数 / 環境）
    - 再現条件（分かる範囲）
    - 期待動作 / 実際の挙動
    - 原因仮説（Confidence 付き）
    - 修正方針（実装は別指示）
- 末尾に実装ルールについて必ず記述する
    - 修正する時は、再度Sentryのエラーをみにいって、詳細を把握すること
    - diffは最小限の変更で行うようにすること
    - 修正はできるだけ根本的なところを修正すること（例：呼び出し側でcatchせず、呼び出し元でcatchする）
    - メインブランチを修正するのではなくそれぞれにプルリクエストをつくること
    - プルリクエストは日本語で本文を書くこと
    - プルリクエストがマージされたらIssueがクローズされること
    - 可能な限り、修正が正しく動くかテストコードを書くこと。ただし、Devin上では実行できないため、GitHub Actionsのlint.ymlの結果をみるように。

## 禁止事項（厳守）
- 修正実装を開始しない
- ブランチ / PR を作成しない
- 本番設定・データを変更しない
- 指示のない修正をしない
