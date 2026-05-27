<div align="center">
  <h1>Release2Choco</h1>
  <p><b>Build a personal Chocolatey feed from GitHub Releases.</b></p>
  <a href="#japanese-日本語">日本語の説明はこちら</a>
</div>

## Overview

Release2Choco watches the GitHub repositories you list, selects likely Windows release assets, builds Chocolatey packages, and publishes them to your MyGet feed.
You can then use that feed from Chocolatey-compatible clients such as UniGetUI.

This is useful when an app is distributed only through GitHub Releases, or when it is not available in the public Chocolatey or winget repositories.

### How it works

1. Add a MyGet API key and MyGet push source URL to this repository.
2. Add GitHub repository URLs to the `TARGET_URLS` GitHub Actions variable.
3. GitHub Actions checks those repositories on a schedule.
4. The workflow picks likely Windows assets, generates Chocolatey packages, and pushes the resulting `.nupkg` files to MyGet.
5. Your MyGet feed can be registered in Chocolatey or UniGetUI as a package source.

### Features

* **GitHub Releases input:** Tracks the repositories listed in `TARGET_URLS`.
* **Heuristic asset selection:** Scores release assets and prefers likely Windows GUI installers or packages such as `.msixbundle`, `.appx`, `.msi`, `.exe`, `.zip`, and `.7z`.
* **Portable archive support:** Extracts `.zip` and `.7z` archives, looks for a likely main GUI executable, and creates a Start Menu shortcut.
* **Installer fallback:** If an extracted archive appears to contain an installer such as `setup.exe`, the generated package can run it instead of treating the archive as a portable app.
* **GitHub API hash support:** Uses release asset metadata when available to avoid unnecessary large downloads during package generation.
* **Configurable MyGet destination:** The MyGet push URL is stored in the `MYGET_PUSH_SOURCE_URL` repository variable instead of being hard-coded in the workflow.
* **Scheduled automation:** Runs every 3 hours by default and includes a keepalive commit to reduce the chance of scheduled GitHub Actions being disabled after long inactivity.

### Scope and limitations

Release2Choco does not replace Chocolatey, MyGet, winget, or UniGetUI. It generates Chocolatey packages from GitHub Releases and publishes them to a MyGet feed.

Asset selection is heuristic. It works best for projects that publish conventional Windows release assets with clear filenames. Some projects may need script changes or manual review.

### Setup

#### 1. Create a MyGet feed

1. Create a MyGet account and a feed.
2. Open `Feed Settings` > `Access Tokens (API Keys)`.
3. Generate a Full Access API key.

#### 2. Configure GitHub Secrets and Variables

Open this repository on GitHub, then go to `Settings` > `Secrets and variables` > `Actions`.

Create this repository secret:

```text
MYGET_API_KEY
```

Create this repository variable for the MyGet push source:

```text
MYGET_PUSH_SOURCE_URL=https://www.myget.org/F/YOUR-FEED-NAME/api/v2/package
```

Create this repository variable with one GitHub repository URL per line:

```text
TARGET_URLS=https://github.com/jlcodes99/cockpit-tools
https://github.com/koala73/worldmonitor
```

#### 3. Register the feed on a client PC

Run this in an elevated PowerShell, replacing `YOUR-FEED-NAME` with your MyGet feed name:

```powershell
choco source add -n="MyGetCustomFeed" -s="https://www.myget.org/F/YOUR-FEED-NAME/api/v2"
```

The GitHub Actions push URL ends with `/api/v2/package`.
The Chocolatey or UniGetUI source URL ends with `/api/v2`.

---

<h2 id="japanese-日本語">Overview (日本語)</h2>

Release2Choco は、指定した GitHub リポジトリの Releases を監視し、Windows 向けと思われる配布ファイルを選び、Chocolatey パッケージを生成して MyGet feed に公開します。
作成された feed は、UniGetUI などの Chocolatey 互換クライアントから利用できます。

GitHub Releases だけで配布されている Windows アプリや、Chocolatey / winget の公式リポジトリにないアプリを、自分用の更新ソースとして扱いたい場合に使います。

### 仕組み

1. MyGet の API key と push 先 URL を、このリポジトリに設定します。
2. GitHub Actions の `TARGET_URLS` variable に対象リポジトリの URL を並べます。
3. GitHub Actions が定期的に対象リポジトリの Releases を確認します。
4. Windows 向けと思われる asset を選び、Chocolatey パッケージを生成し、`.nupkg` を MyGet に push します。
5. 作成された MyGet feed を Chocolatey や UniGetUI の source として登録できます。

### 主な機能

* **GitHub Releases を入力にする:** `TARGET_URLS` に並べたリポジトリを監視します。
* **ヒューリスティックな asset 選択:** `.msixbundle`, `.appx`, `.msi`, `.exe`, `.zip`, `.7z` など、Windows GUI アプリらしい配布ファイルをスコアリングして選びます。
* **ポータブル archive 対応:** `.zip` / `.7z` を展開し、メインの GUI 実行ファイルと思われるものを探して、スタートメニュー shortcut を作ります。
* **installer fallback:** 展開後の中身が `setup.exe` などの installer に見える場合は、ポータブル配置ではなく installer 実行に切り替えられます。
* **GitHub API hash 利用:** 利用できる場合は GitHub Releases API の asset metadata を使い、パッケージ生成時の大きなファイル download を避けます。
* **MyGet 宛先を variable で管理:** MyGet push URL は workflow に直書きせず、`MYGET_PUSH_SOURCE_URL` repository variable で変更できます。
* **定期実行:** デフォルトでは3時間ごとに実行します。長期間 commit がない repository で scheduled GitHub Actions が止まりにくいよう、keepalive commit も含めています。

### 範囲と制限

Release2Choco は Chocolatey、MyGet、winget、UniGetUI の代替ではありません。
GitHub Releases から Chocolatey パッケージを作り、MyGet feed に公開するための自動化テンプレートです。

asset 選択はヒューリスティックです。
分かりやすい名前の Windows 向け release asset を公開しているプロジェクトでは動きやすいですが、プロジェクトによってはスクリプト修正や手動確認が必要です。

### 使い方

#### 1. MyGet feed を作る

1. MyGet アカウントと feed を作成します。
2. `Feed Settings` > `Access Tokens (API Keys)` を開きます。
3. Full Access API key を生成します。

#### 2. GitHub Secrets and Variables を設定する

GitHub 上のこのリポジトリで、`Settings` > `Secrets and variables` > `Actions` を開きます。

repository secret を作成します:

```text
MYGET_API_KEY
```

MyGet の push 先 URL を repository variable に設定します:

```text
MYGET_PUSH_SOURCE_URL=https://www.myget.org/F/YOUR-FEED-NAME/api/v2/package
```

対象リポジトリの URL を、1行に1つずつ repository variable に設定します:

```text
TARGET_URLS=https://github.com/jlcodes99/cockpit-tools
https://github.com/koala73/worldmonitor
```

#### 3. クライアント PC に feed を登録する

管理者権限の PowerShell で以下を実行します。
`YOUR-FEED-NAME` は自分の MyGet feed 名に置き換えてください。

```powershell
choco source add -n="MyGetCustomFeed" -s="https://www.myget.org/F/YOUR-FEED-NAME/api/v2"
```

GitHub Actions で package を push する URL は `/api/v2/package` で終わります。
Chocolatey / UniGetUI に source として登録する URL は `/api/v2` で終わります。
