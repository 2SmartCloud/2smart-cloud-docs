## Notifications API

To send notifications which will be displayed in mobile app should be used dedicated topic `<user-hash>/notifications/create` and published non-retain message with JSON string like this:

```JSON
{
    "message": "message to display",
    "logLevel": "info", // "info", "warning", "error"
    "senderType": "device",
    "type": "text",
    "senderId": "<device-id>"
}
```

Fields which could be changed:

* `logLevel` - allowed values: "info", "warning", "error". Represent how message will be displayed in mobile app interface ("info" - gray, "warning" - yellow, "error" - red).
* `message` - text string to show notification content.
* `senderId` - should contain value of your device_id in system.

After publishing message will be processed by system and pushed notification to mobile device.
