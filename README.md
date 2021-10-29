# 2smart-cloud-docs

## SDK
- [JavaScript](https://github.com/2SmartCloud/homie-sdk)
- [ESP32](https://github.com/2SmartCloud/2smart-cloud-esp32-boilerplate)
- [ESP8266](https://github.com/2SmartCloud/2smart-cloud-esp8266-boilerplate)

## 2Smart Cloud protocol
The protocol in the project is based on the convention [homie.io](https://homieiot.github.io/specification/spec-core-v3_0_1/) of the **3.0.1** version.

Root topic in 2Smart Cloud: `<user-hash>/sweet-home`

`<user-hash>` - is a sha256 hash from user email in mobile app.

### Extension of the protocol

1.  The device has a group of attributes `$options`, which in format repeats the `$properties` from the node. Used to store device settings, both editable and not. Example: `<user-hash>/sweet-home/device/$options/some-option`.
2.  The device has a group of attributes `$telemetry`, which in format repeats the `$properties` from the node. Used to store various telemetry and statistics. `$stats` is not used. Example: `<user-hash>/sweet-home/device/$telemetry/some-telemetry`.
3.  A node has a `$state` attribute, similar to a device. Example: `<user-hash>/sweet-home/device/node/$state`.
4.  The node has a group of attributes `$options`, similar to the device. Example: `<user-hash>/sweet-home/device/node/$options/some-option`.
5.  The node has a group of attributes `$telemetry`, similar to the device. Example: `<user-hash>/sweet-home/device/node/$telemetry/some-telemetry`.
6.  A mechanism for validating the set values has been added to the application:
    *  On the received message, in case of incorrect values, the error body should be sent to a topic like `errors/sweet-home/device/node...` The error body should be in the JSON format of the string "{" code ": 'error_code'," message ": 'error message'}". (For example, if an incorrect value has come to the topic `<user-hash>/sweet-home/device/node/sensor/set`, then the error should be sent to the topic `<user-hash>/errors/sweet-home/device/node/sensor`)
     *  Valid for any editable values (`$properties`,`$options`, `$telemetry`).

7.  Device and node indicators are taken from the following attributes:
     *  Status (state) - `$state`.
     *  Signal strength - `$telemetry/signal`.
     *  Battery charge level - `$telemetry/battery`.


### Required state for creating an entity in an application

* For device: `$homie`, `$name`, `$localip`, `$mac`, `$fw/name`, `$fw/version`, `$implementation`, `$state`.
* For node: `$name`, `$state` plus at least one property.
* For properties: (`$properties`, `$options`, `$telemetry`): `$name`.


### Using in interfaces

*  `$options`, `$telemetry` for a device and properties, `$options`, `$telemetry` for a node are displayed in separate tabs in the same way. Any attribute from any group can be editable. If `$settable=true`, then a controller should be displayed next to the field, the type of which depends on `$datatype`.

*  Homieiot does not support device and node configuration, which is why we add `$options`. It is similar to Properties so that it is easy to add new parameters and reuse controllers to control them.

*  Homieiot has `$stats`, but the set of metrics is fixed and not self-descriptive. Those. it is impossible not only to add a new metric, but for the existing ones it is necessary to flash the field names, their type, etc. in the interfaces. Therefore, we added `$telemetry` and we also do them according to the principle of Properties.


### Device heartbeat 

The device must publish a non-retain message to the broker in a dedicated topic so that the system can understand if the device is working.
The device has an attribute `$heartbeat`, where the device should send a message any message, for example - 'ping', with a frequency of 10 seconds

Example: `<user-hash>/sweet-home/device/$heartbeat`


### Device example in MQTT

```
{
    "<user-hash>/sweet-home/weather-station/$name": "Weather",
    "<user-hash>/sweet-home/weather-station/$fw/name": "#",
    "<user-hash>/sweet-home/weather-station/$fw/version": "#",
    "<user-hash>/sweet-home/weather-station/$localip": "#",
    "<user-hash>/sweet-home/weather-station/$mac": "#",
    "<user-hash>/sweet-home/weather-station/$implementation": "#",
    "<user-hash>/sweet-home/weather-station/$state": "ready",
    "<user-hash>/sweet-home/weather-station/$options": "location",
    "<user-hash>/sweet-home/weather-station/$options/location": "Kyiv",
    "<user-hash>/sweet-home/weather-station/$options/location/$name": "Location",
    "<user-hash>/sweet-home/weather-station/$options/location/$settable": "true",
    "<user-hash>/sweet-home/weather-station/$options/location/$retained": "true",
    "<user-hash>/sweet-home/weather-station/$options/location/$datatype": "string",
    "<user-hash>/sweet-home/weather-station/$options/location/$unit": "#",
    "<user-hash>/sweet-home/weather-station/$nodes": "thermometer,sky-indicator,vane",
    "<user-hash>/sweet-home/weather-station/thermometer/$name": "Thermometer",
    "<user-hash>/sweet-home/weather-station/thermometer/$type": "#",
    "<user-hash>/sweet-home/weather-station/thermometer/$state": "ready",
    "<user-hash>/sweet-home/weather-station/thermometer/$properties": "temperature-sensor",
    "<user-hash>/sweet-home/weather-station/thermometer/temperature-sensor": "-9",
    "<user-hash>/sweet-home/weather-station/thermometer/temperature-sensor/$name": "Current temperature",
    "<user-hash>/sweet-home/weather-station/thermometer/temperature-sensor/$settable": "false",
    "<user-hash>/sweet-home/weather-station/thermometer/temperature-sensor/$retained": "true",
    "<user-hash>/sweet-home/weather-station/thermometer/temperature-sensor/$datatype": "float",
    "<user-hash>/sweet-home/weather-station/thermometer/temperature-sensor/$unit": "°C",
    "<user-hash>/sweet-home/weather-station/thermometer/$options": "desc",
    "<user-hash>/sweet-home/weather-station/thermometer/$options/desc": "Temperature in Kyiv: -9°C",
    "<user-hash>/sweet-home/weather-station/thermometer/$options/desc/$name": "Description",
    "<user-hash>/sweet-home/weather-station/thermometer/$options/desc/$settable": "false",
    "<user-hash>/sweet-home/weather-station/thermometer/$options/desc/$retained": "true",
    "<user-hash>/sweet-home/weather-station/thermometer/$options/desc/$datatype": "string",
    "<user-hash>/sweet-home/weather-station/thermometer/$options/desc/$unit": "#",
    "<user-hash>/sweet-home/weather-station/sky-indicator/$name": "Sky indicator",
    "<user-hash>/sweet-home/weather-station/sky-indicator/$type": "#",
    "<user-hash>/sweet-home/weather-station/sky-indicator/$state": "ready",
    "<user-hash>/sweet-home/weather-station/sky-indicator/$properties": "sky-sensor",
    "<user-hash>/sweet-home/weather-station/sky-indicator/sky-sensor": "Partly Cloudy",
    "<user-hash>/sweet-home/weather-station/sky-indicator/sky-sensor/$name": "Current sky state",
    "<user-hash>/sweet-home/weather-station/sky-indicator/sky-sensor/$settable": "false",
    "<user-hash>/sweet-home/weather-station/sky-indicator/sky-sensor/$retained": "true",
    "<user-hash>/sweet-home/weather-station/sky-indicator/sky-sensor/$datatype": "string",
    "<user-hash>/sweet-home/weather-station/sky-indicator/sky-sensor/$unit": "#",
    "<user-hash>/sweet-home/weather-station/vane/$name": "Wind indicator",
    "<user-hash>/sweet-home/weather-station/vane/$type": "#",
    "<user-hash>/sweet-home/weather-station/vane/$state": "ready",
    "<user-hash>/sweet-home/weather-station/vane/$properties": "speed-indicator,direction-indicator",
    "<user-hash>/sweet-home/weather-station/vane/speed-indicator": "9",
    "<user-hash>/sweet-home/weather-station/vane/speed-indicator/$name": "Wind speed",
    "<user-hash>/sweet-home/weather-station/vane/speed-indicator/$settable": "false",
    "<user-hash>/sweet-home/weather-station/vane/speed-indicator/$retained": "true",
    "<user-hash>/sweet-home/weather-station/vane/speed-indicator/$datatype": "integer",
    "<user-hash>/sweet-home/weather-station/vane/speed-indicator/$unit": "km/h",
    "<user-hash>/sweet-home/weather-station/vane/direction-indicator": "250",
    "<user-hash>/sweet-home/weather-station/vane/direction-indicator/$name": "Wind direction",
    "<user-hash>/sweet-home/weather-station/vane/direction-indicator/$settable": "false",
    "<user-hash>/sweet-home/weather-station/vane/direction-indicator/$retained": "true",
    "<user-hash>/sweet-home/weather-station/vane/direction-indicator/$datatype": "integer",
    "<user-hash>/sweet-home/weather-station/vane/direction-indicator/$unit": "°"
}
```

## Additional features

- OTA updates
- Device versioning
- Device pairing
- Analytics
- etc

## Contact us

[Contact us](https://2smart.com/business) to get an additional information or read our [blog](https://2smart.com/blog) to stay tuned with our new features!
