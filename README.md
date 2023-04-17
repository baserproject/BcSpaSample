# BcSpaSample plugin for baserCMS

baserCMS の REST API と vue.js を利用する場合のサンプルアプリケーションです。
　
## Installation

You can install this plugin into your baserCMS application using [composer](https://getcomposer.org).

The recommended way to install composer packages is:

```
composer require baserproject/BcSpaSample
```

　
## 利用方法
管理画面より、BcSpaSample プラグインをインストールし、次のURLにアクセスすることで、管理者ログインとユーザー管理を確認することができます。

https://localhost/bc_spa_sample/admin.html

　
## ソースを確認する
ソースファイルは、 `/src/` に配置しています。

　
## コンパイル
webpack を使ってコンパイルします。 プラグインのディレクトリに移動し次のコマンドを実行してください。

```javascript
npm install
npm run dev
```

　
## REST API
ログイン以外のリクエストヘッダーには、 `Authorization` をキーとしてJWT形式のアクセストークンが必要となります。

### ログイン

#### メソッド

```javascript
POST
```

#### URL

```javascript
/baser/api/admin/baser-core/users/login.json
```

#### レスポンス

```javascript
{ 
    "access_token":"YOUR_ACCESS_TOKEN", 
    "refresh_token":"YOUR_REFRESH_TOKEN" 
}
```

#### コード例
```javascript
axios.post('/baser/api/admin/baser-core/users/login.json', {
    email: this.name,
    password: this.password
}).then(function (response) {
    if (response.data) {
        this.$emit('set-login', response.data.access_token, response.data.refresh_token)
        this.$router.push('user_index')
    }
}.bind(this))
.catch(function (error) {
    this.isError = true
    if(error.response.status === 401) {
        this.message = 'アカウント名、パスワードが間違っています。'
    } else {
        this.message = error.response.data.message
    }
}.bind(this))
```

　
### トークンリフレッシュ

#### メソッド

```javascript
GET
```

#### URL

```javascript
/baser/api/admin/baser-core/users/refresh_token.json
```

#### レスポンス

```javascript
{ 
    "access_token":"YOUR_ACCESS_TOKEN", 
    "refresh_token":"YOUR_REFRESH_TOKEN" 
}
```

#### コード例
```javascript
await axios.get('/baser/api/admin/baser-core/users/refresh_token.json', {
    headers: {"Authorization": refreshToken},
    data: {}
}).then(function (response) {
    if (response.data) {
        this.setToken(response.data.access_token, response.data.refresh_token)
    } else {
        this.$router.push('/')
    }
}.bind(this))
.catch(function (error) {
    if (error.response.status === 401) {
        localStorage.refreshToken = ''
    }
})
```

　
### ユーザー一覧取得

[baser Admin Api ユーザー一覧取得](https://baserproject.github.io/5/web_api/baser_admin_api/baser-core/users/index) を参照ください。

#### コード例
```javascript
axios.get('/baser/api/admin/baser-core/users/index.json', {
    headers: {"Authorization": this.accessToken},
    data: {}
}).then(function (response) {
    if (response.data) {
        this.users = response.data.users
    } else {
        this.$router.push('/')
    }
}.bind(this))
```

### その他のAPI
次のドキュメントをご覧ください。

- [Web APIガイド](https://baserproject.github.io/5/web_api/)
- [baser API](https://baserproject.github.io/5/web_api/baser_api/)
- [baser Admin API](https://baserproject.github.io/5/web_api/baser_admin_api/)

　
## BcSpaSample で TypeScript を利用する

### 変更点

単一の vue ファイルを template（vueファイル）と script（tsファイル）に分離

例) Login.vue → script の箇所を Login.ts に移行

```html
<script lang="ts" src="./Login.ts"></script>
```

tsファイル内でVue.extendを使用することで、Vueコンポーネントの記述に近い書き方で TypeScript を書くことができます。

```javascript
// *.vue
export default {
    data: function () {...}
```
↓
```javascript
// *.ts
export default Vue.extend({
    data: () => {
```
### 型の定義

type または interface を使って、型を定義できます。
代入される変数や返り値が型とそぐわない場合TypeErrorが返ります。

```ts
type DataType = {
    message? : string,
};
// dataメソッドが返す値がstringもしくはundefinedという定義が出来る
data: (): DataType => {
    return {
        message : undefined,
    }
},
// TypeErrorの場合 文字列定義の変数に数値は定義できません
// Type 'number' is not assignable to type 'string'.ts(2322)
this.message = 100; // (property) message?: string | undefined
```

### 型の再利用

main.tsにてUserタイプを定義してるので、importして再利用できます。

```ts
// main.ts
export type User = {
    ...
};
// form内で使用されるthis.userがUserタイプだと定義
// Form.ts
import { User } from '../../main';
type DataType = {
    user: User,
};
// index内で使用されるthis.usersがUserタイプを複数持つ配列だと定義
// user/Index.ts
import { User } from '../../main';
type DataType = {
    users?: User[],
};
```



