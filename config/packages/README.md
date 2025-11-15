# Elegoo Filament Spool Manager

This package provides filament tracking for your Elegoo CC (OC patch to enable extrusion data required).

## Features

- **4 Configurable Spools**: Each spool can be named and tracked independently
- **Automatic Usage Tracking**: Automatically tracks filament usage based on printer extrusion
- **Remaining Filament Calculation**: Real-time calculation of remaining filament
- **Easy Spool Switching**: Quick selection of active spool
- **Dashboard Cards**: Pre-built dashboard cards for easy visualization

## Setup Instructions

### 1. Initial Configuration

After starting Home Assistant, the spool manager will be automatically loaded. You need to configure one setting:

1. Open `config/packages/elegoo_spool_manager.yaml`
2. Find line 248 (the automation trigger)
3. Replace `sensor.elegoo_printer_total_extrusion` with your actual printer's total extrusion sensor entity ID (this should not be needed if installing via HACS, but if you don't see usage or have renamed your entities/devices, make the needed changes)

   **How to find your sensor entity ID:**
   - Go to Developer Tools → States in Home Assistant
   - Search for "total_extrusion"
   - Copy the full entity ID (e.g., `sensor.<whatever>_total_extrusion`)

### 2. Configure Your Spools

1. Go to **Settings → Devices & Services → Helpers**  (note - this can also be done easily from the dashboard)
2. You'll see all the spool configuration helpers:
   - **Spool Names**: Rename each spool (e.g., "Black PLA", "White PETG")
   - **Initial Lengths**: Set the starting length of each spool in millimeters
   - **Used Lengths**: These are automatically updated by the system

### 3. Add the Dashboard Card

1. Go to your Home Assistant dashboard
2. Click **Edit Dashboard** (three dots → Edit Dashboard)
3. Click **Add Card** (bottom right)
4. Choose **Manual** card
5. Copy and paste the contents from one of these files:
   - `config/dashboards/elegoo_spool_manager_simple.yaml` (recommended - no custom cards needed)
   - `config/dashboards/elegoo_spool_manager_card.yaml` (requires custom:bar-card)

When you install a new spool:

1. Set the density (per TDS) and weight in grams (1000 for 1kg spool etc.)
2. The length and remaining will automatically update

## Sensors Created

The package creates the following sensors:

### Control Entities
- `input_select.active_spool` - Select which spool is currently active
- `input_boolean.spool_tracking_enabled` - Enable/disable automatic tracking

### Spool Configuration (per spool)
- `input_text.spool_X_name` - Custom name for the spool
- `input_number.spool_X_initial_length` - Starting length in mm
- `input_number.spool_X_used_length` - Currently used length in mm (auto-updated)

### Calculated Sensors
- `sensor.remaining_filament` - Remaining filament on the active spool
- `sensor.active_spool_name` - Name of the currently active spool
- `sensor.spool_X_remaining` - Remaining filament for each spool (1-4)

Each spool remaining sensor includes attributes:
- `name` - Spool name
- `initial_length` - Starting length
- `used_length` - Used length
- `percent_remaining` - Percentage of filament remaining

## Advanced Customization

### Adding More Spools

To add more than 4 spools, duplicate the configuration blocks in `elegoo_spool_manager.yaml` and increment the numbers.


### Adding Notifications

You can create automations to notify you when a spool is running low:

```yaml
automation:
  - alias: "Low Filament Warning"
    trigger:
      - platform: numeric_state
        entity_id: sensor.remaining_filament
        below: 50000  # 50 meters
    action:
      - service: notify.notify
        data:
          message: "Warning: Active spool has less than 50m remaining!"
```