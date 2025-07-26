---
description: サブエージェントを使用してPRレビューに自動対応
argument-hint: <owner> <repo> <pull_number>
---

# レビューコメント自動対応

プルリクエストのレビューコメントを3つの専門エージェントで対応：

1. `github-api` - コメント取得と返信
2. `review-responder` - 修正内容の実装
3. `git-operations` - コミットとプッシュ

引数: owner={owner} repo={repo} pr={pull_number}