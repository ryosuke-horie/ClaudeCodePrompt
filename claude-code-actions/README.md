# Claude Code Actions

このディレクトリは、GitHub ActionsでClaude Codeを活用するためのワークフロー定義を管理します。

## 注意事項

**これらのワークフローは参考用であり、このリポジトリでは実行されません。**
実際に使用する場合は、対象のリポジトリの `.github/workflows/` ディレクトリにコピーしてください。

## ワークフロー一覧

### claude-code-review.yml

自動コードレビューを実行するワークフロー。

**主な機能:**
- PRの自動レビュー
- インラインコメントでの具体的な指摘
- CLAUDE.mdのガイドラインに基づくチェック
- 建設的なフィードバック（日本語）

**使用方法:**
1. このファイルを対象リポジトリの `.github/workflows/` にコピー
2. `CLAUDE_CODE_OAUTH_TOKEN` をリポジトリのSecretsに設定
3. 必要に応じてdirect_promptをカスタマイズ

**カスタマイズポイント:**
- `paths`: 特定のファイルタイプのみレビュー対象にする
- `if`: 特定の投稿者のPRのみレビュー
- `direct_prompt`: レビュー基準をプロジェクトに合わせて調整

## セットアップ

1. Claude Code OAuthトークンの取得
2. GitHub Secretsへの登録
3. ワークフローファイルの配置

詳細は[公式ドキュメント](https://docs.anthropic.com/en/docs/claude-code/github-actions)を参照してください。