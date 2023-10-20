# Broadcast channel

Alike to `Broadcast Topic` described in `Homie` convention, we have a `Broadcast channel` in 2Smart Cloud platform.
We don't use `Homie` broadcast topic because it doesn't cover all our requirements, so we took an idea from it and made a custom topic structure.

The main idea behind `Broadcast` is to allow devices to sync values within user account (if needed).
For instance, this could be used in smart lighting, when user have multiple LED strips, LED bulbs, lamps, etc and he wants to control their state with a single request (like, turn on the light, change color to green, etc).

## API

### Description

The device value synchronization should be implemented on a firmware side. 2Smart Cloud only provides a "bus" to transfer data between devices within user's account.

```
Topic: `<user-hash>/sync-group/<subject>/<zone>/<namespace>`
Value: `<any>` // JSON string is the best choice, to cover multiple cases of synchronization
```

<subject> - one of `user` or `products`. This will allow us to setup value sync within products or by user configuration.
<zone> - synchronization zone. "default" for `products` <subject> and any custom name defined by user.
<namespace> - any ID that will allow devices to synchronize values within this namespace.

### Examples

```
<user-hash>/sync-group/products/default/led-lights => { "led-state": "false", "led-brightness": "100", ... }
<user-hash>/sync-group/user/kitchen/led-lights => { "led-state": "false", "led-brightness": "100", ... }
```

### Use cases

Let's imagine you are developing a smart LED lamp and you want to resolve next tasks:

1. Provide an ability for user to set the single color for LED lamp in house;
2. Provide an ability for user to set a single brightness for LED in room;

Without value synchronization, user should open an APP and set color or brightness for each device manually.
But it will be getting worse with 3 and more devices added to an account.

To resolve 1st case, device should be able to sync values within `<subject>: products`.
Basically, vendor should keep in firwmare a `<namespace>` which could be used for value synchronization between devices even from different products.
For instance, imagine you (as a vendor) have smart LED lamp and LED strip. And you need to keep the synchronization for such case too.
In this case, vendor should set the `<namespace>` for each product to define, which other products could also sync values within user's account.

To resolve 2d case, device firmware should have an ability to allow user to set the `<zone>` (for instance, room, house floor, etc) within which devices from the same `<namespace>` will be synchronizing the values.

### Important notes

1. At the moment, 2Smart Cloud is not responsible for providing unique `<namespace>` name for each vendor, so to keep the unique naming, we suggest to use some random values to prevent products of other vendors affect your devices.
We will be adding such feature in future.

2. Both use cases could be implemented independently, so device could provide user an ability to create it's own sync zones or to keep the sync feature ONLY within products.
