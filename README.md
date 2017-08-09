# WePay Bootstrap Checkout Form
A Boostrap styled checkout form with built in WePay tokenization functionality.  Just drop your WePay app's Client ID in to the right spot, and configure your backend, and you are good to go.

![Checkout form](/assets/USScreenshot.png)


## WePay Tokenization
This checkout form comes with all the hooks into the [WePay tokenization library](https://developer.wepay.com/docs/process-payments/custom-checkout). Tokenizing credit cards requires you to have a [PCI SAQ-A-EP](https://www.pcisecuritystandards.org/documents/SAQ_A-EP_v3.pdf) compliance level.  Without tokenization, you would need to pass the payer's credit card info to your server, which greatly increases your PCI scope.  Tokenization gives you the best of both worlds -- reduced PCI scope and complete control over the checkout experience.

Once you've tokenized the card, you can then send it to your backend and then call WePay's `/checkout/create` endpoint to actually charge the card.

## JQuery
This page uses JQuery to dynamically manipulate DOM elements.  We also use the [`jquery.payments.min.js`](https://github.com/stripe/jquery.payment) jquery plugin.  The plugin is officially deprecated, but it still provides useful functionality.  It helps format card info in a way that is easier to read, and helps take care of a lot of different validation on the client side.  The more errors that you catch on the client side, the better.

## Outside US Payments
WePay accepts credit card payments from anywhere in the world, but there are different requirements depending on where that person is from.

For US based payments, only a zipcode is required.  For outside of the US, a full address is required.  For Canadian based payers, we've added all of their available provinces in a dropdown.  For all other countries, they can enter their region.  There is no validation on regions or zip codes, but that could be added in future versions.


## Configuring for your System
There is some setup that you need to do to have this checkout form work with your existing system.  Everyone architects their websites differently, and while this checkout form is meant to work for everyone, there may be some nuances of your system that were not considered.

First is how you include your WePay Client ID.  We use a `meta` tag with the name `wepay_client_id` to assign the value.  The JavaScript included in the page will retrieve that value when attempting to tokenize the card info.

Second, is the currency that your merchant is allowed to process.  Every account associated with WePay has a currency value associated with it.  That currency matters in how we display the final total price.  We use a `meta` tag with the name `wepay_merchant_currency` to assign the value.  The available values are `USD`, `CAD` and `GBP`.

Third, you need to be able to display a total amount to the payer so they know how much they are paying.  This form displays the total in an input field with id `total`.  You can set that input field's value dynamically to show the total amount.  You likely don't want to pass this total back to your server though.  The total price should have already been decided.

Finally, you need to determine how to pass the data back to your server.  The code for this is left wide open for you to enter.  After the page tokenizes your card using the WePay tokenization library, you need to send that token to your backend to actually run the transaction using the WePay `/checkout/create` endpoint.

## Handling Errors
Even though we do a lot of front end validation, there could be errors that occur during the `/checkout/create` call.  You will want to have your backend pass those errors back to the front end and then display them appropriately.

The front end validation will highlight offending input fields.  There is also an element with id `error-message-text` where you can place other error messages to the user.  It's contained within another div with id `error-message` that is hidden to start.  Every time a new checkout request is made, `error-message` is hidden.  Make sure to call `$("#error-message").show()` to make the element appear after you write the error.
