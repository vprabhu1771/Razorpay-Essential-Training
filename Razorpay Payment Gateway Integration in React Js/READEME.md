#  Razorpay Payment Gateway Integration in React Js 

npm create vite payment_app
√ Select a framework: » React
√ Select a variant: » JavaScript

Scaffolding project in C:\Users\windows_rig2\Desktop\payment_app...

Done. Now run:

cd payment_app
npm install
npm run dev

https://razorpay.com -> Login -> Change to Test Mode -> API KEYS and Plugins 

COPY Test Key ID

COPY  Test Key Secret

`.env`

```
VITE_RAZOR_PAY_KEY = "RAZORPAY KEY"

VITE_RAZOR_PAY_KEY_SECRET = "YOUR RAZORPAY KEY SECRET"
```

open `App.jsx`

```
import React, { useState } from 'react';

function App() {
  
  const [amount, setAmount] = useState('');

  const handlePayment = (e) => {
    setAmount(e.target.value)
  }

  const handleSubmit = (e) => {
    e.preventDefault();

    if(amount === "")
    {
      alert("please enter amount")
    }      
    else
    {
      // alert(amount);
      var options = {
        key: import.meta.env.VITE_RAZOR_PAY_KEY,
        key_secret: import.meta.env.VITE_RAZOR_PAY_KEY_SECRET,
        amount: amount * 100,
        currency: "INR",
        name: "payment app",
        description: "for testing purpose",
        handler: function(response){
          alert(response.razorpay_payment_id);
        },
        prefill: {
          name: "YOUR NAME",
          email: "YOUR EMAIL",
          contact: "YOUR MOBILE NO"
        },
        notes: {
          address: "Razorpay Corporate office"
        },
        theme: {
          color: "#3399cc"
        }
      };

      var pay = new window.Razorpay(options);

      pay.open();
    }
      


  }

  return (
    <>
      
      <h2>Razorpay Payment Gateway Integration in React Js</h2>

      <br />

      <input 
        type="text"
        placeholder='Enter Amount'
        value={amount}
        // onChange={ (e) => setAmount(e.target.value) } 
        onChange={handlePayment}
        />

      <br />

      <button onClick={handleSubmit}>Submit</button>
        
    </>
  )
}

export default App
```