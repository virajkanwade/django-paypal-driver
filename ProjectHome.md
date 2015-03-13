<font face='verdana' size='1'>
<blockquote><i>Author:</i><b>Özgür Vatansever</b> <ozgurvt@gmail.com>_<br>
</font></blockquote>_

Pluggable paypal nvp (Name Value Pair) API implementation for django based web applications. The following image simply describes the express checkout flow (obtained from [Express Checkout Integration Guide](https://cms.paypal.com/cms_content/en_US/files/developer/PP_ExpressCheckout_IntegrationGuide.pdf))

![http://django-paypal-driver.googlecode.com/files/flow.png](http://django-paypal-driver.googlecode.com/files/flow.png)

# Installation #

All you need to do is copying the "paypal" directory (or make a symbolink) under one of the directories in your PYTHON\_PATH such as:

If you are using Python 2.5;
  * /usr/local/lib/python2.5/site-packages/
  * /usr/lib/python2.5/site-packages/

If you are using Python 2.6;
  * /usr/local/lib/python2.6/dist-packages/
  * /usr/lib/python2.6/dist-packages/


There are simply 4 main methods to be executed in order to finish the PayPal payment process.

Those are:
  * SetExpressCheckout
  * GetExpressCheckoutDetails (optional)
  * DoExpressCheckoutPayment
  * RefundTransaction (for refunds)
  * DoDirectPayment (directly charge money from the credit card)

This package also includes 3 views that help you integrate PayPal checkout process to your application easily. Those views are;

  * paypal.views.setcheckout
  * paypal.views.docheckout
  * paypal.views.dorefund

This package also includes 2 models that help you store actual PayPal API responses in your database. Those models are;
  * paypal.models.PayPalResponse
  * paypal.models.PayPalResponseStatus


You need to define your PayPal API Credientials in your projects settings file. Those are;
  * PAYPAL\_USER (api username)
  * PAYPAL\_PASSWORD (api password)
  * PAYPAL\_SIGNATURE (api signature)

# Example #


You have to have the following PayPal credientials for debugging and testing your app.
```
PAYPAL_USER  = "your paypal seller account"
PAYPAL_PASSWORD = "seller account password"
PAYPAL_SIGNATURE = "your api signature"
PAYPAL_DEBUG = True # for sandbox. False for real environment.
```

Once you copy the "paypal" directory under your PYTHON\_PATH; you can enter shell by typing "python manage.py shell" command under your project root.

Then you can actually perform the following methods;
```
    In [1]: from paypal.driver import PayPal

    In [2]: p = PayPal()

    In [3]: p.SetExpressCheckout("10.00", "USD", "http://localhost/return", "http://localhost/cancel")

    - - - - - - - - - - 
    You can see the error that the server sends by calling
    - p.apierror

    Also, you can see the full response that comes from the PayPal server by calling
    - p.api_response

    when you are in python shell.
    - - - - - - - - - - 
    
    In [4]: p.DoExpressCheckoutPayment("USD", "10.00", "token", "payerid")
    
Also you can refund money;

   In [5]: p.RefundTransaction(transid = "transactionid", refundtype = "Partial", currency = "USD", amount = "10.00")
```


You can persist the results to your database. This package includes two DB models;
  * PayPalResponse (responsible for storing API responses)
  * PayPalResponseStatus (can be "Sale" or "Refund")

```
   p = PayPal()
   result = p.DoExpressCheckoutPayment("USD", "10.00", "token", "payerid")
   if result == True:
       response = PayPalResponse()
       response.fill_from_response(p.GetPaymentResponse())
       response.status = PayPalResponse.get_default_status()
       response.save()
```

You can also do this for refunds:

```
   p = PayPal()
   result = p.RefundTransaction("transid", "Partial", "USD", "10.00")
   if result == True:
       response = PayPalResponse()
       response.fill_from_response(p.GetRefundResponse(), action = "Refund")
       response.status = PayPalResponse.get_cancel_status()
       response.save()
```

You can directly charge money from the customer's credit card by simply performing <b>DoDirectPayment</b> method.

```

    p.DoDirectPayment("5475216669208487", "062015", "487", "MasterCard", "Ozgur", "Vatansever", Decimal(1), ipaddress = "127.0.0.1", street="my street", city = "istanbul", state="marmara", countrycode="TR", zip="34347")
```

# TEST APPLICATION #
I've just implemented a simple test application to show how to use PayPal Django Driver in your django applications. After you check out the source code, enter the directory "testapp" and run the application.