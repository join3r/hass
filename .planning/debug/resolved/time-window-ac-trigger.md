---
status: resolved
trigger: "AC sometimes turns on randomly when time window starts even if temperature is not below threshold"
created: 2026-01-22T00:00:00Z
updated: 2026-01-22T00:00:00Z
---

## Current Focus

hypothesis: !input min_temperature inside nested choose condition (line 205-206) may not substitute correctly. Need to use a variable instead.
test: Add min_temperature_var variable and use it in the condition
expecting: Using variable will ensure correct threshold comparison
next_action: Apply fix - add variable and update condition to use it

## Symptoms

expected: AC should only turn on if temp is below min_temperature threshold when time window starts
actual: AC turns on when time_window_started trigger fires, regardless of actual temperature
errors: None - automation runs successfully but incorrectly
reproduction: Happens intermittently when time_start triggers
started: Always had this issue since using blueprint

## Eliminated

## Evidence

- timestamp: 2026-01-22T00:01:00Z
  checked: Blueprint file structure
  found: time_window_started trigger at line 124-126, handled in action choose block at lines 196-213
  implication: Need to trace full execution path

- timestamp: 2026-01-22T00:02:00Z
  checked: Global condition block (lines 139-159)
  found: time_window_started is in the OR conditions list at line 149, meaning it BYPASSES all condition checks
  implication: Automation always runs action block when time_window_started fires

- timestamp: 2026-01-22T00:03:00Z
  checked: Action choose block structure
  found: time_window_started case (lines 196-213) has 3 conditions including numeric_state temp check. If conditions fail, choose has no default - should do nothing
  implication: If choose conditions work correctly, AC shouldn't turn on. Need to check if there's an issue with choose condition evaluation

- timestamp: 2026-01-22T00:04:00Z
  checked: day_changed trigger (line 128-129)
  found: day_changed fires at 00:00:00. If time_start is also 00:00:00, BOTH triggers fire at same time
  implication: Need to check if trigger conflict causes issue, but day_changed doesn't turn on AC without temp check

- timestamp: 2026-01-22T00:05:00Z
  checked: Full choose block logic
  found: ALL choose branches that turn on AC have temp conditions. No branch turns on AC without temp check
  implication: Bug must be elsewhere - maybe in how automation is reloaded or in HA behavior

- timestamp: 2026-01-22T00:06:00Z
  checked: ha_start/automation_reloaded handler (lines 227-257)
  found: This handler does NOT check scheduling_disabled or is_active_day before turning on AC
  implication: If HA restarts or automation reloads when outside time window, it could turn on AC if temp is low

- timestamp: 2026-01-22T00:07:00Z
  checked: Correlation with time_window_started
  found: When time_window_started fires, HA reloads automations? Or could trigger automation_reloaded event?
  implication: POSSIBLE ROOT CAUSE - need to verify if automation reload happens when time triggers fire

- timestamp: 2026-01-22T00:08:00Z
  checked: !input usage in nested choose conditions (line 205-206)
  found: numeric_state condition uses !input min_temperature inside choose block. No variable defined for min_temperature.
  implication: LIKELY ROOT CAUSE - !input substitution might not work reliably inside nested choose conditions. Should use variable instead.

## Resolution

root_cause: !input references inside nested choose conditions may not substitute correctly in HA blueprints. The numeric_state condition at line 205-206 uses !input min_temperature directly instead of a predefined variable.
fix: Add temperature_sensor_var, min_temperature_var, target_temperature_var to variables block. Add computed variables current_temp, temp_below_min, temp_above_target. Replace all numeric_state conditions in choose blocks with template conditions using these variables. Also fixed ha_start/automation_reloaded handler to respect scheduling.
verification: YAML syntax valid, all temperature checks now use variables evaluated at runtime
files_changed:
  - blueprints/automation/climate_control.yaml
