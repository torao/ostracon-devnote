---
title: "How to Read a Paper PDF like e-book on Kindle"
emoji: "📚"
type: "tech" # tech: 技術記事 / idea: アイデア
topics: ["PDF", "kindle"]
published: true
---

# Kindle やスマートフォンで PDF の論文を電子書籍のように読む方法

論文というと二段組 A4 サイズの PDF で配布されていることが多く、持ち歩いて読むには印刷するか 10 インチ以上のタブレットを使わないと大変ですよね。できればスマートフォンや軽量な Kindle で電子書籍のように読みたいところですが A4 サイズのままで読むのはかなり困難です。

しかし一度 Microsoft Word に読み込んでから Kindle に送信すると通常の電子書籍と同じようにサクサク読むことができます。二段組も解除されますし、もちろん文字や行間の拡大も可能です。早速試してみましょう。

![Paper on Kindle](/images/how-to-read-pdf-paper-on-kindle/paper-on-kindle.gif)

## Kindle 向けに変換する手順

### 1. PDF を Word に変換する

まず Kindle で読みたい PDF を Word で開いて docx 形式で保存します。保存するファイル名は Kindle のライブラリ上で書籍のタイトルとして使用されるので分かりやすい名前をつけてください。

### 2. Word ファイルを Kindle に送信する

[Amazon のサイト](https://www.amazon.co.jp/)の [アカウント & リスト]-[コンテンツと端末の管理]-[設定] で
「パーソナルドキュメント設定」を開きます。ここで自分のメールアドレスが「承認済み Eメールアドレス一覧」に設定されていない場合はメールアドレスを追加してください。

先ほど作成した docx 形式のファイルをメールに添付して「Send-to-Kindle Eメールアドレス」の宛先に送信します。

![Kindle Settings](/images/how-to-read-pdf-paper-on-kindle/kindle-settings.png)

### 3. 文書の追加を承認する

メールを送信すると Amazon アカウントに登録しているメールアドレス宛てに確認のメールが届きます。メール中の URL にアクセスして承認してください。

![Approve Document](/images/how-to-read-pdf-paper-on-kindle/approval.png)

### 4. Kindle で確認する

承認を行ったあとに Kindle を同期すると、先ほど送信した docx ファイルがライブラリに現れています。

![Sync and Download Document](/images/how-to-read-pdf-paper-on-kindle/complete.png)

Kindle ように最適化された電子文書と同じように、文字や行間の拡大縮小も正しく行えています。注目したいのは、二段組 PDF であっても Word が正しく解釈して変換を行えていれば Kindle に最適化されるというところです。

論文だけではなく PDF でしか配布されていない電子書籍も Word でうまく読み込むことさえできれば同じように Kindle で読むことができます。

### 5. 文書を削除する

単に Kindle やスマートフォンから削除するだけであれば文書を長タップして「端末から削除」を選択してください。

自分で送信した文書をライブラリから削除するには、[Amazon のサイト](https://www.amazon.co.jp/)の [アカウント & リスト]-[コンテンツと端末の管理]-[コンテンツ] で「表示」から「パーソナル・ドキュメント」を選択し、削除したい文書を選択して削除してください。

## どうにもならない問題点😇

2010 年以前に作成された古い形式の PDF は Word で読み込んだときに表示崩れを起こすことがそこそこあります。特に 19xx 年代の論文は紙面を画像としてスキャンしたものがほとんどで Word でもテキストとして認識できません。そのまま Kindle に送信しても期待した表示にはならないので諦めましょう。

表組み、数式、擬似コードといった非テキスト要素はやはり崩れたり不正確になりやすいです。Kindle では文章を読むことだけに専念して正確な表現は PDF を参照しましょう。
