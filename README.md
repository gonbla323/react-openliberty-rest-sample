# React + Open Liberty REST API Sample

## 1. プロジェクト概要

本リポジトリは、React SPAとOpen Libertyを使用したBFF・App構成のサンプルプロジェクトです。

独自REST APIフレームワークを使用したアプリケーション開発の実装例として、プロジェクトの作成方法、開発環境の構築方法、アプリケーションの起動方法を説明します。

### システム構成

```text
React SPA
   |
   | REST API (JSON)
   v
BFF (Open Liberty / Jakarta REST)
   |
   | REST API (JSON)
   v
App (Open Liberty / Jakarta REST)
```

| プロジェクト | 使用技術 | 役割 |
|---|---|---|
| frontend | React / TypeScript / Vite | 画面表示・ユーザー操作 |
| bff | Java 21 / Open Liberty / Jakarta REST | フロントエンド向けAPI |
| app | Java 21 / Open Liberty / Jakarta REST | 業務処理API |

## 2. 開発環境の前提条件

以下のソフトウェアを事前にインストールしてください。

| ソフトウェア | バージョン | ダウンロード先 |
|---|---|---|
| Node.js | 24.x (LTS) | [公式サイト](https://nodejs.org/) |
| Java JDK | 21 | [Eclipse Temurin](https://adoptium.net/) |
| Visual Studio Code | 最新安定版 | [公式サイト](https://code.visualstudio.com/) |

Node.jsにはnpmが含まれます。

BFF・AppのビルドはMaven Wrapperを使用するため、Maven本体の個別インストールは不要です。

### インストール確認

VS Codeのターミナルで以下を実行します。

```powershell
node -v
npm -v
java -version
javac -version
```

Node.js 24.x、Java 21が表示されることを確認してください。

## 3. VS Code拡張機能のインストール

VS Codeの拡張機能画面（`Ctrl + Shift + X`）から、以下をインストールします。

### 必須拡張機能

| 拡張機能 | 用途 |
|---|---|
| [Extension Pack for Java](https://marketplace.visualstudio.com/items?itemName=vscjava.vscode-java-pack) | Java開発、デバッグ、テスト、Maven管理 |
| [Liberty Tools](https://marketplace.visualstudio.com/items?itemName=Open-Liberty.liberty-dev-vscode-ext) | Open Libertyの起動・停止・デバッグ |

Extension Pack for Javaには、Language Support for Java by Red HatやMaven for Javaなどが含まれます。

Liberty Toolsには、MicroProfileやLiberty設定ファイルの編集支援機能も含まれます。

### 推奨拡張機能

| 拡張機能 | 用途 |
|---|---|
| [ESLint](https://marketplace.visualstudio.com/items?itemName=dbaeumer.vscode-eslint) | React / TypeScriptの静的解析 |
| [Prettier](https://marketplace.visualstudio.com/items?itemName=esbenp.prettier-vscode) | ソースコードの自動整形 |

## 4. プロジェクトの作成

以下の3プロジェクトを作成します。

```text
react-openliberty-rest-sample/
├── frontend/
├── bff/
└── app/
```

### 4.1 Reactプロジェクトの作成

Reactのひな形にはViteを使用します。

公式サイト：https://vite.dev/

**STEP 1. 作業フォルダーを作成する**

```powershell
mkdir react-openliberty-rest-sample
cd react-openliberty-rest-sample
```

**STEP 2. ViteでReactプロジェクトを生成する**

```powershell
npm create vite@latest frontend -- --template react-ts
```

React + TypeScriptのひな形が`frontend`フォルダーに生成されます。

**STEP 3. 依存ライブラリをインストールする**

```powershell
cd frontend
npm install
```

**STEP 4. 開発サーバーを起動する**

```powershell
npm run dev
```

ターミナルに表示されたURLをブラウザで開き、Reactの初期画面が表示されることを確認します。

通常は以下のURLです。

http://localhost:5173/

確認後、`Ctrl + C`で終了してください。

### 4.2 BFFプロジェクトの作成

BFFにはOpen Liberty Starterを使用します。

**STEP 1. Open Liberty Starterを開く**

https://openliberty.io/start/

**STEP 2. 以下の項目を設定する**

| 項目 | 設定値 |
|---|---|
| Group | `com.example.sample` |
| Artifact | `bff` |
| Build Tool | Maven |
| Java SE Version | 21 |
| Java EE/Jakarta EE Version | 10.0 |
| MicroProfile Version | 7.0 |

**STEP 3. 「Generate Project」を押す**

`bff.zip`がダウンロードされます。

**STEP 4. ZIPを展開する**

生成されたプロジェクトを作業フォルダーの`bff`配下に配置します。

`bff/pom.xml`となるように配置してください。

### 4.3 Appプロジェクトの作成

BFFと同様にOpen Liberty Starterを使用します。

https://openliberty.io/start/

以下の設定で生成します。

| 項目 | 設定値 |
|---|---|
| Group | `com.example.sample` |
| Artifact | `app` |
| Build Tool | Maven |
| Java SE Version | 21 |
| Java EE/Jakarta EE Version | 10.0 |
| MicroProfile Version | 7.0 |

「Generate Project」を押し、`app.zip`をダウンロードします。

ZIPを展開し、作業フォルダーの`app`配下に配置します。

`app/pom.xml`となるようにしてください。

## 5. Open Libertyのポート設定

BFFとAppを同時に起動するため、HTTP・HTTPSポートが重複しないように設定します。

| プロジェクト | HTTP | HTTPS |
|---|---|---|
| BFF | 9080 | 9443 |
| App | 9081 | 9444 |

それぞれの以下の設定ファイルを確認します。

```text
bff/src/main/liberty/config/server.xml
app/src/main/liberty/config/server.xml
```

App側では、例えば`httpEndpoint`を以下のように設定します。

```xml
<httpEndpoint
    id="defaultHttpEndpoint"
    host="*"
    httpPort="9081"
    httpsPort="9444" />
```

生成された設定ファイルが変数を使用している場合は、対応する変数の定義値を変更してください。

## 6. VS Codeでプロジェクトを開く

VS Codeで`react-openliberty-rest-sample`フォルダーを開きます。

Java拡張機能が`bff`および`app`のMavenプロジェクトを認識することを確認してください。

## 7. アプリケーションの起動

### 7.1 Appの起動

VS Codeのターミナルで実行します。

```powershell
cd app
.\mvnw.cmd liberty:dev
```

Open Libertyがdev modeで起動することを確認します。

### 7.2 BFFの起動

別のターミナルで実行します。

```powershell
cd bff
.\mvnw.cmd liberty:dev
```

BFF側のOpen Libertyが起動することを確認します。

### 7.3 Reactの起動

さらに別のターミナルで実行します。

```powershell
cd frontend
npm run dev
```

ブラウザで表示されたURLにアクセスし、画面が表示されることを確認します。

**注意：** ここまでの手順では各プロジェクトのひな形が起動します。React → BFF → AppのAPI連携は、別途API実装および接続設定が必要です。

## 8. サンプルプロジェクト（ZIP）

本READMEで説明したプロジェクト構成をもとに、独自フレームワークを利用して実装したサンプルプロジェクトを公開しています。

以下からダウンロードできます。

**[サンプルプロジェクトZIPをダウンロード](./downloads/sample-project.zip)**

ZIPにはReact、BFF、Appの各プロジェクトを格納します。

`node_modules`、`target`などの生成物は含めず、ダウンロード後に依存関係を取得してビルドする構成とします。
