To implement **Razorpay in a plain HTML + PHP project** using **Composer**, follow these steps:

---

## ✅ Step 1: Set Up Project Folder

```bash
mkdir razorpay-php-demo
cd razorpay-php-demo
composer init
```

When prompted, press enter through the defaults or add your project details.

---

## ✅ Step 2: Install Razorpay PHP SDK

```bash
composer require razorpay/razorpay
```

This will add the SDK to your `vendor/` directory.

---

## ✅ Step 3: Create Project Structure

```plaintext
razorpay-php-demo/
├── vendor/
├── index.html
├── payment.php
├── success.php
├── composer.json
```

---

## ✅ Step 4: HTML Payment Page (`index.html`)

```html
<!DOCTYPE html>
<html>
<head>
  <title>Razorpay Payment</title>
  <script src="https://checkout.razorpay.com/v1/checkout.js"></script>
</head>
<body>

<h2>Pay with Razorpay</h2>

<form action="payment.php" method="POST">
  <input type="text" name="amount" placeholder="Enter Amount (in INR)" required />
  <button type="submit">Pay Now</button>
</form>

</body>
</html>
```

---

## ✅ Step 5: Backend Payment Handler (`payment.php`)

```php
<?php
require 'vendor/autoload.php';

use Razorpay\Api\Api;

$keyId = 'YOUR_KEY_ID';
$keySecret = 'YOUR_KEY_SECRET';

$api = new Api($keyId, $keySecret);

// Amount from form
$amount = $_POST['amount'] * 100; // Convert to paise

$order = $api->order->create([
    'receipt' => uniqid(),
    'amount' => $amount,
    'currency' => 'INR'
]);

$orderId = $order['id'];
?>

<script src="https://checkout.razorpay.com/v1/checkout.js"
        data-key="<?= $keyId ?>"
        data-amount="<?= $amount ?>"
        data-currency="INR"
        data-order_id="<?= $orderId ?>"
        data-buttontext="Pay with Razorpay"
        data-name="My Demo"
        data-description="Test transaction"
        data-image=""
        data-prefill.name="Test User"
        data-prefill.email="test@example.com"
        data-theme.color="#F37254">
</script>
<noscript>Please enable JavaScript</noscript>
```

---

## ✅ Step 6: Handle Success Callback (Optional)

To handle a successful payment, Razorpay typically returns to a callback URL. If you want to verify the signature:

### `success.php` (Optional - signature verification)

```php
<?php
require 'vendor/autoload.php';

use Razorpay\Api\Api;
use Razorpay\Api\Errors\SignatureVerificationError;

$api = new Api('YOUR_KEY_ID', 'YOUR_KEY_SECRET');

$attributes = [
    'razorpay_order_id' => $_POST['razorpay_order_id'],
    'razorpay_payment_id' => $_POST['razorpay_payment_id'],
    'razorpay_signature' => $_POST['razorpay_signature']
];

try {
    $api->utility->verifyPaymentSignature($attributes);
    echo "Payment successful!";
} catch(SignatureVerificationError $e) {
    echo "Payment failed: " . $e->getMessage();
}
?>
```


```php
<?php
require 'vendor/autoload.php';

use Razorpay\Api\Api;

$keyId = 'rzp_test_ST8iOahJJpWDVp';
$keySecret = 'WknAG0tFstpj7QzCnXXzWisF';

$api = new Api($keyId, $keySecret);

// Get the amount from the form and convert it to paise
$amount = $_POST['amount'] * 100;

$order = $api->order->create([
    'receipt' => uniqid(),
    'amount' => $amount,
    'currency' => 'INR'
]);

$orderId = $order['id'];
?>

<!DOCTYPE html>
<html>
<head>
    <title>Processing Payment</title>
</head>
<body>

<h3>Redirecting to Razorpay...</h3>

<script src="https://checkout.razorpay.com/v1/checkout.js"></script>
<script>
    var options = {
        "key": "<?php echo $keyId; ?>", // Enter the Key ID
        "amount": "<?php echo $amount; ?>",
        "currency": "INR",
        "name": "My Demo Store",
        "description": "Test Transaction",
        "order_id": "<?php echo $orderId; ?>",
        "handler": function (response){
            // Optional: Post response to success.php
            var form = document.createElement('form');
            form.method = 'POST';
            form.action = 'success.php';

            var fields = {
                razorpay_payment_id: response.razorpay_payment_id,
                razorpay_order_id: response.razorpay_order_id,
                razorpay_signature: response.razorpay_signature
            };

            for (var key in fields) {
                if (fields.hasOwnProperty(key)) {
                    var hiddenField = document.createElement('input');
                    hiddenField.type = 'hidden';
                    hiddenField.name = key;
                    hiddenField.value = fields[key];

                    form.appendChild(hiddenField);
                }
            }

            document.body.appendChild(form);
            form.submit();
        },
        "prefill": {
            "name": "Test User",
            "email": "test@example.com"
        },
        "theme": {
            "color": "#3399cc"
        }
    };
    var rzp = new Razorpay(options);
    rzp.open();
</script>

<noscript>
    <p style="color: red;">Please enable JavaScript to use Razorpay Checkout.</p>
</noscript>

</body>
</html>
```

---

## ✅ Final Notes

- Replace `YOUR_KEY_ID` and `YOUR_KEY_SECRET` with actual keys from your Razorpay Dashboard.
- You can test it with Razorpay Test Mode enabled.
- Use HTTPS in production.

---

Would you like me to zip up a sample project for you or guide you through adding success/failure pages with more form validation?
