# Climate Control Automation Blueprint - Features

## Overview
A comprehensive Home Assistant blueprint for intelligent climate control with extensive customization options.

## Core Features

### 🌡️ Temperature-Based Control
- **Minimum Temperature Threshold**: Set the temperature below which heating activates
- **Target Temperature**: Set the desired temperature at which heating deactivates
- **Smart Sensor Monitoring**: Continuously monitors temperature sensor readings
- **Availability Checks**: Ensures sensors and climate entities are available before acting

### ⏰ Schedule Control
- **Active Hours**: Define start and end times for daily operation
  - Default: 09:00 - 21:00
  - Prevents unwanted heating during night hours
  - Fully customizable per automation

- **Day Selection**: Choose specific days of the week
  - Select any combination of days (Monday - Sunday)
  - Perfect for weekday-only office heating
  - Weekend-only guest room heating
  - Or any custom schedule

### ⏱️ Configurable Timing Delays

#### Heating Activation Delay
- **Purpose**: How long temperature must stay below minimum before heating starts
- **Default**: 30 minutes
- **Range**: 1 - 120 minutes
- **Use Cases**:
  - Short delay (10-20 min): Quick response for bedrooms, living areas
  - Medium delay (30-45 min): Balanced for most rooms, prevents false triggers
  - Long delay (60+ min): Energy efficient for rarely used spaces

#### Heating Duration After Target Reached
- **Purpose**: How long to keep heating on after reaching target temperature
- **Default**: 15 minutes
- **Range**: 0 - 120 minutes
- **Use Cases**:
  - Short duration (5-10 min): Bedrooms to avoid overheating during sleep
  - Medium duration (15-20 min): Standard rooms for comfort maintenance
  - Long duration (30+ min): Large spaces or poorly insulated rooms

### 🎯 Smart Actions

#### When Temperature Drops
1. Waits for configured delay to confirm sustained low temperature
2. Checks if within active schedule (time + days)
3. Verifies climate entity is available
4. Sets climate to heating mode
5. Sets target temperature
6. Logs detailed action to Home Assistant logbook

#### When Temperature Reaches Target
1. Detects when temperature reaches or exceeds target
2. Checks if within active schedule (time + days)
3. Verifies climate entity is available and in heat mode
4. Continues heating for configured duration (allows temperature to stabilize)
5. After duration expires, turns off climate entity
6. Logs detailed action to Home Assistant logbook

### 🛡️ Safety Features
- **Sensor Availability Check**: Won't act if temperature sensor is unavailable/unknown
- **Climate Entity Check**: Won't act if climate device is unavailable/unknown
- **Schedule Enforcement**: Only operates during configured time and days
- **Single Mode**: Prevents multiple instances from conflicting
- **Time Delays**: Prevents rapid cycling and temperature oscillation
- **Detailed Logging**: All actions logged with context for troubleshooting

## Configuration Options Summary

| Input | Type | Default | Range/Options | Description |
|-------|------|---------|---------------|-------------|
| Climate Entity | Entity Selector | Required | Climate domain | AC/heater to control |
| Temperature Sensor | Entity Selector | Required | Temperature sensors | Sensor to monitor |
| Min Temperature | Number | 18° | 5° - 35° | Heating activation threshold |
| Target Temperature | Number | 22° | 10° - 40° | Heating deactivation threshold |
| Active Start Time | Time | 09:00:00 | Any time | Daily start time |
| Active End Time | Time | 21:00:00 | Any time | Daily end time |
| Active Days | Multi-select | All days | Mon-Sun | Days of week to operate |
| Heating Delay | Number | 30 min | 1 - 120 min | Delay before activation |
| Heating Duration | Number | 15 min | 0 - 120 min | Duration after target reached |

## Example Scenarios

### Scenario 1: Home Office (Weekdays Only)
```yaml
Active Days: Monday - Friday
Active Hours: 08:30 - 18:00
Min Temperature: 20°
Target Temperature: 24°
Heating Delay: 45 minutes
Heating Duration: 20 minutes
```
**Result**: Office stays warm during work hours on weekdays only, with longer delays for energy efficiency.

### Scenario 2: Bedroom (Night Time)
```yaml
Active Days: All days
Active Hours: 20:00 - 08:00
Min Temperature: 19°
Target Temperature: 23°
Heating Delay: 20 minutes
Heating Duration: 10 minutes
```
**Result**: Bedroom heating during sleeping hours with quick response and short duration to avoid overheating.

### Scenario 3: Living Room (All Week)
```yaml
Active Days: All days
Active Hours: 08:00 - 22:00
Min Temperature: 18.5°
Target Temperature: 22°
Heating Delay: 30 minutes
Heating Duration: 15 minutes
```
**Result**: Comfortable living room temperature throughout the week during waking hours.

### Scenario 4: Guest Room (Weekends Only)
```yaml
Active Days: Saturday - Sunday
Active Hours: 09:00 - 21:00
Min Temperature: 18°
Target Temperature: 21°
Heating Delay: 60 minutes
Heating Duration: 30 minutes
```
**Result**: Energy-efficient heating only when guests are likely present on weekends.

## Benefits

### Energy Efficiency
- Only heats during specified times and days
- Configurable delays prevent unnecessary heating
- Turns off promptly when target reached
- Prevents heating empty rooms

### Comfort
- Maintains desired temperature range
- Prevents temperature drops during active hours
- Customizable per room and schedule
- Smooth temperature transitions

### Flexibility
- Different schedules for different rooms
- Seasonal adjustments easy to make
- Work-from-home vs office schedules
- Weekend vs weekday patterns

### Reliability
- Comprehensive error checking
- Detailed logging for troubleshooting
- Single mode prevents conflicts
- Handles sensor/device unavailability gracefully

## Technical Details

- **Blueprint Domain**: automation
- **Trigger Type**: Numeric state with time delays
- **Condition Type**: Time-based with day selection + availability checks
- **Action Type**: Choose-based with climate service calls
- **Mode**: Single (prevents overlapping executions)
- **Logging**: Logbook integration with detailed messages

## Home Assistant Compatibility

- Requires Home Assistant with automation support
- Compatible with any climate entity supporting heating mode
- Works with any temperature sensor with device class "temperature"
- Uses standard Home Assistant services (climate.set_temperature, climate.turn_off)
- Follows Home Assistant blueprint best practices
