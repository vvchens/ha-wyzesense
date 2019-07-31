# WYZE Sense Home Assistant Component
*Note: You must be running **Home Assistant > 0.92**. If you run an older version you may follow the instructions below but replace binary_sensor.py with [this file](https://gist.github.com/kevinvincent/375a063723ecd8b0b06943e3d28ebc93).*

## Installation (HACS)
1. Add `https://github.com/kevinvincent/ha-wyzesense` as a [custom repository](https://custom-components.github.io/hacs/usage/settings/#add-custom-repositories)
2. Click install under "wyzesense"
3. Go to Configuration below

## Installation (Manual)
1. Download this repository as a ZIP (green button, top right)
2. Unzip the archive
3. Rename to just `wyzesense`
4. Place `wyzesense` under `<config_dir>/custom_components/`
   * You will need to create the `custom_components` folder if it does not exist
   * On Hassio the final location will be `/config/custom_components/wyzesense`
   * On Hassbian the final location will be `/home/homeassistant/.homeassistant/custom_components/wyzesense`
5. Plug in the WYZE Sense hub (the usb device) into an open port on your device.
6. Go to Configuration below

## Configuration
Add the following to your configuration file

```yaml
binary_sensor:
  - platform: wyzesense
    device: "/dev/hidraw0"
```
Most likely your device will be mounted to `/dev/hidraw0`. If you know it is mounted somewhere else then add the appropriate device.

## Usage
* Restart HA and the sensors you have already bound to the hub will show up in your entities as `off` with `assumed_state: true` and no `device_class`. These will update once the sensor triggers once.
  * This is because the hub will not know the state / type of the sensor until it triggers for the first time. The first time the component hears from the sensor, the state and the rest of the fields such as battery, device type (motion or door), signal strength, etc. will be shown. Icons that depend on the `device_class` will also automatically update on your UI once this is received.

* Entities will show up as `binary_sensor.<MAC>` for example (`binary_sensor.777A4656`).
  * As like any other entity you can change the entity id and friendly name from the states page, which will stick even after restarts.

## Services
### `wyzesense.scan`
* Call this service and then within 30 seconds, press the button on the side of a sensor with a pin until the red led flashes three times. The sensor will now be bound and show up in your entities.

### `wyzesense.remove`
* Removes a sensor. Make sure you call this service with the correct MAC address of the sensor (which is the string of numbers and possibly letters that looks like `777A4656`). You can find this in the entity's attributes in the developer section.

## Running into issues?

### Troubleshooting
* Permission denied /dev/hidraw0
  * Info
    * If you see this error on a Hassio installation please follow Reporting an Issue below. It is most likely an issue with your specific setup.
    * This is known to occur on Hassbian. This occurs when the group homeassistant is denied from accessing hidraw devices.
  * Solution
    * Create / Modify the file `/etc/udev/rules.d/99-com.rules` on your instance and insert `KERNEL=="hidraw*", SUBSYSTEM=="hidraw", MODE="0664", GROUP="homeassistant"`
    * Ensure the user running Home Assistant belongs to the homeassistant group

### Reporting an Issue
1. Setup your logger to print debug messages for this component using:
```yaml
logger:
  default: info
  logs:
    custom_components.wyzesense: debug
```
2. Restart HA
3. Verify you're still having the issue
4. File an issue in this Github Repository containing your HA log (Developer section > Info > Load Full Home Assistant Log)
   * The log file can also be found at `/<config_dir>/home-assistant.log`

