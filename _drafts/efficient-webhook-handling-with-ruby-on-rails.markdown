---
layout: post
title: Efficent webhook handlig with Ruby on Rails
author: arandi
published: false
tags: webhooks ruby rails ror
lang: en
---

When implementing a payment system as Paypal or Stripe, the more tedious part is implementing webhooks. In this post I wil explain you a how you can optimize the webhook and get a clean and light implementation.

---

## What are the Webhooks for?

[Webhooks](https://en.wikipedia.org/wiki/Webhook) are endpoints in your application where a service like Paypal or Stripe will inform you about the events happening in them. Paypal and Stripe use the to notify whether a charge was successful or not, for example. This mechanism is necesary given the charges proccess are asyncronous. Ergo, in the moment we create a charge using thier API we won't know until in the future if the charge was successful or not.

## Standard way

The webhook is just an endpoint (route or path) which receives information through `POST` method. In Rails a barebones implementation of Stripe webhook could be:

``` ruby
# controllers/stripe_controller.rb
# Method responsbile for handling stripe webhooks
def webhook
  begin
    event = JSON.parse(request.body.read)
    case event['type']
      when 'invoice.payment_succeeded'
        # handle success invoice event
      when 'invoice.payment_failed'
        # handle failure invoice event
      # More events...
    end
  rescue Exception => ex
    render :json => {:status => 400, :error => "Webhook failed"} and return
  end
  render :json => {:status => 200}
end
```

This `webhook` method uses a `switch-case` in order to determinate, with the event type that was received, which code block execute. Now imagine implement mor events, this method will smell and get hard to maintain.

Sure we can write more methods to put the logic of each event and call them into the `switch-case`, however we can still improve this webhook method.

## Cleaner way

The cleanest webhook implementation I've seen is in [Laravel Cashier](https://laravel.com/docs/5.6/billing#defining-webhook-event-handlers). Cashier's Webhook Controller allows you to handle automaticaly by mapping each event to a method. The controller convention says that if you want to handle the event `invoice.payment_succeeded` create a method in the controller whose name start with `handle` and the `camelCase` form of the event type: `handleInvoicePaymentSucceeded`.

```php
<?php

namespace App\Http\Controllers;

use Laravel\Cashier\Http\Controllers\WebhookController as CashierController;

class WebhookController extends CashierController
{
    /**
     * Handle a Stripe webhook.
     *
     * @param  array  $payload
     * @return Response
     */
    public function handleInvoicePaymentSucceeded($payload)
    {
        // Handle The Event
    }
}
```

## Refactoring

How can we achieve this with Rails?. Easy, thanks to metaprogramming in Ruby. Refactoring the previous method could get like this:

```ruby
def webhook
  begin
    event = JSON.parse(request.body.read)
    method = "handle_" + event['type'].tr('.', '_')
    self.send method, event
  rescue JSON::ParserError => e
    render json: {:status => 400, :error => "Invalid payload"} and return
  rescue NoMethodError => e
    # missing event handler
  end
  render json: {:status => 200}
end

def handle_invoice_payment_succeeded(event)
  #handle the event
end

def handle_invoice_payment_failed(event)
  #handle the event
end

# and more...
```

Int he webhook method we need to parse the event type to the mane of the method which will handle that event.

1. Translate all `'.'` to `'_'` and concatenate with `"handle_"`.
2. Then send the message to call the resultant method with the payload obtained.
3. Later, if there's no method implemented for that event, it will be rescued where we can make another action.

Laravel Cashier implementation looks wonderful to handle webhook events. Therefore this way you get a clean and maintainable controller.

### ProTip

As a next step you could write [concerns](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html) to group event handlers. Now you have a lighter controller.
