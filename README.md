###### tags: `Documentation`, `Cryptocurrency` , `Payment`
# Zanjir
Zanjir is a cryptocurrency payment gateway service for your online store.

## Features
* No authentication needed (registeration or KYC)
* No information is collected from you or your customers
* Very simple web service
* Instant and automatic settlement
* Lowest payment fee (only 1%)
* Online technical support
* Security in payment and settlement
* Supports all popular cryptocurrencies
* White label service (Use it as your own)


## How does it work?
In this service, a temporary wallet is created for each payment request. After successful payment from your customer to the temporary wallet, the amount paid by the customer will be credited to your main wallet. All steps are done automatically and instantly.


## Documentation

### How to Create a Payment Request
Use this API to create payment requests. Request parameters are listed below.

#### Request parameters:
##### 1) Ticker 
This **required** parameter represents the coin you want your customer to pay in. You can find the complete list of supported coins and their minimum value down below. Simply pass the coin name (Ticker).

| Ticker   |      Coin/Token      |    Blockchin    | Min Amount |
|----------|:-------------:|:---------------:|:----------:|
| btc_bitcoin |  BTC |     Bitcoin     |   0.001    |
| ltc_litecoin |    LTC   |     Litcoin     |    0.01    |
| trx_trc20 | TRX |      TRC20      |     10     |
| dash_dashcoin | DASH |    Dashcoin     |    0.01    |
| doge_dogecoin | DOGE |    Dogecoin     |     7      |
| usdt_trc20 | USDT |      TRC20      |     2      |
| bnb_bep20 | BNB |      BEP20      |    0.01    |
| busd_bep20 | BUSD |      BEP20      |     1      |
| bnb_bep20_testnet | BNB |  BEP20_testnet  |    0.01    |
| btc_bitcoin_testnet | BTC | Bitcoin_testnet |   0.001    |

##### 3) Amount
This **required** parameter represents the amount you expect your customer to pay. Minimum amount of each coin is mentioned in [Tickers](#1-Ticker) table.

##### 2) Currency
This **optional** parameter represents your currency of choice. This helpful parameter makes it easier for you to specify the amount you want customer to pay. When you specify currency, Zanjir will automatically convert the amount, to the coin (Ticker).
For example, if you want the customer to pay 10 USD in bitcoins, you can simply specify these three parameters as below:
```gherkin=
{
    address: '18XBGgoZ8zgVG6....'
    ticker: 'btc_bitcoin',
    amount: 10,
    currency: 'USD',
    callback: 'https://mysite.com/'
    ... other parameters,
}
```
You can find the list of all supported currencies below.

|  Fiat currency  |      Name      |
|----------|:-------------:|
| USD |  US Dollar |
| EUR |  Euro |
| AED |  Emirati Dirham |
| GBP |  Pounds |
| AUD |  Australian Dollar |
| CAD |  Canadian dollar |
| TRY |  Turkish Lira |
| MYR |  Malaysian Ringgit |
| INR |  Indian Rupee |
| IRR |  Iranian rial |

##### Address
This **required** parameter represents your wallet.
After a successful payment, Zanjir will deposit the amount paid by the customer to this wallet.

:warning:	&nbsp; Since every blockchain has a unique addressing format, make sure to specify this parameter based on the coin you chose (Ticker parameter).


##### Callback
This **required** parameter is a web address that Zanjir will call after a successfull transaction.
If the payment is made successfully by the customer, Zanjir will send a web hook of the payment information to this web address.
Payment information will be sent to you by POST method.

:warning:	&nbsp; Zanjir will send payment information to you when payment has been made successfully and at least one block is passed since that payment.

:warning:	&nbsp; In case of getting an error while calling the callback, Zanjir will try to continue calling the callback for **1 hours** until getting a status 200 response. After that, Zanjir will stop calling the callback.


Zanjir will call your callback address with these parameters which are listed below.

| Parameter              |                                                      Description                                                       |
|------------------------|:----------------------------------------------------------------------------------------------------------------------:|
| id                     |                                                 Zanjir Transaction Id                                                  |
| amount_final           |                            The final amount deposited into your wallet with a fee deduction                            |
| amount                 |                                                   amount_final + fee                                                   |
| fee                    |                                                Zanjir fee + Network fee                                                |
| in_wallet              |                       temporary wallet Zanjir for which your customer has deposited the amount.                        |
| out_wallet             |                              The address of your wallet to which we deposited the amount.                              |
| ticker                 |                                                      Ticker name                                                       |
| in_transaction_confirm |           It confirms that your customer's transaction has been settled in the in_wallet.                  |
| date                   | Date of payment request submitted|
| nonce                  | nonce is a random string , that you validate when you receive our request, to make sure the request is coming from us. |
##### Secret
This parameter is for VIP customers.

---
##### Request Information
**Request URL:** https://gate.zanjir.network/api/create
**Request Method:** **POST**
**Request Schema:**
```gherkin=
{
    amount    : NUMBER(REQUIRED),
    address   : STRING(REQUIRED),
    ticker    : STRING(REQUIRED),
    callback  : STRING(REQUIRED),
    currency  : STRING(Optional),
    secret    : STRING(Optional),
}
 ```
**Response Schema:**
```gherkin=
{
    id            : STRING,
    status        : INT,
    ticker        : STRING,
    amount        : STRING,
    in_wallet     : STRING,
    nonce         : STRING,
}
```

##### Response Information

###### id
For each transaction, a unique ID is created in UUID format.
###### status
Represents the result of request. List of all possible values and their description is listed below.

|  Code  |      description      |
|----------|:-------------:|
| 1000 |  successful |
| 1001 |  The amount is less than the allowable limit |
| 1002 |  Your wallet address structure is invalid or does not support Zanjir. |
| 1003 |  Callback url is invalid. |
| 1004 |  Ticker is invalid. |
| 1005 |  currency is invalid. |
| 1006 |  The secret has not been verified. |
| 1007 |  Ticker is Disable. |
| 1008 |  The ticker is temporarily unavailable. |

###### ticker
The coin you requested for transaction. See [Tickers](#1-Ticker) table.

###### amount
The amount you requested for transaction.

:warning:	&nbsp; This amount is always in cryptocurrency, whether you have specified a currency (automatically calculated) or not.


###### in_wallet
The address of the temporary wallet that you must give to your customer. This wallet is different in each request and is unique.

###### nonce
nonce is validation string (a random string), that you can use to validate your request, to make sure the request is coming from us.

---


### How to verify transactions? 
You can confirm successful transactions using this API.

**URL:** https://gate.zanjir.network/api/verify/
**Method:** GET
**Parameters:**

| Parameter | Description                     |
|----------|:-------------:|
| id | Zanjir Transaction Id (UUID) |


**Response Schema:**
```gherkin=
{
    id                          : STRING,
    in_wallet                   : STRING,
    out_wallet                  : STRING,
    in_transaction_confirm      : BOOL,
    out_transaction_confirm     : BOOL,
    ticker                      : STRING,
    out_txid                    : STRING,
    callback_status             : STRING
    amount                      : STRING,
    fee                         : STRING,
    date                        : INT,
}
```

---

### Important Notes 

* If payment is successful, the callback address will be called.
* We recommend using the Verify API in order to make sure that the payment is successful.
* The callback will be called after at least one confirmation block.
* Zanjir service fee is fixed at 1%.
* The network fee will be calculated on each payment, but we use higher than usual fees to increase the security and speed of payment confirmation in the blockchain network.
* Each payment request and it's corresponding temporary wallet address is valid for a maximum of 2 hours. After that, the temporary wallet address will be invalid.

