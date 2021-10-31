# Square Webhook PHP

This is a simple Webhook Router Class for SquareUps Webhooks

1) Add this anywhere in your project

```php
class Webhook
{
    static function route($type, $function)
    {
        $callbackBody = file_get_contents('php://input');
        $callbackSignature = getallheaders()['X-Square-Signature'];

        if (Signature::isValidCallback($callbackBody, $callbackSignature)) {
            $callbackBodyJson = json_decode($callbackBody);

            if (property_exists($callbackBodyJson, 'type') and $callbackBodyJson->type == $type) {
                return $function($callbackBodyJson);
            }
        }
    }
}

class Signature extends Webhook
{
    static function isValidCallback($callbackBody, $callbackSignature)
    {
        $stringToSign = webhookUrl . $callbackBody;
        $stringSignature = base64_encode(hash_hmac('sha1', $stringToSign, webhookSignatureKey, true));

        return (sha1($stringSignature) === sha1($callbackSignature));
    }
}

```


2) Then if you have namespaced it don't forget to use
```php
use Namespace\Webhook;
```


3) Add Constants for the Webhook URL and Signature
```php
define('webhookUrl', "https://YOUR_APPS_WEBHOOK_URL_YOU_SET_IN_SQUARE_DASHBOARD.com");
define('webhookSignatureKey', "YOUR_SIGNATURE_KEY_IN_SQUARE_DASHBOARD");
```


4) Now call the class in a file that you want to point your webhooks to
```php
Webhook::route('ADD EVENT TYPE HERE example "order.fulfillment.updated"', function ($callback) {
    
});

```

5) Inside the function you can use the varible $callback to access a std object of the data sent in the webhook example 
```php
$callback->data->object->order_fulfillment_updated->created_at;
```
