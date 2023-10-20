## Notifications API

To send notifications which will be displayed in mobile app should be used dedicated topic `<user-hash>/notifications/create` and published non-retain message with JSON string like this:

```JSON
{
    "message": "message to display",
    "level": "info", // "info", "warning", "error"
    "senderType": "device", // "device", "device_event"
    "type": "text",
    "senderId": "<device-id>"
}
```

Fields which could be changed:

* `level` - allowed values: "info", "warning", "error". Represent how message will be displayed in mobile app interface ("info" - gray, "warning" - yellow, "error" - red).
* `message` - text string to show notification content.
* `senderId` - should contain value of your device_id in system.
* `senderType` - allowed values: "device", "device_event". See `Notification types` below.


### Notification types

Device is able to send 2 different types of notifications to a platform:

1. Send notification to a user (`senderType: "device"`);
2. Send notification to a platform (`senderType: "device_event"`);

1st type could be used to send a user specific notifications. It could be any type of notification, for instance, the device is overheating, device changed configuration, the wifi connection quality drops, etc.

2d type is ONLY available in [Business Platform](https://2smart.com/wiki/business/about/)
This type could be used to send a short events (or reports) to a business platform, so admin could inspect device state and stability.
[More info could be found here](https://2smart.com/wiki/business/admin_panel/devices_management_page.html) in `Monitor device activity from the admin panel` topic.

