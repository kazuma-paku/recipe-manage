# レシピ帳 - セットアップと公開ガイド

家族と共有できるレシピ／作り置き／買い物リスト管理アプリ。招待コードで家族に参加してもらい、自動で同期されます。

## 全体の流れ

1. Firebaseプロジェクトを作る（5〜10分）
2. `index.html` に設定を貼り付ける
3. Netlify DropかVercelに公開（1〜2分）
4. スマホでURLを開き、ホーム画面に追加
5. 招待コードを家族に伝えて参加してもらう

---

## 1. Firebaseプロジェクトを作る

### 1-1. プロジェクト作成

1. [Firebase コンソール](https://console.firebase.google.com/) にGoogleアカウントでログイン
2. **「プロジェクトを追加」** をクリック
3. プロジェクト名を入力（例：`recipe-family`）
4. Google アナリティクスは **無効** で OK（有効でも可）
5. 「プロジェクトを作成」

### 1-2. Firestore を有効化

1. 左メニュー **「構築」→「Firestore Database」**
2. **「データベースの作成」**
3. モード：**「本番環境モード」** を選択
4. リージョン：**`asia-northeast1`（東京）** を選択（変更不可なので注意）
5. 「有効にする」

### 1-3. セキュリティルールを設定

1. Firestore Database の **「ルール」タブ** に移動
2. 既存のルールをすべて削除し、`firestore.rules` の内容を貼り付け
3. 「公開」をクリック

### 1-4. 匿名認証を有効化

1. 左メニュー **「構築」→「Authentication」**
2. **「始める」**
3. **「Sign-in method」** タブ
4. **「匿名」** をクリック → **「有効にする」** → 保存

### 1-5. 設定情報を取得

1. 左上の歯車 **⚙️ →「プロジェクトの設定」**
2. **「マイアプリ」** セクションで **`</>`（ウェブ）** アイコンをクリック
3. アプリのニックネームを入力（例：`recipe-app`）
4. **「Firebase Hosting も設定する」はチェックしない**
5. **「アプリを登録」**
6. 表示された `firebaseConfig` オブジェクトをコピー

```javascript
const firebaseConfig = {
  apiKey: "AIzaSy...",
  authDomain: "recipe-family.firebaseapp.com",
  projectId: "recipe-family",
  storageBucket: "recipe-family.appspot.com",
  messagingSenderId: "123456789",
  appId: "1:123456789:web:abc..."
};
```

---

## 2. `index.html` に設定を貼り付け

`index.html` を開き、以下の部分を書き換え：

```html
<script>
  window.FIREBASE_CONFIG = {
    apiKey: "PASTE_YOUR_API_KEY_HERE",
    ...
  };
</script>
```

↓ 先ほどコピーした値に置き換える

```html
<script>
  window.FIREBASE_CONFIG = {
    apiKey: "AIzaSy...",
    authDomain: "recipe-family.firebaseapp.com",
    projectId: "recipe-family",
    storageBucket: "recipe-family.appspot.com",
    messagingSenderId: "123456789",
    appId: "1:123456789:web:abc..."
  };
</script>
```

**このconfigは公開しても安全です**（Firebaseの仕組み上、アクセス制限は上のセキュリティルールで行うため）。

---

## 3. 公開する（Netlify Drop が一番簡単）

### Netlify Drop での公開（推奨・アカウント不要）

1. このフォルダ（`index.html`, `app.js`, `manifest.webmanifest`, アイコン各種）をまとめて選択
2. [Netlify Drop](https://app.netlify.com/drop) をブラウザで開く
3. ファイル一式を **ドラッグ＆ドロップ**
4. 自動でURLが発行される（例: `https://cheerful-cat-abc123.netlify.app`）
5. **無料アカウントを作れば、独自のURL名に変更可能**（例: `family-recipes.netlify.app`）

### Vercelでの公開（アカウントあり）

1. [vercel.com](https://vercel.com/) にログイン
2. 「New Project」→ フォルダをドラッグ、または CLI `vercel deploy` を実行

---

## 4. スマホで使えるようにする

### iPhoneの場合

1. Safariで発行されたURLを開く
2. 下の **共有ボタン** をタップ
3. **「ホーム画面に追加」** をタップ
4. 名前を確認して「追加」

### Androidの場合

1. Chromeで発行されたURLを開く
2. アドレスバー横の **︙メニュー**
3. **「ホーム画面に追加」** または「アプリをインストール」

これでアプリのアイコンがホーム画面に追加され、普通のアプリのように起動できます。

---

## 5. 家族に共有する

1. アプリを開くと、初回はオンボーディング画面になる
2. **「新しくグループを作る」** をタップ
3. 招待コード（例: `ABCD-EFG-HJK`）が発行される
4. 家族に **URL + 招待コード** を伝える（設定画面から共有ボタンで送れる）
5. 家族はURLを開いて、同じホーム画面に追加 → **「招待コードで参加する」** にコードを入力

以降、誰かがレシピ・買い物リスト・作り置きを更新すると、他の家族のアプリにも数秒で反映されます。

---

## データの引き継ぎ（Claude内のデータを移行したい場合）

1. Claudeで動いている旧アプリを開く
2. 設定 →「エクスポート」でJSONファイルをダウンロード
3. スマホの新アプリを開く
4. 設定 →「取り込み（追加）」で先ほどのJSONを選択

これで既存のレシピが引き継がれます。

---

## 費用について

Firebaseの無料プラン（Spark）で運用できます。個人／家族利用で無料枠を超えることはほぼありません。

- Firestore: 1GB 保存、1日 5万回読み込み、1日 2万回書き込みまで無料
- Authentication: 匿名認証は無料

万一超えても、無料プランでは課金は発生せず、単に一時的にサービスが停止するだけです。

---

## トラブルシューティング

**「Firebase設定が必要です」と表示される**
→ `index.html` の設定貼り付けを確認

**「認証エラー」と表示される**
→ Firebase の Authentication で匿名認証が有効になっているか確認

**招待コードで参加できない**
→ Firestoreルールが正しく公開されているか確認。またコード大文字小文字は自動変換されるので気にしなくてOK

**同期されない**
→ 家族が同じ招待コードで参加しているか確認。設定画面でコードを見比べる
