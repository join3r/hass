# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## Repository Overview

Home Assistant blueprints repo. Contains reusable automation blueprints for HA.

## Structure

- `blueprints/automation/` - Automation blueprint YAML files
- `CONFIGURATION_GUIDE.md` - User docs for climate control blueprint

## Blueprint Format

Blueprints use HA blueprint schema:
- `blueprint:` block with name, description, domain, inputs
- `trigger:` defines automation triggers
- `variables:` for template vars
- `condition:` controls when actions run
- `action:` with `choose:` for conditional logic

Key patterns in climate_control.yaml:
- `!input` references blueprint inputs
- Jinja2 templates in `value_template`
- `numeric_state` triggers for temp thresholds
- Time-based scheduling with day filtering

## Testing Changes

After modifying blueprints:
1. Copy to HA config: `~/.homeassistant/blueprints/automation/`
2. Reload: Developer Tools > YAML > Automations > Reload
3. Create/edit automation using the blueprint
