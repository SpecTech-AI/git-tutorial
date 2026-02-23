# Git/GitHub チュートリアル

Git と GitHub の基本的な使い方を、実践を通じて学ぶチュートリアルリポジトリです。

## このチュートリアルで学べること

| ステップ | 内容 | 学べるスキル |
|----------|------|-------------|
| [Step1: 自己紹介を作ろう](docs/step1-自己紹介/README.md) | 自分のファイルを追加してマージ | branch, add, commit, push, PR, merge |
| [Step2: コンフリクトを体験しよう](docs/step2-コンフリクト/README.md) | 同じファイルを複数人で編集 | conflict解消, pull, merge vs rebase |
| [Step3: よくあるミスとエラー対応](docs/step3-エラー対応/README.md) | Gitでやりがちなミスの対処法 | amend, stash, revert, .gitignore |
| [Step4: チーム開発の流れ](docs/step4-チーム開発/README.md) | Issue → PR → レビュー → マージ | GitHub Flow, Issue, PRテンプレート |

## 前提知識

- ターミナル（コマンドライン）の基本操作ができる
- GitHubアカウントを持っている
- Gitがインストール済み（`git --version` で確認）

## 環境構築

### 1. リポジトリをクローン

```bash
git clone git@github.com:SpecTech-AI/git-tutorial.git
cd git-tutorial
```

### 2. Gitの初期設定（未設定の場合）

```bash
git config --global user.name "あなたの名前"
git config --global user.email "あなたのメールアドレス"
```

### 3. 準備完了！

Step1から順番に進めていきましょう。各ステップのREADMEに手順が書いてあります。

## 進め方

1. **Step1 → Step2 → Step3 → Step4** の順番で進めるのがおすすめです
2. 各ステップにはREADMEに手順が書いてあるので、それに沿って作業してください
3. わからないことがあればチームメンバーに聞いたり、Issueで質問してください
