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
- **Active Days**: Days of the week when automation is active (default: all days)
- **Heating Activation Delay**: How long temperature must stay below minimum before activating (default: 30 minutes)
- **Heating Duration After Target Reached**: How long to keep heating on after reaching target temperature (default: 15 minutes)

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
4. **Configure Active Schedule**:
   - **Active Start Time**: When the automation should start each day (default: 09:00)
   - **Active End Time**: When the automation should stop each day (default: 21:00)
   - **Active Days**: Select which days of the week the automation should run (default: all days)
5. **Configure Timing Delays**:
   - **Heating Activation Delay**: How long temperature must stay low before heating starts (default: 30 minutes)
   - **Heating Duration**: How long to keep heating on after reaching target (default: 15 minutes)
6. **Save the Automation**: Give it a descriptive name and save

## How It Works

### Schedule-Based Control
- Only operates during the configured active hours (default: 09:00 - 21:00)
- Only runs on selected days of the week (default: all days)
- Outside of active schedule, the automation will not trigger regardless of temperature

### Heating Activation
- Monitors the selected temperature sensor continuously during active schedule
- When temperature drops below the minimum threshold AND stays there for the configured delay (default: 30 minutes):
  - Sets the climate entity to heating mode
  - Sets the target temperature to your desired temperature
  - Logs the action to the Home Assistant logbook

### Heating Deactivation
- When temperature reaches or exceeds the target temperature:
  - Continues heating for the configured duration (default: 15 minutes)
  - After the duration expires, turns off the climate entity
  - Logs the action to the Home Assistant logbook

### Safety Features
- Only active during specified time range and days to prevent unwanted operation
- Checks that both temperature sensor and climate entity are available before taking action
- Uses configurable time delays to prevent rapid cycling and allow temperature stabilization
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
        active_days:
          - mon
          - tue
          - wed
          - thu
          - fri
          - sat
          - sun
        heating_delay: 30
        heating_duration: 15
```

## Common Use Cases

### 1. Office - Weekdays Only
Perfect for home offices that only need heating during work hours on weekdays:
- **Active Days**: Monday - Friday
- **Active Hours**: 08:30 - 18:00
- **Heating Delay**: 45 minutes (to avoid heating during brief temperature drops)
- **Heating Duration**: 20 minutes (keeps room warm after target reached)

### 2. Bedroom - Night Time Only
For bedrooms that only need heating during sleeping hours:
- **Active Days**: All days
- **Active Hours**: 20:00 - 08:00
- **Heating Delay**: 20 minutes (faster response for comfort)
- **Heating Duration**: 10 minutes (shorter to avoid overheating while sleeping)

### 3. Living Room - All Week
For main living areas used throughout the week:
- **Active Days**: All days
- **Active Hours**: 08:00 - 22:00
- **Heating Delay**: 30 minutes (balanced response)
- **Heating Duration**: 15 minutes (standard duration)

### 4. Guest Room - Weekends Only
For guest rooms only used on weekends:
- **Active Days**: Saturday - Sunday
- **Active Hours**: 09:00 - 21:00
- **Heating Delay**: 60 minutes (energy efficient, slower response)
- **Heating Duration**: 30 minutes (longer to maintain comfort)

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
