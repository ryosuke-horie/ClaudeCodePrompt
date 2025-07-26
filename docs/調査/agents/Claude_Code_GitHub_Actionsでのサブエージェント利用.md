# Claude Code GitHub Actionsでのサブエージェント利用

## 記事情報
- **調査元**: Anthropic公式ドキュメント
- **URL**: 
  - https://docs.anthropic.com/en/docs/claude-code/github-actions
  - https://docs.anthropic.com/en/docs/claude-code/sub-agents
  - https://docs.anthropic.com/en/docs/claude-code/sdk
- **種別**: 公式ドキュメント調査

## 調査結果の概要
Claude Code GitHub Actionsでは、サブエージェント機能（Task tool）が利用可能であることが確認できました。GitHub Actions環境でも専門的なタスクをサブエージェントに委譲できます。

## 重要な発見

### 1. サブエージェントの実装方式
- サブエージェントは「Task tool」として実装されている
- フックドキュメントに「Claude Code sub agent (Task tool call)」という記載がある
- GitHub Actions環境でも通常のClaude Code同様に動作

### 2. GitHub Actionsでの利用方法

#### 基本的な統合
- Claude Code GitHub ActionsはSDKベースで構築
- `@claude`メンションでPRやissueから起動可能
- 自動的にPR作成、コード実装、バグ修正を実行

#### サブエージェントの活用
```yaml
# GitHub Actions内でサブエージェントを利用する例
- name: Run Claude Code with sub agents
  uses: anthropics/claude-code-action@v1
  with:
    # サブエージェントを含むプロジェクト設定
    agents-dir: ./agents
```

### 3. SDK経由での高度な利用

#### 非対話モードでの実行
```bash
claude -p "Deploy the application" \
  --mcp-config mcp-servers.json \
  --allowedTools "mcp__permissions__approve" \
  --permission-prompt-tool mcp__permissions__approve
```

#### プログラマティックな統合
- TypeScript、Python、コマンドラインから利用可能
- CI/CD環境に最適化された非対話モード
- MCP（Model Context Protocol）との統合も可能

## 着目点（なぜこの調査をしたか）
- GitHub Actionsでサブエージェント機能が使えるか確認
- CI/CD環境での自動化の可能性を探る
- エンタープライズ環境での活用方法を理解

## 実践可能な内容の抽出

### 1. GitHub Actions + サブエージェント構成例

#### ディレクトリ構造
```
.github/
  workflows/
    claude-review.yml
agents/
  code-reviewer.md
  security-scanner.md
  test-writer.md
CLAUDE.md
```

#### ワークフロー例
```yaml
name: Claude Code Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  review:
    runs-on: ubuntu-latest
    steps:
      - uses: anthropics/claude-code-action@v1
        with:
          task: "Review this PR using code-reviewer agent"
```

### 2. サブエージェントの分離メリット
- **独立したコンテキスト**: 各エージェントが独自のコンテキストで動作
- **専門性の維持**: タスク固有の知識を汚染しない
- **並列実行**: 複数のサブエージェントを同時実行可能

### 3. エンタープライズでの活用パターン
- **セキュリティスキャン**: 専門エージェントによる脆弱性チェック
- **コンプライアンス**: 社内規約準拠のチェック
- **自動テスト生成**: PR毎にテストを自動生成

### 4. MCPとの組み合わせ
```json
{
  "mcpServers": {
    "database": {
      "command": "mcp-server-database",
      "args": ["--connection-string", "${DB_CONNECTION}"]
    }
  }
}
```

## 結論
Claude Code GitHub Actionsでサブエージェント機能は完全にサポートされており、以下が可能：

1. **Task toolとして実装**: サブエージェントはTask tool呼び出しとして動作
2. **GitHub Actions統合**: CI/CD環境で自然に利用可能
3. **SDK経由の高度な制御**: プログラマティックな統合が可能
4. **MCP連携**: 外部ツールとの統合も実現

これにより、GitHub Actions環境でも専門的なタスクの自動化が実現できます。