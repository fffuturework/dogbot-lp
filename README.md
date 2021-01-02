# その作業、Dog bot で簡単に自動化できるかも？　日々のルーチンワークは「Dog bot」で置き換えよう

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
これを解決したいと思い Dog botを作りました。

### レシピ
1. タイムズカーシェアのトップページのログインボタンをクリック
2. ログインフォームからログイン
3. 簡単予約ボタンをクリック
4. 予約できる一番先の日付を選択して検索
5. 利用開始時間、返却予定時間を取得し予約

### 手動作業とDog botの違いを御覧ください

![DEMO](./images/demo.gif)


## 特徴
1. かんたんに書ける

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

2. 環境構築がかんたん
3. 作ったレシピをみんなで共有しあえる

### 特徴1.かんたんに書ける
### 特徴2.環境構築がかんたん
### 特徴3. 作ったレシピをみんなで共有しあえる
