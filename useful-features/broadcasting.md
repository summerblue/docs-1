# Broadcasting

## Introduction

Masonite understands the developer need for building modern web applications so Masonite 1.4+ ships with WebSocket support. With a new Service Provider, configuration file and support for the `pusher` and `ably` drivers out of the box, we can now have full web socket support quickly and easily.

## Configuration

All broadcasting configuration is located in the `config/broadcast.py` file. There are only two options: `DRIVER` and `DRIVERS`. The `DRIVER` should hold the value of the driver you want to use such as `pusher`:

```python
DRIVER = 'pusher'
```

and `DRIVERS` should hold the configuration data:

```python
DRIVERS = {
    'pusher': {
        'app_id': os.getenv('PUSHER_APP_ID', '29382xx..'),
        'client': os.getenv('PUSHER_CLIENT', 'shS8dxx..'),
        'secret': os.getenv('PUSHER_SECRET', 'HDGdjss..'),
    },
    'ably': {
        'secret': os.getenv('ABLY_SECRET', 'api:key')
    }
}
```

Each driver may require it's own individual setting values so be sure to check the documentation for the driver you are using. For the `ably` and `pusher` drivers, these are the only values you will need.

Make sure that the key in the `DRIVER` setting has a corresponding key in the `DRIVERS` setting.

### Pusher

If you are using Pusher you will need the Pusher library:

{% code title="terminal" %}
```text
$ pip install pusher
```
{% endcode %}

### Ably

If you are using Ably you will need the ably driver:

{% code title="terminal" %}
```text
$ pip install ably
```
{% endcode %}

## Usage

Since we have a `ServiceProvider` Service Provider which takes care of the container bindings for us, we can now it simply by passing `Broadcast` into our parameter list in our controller methods like so:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    print(broadcast) # prints the driver class
```

We can change the driver on the fly as well:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    print(broadcast.driver('ably')) # prints the ably driver class
```

All drivers have the same methods so don't worry about different drivers having different methods.

### Channels

We can send data through our WebSocket by running:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    broadcast.channel('channel_name', 'message')
```

That's it! we have just sent a message to anyone subscribed to the `channel_name` channel.

We can also send a dictionary:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    broadcast.channel('channel_name', {'message': 'hello world'})
```

We can also send a message to multiple channels by passing a list:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    broadcast.channel(['channel1', 'channel2'], {'message': 'hello world'})
```

This will broadcast the message out to both channels. We can pass as many channels into the list as we like.

Masonite also has an optional third parameter which is the event name:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    broadcast.channel('channel_name', 'message', 'subscribed')
```

Which will pass the event on to whoever is receiving the WebSocket.

### Changing Drivers   <a id="changing-drivers"></a>

You can also swap drivers on the fly:

```python
from masonite import Broadcast

def show(self, broadcast: Broadcast):
    broadcast.driver('ably').channel('channel_name', 'message', 'subscribed')
```

or you can explicitly specify the class:

```python
from masonite.drivers import BroadcastAblyDriver

from masonite import Broadcast

def show(self, broadcast: Broadcast):
    broadcast.driver(BroadcastAblyDriver).channel('channel_name', 'message', 'subscribed')
```

