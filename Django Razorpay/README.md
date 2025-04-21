Install `razorpay`

Run the following command to install `razorpay`:

```bash
pip install razorpay
```

The error you're seeing means that the `pkg_resources` module is missing. This module is part of the **`setuptools`** package, which is either not installed or somehow corrupted in your virtual environment.

---

### ✅ Fix: Install `setuptools`

Run the following command to install `setuptools`:

```bash
pip install setuptools
```

Or if you're inside the virtual environment and want to ensure it's fresh:

```bash
python -m ensurepip --upgrade
```

Then verify:

```bash
pip show setuptools
```

---

### 🔁 If Problem Persists

You can try reinstalling `setuptools`:

```bash
pip uninstall setuptools
pip install setuptools
```

---

### 🧠 Tip

After installing, restart your Django development server:

```bash
python manage.py runserver
```

Let me know if you need help with Razorpay integration logic too!


# Razorypay and Django Integration — about:blank#blocked Solved — Simple Steps

```
https://medium.com/@muhammadshamshad365/razorypay-and-django-integration-about-blank-blocked-solved-simple-steps-5942004dfb99
```

`settings.py`

```
#For To Enable Popus in Django or else it will block the payment popup
SECURE_CROSS_ORIGIN_OPENER_POLICY = "same-origin-allow-popups"
```

---

## 💸 4. Fines List (`fine_list.html`)
```
<!-- templates/fine_list.html -->
<body class="container mt-5">
  <h2>Your Fines</h2>
  <table class="table table-striped">
    <thead>
      <tr>
        <th>Loan Book</th>
        <th>Fine Date</th>
        <th>Fine Amount</th>
        <th>Action</th>
      </tr>
    </thead>
    <tbody>
      {% for fine in fines %}
      <tr>
        <td>{{ fine.loan.book.title }}</td>
        <td>{{ fine.fine_date }}</td>
        <td>₹{{ fine.fine_amount }}</td>
        <td>
          <form action="{% url 'pay_fine' fine.id %}" method="POST">
            {% csrf_token %}
            <button class="btn btn-sm btn-danger">Pay</button>
          </form>
        </td>
      </tr>
      {% empty %}
        <tr><td colspan="4">No fines due.</td></tr>
      {% endfor %}
    </tbody>
  </table>
</body>
```

---

## 🧾 5. Razorpay Fine Payment (View Logic + Script)
```python
# views.py
import razorpay
from django.conf import settings
from django.views.decorators.csrf import csrf_exempt

@csrf_exempt
def pay_fine(request, fine_id):
    fine = Fine.objects.get(id=fine_id)
    client = razorpay.Client(auth=(settings.RAZORPAY_KEY_ID, settings.RAZORPAY_KEY_SECRET))

    order_data = {
        'amount': int(fine.fine_amount * 100),  # Razorpay takes amount in paisa
        'currency': 'INR',
        'payment_capture': 1
    }
    order = client.order.create(order_data)

    return render(request, 'pay_fine.html', {
        'fine': fine,
        'order': order,
        'razorpay_key': settings.RAZORPAY_KEY_ID
    })
```

### `pay_fine.html` (Razorpay Payment)
```
<script src="https://checkout.razorpay.com/v1/checkout.js"></script>

<button id="rzp-button1" class="btn btn-success">Pay ₹{{ fine.fine_amount }}</button>

<script>
  var options = {
    "key": "{{ razorpay_key }}",
    "amount": "{{ order.amount }}",
    "currency": "INR",
    "name": "Library Payment",
    "description": "Fine Payment",
    "order_id": "{{ order.id }}",
    "handler": function (response){
        alert('Payment successful!');
        window.location.href = "/"; // redirect after payment
    },
    "prefill": {
        "email": "{{ fine.member.email }}"
    },
    "theme": {
        "color": "#3399cc"
    }
  };
  var rzp1 = new Razorpay(options);
  document.getElementById('rzp-button1').onclick = function(e){
    rzp1.open();
    e.preventDefault();
  }
</script>
```
