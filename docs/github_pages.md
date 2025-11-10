# GitHub Pages へ学習ノートをデプロイするまでのトライアンドエラー

## 概要

学習ノートを GitHub Pages で公開できるようにするまでの作業を、
実際に行ったプロセスに沿って時系列でまとめた記録。

---

## 1. リポジトリの準備

- GitHub Pages の公式ドキュメントを確認しながら、ノート公開用のリポジトリを新規作成。
- GitHub Pages の設定を以下のように指定：
  - **Branch:** `main`
  - **Publish directory:** `/docs`

その後、作業用として `main` から **work ブランチ**を作成。

---

## 2. push → CI/CD（GitHub Actions）の自動実行

最初のノートを push したところ、GitHub Pages のビルドが自動で走り、**1 回目のエラー**が発生。

### 🔍 エラー内容

```bash
Error: No such file or directory @ dir_chdir0 - /github/workspace/docs
```

### 🔎 対応

- GitHub Pages の設定で `/docs` を公開対象にしたが、リポジトリ内には **doc** になっており、名前が一致していなかった。
- 現状のプロジェクトツリーを local / remote で確認し原因を特定。

### 📝 Issue を作成

- [fix: build_error Githab Page の CI/CD にてエラー #2](https://github.com/linuxfirstMint/web_learning_notes/issues/2)

### 🛠 修正作業

- issue2 ブランチを作成（main をベース）。
- `doc` → `docs` へ修正。
- **issue2 → work → main** とプルリクを順にマージ。

※ このフローが個人作業では少し面倒に感じた。
→ 個人開発では issue ブランチを直接 main から切った方がシンプルかも。

---

## 3. 404 Not Found（トップページ）

`https://linuxfirstmint.github.io/web_learning_notes/` を確認するが **404** のまま。

### 🔍 原因調査

- 公開フォルダ `/docs` に **index.md が存在しない**ことに気づく（index.md はリポジトリ直下）。

### 📝 Issue を作成

- [fix: 404 not found https://linuxfirstmint.github.io/web_learning_notes/ #5](https://github.com/linuxfirstMint/web_learning_notes/issues/5)

### 🛠 修正作業

- main から issue5 ブランチを切る。
- index.md を `/docs` 配下へ移動。
- issue5 → main にプルリクしてマージ。

### ✔ 結果

- トップページが正常に表示されるようになった！

---

## 4. 404 Not Found（個別ノート）

`webhint_viewport` のノートへ移動しようとすると再び 404。

### 🔍 原因調査

- URL が以下になっていた：

```bash
/doc/webhint_viewport.md
```

しかしフォルダ名はすでに `docs` に修正済み。

- index.md 内のアンカーリンクを DevTools で確認したところ、相対パスが古いままだった（doc/〜）。

### 📝 Issue を作成

- [fix: 404 not found https://linuxfirstmint.github.io/web_learning_notes/doc/webhint_viewport.md #7](https://github.com/linuxfirstMint/web_learning_notes/issues/7)

### 🛠 修正作業

- href を `doc/...` → `webhint_viewport.md` に修正。
- ローカルで遷移確認後、main にプルリク。

### ✔ 結果

- 個別ノートも GitHub Pages で表示できるようになった！

---

## 5. 振り返り

- GitHub Pages の公開フォルダ（/docs）とローカルのディレクトリ名の不一致が主な原因だった。
- CI/CD のログ確認・issue 管理・ブランチ運用など、実際の開発の流れに近いと思われる作業を経験できた。
- エラーの起因が構文やパスの設定ミスの場合、指摘してくれるようなツールもしくは ci にてテストが通らない場合はマージできないようにしておくと良いのかも？だが、形式的にあっていても仕様もとい意味的に正しいかどうかは別の問題のため考える必要がありそう。
- 今後も疑問点や改善点は issue として記録しながら進めていく。
- GitHub のブランチ運用と issue 駆動で進める習慣を身に着けたい。

---

## 👍 成果

- GitHub Pages による静的サイト公開の流れを理解できた。
- CI のログを見て原因を特定する経験を得た。

---

### 最後に

[振り返りログ](meta_log/github_pages.txt)の整理は LLM に手伝ってもらいました。
自分で書いた内容を読みやすいように整えてもらっただけで、
実際の学び・気づき・調査はすべて自前です。
