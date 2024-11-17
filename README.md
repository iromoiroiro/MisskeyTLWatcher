# Misskey Timeline Watcher
- misskey.io の Timeline を定期的に監視し、何らかのメディアを含むノートを Slack に通知するためのツールです
- 以下のツールを使用します
    - Google Apps Script
    - Google Drive (Spread Sheet)
    - Slack

## 注意書き
- 本ツール及び手順書は、使用者の責任で内容を確認して使用してください
- 疑問点などは作者までご連絡ください
    - https://x.com/iromoiro
    - https://bsky.app/profile/iromoiro.bsky.social
    - https://misskey.io/@iromoiro
 
## 1. Slack の Incoming Webhooks URL を取得
- Slack への通知に、Incoming Webhooks を利用します
    - このために、専用の URL を発行する必要があります 

1. 通知用の Slack チャンネルを作成
2. 通知用の Slack App を作成
    - https://api.slack.com/apps/new をクリックし、`From scratch` を選択
         - ![image](https://github.com/user-attachments/assets/764dde2c-ed21-4ad5-b41b-99120934ab8b)
    - `App Name` (アプリ名) は自由に入力
    - `Pick a workspace to develop your app in` には、通知先の Slack workspace を選択
    - `Create App` でアプリ作成
        - ![image](https://github.com/user-attachments/assets/1cbeb2ad-f0dd-483a-9b72-57c9eb84fe1b)
3. Incoming Webhooks URL を取得
    - 上記操作後、以下のようなページに遷移します
    - 左メニューの `Incoming Webhooks` を選択してください
        - ![image](https://github.com/user-attachments/assets/376eaa7d-4973-4cea-abea-d3b62a0b9c32)
    - 右上のトグルをクリックし、`On` の状態にしてください
        - ![image](https://github.com/user-attachments/assets/7314f117-4286-4367-9dcd-f982c5e60016)
    - ページ下部の `Add New Webhook to Workspace` をクリックします
        - ![image](https://github.com/user-attachments/assets/9d1ec77c-a441-4a81-9a8e-85fd65368f65)
    - 通知先のチャンネルを選択し、`許可する` をクリックします
        - ![image](https://github.com/user-attachments/assets/51843cbb-ab48-4e51-bd26-8111d0987ea0)
    - 以下のページに遷移します
    - ページ下部の `Webhook URL` に表示されているのが、Webhooks URL です
        - `Copy` ボタンをクリックするとクリップボードにコピーされます
        - 後々使います
    - **Webhooks URL の管理は厳重に行ってください。これが流出した場合、第三者があなたのチャンネルに自由にメッセージを投稿できます。もし漏れた場合、チャンネル名の右にあるゴミ箱ボタンから無効化してください**
        - ![image](https://github.com/user-attachments/assets/b924ad6c-ead4-49da-9328-5de602854be7)
     

## 2. Google Apps Script の作成
- TL 監視に必要な情報を Spreadsheet に記載し、定期実行は Google Apps Script の仕組みで行います

1. Google Spreadsheet 作成
    - 適当なスプレッドシートを作成してください
    - **このスプレッドシートには、あなたの Misskey 認証情報が記載されます。他人と共有するシートは選択しないでください** 
2. 上部のメニューから `拡張機能 > Apps Script` を選択してください
    - ![image](https://github.com/user-attachments/assets/b2be1894-0a32-48db-9f5d-cac40e24db07)
3. 以下のようなページ (Apps Script の編集ページ) に遷移します
    - ![image](https://github.com/user-attachments/assets/d8ef5f62-eb3a-45c4-a2b2-c472c64d3854)
4. Apps Script のエディタ部分に、https://github.com/iromoiroiro/MisskeyTLWatcher/blob/main/code.gas の内容を全てコピペし、保存してください
    - ![image](https://github.com/user-attachments/assets/af1ef59e-0511-4e35-8712-0f084d64684b)
5. 1 行目で `__HERE__` となっている部分を、あらかじめ取得した Webhooks URL に置換してください
    - ![image](https://github.com/user-attachments/assets/6e7fcbb1-c2f9-4ecc-ae6e-3ca2759f348c)

## 3. Misskey アカウントの認証
- 定期監視に必要な認証情報 (トークン) を取得します
- **注意**
    - 以下の操作では、Apps Script に「あなたの Google アカウントが持っている情報・権限」へのアクセス許可を行います
    - 以下の操作では、Apps Script に「あなたの misskey.io アカウントのアカウント情報」へのアクセス許可を行います
    - **少しでも不安・疑問点がある場合、操作を中止して信頼のおける第三者に確認してもらってください**


1. 実行する関数を `CheckTimeline` に設定し、`実行` ボタンを押してください
    - ![image](https://github.com/user-attachments/assets/5c25528e-2909-43f6-ad93-e548046fa117)
2. いくつかの機能を使用するため、権限の設定を行います
    - `権限を確認` をクリックしてください
        - ![image](https://github.com/user-attachments/assets/b1d72915-7172-4114-adaa-236dfc0542e3)
    - 自身のアカウントを選択してください
        - ![image](https://github.com/user-attachments/assets/ad81bcaf-de1c-4ff6-ab81-b42ee22cd52b)
    - 左下の `詳細` をクリックしてください
        - ![image](https://github.com/user-attachments/assets/50d96f73-bd63-43d9-a864-5a56f2c4907a)
    - 下部の `無題のプロジェクト (安全ではないページ) に移動` をクリックしてください
        - ![image](https://github.com/user-attachments/assets/24aeb179-0528-4df1-8f1a-4283818b8e15)
    - 内容を確認し、`許可` を押してください
        - `Google スプレッドシートの...`：スプレッドシートシートに情報を記録するために使用します
        - `外部サービスへの接続`：Misskey へのアクセスに使用します
        - ![image](https://github.com/user-attachments/assets/1f1964fb-79fc-433c-92c3-9003a9ca7e7a)
3. Apps Script 及び Spreadsheet のタブを確認してください
    - 正しく実行されている場合、Spreadsheet のタブに以下のメッセージボックスが表示されます
        - ![image](https://github.com/user-attachments/assets/607c67d0-04bc-4039-9ffc-88625238deb5)
    - URL をコピーし、ブラウザの別タブで開いてください
    - **メッセージボックスの OK はクリックしないでください**
4. Misskey の認証画面が表示されたら、アカウントを選択して `続ける` をクリックしてください
    - ![image](https://github.com/user-attachments/assets/3be7f0cb-8752-4f19-bd1b-d4efa0374cc9)
5. 内容を確認して `許可` をクリックしてください
    - これは、あなたのタイムライン情報を取得するための権限です `read:account`
    - ![image](https://github.com/user-attachments/assets/d7e451cb-4be8-429b-9903-e2149a256353)
6. 以下の画面が表示されたら、Spreadsheet のタブに戻り、メッセージボックスの `OK` をクリックしてください
    - ![image](https://github.com/user-attachments/assets/36edecb4-2b15-40d3-84a7-f234d5acbad1)
7. 以下の表示が出たことを確認し、`OK` をクリックしてください
    - ![image](https://github.com/user-attachments/assets/98bcc642-c1fe-4320-8931-1406829a40de)
8. スプレッドシートの A1 に文字列が記載されていると思います
    - **この文字列は厳重に管理してください**
        - 仮にこの文字列が流出した場合、第三者があなたのアカウント情報を読み出すことができます
    - **A1 セルはいじらないでください**
        - 消してしまった場合などは、A1 セルを空欄にしたまま、認証手順をやり直してください
9. 自動で初回実行が行われ、A2 に文字列が記載されます
    - **A2 セルはいじらないでください**
        - この値は、misskey.io サーバへの負荷を抑えるための値です (前回参照したノートは参照しない)


## 4. 定期実行の設定
- タイムライン監視を定期的に行うように設定します

1. Apps Script の左メニュー `トリガー` をクリック
    - ![image](https://github.com/user-attachments/assets/7d66d24f-f0bd-4248-b2f4-6eeb468bcb9b)
2. `新しいトリガーを作成します` をクリック
    - ![image](https://github.com/user-attachments/assets/3ffada0f-71b5-4f8d-94ac-0b020120ec51)
3. 実行する関数は `CheckTimeline` にして、`イベントのソース` は `時間主導型` に設定
    - ![image](https://github.com/user-attachments/assets/3803125f-fa90-4243-b767-b641b6e48ac0)
4. 実行間隔を適切に設定し、`保存` をクリック
    - 以下の例では、30分毎に自動実行されます 
    - ![image](https://github.com/user-attachments/assets/2e6c29f7-c1d9-42e0-83e1-75d73c1836bc)

## 5. 終了
- 以上で設定は終了です






