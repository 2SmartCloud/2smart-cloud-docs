## Device Pairing

There such requirements for pairing process:

1. Device should host Wi-Fi access point (AP)
2. AP name should be like 'Product title AP' to be clear for user
3. Device should implement 2 endpoints
    1) `GET /healthcheck` should return HTTP status `200 OK` if device is available.
    2) `GET /pair` accept query parameters to connect to user`s Wi-Fi AP:
        - `ssid` - name of user`s AP
        - `psk` - password for user`s AP
        - `wsp` - user`s email in system (for login in broker you need get SHA256 hash from this string and use it as login)
        - `token` - user`s password to connect to broker
        - `host` - hostname of 2Smart Cloud to connect to
        - `brport` - mqtt TCP port
        - `rootTopic` - root topic of a device for mqtt broker
        - `mqtts_port` - mqtt TLS port

        Device should respond to this request with `200 OK` in case of no errors, `400 <message>` in case of validation error, `500 <message>` in case of internal error. This credential should be stored at device's filesystem for future communication with the platform.

