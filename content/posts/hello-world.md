+++
date = '2025-12-28T16:54:02+08:00'
draft = false
title = 'Hello World, Hugo 架設'
tags = ["Hugo", "教學"]
+++

這是一篇利用gemini實作架設的hugo

## 第一篇Hugo 文章

macos 安裝
```bash
brew install hugo
```
建立新網站專案
```bash
# 1. 建立新站點 (名稱可自訂，例如 my-portfolio)
hugo new site my-portfolio

# 2. 進入資料夾
cd my-portfolio

# 3. 初始化 Git (這步很重要)
git init
```

安裝主題PaperMod
```bash
# 使用 git submodule 將主題加入 (這樣方便日後更新主題)
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

設定hugo.toml 設定檔
```ini,TOML
baseURL = 'https://你的GitHub帳號.github.io/'
languageCode = 'zh-tw'
title = '我的技術博客'
theme = 'PaperMod'

[params]
  defaultTheme = "auto" # 自動切換深色/淺色模式
  ShowReadingTime = true # 顯示閱讀時間
  ShowShareButtons = true # 顯示分享按鈕
```

撰寫第一篇文章
```bash
# 建立文章
hugo new content posts/hello-world.md
```

本地預覽
```bash
hugo server -D
```
打開瀏覽器輸入  <http://localhost:1313>

就可以看到第一篇hugo 的文章了

## 上傳至github 並使用github action

Github 建立新Repository

Repo 名稱必須是：你的帳號ID.github.io (例如：johndoe.github.io)。

設為 Public。

在你的本地專案資料夾中，建立資料夾結構： .github/workflows/

在裡面新增一個檔案 hugo.yaml。

貼上以下官方標準腳本： 
```yaml
name: Build and deploy
on:
  push:
    branches:
      - main
  workflow_dispatch:
permissions:
  contents: read
  pages: write
  id-token: write
concurrency:
  group: pages
  cancel-in-progress: false
defaults:
  run:
    shell: bash
jobs:
  build:
    runs-on: ubuntu-latest
    env:
      DART_SASS_VERSION: 1.97.1
      GO_VERSION: 1.25.5
      HUGO_VERSION: 0.154.2
      NODE_VERSION: 24.12.0
      TZ: Europe/Oslo
    steps:
      - name: Checkout
        uses: actions/checkout@v5
        with:
          submodules: recursive
          fetch-depth: 0
      - name: Setup Go
        uses: actions/setup-go@v5
        with:
          go-version: ${{ env.GO_VERSION }}
          cache: false
      - name: Setup Node.js
        uses: actions/setup-node@v4
        with:
          node-version: ${{ env.NODE_VERSION }}
      - name: Setup Pages
        id: pages
        uses: actions/configure-pages@v5
      - name: Create directory for user-specific executable files
        run: |
          mkdir -p "${HOME}/.local"
      - name: Install Dart Sass
        run: |
          curl -sLJO "https://github.com/sass/dart-sass/releases/download/${DART_SASS_VERSION}/dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
          tar -C "${HOME}/.local" -xf "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
          rm "dart-sass-${DART_SASS_VERSION}-linux-x64.tar.gz"
          echo "${HOME}/.local/dart-sass" >> "${GITHUB_PATH}"
      - name: Install Hugo
        run: |
          curl -sLJO "https://github.com/gohugoio/hugo/releases/download/v${HUGO_VERSION}/hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
          mkdir "${HOME}/.local/hugo"
          tar -C "${HOME}/.local/hugo" -xf "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
          rm "hugo_extended_${HUGO_VERSION}_linux-amd64.tar.gz"
          echo "${HOME}/.local/hugo" >> "${GITHUB_PATH}"
      - name: Verify installations
        run: |
          echo "Dart Sass: $(sass --version)"
          echo "Go: $(go version)"
          echo "Hugo: $(hugo version)"
          echo "Node.js: $(node --version)"
      - name: Install Node.js dependencies
        run: |
          [[ -f package-lock.json || -f npm-shrinkwrap.json ]] && npm ci || true
      - name: Configure Git
        run: |
          git config core.quotepath false
      - name: Cache restore
        id: cache-restore
        uses: actions/cache/restore@v4
        with:
          path: ${{ runner.temp }}/hugo_cache
          key: hugo-${{ github.run_id }}
          restore-keys:
            hugo-
      - name: Build the site
        run: |
          hugo \
            --gc \
            --minify \
            --baseURL "${{ steps.pages.outputs.base_url }}/" \
            --cacheDir "${{ runner.temp }}/hugo_cache"
      - name: Cache save
        id: cache-save
        uses: actions/cache/save@v4
        with:
          path: ${{ runner.temp }}/hugo_cache
          key: ${{ steps.cache-restore.outputs.cache-primary-key }}
      - name: Upload artifact
        uses: actions/upload-pages-artifact@v3
        with:
          path: ./public
  deploy:
    environment:
      name: github-pages
      url: ${{ steps.deployment.outputs.page_url }}
    runs-on: ubuntu-latest
    needs: build
    steps:
      - name: Deploy to GitHub Pages
        id: deployment
        uses: actions/deploy-pages@v4
```

推送到Github

```Bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/你的帳號ID/你的帳號ID.github.io.git
git push -u origin main
```

回到 GitHub 網頁該 Repo 的 Settings > Pages。

在 Source 處，確認改為 GitHub Actions (原本可能是 Deploy from a branch)。

等待約 1-2 分鐘，Action 跑完變綠色後，打開 https://你的帳號ID.github.io，你的網站就正式上線了！