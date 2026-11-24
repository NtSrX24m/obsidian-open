# obsidian-redirect

Discord などのカスタム URI スキーム（`obsidian://...`）がリンク化・マスクリンク化できないチャット／メッセージツールから、安全かつ簡潔に Obsidian の特定ノートを開くための**単一ファイル・クライアントサイド完結型リダイレクトページ**です。

## 概要

チャット上では通常の `https://` マスクリンクとして共有し、リンクを開いたブラウザ側の JavaScript で即座に `obsidian://open?vault=...&file=...` へ遷移します。

```markdown
[Notes/Example](<https://<your-username>.github.io/obsidian-open/#vault=MyVault&file=Notes%2FExample>)
```

## URL 形式

* **推奨（ハッシュフラグメント形式）**:
  ```text
  https://<your-username>.github.io/<repo-name>/#vault=<VaultName>&file=<EncodedFilePath>
  ```
* **互換（クエリパラメータ形式）**:
  ```text
  https://<your-username>.github.io/<repo-name>/?vault=<VaultName>&file=<EncodedFilePath>
  ```

| パラメータ | 説明 | 例（エンコード後） |
| :--- | :--- | :--- |
| `vault` | 開く対象の Obsidian Vault 名 | `MyVault` |
| `file` | Vault 内の相対ノートパス（`.md` 省略可） | `Notes%2FExample` |

## プライバシーとセキュリティ設計

1. **サーバーへノート名を送信しない設計**:
   * `#`（URL フラグメント）以降の文字列は HTTP リクエストに含まれず、Web サーバーやホスティング先のアクセスログに Vault 名・ノート名が残りません。
   * 解析はすべて閲覧者のブラウザ内の JavaScript で完結し、外部 API・CDN・トラッキングは一切使用していません。
2. **オープンリダイレクト防止**:
   * 遷移先のスキームは `obsidian://open?vault=...&file=...` のみに固定されており、任意の外部 URL へリダイレクトされることはありません。
3. **検索エンジン除外**:
   * `<meta name="robots" content="noindex, nofollow">` を設定しています。
4. **モバイルアプリ内ブラウザ対応**:
   * 自動リダイレクトがブロックされた場合でも、1 タップで開けるフォールバックボタン（「Obsidian で開く」）を表示します。

## セルフホスト手順（GitHub Pages）

1. このディレクトリ内の `index.html`（および任意でこの `README.md`）を新しい GitHub リポジトリに配置します。
2. リポジトリの **Settings → Pages** を開き、**Build and deployment** の Source を `Deploy from a branch`、Branch を `main` / `/ (root)` に設定して保存します。
3. 発行された `https://<your-username>.github.io/<repo-name>/` の末尾に `#vault=...&file=...` を付けて利用します。
