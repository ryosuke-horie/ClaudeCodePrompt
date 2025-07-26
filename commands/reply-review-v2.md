---
description: サブエージェントを使用してPRレビューに自動対応
argument-hint: <owner> <repo> <pull_number>
---

# レビューコメント自動対応

プルリクエストのレビューコメントを専門エージェントのチェーンで対応：

1. `review-reader` - コメント取得と分析
2. フレームワーク専門エージェント - 修正実装（Hono/Next.js等）
3. `review-responder` - レビューへの返信
4. `git-operations` - コミットとプッシュ

引数: owner={owner} repo={repo} pr={pull_number}