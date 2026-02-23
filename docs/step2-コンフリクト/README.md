# Step2: コンフリクトを体験しよう

## このステップの目的

- Gitでコンフリクト（競合）が発生する原因を理解する
- コンフリクトが起きたときに、落ち着いて解消できるようになる

## コンフリクトとは？

複数人が **同じファイルの同じ箇所** を別々に変更し、それぞれの変更をマージしようとすると、Gitはどちらの変更を採用すればよいか判断できません。この状態を **コンフリクト（競合）** と呼びます。

コンフリクトは怖いものではありません。Gitが「ここは自動でマージできないから、人間が判断してね」と教えてくれているだけです。

---

## 演習手順

### 1. mainブランチから自分のブランチを作成する

```bash
git checkout main
git pull origin main
git checkout -b <自分の名前>/team-profile
```

例: `git checkout -b tanaka/team-profile`

### 2. ファイルを編集する

`共同編集/team-profile.md` を開き、「好きな技術」セクションに **自分の項目を追加** してください。

```markdown
## 好きな技術

- React（田中）
- Python（佐藤）
- TypeScript（あなたの名前）   ← ここに追加！
```

### 3. コミットしてプッシュする

```bash
git add 共同編集/team-profile.md
git commit -m "<自分の名前>: 好きな技術を追加"
git push origin <自分の名前>/team-profile
```

### 4. Pull Requestを作成する

GitHub上でPull Requestを作成し、mainブランチへのマージをリクエストしてください。

### 5. マージの結果

- **先にマージした人** → 問題なくマージされます
- **後からマージしようとした人** → コンフリクトが発生します

これは正常な動作です。次のステップでコンフリクトを解消しましょう。

---

## コンフリクトの解消手順

### Step 5-1. mainの最新変更を取り込む

```bash
git checkout <自分の名前>/team-profile
git pull origin main
```

すると、以下のようなメッセージが表示されます:

```
Auto-merging 共同編集/team-profile.md
CONFLICT (content): Merge conflict in 共同編集/team-profile.md
Automatic merge failed; fix conflicts and then commit the result.
```

### Step 5-2. コンフリクトマーカーを確認する

ファイルを開くと、以下のようなマーカーが挿入されています:

```
<<<<<<< HEAD
- TypeScript（鈴木）
=======
- Go（山田）
>>>>>>> origin/main
```

| マーカー | 意味 |
|---|---|
| `<<<<<<< HEAD` | 自分のブランチの変更（ここから） |
| `=======` | 区切り線 |
| `>>>>>>> origin/main` | mainブランチの変更（ここまで） |

### Step 5-3. 手動で解消する

コンフリクトマーカー（`<<<<<<<`、`=======`、`>>>>>>>`）を **すべて削除** し、正しい内容に書き換えます。

両方の変更を残す場合の例:

```markdown
- TypeScript（鈴木）
- Go（山田）
```

### Step 5-4. 解消をコミットしてプッシュする

```bash
git add 共同編集/team-profile.md
git commit -m "コンフリクトを解消"
git push origin <自分の名前>/team-profile
```

これでPull Requestが再びマージ可能になります。

---

## 補足: `git merge` と `git rebase` の違い

コンフリクトを解消する方法には、主に **merge** と **rebase** の2つがあります。

### git merge（今回使った方法）

```bash
git pull origin main
# = git fetch + git merge origin/main
```

- mainの変更を **マージコミット** として取り込む
- 履歴が分岐・合流の形で残る
- 初心者にはこちらがおすすめ

### git rebase

```bash
git pull --rebase origin main
# = git fetch + git rebase origin/main
```

- 自分のコミットをmainの最新の上に **積み直す**
- 履歴が一直線になり、きれいに見える
- コンフリクト解消を各コミットごとに行う必要がある場合がある

### どちらを使うべき？

| 状況 | おすすめ |
|---|---|
| チーム開発で初心者が多い | `merge` |
| 履歴をきれいに保ちたい | `rebase` |
| すでにpush済みのブランチ | `merge`（rebaseは履歴を書き換えるため注意） |

まずは `merge` に慣れてから、`rebase` に挑戦してみましょう。
