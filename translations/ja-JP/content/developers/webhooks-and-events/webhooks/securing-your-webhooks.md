---
title: webhook のセキュリティ保護
intro: 'セキュリティ上の理由から、サーバーが想定されているる {% data variables.product.prodname_dotcom %} リクエストのみを受信していることを確認する必要があります。'
redirect_from:
  - /webhooks/securing
  - /developers/webhooks-and-events/securing-your-webhooks
versions:
  fpt: '*'
  ghes: '*'
  ghae: '*'
  ghec: '*'
topics:
  - Webhooks
---

ペイロードを受信するようにサーバーが設定されると、設定したエンドポイントに送信されたペイロードがリッスンされます。 セキュリティ上の理由から、GitHub からのリクエストに制限することをお勧めします。 これを行うにはいくつかの方法があります。たとえば、GitHub の IP アドレスからのリクエストを許可することですが、はるかに簡単な方法は、シークレットトークンを設定して情報を検証することです。

{% data reusables.webhooks.webhooks-rest-api-links %}

## シークレットトークンを設定する

シークレットトークンは、GitHub とサーバーの 2 か所に設定する必要があります。

GitHub にトークンを設定するには：

1. webhook を設定しているリポジトリに移動します。
2. シークレットテキストボックスに入力します。 エントロピーの高いランダムな文字列を使用します (たとえば、`ruby -rsecurerandom -e 'puts SecureRandom.hex(20)'` を端末で取得することによって)。 ![webhook シークレットトークンフィールド](/assets/images/webhook_secret_token.png)
3. [**Update Webhook**] をクリックします。

次に、このトークンを保存する環境変数をサーバーに設定します。 通常、これは実行と同じくらい簡単です。

```shell
$ export SECRET_TOKEN=<em>your_token</em>
```

トークンをアプリケーションにハードコーディング**しないでください**。

## GitHub からのペイロードを検証する

シークレットトークンが設定されると、{% data variables.product.product_name %} はそれを使用して各ペイロードでハッシュ署名を作成します。 This hash signature is included with the headers of each request as `x-hub-signature-256`.

{% ifversion fpt or ghes or ghec %}
{% note %}

**Note:** For backward-compatibility, we also include the `x-hub-signature` header that is generated using the SHA-1 hash function. If possible, we recommend that you use the `x-hub-signature-256` header for improved security. The example below demonstrates using the `x-hub-signature-256` header.

{% endnote %}
{% endif %}

たとえば、webhook をリッスンする基本的なサーバーがある場合、次のように設定されている可能性があります。

``` ruby
require 'sinatra'
require 'json'

post '/payload' do
  request.body.rewind
  push = JSON.parse(request.body.read)
  "I got some JSON: #{push.inspect}"
end
```

目的は、`SECRET_TOKEN` を使用してハッシュを計算し、結果が {% data variables.product.product_name %} のハッシュと一致することを確認することです。 {% data variables.product.product_name %} は HMAC hex digest を使用してハッシュを計算するため、サーバーを次のように再設定できます。

``` ruby
post '/payload' do
  request.body.rewind
  payload_body = request.body.read
  verify_signature(payload_body)
  push = JSON.parse(payload_body)
  "I got some JSON: #{push.inspect}"
end

def verify_signature(payload_body)
  signature = 'sha256=' + OpenSSL::HMAC.hexdigest(OpenSSL::Digest.new('sha256'), ENV['SECRET_TOKEN'], payload_body)
  return halt 500, "Signatures didn't match!" unless Rack::Utils.secure_compare(signature, request.env['HTTP_X_HUB_SIGNATURE_256'])
end
```

{% note %}

**注釈:** Webhookペイロードには、Unicode 文字を含めることができます。 言語とサーバーの実装で文字エンコーディングが指定されている場合は、ペイロードをUTF-8として扱うようにしてください。

{% endnote %}

言語とサーバーの実装は、この例で使用したコードとは異なる場合があります。 ただし、次のようないくつかの非常に重要な事項があります。

* No matter which implementation you use, the hash signature starts with `sha256=`, using the key of your secret token and your payload body.

* プレーンな `==` 演算子を使用することは**お勧めしません**。 [`secure_compare`][secure_compare] のようなメソッドは、「一定時間」の文字列比較を実行します。これは、通常の等式演算子に対する特定のタイミング攻撃を軽減するのに役立ちます。

[secure_compare]: https://rubydoc.info/github/rack/rack/main/Rack/Utils:secure_compare
