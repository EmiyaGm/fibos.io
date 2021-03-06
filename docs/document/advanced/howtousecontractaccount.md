# 如何使用 FIBOS 合约子钱包

> 学习本文档前，你需要对 FIBOS 和 fibos.js 有一定的了解。假如你对 FIBOS 和 fibos.js 不了解，请参阅 [使用 fibos.js 与 FIBOS 交互](../start/fibosjs.md) 。

## 为什么要用合约子钱包

### 场景一

假设有这样一个业务场景，公司 A 发布了一款产品叫做 FO 单车，使用 FO 单车需要使用 FO 单车通证付款，且FO 单车通证可以与 FO 通证进行互相兑换。用户 A 想要使用 FO 单车，那么他就要在 FO 单车平台注册一个账户并向其中冲入一定数量的 FO 单车通证。每使用一次 FO 单车，需要进行一次支付，但是每次支付都需要用户输入密码，影响了用户的体验。那么有什么好的解决方案呢？

合约子钱包就能解决这个问题，我们可以这样理解，平台相当于合约，子钱包相当于平台账户。当用户想要使用 FO 单车时，就往子钱包中冲入 FO 单车通证，合约会被授权可以对你的子钱包中的余额进行操作。这样每次使用完单车后，合约会自动扣款，就不需要用户再去手动操作了。当用户不再需要使用 FO 单车时，将 FO 单车通证从子钱包中提出即可。

### 场景二

假设 A 和 B 竞猜，各自投入 50 个 FO 通证，但是没有人来裁判竞猜结果，两人都可以耍赖，这时候该怎么办呢？

合约子钱包也能解决这个问题，A 和 B 需要一个第三方平台 C 来监管。A 和 B 在竞猜之前分别把 50 个 FO 通证打到 C 平台的账户中，平台可以对 A 和 B 账户的通证进行操作，当结果出来之后。平台直接将输的一方的通证打入到获胜的一方。

上述两个场景中，给大家简单的展示了合约子钱包的用法。还有更多合约子钱包的用法等着大家去发掘，下面给大家讲解一下合约子钱包三个接口的用法。

## 合约子钱包的三种用法

前提条件：用户 `nmslwsndhjyz` 发行了一个合约，以及精度为四位小数，名字叫做 `ADC` 的智能通证。用户 `tteesstt1122` 通过 [兑换](./howtoexchangetokeninfibos.md) 持有 1000  个 `ADC` 流通通证。

### 充值

**接口**

```js
void token::ctxrecharge(
    account_name owner, //账户拥有者
    extended_asset quantity, //充值通证数量
    string memo //附言
)
```

**实例**

```js
//初始化 fibos 客户端
...
let ctx = fibos.contractSync("eosio.token");
let r = ctx.ctxrechargeSync("tteesstt1122", "100.0000 ADC@nmslwsndhjyz", "ctxrecharge", {
			authorization: "tteesstt1122"
		})
console.notice(r);
```

上述代码中，`tteesstt1122` 调用 `ctxrechargeSync` 方法给自己的合约子钱包中冲入了200个 `ADC` 智能通证。

充值成功后，我们可以调用如下的方法分别来查询流通通证余额和合约子钱包的余额。

查询可用余额：

```js
//初始化 fibos 客户端
...
let r = fibos.getTableRowsSync(true, "eosio.token", "nmslwsndhjyz", "accounts")
console.notice(r);
```

查询合约子钱包余额：

```js
//初始化 fibos 客户端
...
let s = fibos.getTableRowsSync(true, "eosio.token", "nmslwsndhjyz", "ctxaccounts")
console.notice(s);
```

### 提现

**接口**

```js
void token::ctxextract(
    account_name owner,//账户拥有者
    extended_asset quantity, //提现通证数量
    string memo //附言
)
```

**实例**

```js
//初始化 fibos 客户端
...
let ctx = fibos.contractSync("eosio.token");
let r = ctx.ctxextractSync("tteesstt1122", "100.0000 ADC@nmslwsndhjyz", "ctxextract", {
			authorization: "tteesstt1122"
		})
console.notice(r);
```

调用 `ctxextractSync` 方法将自己的合约子钱包中的100个通证提回到流通通证余额中。

### 转账

**接口**

```javascript
void token::ctxtransfer(
	account_name from, //通证转出方
	account_name to, //通证转入方
	extended_asset quantity, //通证数量
	string memo //附言
)

```

**实例**

```js
ctx.ctxtransferSync("tteesstt1122", "tteesstt2222", "50.0000 ADC@nmslwsndhjyz", "ctxtransfer", {
			authorization: "nmslwsndhjyz"
		})
```

`ADC` 合约发行者 `nmslwsndhjyz` 调用转账方法将 `ttteesstt1122` 用户合约子钱包中的 50 个 `ADC` 通证转到了用户 `tteesstt2222` 的合约子钱包中。