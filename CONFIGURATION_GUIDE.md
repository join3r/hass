# Climate Control Automation - Configuration Guide

## Blueprint Inputs

### Required Inputs
1. **Climate Entity** - Your AC/climate device to control
2. **Temperature Sensor** - The sensor to monitor room temperature

### Temperature Thresholds
3. **Minimum Temperature Threshold** (default: 20°)
   - When sensor drops **below** this temperature → AC turns ON
   
4. **Target Temperature** (default: 22°)
   - When sensor rises **above** this temperature → AC turns OFF

### AC Settings
5. **AC Set Temperature** (default: 24°)
   - The temperature to set on the AC when it turns on
   - This is what the AC will be set to (not necessarily what the room will reach)

6. **AC Mode** (default: Heat)
   - **Heat** - Heating mode
   - **Cool** - Cooling mode
   - **Heat/Cool (Auto)** - Automatic mode
   - **Dry** - Dehumidification mode
   - **Fan Only** - Fan without heating/cooling

## Example Configurations

### Example 1: Winter Heating
```yaml
Climate Entity: climate.bedroom_ac
Temperature Sensor: sensor.bedroom_temperature
Minimum Temperature Threshold: 22.5°C
Target Temperature: 24°C
AC Set Temperature: 26°C
AC Mode: Heat
```

**How it works:**
- Room temperature drops to 22.4°C → AC turns ON in Heat mode, set to 26°C
- Room temperature rises to 24.1°C → AC turns OFF

### Example 2: Summer Cooling
```yaml
Climate Entity: climate.living_room_ac
Temperature Sensor: sensor.living_room_temperature
Minimum Temperature Threshold: 18°C
Target Temperature: 26°C
AC Set Temperature: 22°C
AC Mode: Cool
```

**How it works:**
- Room temperature rises to 26.1°C → AC turns ON in Cool mode, set to 22°C
- Room temperature drops to 17.9°C → AC turns OFF

Note: For cooling, set the minimum threshold lower than target temperature.

### Example 3: Dehumidification
```yaml
Climate Entity: climate.bathroom_ac
Temperature Sensor: sensor.bathroom_temperature
Minimum Temperature Threshold: 20°C
Target Temperature: 24°C
AC Set Temperature: 23°C
AC Mode: Dry
```

**How it works:**
- Room temperature drops to 19.9°C → AC turns ON in Dry mode, set to 23°C
- Room temperature rises to 24.1°C → AC turns OFF

## Understanding the Difference

### Minimum Temperature Threshold vs AC Set Temperature

- **Minimum Temperature Threshold**: The room temperature that triggers the automation
- **AC Set Temperature**: What temperature you tell the AC to achieve

**Example:**
```
Minimum Threshold: 22.5°C  ← Automation trigger
AC Set Temperature: 26°C   ← What AC is set to
```

When room drops to 22.5°C, the AC turns on and is set to 26°C. The AC will heat the room, and when the room sensor reads above the Target Temperature, the automation turns off the AC.

## Tips

### For Heating Mode
- Set **AC Set Temperature** higher than **Target Temperature**
- This ensures the AC heats effectively until the target is reached

### For Cooling Mode
- Set **AC Set Temperature** lower than **Minimum Temperature Threshold**
- Reverse the logic: use high "minimum" and low "target" for cooling

### For Best Results
- Place temperature sensor away from the AC unit
- Ensure sensor updates frequently (every 1-2 minutes)
- Set thresholds with at least 1-2°C difference to avoid rapid cycling

## After Updating Blueprint

1. **Reload Automations**:
   - Go to Developer Tools > YAML > Automations > Reload

2. **Edit Your Automation**:
   - Go to Settings > Automations & Scenes
   - Find your automation
   - Click Edit
   - Configure the new AC Temperature and AC Mode settings
   - Save

3. **Test**:
   - The automation will check current temperature immediately after reload
   - If temperature is below minimum threshold, AC should turn on
