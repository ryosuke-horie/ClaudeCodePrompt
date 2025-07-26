# [小ネタ]Claude Code のSub agentでレビュー& prにコメントさせる

## 記事情報
- **URL**: https://dev.classmethod.jp/articles/claudecode-sugagent-github-pr/
- **公開元**: クラスメソッド開発者ブログ
- **種別**: 実装例・チュートリアル
- **環境**: Claude Code 1.0.60, GitHub CLI 2.73.0

## 記事の概要
Claude CodeのSub-Agents機能を使用して、GitHubのPRに自動でレビューコメントを投稿するコードレビューエージェントの実装例。

## 記載されているTipsやテクニック

### 1. Sub-Agent作成の基本手順
1. `/agents`コマンドでエージェント作成
2. `code-reviewer.md`ファイルにレビュー用プロンプトを設定
3. GitHub CLIと連携してPRにコメント投稿

### 2. 重要な設定ポイント

#### レビューフォーマットの指定
```markdown
## GitHub PR Review Format (MANDATORY when PR is mentioned)

Your response MUST have exactly TWO sections:

### Section 1: Standard Review
Provide your detailed code review

### Section 2: GitHub API JSON
End your response with GitHub API compatible JSON
```

#### GitHub API形式のJSON出力
- レビュー結果をGitHub APIが受け付ける形式で出力
- 構造化されたレスポンスフォーマットの定義

### 3. 実装の特徴
- **独自のコンテキストウィンドウ**: メインのClaude Codeから独立
- **安全性**: 許可されたツールのみ使用可能
- **柔軟性**: カスタマイズ可能なレビュー基準

## 着目点（なぜこの記事を共有したか）
- コードレビューエージェントの具体的な実装例
- GitHubとの実践的な連携方法
- 構造化された出力フォーマットの活用
- 自動化による開発効率向上の実例

## 実践可能な内容の抽出

### 1. コードレビューエージェントの基本構成
```markdown
---
name: code-reviewer
description: GitHubのPRをレビューしてコメントを投稿
tools: Read, Grep, Glob, Bash
---

あなたは経験豊富なコードレビュアーです。
PRの変更内容を分析し、建設的なフィードバックを提供してください。

## レビューの観点
- コードの品質と可読性
- セキュリティの懸念事項
- パフォーマンスの最適化
- ベストプラクティスへの準拠

## GitHub PR Review Format (MANDATORY)
[構造化されたレスポンスフォーマット]
```

### 2. GitHub CLIとの連携パターン
```bash
# PRの情報を取得
gh pr view --json files,title,body

# レビューコメントを投稿
gh pr review --comment --body "レビュー内容"
```

### 3. 応用可能な使用例
- **テストランナー**: テスト実行と結果報告
- **ドキュメント生成**: コードからドキュメント自動生成
- **リファクタリング提案**: 改善点の具体的な提案
- **セキュリティ監査**: 脆弱性の検出とレポート

### 4. 実装時の注意点
- **出力フォーマット**: GitHub APIに準拠した形式が必要
- **権限管理**: 必要最小限のツールアクセス
- **エラーハンドリング**: API呼び出し失敗時の対処

### 5. 効果的な活用シナリオ
1. PR作成時の自動レビュー
2. マージ前の最終チェック
3. 定期的なコード品質監査
4. チーム全体のレビュー品質向上

## 得られた知見
- Sub-Agentsは外部APIとの連携も可能
- 構造化された出力により自動化が容易
- レビュープロセスの標準化に貢献
- チーム開発の効率化に有効