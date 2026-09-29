# mycoder GitHub Action

English | [繁體中文](#mycoder-github-action-繁體中文)

A GitHub Action that integrates [mycoder](https://mycoder.afs-suite.ai) directly into your GitHub workflow.

Mention `/mycoder` in your comment, and mycoder will execute tasks within your GitHub Actions runner.

## Features

#### Explain an issue

Leave the following comment on a GitHub issue. `mycoder` will read the entire thread, including all comments, and reply with a clear explanation.

```
/mycoder explain this issue
```

#### Fix an issue

Leave the following comment on a GitHub issue. mycoder will create a new branch, implement the changes, and open a PR with the changes.

```
/mycoder fix this
```

#### Review PRs and make changes

Leave the following comment on a GitHub PR. mycoder will implement the requested change and commit it to the same PR.

```
Delete the attachment from S3 when the note is removed /mycoder
```

#### Review specific code lines

Leave a comment directly on code lines in the PR's "Files" tab. mycoder will automatically detect the file, line numbers, and diff context to provide precise responses.

```
[Comment on specific lines in Files tab]
/mycoder add error handling here
```

When commenting on specific lines, mycoder receives:

- The exact file being reviewed
- The specific lines of code
- The surrounding diff context
- Line number information

This allows for more targeted requests without needing to specify file paths or line numbers manually.

## Installation

Run the following command in the terminal from your GitHub repo:

```bash
mycoder github install
```

This will walk you through selecting a provider and model, creating the workflow, and setting up secrets. No GitHub app is required: the workflow uses the runner's built-in `GITHUB_TOKEN`.

### Manual Setup

1. Add the following workflow file to `.github/workflows/mycoder.yml` in your repo. Set the appropriate `model` and required API keys in `env`.

   ```yml
   name: mycoder

   on:
     issue_comment:
       types: [created]
     pull_request_review_comment:
       types: [created]

   jobs:
     mycoder:
       if: |
         contains(github.event.comment.body, ' /mycoder') ||
         startsWith(github.event.comment.body, '/mycoder')
       runs-on: ubuntu-latest
       permissions:
         contents: write
         pull-requests: write
         issues: write
       steps:
         - name: Checkout repository
           uses: actions/checkout@v6
           with:
             persist-credentials: false

         - name: Run mycoder
           uses: reneechang-twsc/mycoder-action-test@master
           env:
             GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
             MYCODER_AFS_API_KEY: ${{ secrets.MYCODER_AFS_API_KEY }}
             MYCODER_AFS_API_URL: ${{ secrets.MYCODER_AFS_API_URL }}
           with:
             model: mycoder/anthropicModel/claude-sonnet-5
             use_github_token: true
   ```

2. Store the API keys in secrets. In your organization or project **settings**, expand **Secrets and variables** on the left and select **Actions**. Add the required API keys.

3. Allow `GITHUB_TOKEN` to open pull requests. In your repository **Settings**, go to **Actions** → **General** → **Workflow permissions**, select **Read and write permissions**, and check **Allow GitHub Actions to create and approve pull requests**. If the option is disabled, enable it first in your organization's **Settings** → **Actions** → **General**. Otherwise opening a PR fails with `GitHub Actions is not permitted to create or approve pull requests`.

   Pull requests opened with `GITHUB_TOKEN` do not trigger other workflows, such as CI. Use a personal access token as `GITHUB_TOKEN` if you need them to.

---

# mycoder github action 繁體中文

[English](#mycoder-github-action) | 繁體中文

這是一個 GitHub Action，可以把 [mycoder](https://mycoder.afs-suite.ai) 直接整合進你的 GitHub 工作流程。

在留言中提到 `/mycoder`，mycoder 就會在你的 GitHub Actions runner 裡執行任務。

### 功能

#### 解釋 issue

在 GitHub issue 下留下以下留言。`mycoder` 會讀完整個討論串（包含所有留言），並回覆清楚的說明。

```
/mycoder explain this issue
```

#### 修復 issue

在 GitHub issue 下留下以下留言。mycoder 會建立新分支、實作修改，並用這些修改開一個 PR。

```
/mycoder fix this
```

#### 審查 PR 並修改

在 GitHub PR 下留下以下留言。mycoder 會實作你要求的修改，並 commit 到同一個 PR。

```
Delete the attachment from S3 when the note is removed /mycoder
```

#### 審查特定程式碼行

在 PR 的「Files」分頁中，直接對程式碼行留言。mycoder 會自動偵測檔案、行號和 diff 上下文，給出精準的回應。

```
[在 Files 分頁中對特定行留言]
/mycoder add error handling here
```

對特定行留言時，mycoder 會收到：

- 正在審查的檔案
- 那幾行程式碼
- 周圍的 diff 上下文
- 行號資訊

這樣你提出的要求可以更有針對性，不需要手動指定檔案路徑或行號。

### 安裝

在你的 GitHub repo 目錄下，於終端機執行以下指令：

```bash
mycoder github install
```

它會一步步引導你選擇 provider 和 model、建立 workflow，以及設定 secrets。不需要安裝 GitHub App，workflow 會直接使用 runner 內建的 `GITHUB_TOKEN`。

#### 手動設定

1. 把以下 workflow 檔案加到 repo 的 `.github/workflows/mycoder.yml`。在 `env` 中設定適當的 `model` 和所需的 API key。

   ```yml
   name: mycoder

   on:
     issue_comment:
       types: [created]
     pull_request_review_comment:
       types: [created]

   jobs:
     mycoder:
       if: |
         contains(github.event.comment.body, ' /mycoder') ||
         startsWith(github.event.comment.body, '/mycoder')
       runs-on: ubuntu-latest
       permissions:
         contents: write
         pull-requests: write
         issues: write
       steps:
         - name: Checkout repository
           uses: actions/checkout@v6
           with:
             persist-credentials: false

         - name: Run mycoder
           uses: reneechang-twsc/mycoder-action-test@master
           env:
             GITHUB_TOKEN: ${{ secrets.GITHUB_TOKEN }}
             MYCODER_AFS_API_KEY: ${{ secrets.MYCODER_AFS_API_KEY }}
             MYCODER_AFS_API_URL: ${{ secrets.MYCODER_AFS_API_URL }}
           with:
             model: mycoder/anthropicModel/claude-sonnet-5
             use_github_token: true
   ```

2. 把 API key 存成 secrets。在組織或專案的 **Settings** 中，展開左側的 **Secrets and variables**，選擇 **Actions**，然後加入所需的 API key。

3. 允許 `GITHUB_TOKEN` 開 PR。到 repo 的 **Settings** → **Actions** → **General** → **Workflow permissions**，選擇 **Read and write permissions**，並勾選 **Allow GitHub Actions to create and approve pull requests**。如果這個選項無法勾選，請先到組織的 **Settings** → **Actions** → **General** 開啟。沒有開啟的話，開 PR 時會出現 `GitHub Actions is not permitted to create or approve pull requests` 錯誤。

   用 `GITHUB_TOKEN` 開的 PR 不會觸發其他 workflow（例如 CI）。如果需要觸發，請改用 personal access token 作為 `GITHUB_TOKEN`。
