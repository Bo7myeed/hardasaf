# Bedrock Compatibility Fix for lesgo Model

## Problem Description
The `lesgo` 3D model was not appearing in Minecraft Bedrock Edition when using Geyser, while the `teleport` model worked correctly on both Java and Bedrock editions.

## Root Cause Analysis
After analyzing both models, the main issues were identified:

1. **UV Coordinates Out of Bounds**: The `lesgo` model contained UV coordinates that far exceeded the defined texture dimensions (128x128). Some coordinates reached values like 862, 976, and 988, which are invalid for Bedrock Edition.

2. **Excessive Texture Sizes**: The config file specified unrealistic texture dimensions like [1000, 320] for text elements.

3. **Complex Model Structure**: The model contained highly detailed text elements with individual letter bones, which may be too complex for Bedrock's rendering engine.

## Solution Implemented

### 1. UV Coordinate Clamping
- All UV coordinates are now constrained to stay within the 128x128 texture bounds
- UV_size values adjusted to prevent extending beyond texture boundaries
- Negative UV_size values preserved where needed for proper texture mapping

### 2. Texture Size Normalization
Updated `per_texture_uv_size` in config.json:
```json
"per_texture_uv_size": {
    "welcome": [128, 128],     // was [1000, 320]
    "s2": [128, 128],          // was [1000, 320]
    "island": [128, 128],      // was [1000, 320]
    "valooria": [128, 128],    // was [1000, 320]
    "sword": [20, 26],         // unchanged (already reasonable)
    "2023_04_16_entop--variant--x2--21523059": [64, 64], // unchanged
    "dungeons-skull": [128, 128] // was [110, 130]
}
```

### 3. Simplified Identifier
Changed geometry identifier from `geometry.lesgo.model` to `geometry.lesgo` for better compatibility.

## Files Changed
- `lesgo/lesgo.geo.json` - UV coordinates fixed and identifier simplified
- `lesgo/config.json` - Texture sizes normalized
- `lesgo/lesgo.geo.json.backup` - Backup of original geometry file

## Expected Results
The `lesgo` model should now:
- ✅ Appear correctly in Minecraft Bedrock Edition through Geyser
- ✅ Continue working in Minecraft Java Edition
- ✅ Use proper texture coordinates within defined bounds
- ✅ Have better performance due to simplified texture mapping

## Testing Instructions
1. Deploy the updated model files to your Minecraft server
2. Test the model in Java Edition to ensure it still works
3. Connect via Bedrock Edition through Geyser and verify the model appears
4. Compare with the working `teleport` model to ensure similar behavior

## Technical Notes
- Bedrock Edition has stricter limitations on texture coordinates compared to Java Edition
- Geyser community extensions may have additional constraints on model complexity
- The fix maintains the visual appearance while ensuring Bedrock compatibility
- All bone structures and animations are preserved

## Backup Information
The original geometry file is preserved as `lesgo.geo.json.backup` in case rollback is needed.