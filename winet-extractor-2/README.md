# Sungrow WiNet S/WiNet S2 Extraction Tool

## What is this tool

Some Sungrow inverters such as the SH10RS do not expose their metrics in a clean way. They do support Modbus but various values are missing making that integration unusable. It's also possible to access the metrics via cloud, however this results in 5 minute delayed data and requires internet access.

This project connects to the Sungrow WiNet S2 wifi dongle and communicates with it's Websocket API. This allows access to all available metrics visible from the WiNet interface and updates every 10 seconds (configurable).

It can also work for the older WiNet S dongles and has broad inverter support with minimal configuration.

This project may also be usable with the older Winet-S dongle and a range of other inverters and devices.

## Compatibility

This list is the confirmed working with the following hardware.

### Inverters

- Sungrow SH50RS/SH80RS/SH10RS - Single Phase Hybrid Inverters
- Sungrow SH10RT/SH15T - Three Phase Hybrid Inverters
- Sungrow SG80RS/SG10RS - Single Phase String Inverters
- Sungrow SG80RT/SG10RT - Three Phase String Inverters

### Batteries

- SBR064/SBR096/SBR128/SBR160/SBR192/SBR224/SBR256

Other devices connected to a Sungrow Winet S or S2 adapter may also work but have not been confirmed.

## Configuration

Before starting, you will need to know the hostname or IP address of your WiNet dongle.

You will also have to have your MQTT broker configured and know your credentials. This addon has been tested with Mosquito broker and Home Assistant MQTT autodiscovery.

1. Add the repository

[![Open your Home Assistant instance and show the add add-on repository dialog with a specific repository URL pre-filled.](https://my.home-assistant.io/badges/supervisor_add_addon_repository.svg)](https://my.home-assistant.io/redirect/supervisor_add_addon_repository/?repository_url=https%3A%2F%2Fgithub.com%2FNickStallman%2Fhome-assistant-repo)

2. Install the WiNet Extraction addon
3. Configure your Winet Host, and MQTT URL.

- The Winet Host is simply the IP address or hostname of your Winet-S2 adapter e.g. "192.168.1.100"
- The MQTT URL is in the format of `mqtt://<username>:<password>@<host>`
- (optional) If you have changed the default Winet username and password, you may specify the new password. If these fields are left blank then the default will be used.

4. Start the addon, and observe your detected devices in the addon log or via Integrations -> MQTT

### Useful Entities

These entities may be slightly different depending on your inverter, as the names of entities are pulled from the inverter.

#### Energy Dashbard

These are cumulative energy values measured in kwh, which are useful for Home Assistant's Energy Dashboard

- Grid Consumption: daily_purchased_energy
- Return to grid: daily_feed_in_energy or daily_feed_in_energy_pv
- Battery Charging: daily_battery_charging_energy
- Battery Discharging: daily_battery_discharging_energy

#### Realtime/Instantaneous Values

These are live values measured in kw.

- Total Solar Generation: mppt_total_power
- Individual MPPT Solar Generation: mppt1_power / mppt2_power / mppt3_power / mppt4_power
- Battery Charge: battery_charging_power
- Battery Discharge: battery_discharging_power
- Grid Consumption: purchased_power
- Return to grid: total_export_active_power

### Optional additional sensors

In order to expose the MPPT inputs to the Energy dashboard you need to integrate them to energy.

```yaml
sensor:
  - platform: integration
    unique_id: sh10rs_serialnumber_mppt1_energy
    source: sensor.sh10rs_serialnumber_mppt1_power
    name: SH10RS MPPT1 Energy
    method: left
    round: 4
  - platform: integration
    unique_id: sh10rs_serialnumber_mppt2_energy
    source: sensor.sh10rs_serialnumber_mppt2_power
    name: SH10RS MPPT2 Energy
    method: left
    round: 4
  - platform: integration
    unique_id: sh10rs_serialnumber_mppt3_energy
    source: sensor.sh10rs_serialnumber_mppt3_power
    name: SH10RS MPPT3 Energy
    method: left
    round: 4
  - platform: integration
    unique_id: sh10rs_serialnumber_mppt4_energy
    source: sensor.sh10rs_serialnumber_mppt4_power
    name: SH10RS MPPT4 Energy
    method: left
    round: 4
```

## Standalone mode

This addon can be run as a normal docker container, rather than as a Home Assistant Addon.

First clone the repository and copy `.env.example` to `.env` then fill in the configuration parameters.

Then run `./docker-compose-up.sh`. This is equivelant to `docker compose up` except it detects the correct architecture to build the image from.

Once everything is running smoothly you can run it in detatched mode with `./docker-compose-up.sh -d`

## Related Projects

- [GoSungrow](https://github.com/MickMake/GoSungrow)
- [SunGather](https://github.com/bohdan-s/SunGather)
- [Sungrow SHx Inverter Modbus Home Assistant](https://github.com/mkaiser/Sungrow-SHx-Inverter-Modbus-Home-Assistant)

## Analytics

Some anonymous analytics are collected to survey the use of this addon to see what Sungrow hardware it is working with and if any errors arise (e.g. for newer firmware versions). These analytics can be disabled on the Configuration tab of the addon.

No personal information is collected, only various error states and Sungrow model names.

## Feedback

Feedback is appreciated so please drop me a line or file an issue.
