# Climate Control Automation - Troubleshooting Guide

## Common Issues and Solutions

### Issue: Automation Not Triggering

#### Check 1: Time and Day Conditions
The automation only runs during configured active hours and days.

**Your Configuration:**
- Active Time: 06:00 AM - 04:00 PM (16:00)
- Active Days: Monday, Tuesday, Wednesday, Thursday, Friday

**Solution:**
- Verify current time is between 06:00 and 16:00
- Verify today is a weekday (Monday-Friday)
- If it's outside these times/days, the automation will NOT trigger even if temperature conditions are met

#### Check 2: Temperature Trigger Delay
The automation waits for temperature to stay below minimum for the configured delay.

**Your Configuration:**
- Heating Activation Delay: 2 minutes
- Current Temperature: 23.3°C
- Minimum Threshold: 23.5°C

**Solution:**
- Temperature must stay below 23.5°C continuously for 2 minutes
- If temperature fluctuates above/below 23.5°C, the timer resets
- Check your temperature sensor history to see if it's been stable

#### Check 3: Automation State
**How to Check:**
1. Go to Settings > Automations & Scenes
2. Find your automation
3. Check if it's enabled (toggle should be ON)
4. Click on the automation to see its state and last triggered time

#### Check 4: Sensor Availability
**How to Check:**
1. Go to Developer Tools > States
2. Search for your temperature sensor: "Room temperature"
3. Verify state is a number (not "unavailable" or "unknown")
4. Verify it's updating regularly

#### Check 5: Climate Entity Availability
**How to Check:**
1. Go to Developer Tools > States
2. Search for your climate entity: "Pracovňa"
3. Verify state is not "unavailable" or "unknown"
4. Try manually controlling it to ensure it responds

### Issue: Heating Turns On But Doesn't Turn Off

#### Possible Causes:
1. **Temperature never reaches target**: If room temperature never reaches 24°C, heating won't turn off
2. **Automation disabled**: Check if automation is still enabled
3. **Climate entity state**: Verify climate entity is actually in "heat" mode

#### Solution:
- Check temperature sensor readings over time
- Verify target temperature (24°C) is reachable
- Check automation trace in Home Assistant for errors

### Issue: Heating Turns Off Too Quickly

#### Cause:
The "Heating Duration After Target Reached" is set too low.

**Your Configuration:**
- Heating Duration: 1 minute

#### Solution:
- Increase the duration to 10-15 minutes for better temperature stability
- This allows the room to warm up slightly above target before turning off
- Prevents rapid on/off cycling

### Issue: Heating Turns On Too Slowly

#### Cause:
The "Heating Activation Delay" is set too high.

**Your Configuration:**
- Heating Activation Delay: 2 minutes

#### Solution:
- Your setting is already quite fast (2 minutes)
- If you want even faster response, you can set it to 1 minute
- However, very short delays may cause false triggers from sensor fluctuations

## Understanding Your Current Configuration

### Temperature Logic
```
Current Temperature: 23.3°C
Minimum Threshold:   23.5°C  ← Heating should activate
Target Temperature:  24.0°C  ← Heating will turn off after reaching this + 1 minute
```

**What Should Happen:**
1. Temperature is 23.3°C (below 23.5°C minimum)
2. After staying below 23.5°C for 2 minutes → Heating turns ON
3. Heating warms room to 24°C
4. When temperature reaches 24°C → Wait 1 minute → Heating turns OFF

### Schedule Logic
```
Active Hours: 06:00 - 16:00 (6 AM - 4 PM)
Active Days:  Monday, Tuesday, Wednesday, Thursday, Friday
```

**What This Means:**
- Automation ONLY works during these times and days
- Outside this schedule, heating will NOT activate regardless of temperature
- If you need heating in the evening/night, adjust the end time

## Debugging Steps

### Step 1: Check Automation Trace
1. Go to Settings > Automations & Scenes
2. Click on your automation
3. Click the three dots menu (⋮) → "Traces"
4. Look at recent runs to see what happened

### Step 2: Check Logbook
1. Go to Settings > System > Logs
2. Search for "Climate Control Automation"
3. Look for log entries showing when automation ran

### Step 3: Manual Test
1. Temporarily change settings for immediate testing:
   - Set Heating Activation Delay to 1 minute
   - Set Minimum Threshold slightly above current temperature
   - Ensure current time is within active hours
   - Ensure current day is in active days
2. Wait 1 minute and check if heating activates
3. Check automation trace to see what happened

### Step 4: Check Conditions
Run this in Developer Tools > Template:
```yaml
Current Time: {{ now().strftime('%H:%M') }}
Current Day: {{ now().strftime('%A') }}
Temperature: {{ states('sensor.room_temperature') }}°C
Climate State: {{ states('climate.pracovna') }}
```

Compare results with your configuration to see if conditions are met.

## Recommended Settings for Your Use Case

Based on your configuration (office, weekdays, daytime), here are recommended settings:

### For Faster Response (Current Cold Situation)
```yaml
Minimum Temperature: 23.5°C
Target Temperature: 24.0°C
Heating Activation Delay: 2 minutes  ✓ (already optimal)
Heating Duration: 10 minutes  ← Increase from 1 minute
Active Hours: 06:00 - 16:00  ✓
Active Days: Weekdays  ✓
```

### For Energy Efficiency
```yaml
Minimum Temperature: 22.0°C
Target Temperature: 23.0°C
Heating Activation Delay: 15 minutes
Heating Duration: 15 minutes
Active Hours: 08:00 - 16:00
Active Days: Weekdays
```

### For Maximum Comfort
```yaml
Minimum Temperature: 23.0°C
Target Temperature: 24.0°C
Heating Activation Delay: 5 minutes
Heating Duration: 15 minutes
Active Hours: 06:00 - 18:00
Active Days: Weekdays
```

## Why Your Automation Might Not Be Working Right Now

### Most Likely Reasons:

1. **Outside Active Hours**: If current time is after 16:00 (4 PM), automation won't run
2. **Weekend**: If today is Saturday or Sunday, automation won't run
3. **Temperature Just Dropped**: If temperature dropped below 23.5°C less than 2 minutes ago, still waiting
4. **Automation Not Reloaded**: After updating blueprint, you need to reload automations

### Quick Fix Checklist:
- [ ] Current time is between 06:00 and 16:00
- [ ] Today is Monday-Friday
- [ ] Temperature has been below 23.5°C for at least 2 minutes
- [ ] Automation is enabled
- [ ] Temperature sensor is working (not unavailable)
- [ ] Climate entity is working (not unavailable)
- [ ] Automations have been reloaded after blueprint update

## Getting Help

If automation still doesn't work after checking all above:

1. **Export Automation Trace**:
   - Go to automation → Traces → Click on a trace
   - Copy the trace data

2. **Check Home Assistant Logs**:
   - Settings > System > Logs
   - Look for errors related to your automation

3. **Verify Blueprint Installation**:
   - Blueprint file should be in: `config/blueprints/automation/climate_control_automation.yaml`
   - After updating blueprint, reload automations: Developer Tools > YAML > Automations

4. **Test with Simple Settings**:
   - Set activation delay to 1 minute
   - Set minimum temperature well above current (e.g., 25°C)
   - Wait and see if it triggers when temperature drops below 25°C
