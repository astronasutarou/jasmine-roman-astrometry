# jasmine-roman-astrometry

## GitHub Pages

このリポジトリは、`main` または `master` ブランチに変更がプッシュされると、MkDocs を使用してドキュメントを自動的にビルドし、GitHub Pages にデプロイするように設定されています。

デプロイワークフローは、Actions タブから手動でトリガーすることもできます。

### 仕組み

1. `main` または `master` ブランチに変更がプッシュされると、ワークフローが自動的に実行されます
2. `requirements.txt` から Python パッケージがインストールされます
3. `docs/` ディレクトリから `mkdocs build` を使用してウェブサイトが生成されます
4. 生成されたサイトが GitHub Pages にデプロイされます

### ドキュメントの更新方法

1. `docs/` ディレクトリ内の markdown ファイルを編集します
2. `mkdocs.yml` を更新してサイト構造とテーマを設定します
3. `main` または `master` ブランチに変更をプッシュします
4. ワークフローが自動的にサイトをビルドしてデプロイします

### ローカル開発

ローカルでサイトをビルドしてプレビューするには：
```bash
pip install -r requirements.txt
mkdocs serve
```