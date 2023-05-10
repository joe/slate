---
title: API Reference

# language_tabs: # must be one of https://github.com/rouge-ruby/rouge/wiki/List-of-supported-languages-and-lexers
#   - shell
#   - ruby
#   - python
#   - javascript

toc_footers:
  - <a href='https://www.revalfield.com'>Visit Revalfield</a>

includes:
  - errors

search: true

code_clipboard: true

meta:
  - name: description
    content: Documentation for the Revalfield API
---

# Introduction

Welcome to the Revalfield API documentation. Revalfield provides a collection of ways to:

- pass a customer to Revalfield for the purpose of depositing funds into their account with you
- query the status of a customer deposit
- receive webhook notifications of customer deposit events

When you sign up for a Revalfield merchant account, you gain the opportunity to:

- view transaction history for your customers
- view your merchant account parameters (created by your account team)
- manage Secret API Keys
- manage webhook endpoints

Please see the documentation below for how to utilize those tools.

# Authentication

Through your Revalfield merchant account, you have the ability to manage Secret API Keys. These keys are used for the purposes of querying customer deposit statuses and, optionally, signing your customer deposit requests.

```shell
curl "https://www.revalfield.com/api/endpoint" \
  -H "Authorization: Bearer <your-secret-api-key> <your-account-identifier>"
```

In the case of querying a customer deposit status, you are required to include a valid Secret API Key as an `Authorization` header with a prefix of `Bearer` followed by a single space and a `Secret API Key`, another space, and your `Account Identifier` (without any quotes or surrounding characters).

<aside class="warning">
Never share a Secret API Key with anybody, include a Secret API Key a URL, or send a Secret API Key over an unencrypted HTTP request. Secret API Keys may only be communicated as a Header within a HTTPS communication to Revalfield's API.
</aside>

<aside class="notice">
You must contact your Revalfield account team to verify and authorize your user account(s).
</aside>

# Create a Customer Deposit Request

Customer deposit requests are GET requests to the Revalfield API that act as the hand-off of your customer to the Revalfield web site so the customer is able to create the deposit that ends in a transfer of funds to your merchant account. For your convenience, the request is a GET request with a URL that is conveniently formulated for use within a standard `a`, `button`, or `form` on your web site. URL parameters define the context for the customer deposit.

Once the customer completes their deposit, they will be offered the opportunity to click a button to return to your web site. Their return to your web site is determined by the redirect URL that you provide to Revalfield in your deposit request. Revalfield sends a notification to your webhook URL as soon as the transaction is complete. In addition, you are able to query transaction status at any time using Revalfield's Query Customer Deposit Status API call.

### HTTP Request
`GET http://www.revalfield.com/purchases/new`

### Query Parameters

Parameter | Default | Required? | Description
--------- | ------- | ------------ | -----------
m | N/A | Yes | Your account identifier that uniquely identifies your merchant account within requests
a | N/A | Yes | The EUR deposit amount as an integer value (e.g. `300` for a deposit of EUR 300).
r | N/A | No | The URL where you would like your customer redirected to at the end of the deposit process. This URL should respond to a GET request. Revalfield may append additional GET parameters on the URL depending upon your account setup.
t | N/A | Yes | Your internal transaction identifier for the purpose of reconciling this customer deposit. The identifier is any valid string up to 100 characters in length. The string may include alphanumeric and punctuation characters as desired.
u | N/A | No | This field only applies to merchants using Revalfield's trusted verified user feature. Your internal unique identifier for a user. The identifier is any valid string up to 100 characters in length.
s | N/A | No | A signature of the query parameters (please see "Optional Signing Process" below)
sid | N/A | No | The id of the relevant Secret API Key, if you utilize the optional signing process. Required if you do use the signing process. Please note: the optional signing process is *required* if you use Revalfield's trusted verified user feature.

When you direct a customer to the Revalfield web site with the above GET request, Revalfield manages the process of guiding the customer through the deposit of funds (culminating in Revalfield sending the associated funds to you).

### Optional Signing Process

You may enable an optional security measure within your merchant account that triggers a requirement for the query parameters to be signed by one of your Secret API Keys. If this security feature is enabled, Revalfield will reject any Customer Deposit Requests that are not accurately signed.

To create a signature, execute the following in your language of choice:

1. Concatenate your query parameters' contents (except the s and sid parameters) in alphabetical order. For example, if you have an amount (`a` parameter) of `200` and a merchant account identifier (`m` parameter) of `tstmerchant` then your concatenated string is `200tstmerchant`.
2. Append your desired Secret API Key to the string. Within our example above and if you have a Secret API Key of `as7asfasfd6ad86saasd76asd8as` then you would have the resulting string: `200tstmerchantas7asfasfd6ad86saasd76asd8as`.
3. Produce a SHA256 hex digest of the string. Using our example above, you would have an end result of `7a0d4c3b474a3ddaa84c6a085f5fd5019c62b5f76ad30cc8dbd92bdf73da0c38`.
4. Include the SHA256 hex digest of the string as your `s` parameter and the ID of your Secret API Key as your `sid` parameter.

<aside class="warning">
Never include a Secret API Key in the URL itself!
</aside>

#  Query Customer Deposit Status

You may query a customer deposit request's status at any time. Please make sure you authenticate the request properly with a Secret API Key per the documentation.

### HTTP Request
`GET http://www.revalfield.com/purchases`

### Query Parameters

Parameter | Default | Required? | Description
--------- | ------- | ------------ | -----------
t | N/A | Yes | Your internal transaction identifier for the purpose of reconciling this customer deposit, as provided when you initiated the customer deposit request.

### Response

The response returned is standard JSON.

# Test/Sandbox Environment

Revalfield provides a text/sandbox environment with test credit card processing. To gain access the test environment, please contact your account representative for more information.

All test requests are identical to production requests, except they transit through the `test.revalfield.com` host name.

### Test Card Numbers

Please use the test Visa card of `4242424242424242` with any future expiration date and any three digit CVV to complete the test transaction flow.