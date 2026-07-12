---
name: shop-homepage
description: Build a homepage for a small local business (restaurant, cafe, hair salon, classroom, clinic, freelancer) and publish it to a shareable URL with Publee. Use when the user wants a website/homepage for their shop or business without coding — e.g. "お店のホームページを作りたい", "make a website for my restaurant".
---

# Shop homepage — build & publish with Publee

小規模店舗・教室・個人事業のホームページを、ヒアリング → 1ファイルHTML生成 →
Publeeで公開、の流れで作る。コードを書けない相手を想定し、確認は普通の言葉で行う。

## 1. Interview first — never invent facts

以下が揃うまで生成しない。足りないものは箇条書きでまとめて聞く(1回で):

- 店名 / 業種
- 住所・営業時間・定休日
- 連絡手段(電話 / LINE公式 / Instagram / メール — 予約導線に使う)
- メニュー・コース・料金(価格は必ず本人の申告値を使う)
- お店の雰囲気・こだわり(デザインのトーンを決める最重要情報)
- あれば: SNSリンク、写真、キャッチコピーの希望

**本人が言っていない事実(創業年・受賞歴・「地域No.1」等)は絶対に書かない。**
効能・医療的な断定(治る、若返る等)も避ける。

## 2. Generate a single-file page

- HTML 1ファイル完結(CSSは`<style>`内)。フレームワーク・ビルド不要
- レスポンシブ必須(`meta viewport` + モバイルで崩れないレイアウト)
- 構成の基本形: ヒーロー(店名+キャッチコピー+予約CTA)/ お店について /
  メニュー・料金 / 店舗情報(表形式) / フッター
- 業種に合わせた配色・書体を選ぶ(Google Fontsは使用可)。
  例: 喫茶店=明朝体+暖色、サロン=細身ゴシック+余白、教室=丸ゴシック+パステル
- 住所から「Googleマップで開く」リンクを付ける:
  `https://www.google.com/maps/search/?api=1&query=<URLエンコードした住所>`
- 電話は `tel:` リンク、LINE/InstagramはそのままURLリンクに

### Images — verified URLs only

- **画像URLを記憶から書かない**(存在しないUnsplash URLを生成しがち)。
  使うのは (a) ユーザー提供のファイル、(b) 実在確認済みのフリー素材URLのみ
- フリー素材を使う場合、埋め込む前に必ず確認する:
  `curl -s -o /dev/null -w "%{http_code}" "<画像URL>"` が `200` であること
- ユーザーの写真を使う場合は複数ファイル公開にする(`files` パラメータ、
  画像は `contentBase64`)。HTMLからは相対パス(`photo.jpg`)で参照

## 3. Publish with Publee

公開のAPI仕様・パラメータ詳細は [`publee` skill](../publee/SKILL.md) を参照。
このユースケースでの要点:

- お店のHPは `visibility: "public"` を明示する(デフォルトは `password`)
- 匿名公開は**7日で失効**。必ず `claimUrl` をユーザーに渡し、
  「publee.appに無料登録してこのリンクを開くとサイトが自分のものになる(30日保持)」と伝える
- 恒久運用したい場合はProプラン: サイトが無期限になり、好きなサブドメイン
  (`slug` 指定)、検索エンジン許可(`noindex: false`)、独自ドメインが使える
- 更新は `overwrite: true` + 同じ `slug` + APIトークンで同じURLのまま差し替え

## 4. After publishing

- URLと(あれば)パスワードを渡し、スマホでの確認を促す
- URLの置き場所を提案: Googleビジネスプロフィールのウェブサイト欄、
  InstagramプロフィールのURL、LINE公式アカウントのリッチメニュー
- 修正依頼は会話で受けて再生成 → 再公開(URLは変わらないことを伝える)
