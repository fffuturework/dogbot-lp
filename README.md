# その作業、[Dog bot](https://github.com/fffuturework/dogbot) で簡単に自動化できるかも？　日々のルーチンワークは「[Dog bot](https://github.com/fffuturework/dogbot)」で置き換えよう

Dog botとは [CodeceptJS](https://codecept.io/) ベースのWeb RPAツールです。

## デモ
### 背景
私は、車を持っておらず毎週土日に[タイムズカーシェア](https://share.timescar.jp/) を借りています。

最大2週間先の予約が取れるので、毎週土日に二週間先の予約をWebでポチポチして予約をしています。

### 問題
1. たまにこの作業を忘れていて、車が借りられない時がある
2. 手動なのでめんどくさい

![JIKAN_TOBU_MAN](./images/jikan_tobu_man.png)


### 解決策
[Dog bot](https://github.com/fffuturework/dogbot)でこの作業を自動化してみましょう

### レシピ
1. タイムズカーシェアのトップページのログインボタンをクリック
2. ログインフォームからログイン
3. 簡単予約ボタンをクリック
4. 予約できる一番先の日付を選択して検索
5. 利用開始時間、返却予定時間を取得し予約

### 手動作業と[Dog bot](https://github.com/dogbot)の違いを御覧ください

![DEMO](./images/demo.gif)


## 特徴
1. かんたんに書ける
2. 環境構築がかんたん
3. 作ったレシピをみんなで共有しあえる

### 特徴1.かんたんに書ける

上記のレシピをDog botのコードに置き換えてみましょう

```JavaScript
const {
  I
} = inject();
module.exports = {
  async run() {
    // 1.タイムズカーシェアのトップページのログインボタンをクリック
    I.amOnPage('https://share.timescar.jp/sp/');
    I.click('ログイン');
    I.wait(2);
    // 2. ログインフォームからログイン (環境変数から TIMES_CARDNO1,TIMES_CARDNO2, TIMES_PASSWORD を取得しています)
    I.fillField('tpLoginForm:cardNo1', process.env.TIMES_CARDNO1);
    I.fillField('tpLoginForm:cardNo2', process.env.TIMES_CARDNO2);
    I.fillField('tpLoginForm:tpPassword', process.env.TIMES_PASSWORD);
    I.click('#tpLogin');
    // たまに、モーダルのポップアップが出てくるので、"了解"を押してモーダルをクローズします
    let entry = await I.grabNumberOfVisibleElements(locate('span').withText('了解'));
    if (entry > 0) I.click('了解');
    // 3. 簡単予約ボタンをクリック
    I.click('簡単予約');
    I.click('予約');
    // 4.  予約できる一番先の日付を選択して検索
    const targetDates = await I.grabTextFromAll('#dateSpace > option');
    I.selectOption('#dateSpace', targetDates.pop());
    I.selectOption('#hourSpace', process.env.TIMES_STARTHH);
    I.click('検索');
    // 5. 利用開始時間、返却予定時間を取得し予約 (環境変数から利用開始時間 TIMES_STARTHH 、返却予定時間 TIMES_ENDHH を取得しています)
    I.click('予約入力画面へ');
    I.selectOption('#hourEnd', process.env.TIMES_ENDHH);
    I.click('入力内容確認');
    I.click('予約確定');
    I.wait(2);
  }
}
```

どうでしょう　通常のプログラミングとちがってそれとなく見やすくないですか！？

なぜならばベースとして使っている[CodeceptJS](https://codecept.io/) は手順書を、そのままコードに置き換えるという思想に基づいて設計されているからです。

### 特徴2.環境構築がかんたん

1. [Chrome driver](https://chromedriver.chromium.org/downloads) をダウンロードして立ち上げます
2. [nodejs](https://nodejs.org/ja/download/) をセットアップします
3. [Dog bot](https://github.com/fffuturework/dogbot)をセットアップ

```bash
 $ git clone https://github.com/fffuturework/dogbot
 $ cd dogbot
 $ npm i
```
4. 実行!!

```bash
 $ TIMES_STARTHH=23 \
 TIMES_ENDHH=11 \
 TIMES_CARDNO1=XXXX \
 TIMES_CARDNO2=XXXXX \
 TIMES_PASSWORD=XXXX \
 DB_SOURCE='https://gist.githubusercontent.com/freddiefujiwara/f5be9a6b62f123b2c2734ecdf94bd8a4/raw/c207f5c05306e35caf70184c66d0bb933746738e/dogbot-times-holiday-booking.js' \
 npx codeceptjs run --verbose
```

### 特徴3. つくったレシピをみんなで共有しあえる

つくったレシピを[gist.github.com](https://gist.github.com) で公開して共有しあえます

みんなのつくったレシピは[こちら](https://gist.github.com/search?l=JavaScript&o=desc&q=dogbot&s=updated)

