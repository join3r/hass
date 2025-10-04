# Home Assistant Climate Control Automation Blueprint

This repository contains a Home Assistant blueprint for automatically controlling an air conditioner with heating capability based on temperature sensor readings.

## Features

- **Automatic Heating Control**: Turns on heating when temperature drops below a threshold for 30 minutes
- **Automatic Heating Deactivation**: Turns off heating when temperature reaches target for 15 minutes
- **Configurable Parameters**: All key settings are user-configurable through the Home Assistant UI
- **Error Handling**: Includes checks for unavailable sensors and climate entities
- **Logging**: Provides detailed logbook entries for all actions taken

## Blueprint Inputs

### Required Inputs
- **Climate Entity**: The air conditioner/climate device to control (must support heating mode)
- **Temperature Sensor**: The temperature sensor to monitor for automation triggers

### Optional Inputs (with defaults)
- **Minimum Temperature Threshold**: Temperature below which heating activates (default: 18°)
- **Target/Desired Temperature**: Temperature at which heating deactivates (default: 22°)
- **Active Start Time**: Time when automation becomes active each day (default: 09:00)
- **Active End Time**: Time when automation becomes inactive each day (default: 21:00)

## Installation

1. Copy the blueprint file to your Home Assistant configuration directory:
   ```
   <config>/blueprints/automation/climate_control_automation.yaml
   ```

2. Restart Home Assistant or reload automations

3. Go to **Settings** > **Automations & Scenes** > **Blueprints**

4. Find "Climate Control Automation" and click **Create Automation**

## Usage

1. **Select Climate Entity**: Choose your air conditioner or climate device that supports heating mode
2. **Select Temperature Sensor**: Choose the temperature sensor you want to monitor
3. **Set Temperature Thresholds**:
   - **Minimum Temperature**: The temperature below which heating should activate
   - **Target Temperature**: The temperature at which heating should turn off
4. **Configure Active Hours**:
   - **Active Start Time**: When the automation should start each day (default: 09:00)
   - **Active End Time**: When the automation should stop each day (default: 21:00)
5. **Save the Automation**: Give it a descriptive name and save

## How It Works

### Time-Based Control
- Only operates during the configured active hours (default: 09:00 - 21:00)
- Outside of active hours, the automation will not trigger regardless of temperature

### Heating Activation
- Monitors the selected temperature sensor continuously during active hours
- When temperature drops below the minimum threshold AND stays there for 30 minutes:
  - Sets the climate entity to heating mode
  - Sets the target temperature to your desired temperature
  - Logs the action to the Home Assistant logbook

### Heating Deactivation
- When temperature reaches or exceeds the target temperature AND stays there for 15 minutes:
  - Turns off the climate entity
  - Logs the action to the Home Assistant logbook

### Safety Features
- Only active during specified time range to prevent unwanted operation at night
- Checks that both temperature sensor and climate entity are available before taking action
- Uses time delays (30 min for activation, 15 min for deactivation) to prevent rapid cycling
- Runs in "single" mode to prevent multiple instances from conflicting

## Example Configuration

```yaml
# Example automation created from this blueprint
automation:
  - alias: "Living Room Climate Control"
    use_blueprint:
      path: climate_control_automation.yaml
      input:
        climate_entity: climate.living_room_ac
        temperature_sensor: sensor.living_room_temperature
        min_temperature: 19
        target_temperature: 23
        active_start_time: "08:00:00"
        active_end_time: "22:00:00"
```

## Troubleshooting

- **Automation not triggering**: Check that your temperature sensor is reporting values and is available
- **Climate entity not responding**: Verify your climate entity supports heating mode and is available
- **Rapid cycling**: The blueprint includes 30-minute and 15-minute delays to prevent this, but check your temperature sensor placement
- **Wrong temperature units**: The blueprint works with whatever units your Home Assistant instance uses (°C or °F)

## Requirements

- Home Assistant with automation support
- A climate entity that supports heating mode (`climate.set_temperature` and `climate.turn_off` actions)
- A temperature sensor entity with device class "temperature"

## License

This blueprint is provided as-is for use with Home Assistant. Feel free to modify and distribute.
