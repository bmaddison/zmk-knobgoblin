# KnobGoblin Macropad

A custom macropad with 19 keys and 2 rotary encoders designed for productivity and media control.

## Encoder Functionality

The KnobGoblin features two rotary encoders with both rotation and click actions:

### Top Encoder (Position: Upper Right)
- **Rotation**: 
  - Clockwise: Delete
  - Counter-clockwise: Backspace
- **Click Action**: Play/Pause media
- **Resolution**: 4 detents per rotation
- **Use Cases**: 
  - Quick text editing without moving hands to keyboard
  - Control media playback with a single click

### Bottom Encoder (Position: Middle Right)
- **Rotation**: 
  - Clockwise: Volume Up
  - Counter-clockwise: Volume Down
- **Click Action**: Mute/Unmute
- **Resolution**: 4 detents per rotation
- **Use Cases**: 
  - Adjust audio during calls or media playback
  - Quickly mute audio during meetings

## Layer-Specific Behavior

Encoder functionality is defined in the base layer and inherited by all other layers:

### Base Layer
- Both encoders function as described above
- Press the "NumPad" key to switch to Numpad layer

### Numpad Layer
- Encoders maintain the same functionality as Base layer
- Provides access to number pad keys and basic math operations
- Press the layer key to switch to System layer

### System Layer
- Encoders maintain the same functionality as Base layer
- Provides access to Bluetooth and system functions
- Press the layer key to return to Base layer

### Reserved Layer
- Not actively used but available for custom configurations

## Common Workflows

### Audio Call Management
1. Use bottom encoder to adjust call volume
2. Click the bottom encoder to quickly mute/unmute
3. Use the base layer for navigation during the call

### Text Editing
1. Use top encoder to delete (CW) or backspace (CCW) characters
2. Use arrow keys for cursor movement
3. Use keyboard shortcuts (Ctrl+C, Ctrl+V, etc.) for text operations

### Media Control
1. Click top encoder to play/pause media
2. Use bottom encoder to adjust volume
3. Click bottom encoder to mute/unmute

## Developer Configuration

### ZMK Configuration Requirements

To enable rotary encoders in your KnobGoblin, the following configurations are required in your `knob_goblin.conf` file:

```c
# Enable EC11 encoders
CONFIG_EC11=y
CONFIG_EC11_TRIGGER_GLOBAL_THREAD=y
```

### Matrix Transform

The KnobGoblin uses a 5×5 matrix with the following transform in the overlay file:

```c
/ {
    default_transform: keymap_transform0 {
      compatible = "zmk,matrix-transform";
      columns = <5>;
      rows = <5>;
      map = <
        RC(0,0) RC(0,1) RC(0,2) RC(0,3)
        RC(1,0) RC(1,1) RC(1,2) RC(1,3)
        RC(2,0) RC(2,1) RC(2,2) RC(2,3)
        RC(3,0) RC(3,1) RC(3,2) RC(3,3) RC(3,4)
        RC(4,0) RC(4,1) RC(4,2) RC(4,3) RC(4,4)
        >;
    };
};
```

### Encoder Pin Configuration

Encoders are connected to the following GPIO pins:

```
Encoder 1 (Top):
- A Pin: P0.31
- B Pin: P0.29

Encoder 2 (Bottom):
- A Pin: P0.02
- B Pin: P0.03
```

## Encoder Inheritance Behavior

### How Encoder Bindings Carry Through Layers

In ZMK, encoder bindings are typically specified in each layer. However, in this configuration, we've implemented inheritance to simplify maintenance:

- The base layer defines the encoder bindings for both encoders
- All other layers inherit these bindings using the `inherit_encoders` flag
- This ensures consistent encoder behavior across all layers

### Using the inherit_encoders Flag

In the info.json file, layers use the `inherit_encoders` flag instead of duplicating encoder bindings:

```json
"numpad": {
  "displayName": "Numpad",
  "keymap": [
    // Key mappings...
  ],
  "inherit_encoders": true
}
```

### Overriding Encoder Behavior

If you need to override encoder behavior in a specific layer:

1. Remove the `inherit_encoders` flag from that layer
2. Add explicit encoder bindings:

```json
"custom_layer": {
  "displayName": "Custom Layer",
  "keymap": [
    // Key mappings...
  ],
  "encoder-bindings": [
    ["PAGE_UP", "PAGE_DOWN"],
    ["C_BRIGHTNESS_INC", "C_BRIGHTNESS_DEC"]
  ]
}
```

## Troubleshooting

### Common Encoder Issues

1. **Reversed Direction**: If an encoder works but in the opposite direction, swap the A and B pin connections or update the configuration.

2. **Intermittent Response**: Check for loose connections or cold solder joints at the encoder pins.

3. **Multiple Triggers Per Detent**: Adjust the debounce settings or check for mechanical issues with the encoder.

### Resolution Adjustment

The default resolution is set to 4 detents per rotation. To adjust encoder sensitivity:

1. In the info.json file, modify the `resolution` value in the encoders section:
   ```json
   "resolution": 2  // More sensitive (half as many clicks needed)
   ```
   OR
   ```json
   "resolution": 8  // Less sensitive (twice as many clicks needed)
   ```

2. Higher values require more physical movement to trigger actions.

### Debounce Settings

If experiencing bounce issues (multiple triggers from a single movement):

1. Add the following to your `knob_goblin.conf` file:
   ```
   # Increase encoder debounce time (in milliseconds)
   CONFIG_EC11_DEBOUNCE_MSEC=8
   ```

2. Default is 5ms; increase to 8-10ms for problematic encoders.

## ZMK Studio Support

This configuration includes full ZMK Studio support, allowing you to visualize and customize your KnobGoblin layout through the ZMK Studio interface.
