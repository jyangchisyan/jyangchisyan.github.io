+++
date = '2025-12-28T16:54:02+08:00'
draft = false
title = 'Hello World, Hugo 架設'
tags = ["Hugo", "教學"]
description = '使用 Hugo 與 PaperMod 建立技術部落格，並部署到 GitHub Pages 的入門實作紀錄。'
keywords = ['Hugo', 'PaperMod', 'GitHub Pages', '靜態網站', '教學']

 [cover]
image = '/og/hello-world-hugo.png'
+++

如果你想用一個簡單、好維護、又不需要後端伺服器的方式建立個人技術部落格，Hugo 是很適合的起點。

這篇文章會帶你完成一個最小可用版本的流程：

1. 安裝 Hugo
2. 建立新網站
3. 套用 `PaperMod` 主題
4. 寫出第一篇文章
5. 本機預覽
6. 部署到 GitHub Pages

做完之後，你會得到一個可以公開瀏覽的靜態 blog。

## 什麼是 Hugo

Hugo 是一個靜態網站產生器。你可以把它理解成：

- 內容用 Markdown 寫
- 版型用 theme 管理
- 最後輸出成一組純 HTML、CSS、JS 檔案

這種架構的好處是部署簡單、速度快，而且維護成本低。

## 開始前需要準備什麼

這篇教學預設你使用 macOS，並且已經有：

- `Homebrew`
- `Git`
- 一個 GitHub 帳號

如果你的目標是部署到 GitHub Pages，Repository 名稱建議直接使用 `你的帳號.github.io`。

## 第一步：安裝 Hugo

先用 Homebrew 安裝 Hugo：

```bash
brew install hugo
```

安裝完成後，可以檢查版本是否正常：

```bash
hugo version
```

如果你看到版本資訊，代表 Hugo 已經可用。

## 第二步：建立新的網站專案

接著建立一個新的 Hugo 站點：

```bash
hugo new site my-portfolio
cd my-portfolio
git init
```

這三行分別在做這些事：

- `hugo new site my-portfolio`：建立 Hugo 專案骨架
- `cd my-portfolio`：進入專案資料夾
- `git init`：初始化 Git，方便之後管理版本與部署

## 第三步：安裝主題 PaperMod

Hugo 本身負責網站生成，但外觀通常會交給 theme 管理。這裡使用的是 `PaperMod`：

```bash
git submodule add https://github.com/adityatelange/hugo-PaperMod.git themes/PaperMod
```

這裡使用 `git submodule` 的原因是：

- 主題可以獨立更新
- 不會把 theme 原始碼直接混進你的內容檔案
- 比較適合長期維護

## 第四步：設定 `hugo.toml`

專案根目錄會有一個 `hugo.toml`，這是 Hugo 的主要設定檔。先放一個最基本可運作的版本：

```toml
baseURL = 'https://你的GitHub帳號.github.io/'
languageCode = 'zh-tw'
title = '我的技術博客'
theme = 'PaperMod'

[params]
  defaultTheme = "auto"
  ShowReadingTime = true
  ShowShareButtons = true
```

這幾個欄位的用途如下：

- `baseURL`：網站正式上線後的網址
- `languageCode`：網站語系
- `title`：網站標題
- `theme`：指定使用哪個主題
- `params`：交給 theme 使用的自訂參數

如果你只是先在本機測試，`baseURL` 先填預計的正式網址即可，不影響本地開發。

## 第五步：建立第一篇文章

用 Hugo 內建指令建立文章：

```bash
hugo new content posts/hello-world.md
```

建立後，你會看到一個 Markdown 檔案。內容大概會長這樣：

```toml
+++
date = '2025-01-01T10:00:00+08:00'
draft = true
title = 'Hello World'
+++
```

你可以把 `draft = true` 改成 `false`，然後開始寫內容，例如：

```md
+++
date = '2025-01-01T10:00:00+08:00'
draft = false
title = 'Hello World'
+++

這是我的第一篇 Hugo 文章。
```

## 第六步：本地預覽網站

文章寫完後，啟動本地開發伺服器：

```bash
hugo server -D
```

然後打開瀏覽器進入：

<http://localhost:1313>

這時你應該就能看到網站首頁，以及剛剛新增的文章。

補充一下，`-D` 的意思是連 `draft` 文章也一起顯示。這對寫文章時很方便。

## 第七步：部署到 GitHub Pages

如果你希望網站公開上線，最簡單的方式就是搭配 GitHub Pages。

先在 GitHub 建立一個新的 Repository，名稱建議為：

```text
你的帳號.github.io
```

例如：

```text
johndoe.github.io
```

Repository 建議設為 `Public`。

## 第八步：加入 GitHub Actions 部署流程

在專案中建立 `.github/workflows/hugo.yml`，放入以下設定：

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

這份 workflow 的用途很直接：

- 每次推送到 `main` 分支時，自動建置網站
- 把 Hugo 產生出的 `public/` 內容部署到 GitHub Pages

如果你現在還不熟 GitHub Actions，不需要一行一行背下來。先把它當成「自動部署腳本」即可。

## 第九步：把專案推上 GitHub

接著把本地專案推上 GitHub：

```bash
git add .
git commit -m "Initial commit"
git branch -M main
git remote add origin https://github.com/你的帳號ID/你的帳號ID.github.io.git
git push -u origin main
```

推送完成後，回到 GitHub 頁面，打開：

```text
Settings > Pages
```

確認 Source 使用的是 `GitHub Actions`，而不是 `Deploy from a branch`。

之後等待 1 到 2 分鐘，等 workflow 跑完，你就可以打開：

```text
https://你的帳號ID.github.io
```

如果一切正常，網站就正式上線了。

## 常見問題

### 網站打不開怎麼辦

先檢查這三件事：

- Repository 名稱是不是 `你的帳號.github.io`
- GitHub Pages 的 Source 是否設為 `GitHub Actions`
- Actions workflow 是否有成功跑完

### 明明有文章，首頁卻看不到

通常是下面兩種原因：

- 文章還是 `draft = true`
- 啟動本機預覽時沒有加 `-D`

### `baseURL` 填錯會怎樣

如果 `baseURL` 不是正式網址，部署後有些連結、RSS、分享圖網址可能會錯掉。所以在上線前要改成正確值。

## 結語

如果你的目標是快速建立一個技術 blog，Hugo 是很務實的選擇。它的學習曲線不算低，但一旦把專案骨架搭起來，之後新增文章、換主題、部署上線都會很順。

第一次先不要追求做得很完整。把網站建起來、能寫文章、能成功部署，這三件事完成就夠了。等你熟悉之後，再慢慢補搜尋、評論、SEO 和自訂版型。
