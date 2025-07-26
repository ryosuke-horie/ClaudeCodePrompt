# Claude Code Hooksの実装事例と活用方法

## 調査概要
Claude Code Hooksは、Claude Codeのワークフロー内の特定ポイントで自動実行されるシェルコマンドやスクリプト。一貫性のある自動化を保証し、手動介入なしで特定の操作を実行できる。

## Hooksの基本概念

### イベントタイプ
1. **PreToolUse**: ツール使用前に実行（ブロック可能）
2. **PostToolUse**: ツール使用後に実行
3. **Notification**: 通知時に実行
4. **user-prompt-submit**: ユーザープロンプト送信時

### 利用可能な環境変数
- `$CLAUDE_FILE_PATHS`: 関連ファイルパスのスペース区切りリスト
- `$CLAUDE_NOTIFICATION`: 通知メッセージの内容（Notificationイベントのみ）
- `$CLAUDE_TOOL_OUTPUT`: ツール実行の出力（PostToolUseイベントのみ）
- `$CLAUDE_PROJECT_DIR`: プロジェクトルートディレクトリの絶対パス

## 実装事例

### 1. Bashコマンドロギング
```bash
# PreToolUseフックでBashコマンドを記録
jq -r '"\(.tool_input.command) - \(.tool_input.description // "No description")"' >> ~/.claude/bash-command-log.txt
```

### 2. TypeScriptファイルの自動フォーマット
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "jq -r '.tool_input.file_path' | { read file_path; if echo \"$file_path\" | grep -q '\\.ts$'; then npx prettier --write \"$file_path\"; fi; }"
          }
        ]
      }
    ]
  }
}
```

### 3. デスクトップ通知
```json
{
  "hooks": {
    "Notification": [
      {
        "matcher": "",
        "hooks": [
          {
            "type": "command",
            "command": "notify-send 'Claude Code' 'Awaiting your input'"
          }
        ]
      }
    ]
  }
}
```

### 4. ファイル保護フック
```json
{
  "hooks": {
    "PreToolUse": [
      {
        "matcher": "Edit|MultiEdit|Write",
        "hooks": [
          {
            "type": "command",
            "command": "python3 -c \"import json, sys; data=json.load(sys.stdin); path=data.get('tool_input',{}).get('file_path',''); sys.exit(2 if any(p in path for p in ['.env', 'package-lock.json', '.git/']) else 0)\""
          }
        ]
      }
    ]
  }
}
```

### 5. プロジェクトディレクトリを使用したスクリプト実行
```json
{
  "hooks": {
    "PostToolUse": [
      {
        "matcher": "Write|Edit",
        "hooks": [
          {
            "type": "command",
            "command": "$CLAUDE_PROJECT_DIR/.claude/hooks/check-style.sh"
          }
        ]
      }
    ]
  }
}
```

### 6. コード品質チェック（実践例）
```bash
#!/usr/bin/env bash
# .claude/hooks/check-style.sh

set -euxo pipefail  # デバッグ用の詳細実行トレース

# 環境変数から編集されたファイルを取得
file_path=$(echo "$CLAUDE_FILE_PATHS" | tr ' ' '\n' | head -1)

# ファイル拡張子をチェック
if [[ "$file_path" =~ \.(js|ts|jsx|tsx)$ ]]; then
    # ESLintを実行
    npx eslint "$file_path" --fix
    
    # Prettierでフォーマット
    npx prettier --write "$file_path"
    
    # 型チェック（TypeScriptの場合）
    if [[ "$file_path" =~ \.(ts|tsx)$ ]]; then
        npx tsc --noEmit "$file_path"
    fi
fi
```

## ベストプラクティス

### セキュリティ
1. **シェル変数のクォート**: `"$VAR"`を使用（`$VAR`ではない）
2. **パストラバーサルの防止**: ファイルパスに`..`が含まれていないかチェック
3. **絶対パスの使用**: スクリプトには完全パスを指定
4. **機密ファイルのスキップ**: `.env`、`.git/`、キーファイルなどを避ける

### Bashスクリプトの作成
1. **正しいシェバン**: `#!/usr/bin/env bash`で開始
2. **エラーハンドリング**: `set -euxo pipefail`でデバッグを容易に
3. **手動での再現**: 失敗時はコマンドを手動で実行して検証

### 効率的な実装
1. **小さく集中したフック**: 単一の責任を持つフックを作成
2. **条件付き実行**: ファイルタイプや内容に基づいて処理を分岐
3. **ログ記録**: 重要な操作は記録を残す

## 設定方法

### `/hooks`コマンドでの設定
1. `/hooks`を入力
2. イベントタイプを選択（PreToolUse、PostToolUse等）
3. 対象ツールを指定（必要な場合）
4. フックコマンドを入力
5. 保存場所を選択（User settings/Project settings）

### 設定ファイルでの管理
- プロジェクト単位: `.claude/hooks/`
- ユーザー単位: `~/.claude/hooks/`

## 活用シナリオ

### 1. 開発ワークフロー自動化
- コミット前のLint/Format
- テストの自動実行
- ドキュメント生成

### 2. 品質保証
- コード規約の強制
- セキュリティチェック
- パフォーマンス監視

### 3. チーム連携
- 変更通知
- レビュー自動化
- 進捗トラッキング

## 注意事項

### リスクと責任
- フックは任意のシェルコマンドを自動実行する
- ユーザーアカウントがアクセスできるファイルを変更・削除可能
- 不適切なフックはデータ損失やシステム障害を引き起こす可能性
- Anthropicは保証を提供せず、フック使用による損害に責任を負わない

### ヘッドレスモード
- CI/CDやビルドスクリプトでの使用に対応
- `-p`フラグでプロンプトを指定
- `--output-format stream-json`でJSON形式の出力

## まとめ
Claude Code Hooksは強力な自動化機能だが、適切なセキュリティ対策と慎重な実装が必要。プロジェクトのニーズに合わせて段階的に導入し、チーム全体で共有することで開発効率を大幅に向上させることができる。