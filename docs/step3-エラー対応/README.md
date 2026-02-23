# Step3: よくあるミスとエラー対応

## このステップの目的

Git操作をしていると、コミットメッセージの間違い、誤ったブランチへのコミット、不要なファイルの追跡など、さまざまなミスが起こります。このステップでは、そうしたよくあるミスへの対処法を学びます。

---

## 演習1: コミットメッセージを間違えた

### 状況

コミットした直後に、メッセージにタイプミスがあることに気づいた。まだpushはしていない。

### 解決方法

```bash
git commit --amend -m "正しいコミットメッセージ"
```

### 実行例

```bash
# 間違ったメッセージでコミットしてしまった
git commit -m "ログイン機能を追加"

# メッセージを修正する
git commit --amend -m "ログイン機能を実装"
```

### 注意点

- `--amend` は直前のコミットだけが対象です
- **既にpush済みのコミットには使わないでください。** リモートの履歴と食い違いが生じます

---

## 演習2: 間違ったブランチにコミットしてしまった

### 状況

`main` ブランチにいることに気づかず、本来 `feature/login` ブランチに入れるべき変更をコミットしてしまった。

### 解決方法1: git stash を使う（コミット前の場合）

```bash
# 変更を一時退避する
git stash

# 正しいブランチに移動する
git checkout feature/login

# 退避した変更を復元する
git stash pop
```

### 解決方法2: git cherry-pick を使う（コミット済みの場合）

```bash
# 移動したいコミットのハッシュを確認する
git log --oneline
# 出力例:
# a1b2c3d ログイン機能を実装   ← このコミットを移動したい

# 正しいブランチに切り替える
git checkout feature/login

# コミットをコピーする
git cherry-pick a1b2c3d

# 元のブランチに戻る
git checkout main

# 間違ったコミットを取り消す
git reset HEAD~1
```

### 補足

- `git reset HEAD~1` はデフォルトで `--mixed` モードです。コミットは取り消されますが、変更はワーキングディレクトリに残ります
- 変更も完全に消したい場合は `git reset --hard HEAD~1` を使いますが、復元できないので注意

---

## 演習3: pushした変更を取り消したい

### 状況

リモートにpush済みの変更に誤りがあった。安全に取り消したい。

### 解決方法: git revert を使う

```bash
# 直前のコミットを打ち消す
git revert HEAD
```

### 実行例

```bash
# 現在のログを確認
git log --oneline
# f7g8h9i バグのある変更   ← これを取り消したい
# a1b2c3d 前回の正常なコミット

# revertを実行
git revert HEAD

# ログを確認（打ち消しコミットが追加されている）
git log --oneline
# j0k1l2m Revert "バグのある変更"
# f7g8h9i バグのある変更
# a1b2c3d 前回の正常なコミット

# リモートにpush
git push origin main
```

### git revert と git reset の違い

| 項目 | git revert | git reset |
|------|-----------|-----------|
| 動作 | 打ち消しコミットを新しく作る | コミット履歴自体を巻き戻す |
| 履歴 | 元のコミットが残る | 元のコミットが消える |
| 安全性 | 安全（履歴を壊さない） | 危険（push済みだと問題が起きる） |
| 用途 | push済みの変更を取り消す | ローカルのみの変更を取り消す |

### チーム開発では revert を推奨

- `git reset` でpush済みの履歴を書き換えると、他のメンバーのローカルリポジトリとの間に不整合が生じます
- `git revert` なら履歴が正しく残るので、何が起きたか全員が追跡できます

---

## 演習4: 不要なファイルをコミットしてしまった

### 予防策: .gitignore ファイルを作成する

```bash
# .gitignore ファイルの例
.env
.env.local
node_modules/
.DS_Store
*.log
dist/
build/
.idea/
.vscode/
```

### よくある .gitignore のパターン

| パターン | 説明 |
|---------|------|
| `.env` | 環境変数ファイル（秘密情報を含むことが多い） |
| `node_modules/` | npmの依存パッケージ（容量が大きい） |
| `.DS_Store` | macOSが自動生成するファイル |
| `*.log` | ログファイル全般 |
| `dist/` `build/` | ビルド成果物 |

### 既にtrackされたファイルを除外する

`.gitignore` に追加しただけでは、既にGitが追跡しているファイルは除外されません。

```bash
# ファイルをGitの追跡から外す（ファイル自体は削除されない）
git rm --cached .env

# ディレクトリの場合は -r オプション
git rm --cached -r node_modules/

# .gitignore に追記してコミット
echo ".env" >> .gitignore
git add .gitignore
git commit -m ".gitignoreを追加し、不要ファイルの追跡を解除"
```

### 注意点

- `git rm --cached` はGitの追跡から外すだけで、ローカルのファイルは削除されません
- ただし **Gitの履歴には残っています。** 秘密情報をコミットしてしまった場合は、APIキーの再発行など追加の対応が必要です

---

## 演習5: mainへの直接push防止

### 状況

チーム開発では `main` ブランチに直接pushすると、レビューされていないコードが反映されるリスクがあります。

### 設定手順

1. GitHubのリポジトリページを開く
2. **Settings** タブをクリック
3. 左メニューから **Branches** を選択
4. 「Branch protection rules」セクションの **Add branch protection rule** をクリック
5. 「Branch name pattern」に `main` と入力

### 「Require a pull request before merging」の設定

この設定を有効にすると、`main` ブランチへの直接pushが禁止されます。

```
$ git push origin main
remote: error: GH006: Protected branch update failed for refs/heads/main.
remote: error: Changes must be made through a pull request.
```

### 「Require approvals」の設定

1. **Require approvals** にチェックを入れる
2. 必要な承認数を設定する（通常は1）
3. 少なくとも1人がPRを承認しないとマージできなくなります

### 推奨設定のまとめ

| 設定項目 | 推奨値 | 理由 |
|---------|--------|------|
| Require a pull request before merging | 有効 | 直接pushを防ぐ |
| Require approvals | 有効（1人以上） | 第三者の目を通す |
| Include administrators | 有効 | 管理者も例外なくルールに従う |

---

## まとめ

| ミスの種類 | 対処法 |
|-----------|--------|
| コミットメッセージの間違い | `git commit --amend -m "正しいメッセージ"` |
| 間違ったブランチにコミット | `git cherry-pick` + `git reset` |
| push済みの変更を取り消したい | `git revert HEAD` |
| 不要なファイルをコミットした | `git rm --cached` + `.gitignore` |
| mainへの直接push防止 | GitHub Branch protection rules |

Git操作でミスをしても、ほとんどの場合は復旧できます。大切なのは、慌てずに `git status` や `git log` で現在の状態を確認してから操作することです。
