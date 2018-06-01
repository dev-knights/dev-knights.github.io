---
layout: post
title: Manejo eficiente de Webhooks con Ruby on Rails
author: arandi
published: false
tags: webhooks ruby rails ror
---

Cuando trabajas con sistemas de pagos como Paypal o Stripe, la parte mas tediosa de implementar son los webhooks. Te explicaré de que forma puedes optimizar la implementación de tu webhook de forma limpia y ordenada.

---

## ¿Para qué sirven los Webhooks?

Los [webhooks](https://en.wikipedia.org/wiki/Webhook) son endpoints en tu aplicación por el cual un servicio como Paypal o Stripe te informarán sobre los eventos que ocurran dentro. Paypal y Stripe los usan para avisar sobre si un cargo fue exitoso o falló por ejemplo. Este mecanismo es necesario ya que los procesos de cargos, creación o cancelación de pagos se hacen de forma asincróna de nuestra aplicación. Es decir, en el momento que creamos un cargo a traves de la API no sabremos hasta en un futuro si el cargo fue exitoso o no.

## Implementación estandard

El webhook es un solo endpoint (ruta) que recive información mediante el método `POST` de HTTP. En ruby on rails una implementación de Webhook de Stripe podría ser la siguiente:

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
      when 'charge.failed'
        # handle failure charge event
      when 'customer.subscription.deleted'
      when 'customer.subscription.updated'
    end
  rescue Exception => ex
    render :json => {:status => 422, :error => "Webhook call failed"}
    return
  end
  render :json => {:status => 200}
end
```

El método `webhook` hace uso de un `switch-case` para poder determinar a través del tipo de evento que se ha recibido, cuál será el bloque de código a ejecutar. Ahora imagina implementar más eventos. Este método se volvería muy sucio y difícil de mantener.

## Implementación Limpia

La implementación más limpia que ha visto sobre este tema de webhooks es de [Cashier de Laravel](https://laravel.com/docs/5.6/billing#defining-webhook-event-handlers). El controlador que Cashier expone, permite manejar los eventos del webhook de forma automática mapeando cada evento a un metodo. La convención del controlador dice que si quieres manejar el evento `invoice.payment_succeeded`, entonces crea un método sobre el controlador cuyo nombre empiece por `handle` y la forma en `camelCase` del tipo de evento: `handleInvoicePaymentSucceeded`.

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

¿Cómo podriamos conseguir lo mismo con Rails?. Es sencillo gracias a la metaprogramación en Ruby. La implementación seria más o menos así:

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

En el método del webhook debemos convertir el tipo de evento al nombre del método que se encargara de manejar ese evento específicamente.

1. Convertimos los `'.'` por `'_'` y concatenamos con `"handle_"`.
2. Lo siguiente es pasar el mensaje de invocar el método resultante con el payload obtenido.
3. Posteriormente, si no existe un método implementado para un evento entraría en el `rescue NoMethodError => e` donde podemos realizar otra acción en su caso.

La implementación de Laravel Cashier para manejar los eventos de Stripe se pareció increible. Considero que de esta forma obtendrás un controlador mantenible y limpio. Un siguiente paso podría ser: Implementar [concerns](http://api.rubyonrails.org/classes/ActiveSupport/Concern.html) para agrupar en modulos los eventos que se deseen manejar.
