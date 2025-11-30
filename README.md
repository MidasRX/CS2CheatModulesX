# CS2CheatModulesX 
Made for skibidi toilet for skidder 
A modular Counter-Strike 2 internal cheat featuring advanced scripting, model changing, and customization capabilities.

## 🎯 Features

### 🔧 Core Modules

#### **Lua Scripting System**
- Full Lua scripting support with extensive API
- Hot-reload capabilities
- Custom resolver creation
- ESP and visual modifications
- Movement and input manipulation
- Event-based callback system

#### **Model Changer**
- Character folder support with automatic scanning
- Player model + arms model detection
- Material file detection (.vmat, .vtex)
- Drag-and-drop folder support
- Auto-apply on selection
- Preview and information display

### Lua Scripting
1. Navigate to **Other → Lua Scripts** in the menu
2. Click **Open Folder** to access `lua_scripts/`
4. Create your `.lua` files or use the examples
5. Click **Refresh Scripts** to load them
6. Enable scripts with checkboxes

### Model Changer
1. Navigate to **Skin Changer → Model Changer**
2. Click **Open Models Folder**
3. Create character folders with structure:
   ```
   models/MyCharacter/
   ├── player.vmdl_c
   ├── arms.vmdl_c (optional)
   └── materials/
       ├── texture.vmat_c
       └── texture.vtex_c
   ```
4. Click **Refresh Models List**
5. Select from dropdown and join game

---

## 📚 Module Documentation

### [Lua Manager](LUA_MANAGER.md)
Complete Lua scripting system with API reference and examples.

### [Model Changer](MODEL_CHANGER.md)
Character model and arms customization system.

---

## 🎨 Customization

### Creating Lua Scripts
See `lua_scripts/README.md` for complete API documentation including:
- Drawing functions
- Player information
- Config manipulation
- Resolver functions
- Event callbacks
- Math utilities

### Adding Custom Models
1. Prepare your `.vmdl_c` compiled models
2. Include material files (`.vmat_c`, `.vtex_c`)
3. Optional: Include arms models
4. Place in character folders under `models/`
5. System auto-detects and combines models

---

## 🛠️ Building

### Requirements
- Visual Studio 2022
- Windows SDK 10.0
- C++20 or later

## 🔧 Advanced Features

### Lua Resolver
Create custom resolvers using the Lua API:
```lua
function ResolvePlayer(player)
    local vx, vy, vz = GetVelocity(player)
    local speed = math.sqrt(vx*vx + vy*vy)
    
    if speed < 5 then
        OverrideResolver(player, 60)  -- Standing
    else
        local yaw = math.deg(math.atan2(vy, vx))
        OverrideResolver(player, yaw)  -- Moving
    end
end
```

### Custom ESP
Draw custom visuals with Lua:
```lua
function OnPaint()
    local player = GetLocalPlayer()
    if not player then return end
    
    local x, y, z = GetOrigin(player)
    local sx, sy, onScreen = WorldToScreen(x, y, z)
    
    if onScreen then
        DrawCircle(sx, sy, 5, 255, 0, 0, 255)
    end
end
```

---

## 📝 API Reference

### Lua Scripting API
- **Console**: `Print()`, `PrintColor()`
- **Config**: `GetBool()`, `SetInt()`, `GetFloat()`
- **Player Info**: `GetHealth()`, `GetTeam()`, `GetOrigin()`
- **Resolver**: `GetVelocity()`, `OverrideResolver()`, `IsOnGround()`
- **Drawing**: `DrawLine()`, `DrawRect()`, `DrawText()`
- **World**: `WorldToScreen()`, `Distance()`, `AngleVectors()`
- **Callbacks**: `OnPaint`, `OnCreateMove`, `OnRoundStart`

See `lua_scripts/README.md` for complete documentation.

---

## 🐛 Troubleshooting

### Lua Scripts Not Loading
- Check file extension is `.lua`
- Click **Refresh Scripts** in menu
- Check console for error messages
- Verify script syntax is correct

### Models Not Showing
- Ensure `.vmdl_c` compiled format
- Include material files in folder
- Click **Refresh Models List**
- Check folder structure matches documentation


---

## 📖 Examples

### Example: Velocity ESP
```lua
function OnPaint()
    local player = GetLocalPlayer()
    if not player then return end
    
    local vx, vy, vz = GetVelocity(player)
    local speed = math.sqrt(vx*vx + vy*vy)
    
    DrawText(10, 100, 255, 255, 255, 255, "Speed: " .. string.format("%.1f", speed))
end

RegisterCallback("OnPaint", OnPaint)
```

### Example: Auto-Adaptive Resolver
```lua
local resolverData = {}

function OnCreateMove(cmd)
    -- Your entity loop here
    for _, enemy in pairs(enemies) do
        ResolvePlayer(enemy)
    end
end

function ResolvePlayer(player)
    if not resolverData[player] then
        resolverData[player] = { misses = 0 }
    end
    
    local data = resolverData[player]
    local vx, vy, vz = GetVelocity(player)
    local speed = math.sqrt(vx*vx + vy*vy)
    
    local yaw = 0
    if speed < 5 then
        yaw = (data.misses % 3) == 0 and 0 or (data.misses % 3) == 1 and 60 or -60
    else
        yaw = math.deg(math.atan2(vy, vx))
    end
    
    OverrideResolver(player, yaw)
end

RegisterCallback("OnCreateMove", OnCreateMove)
```

---

## 🤝 Contributing

This is a modular cheat framework. To add new modules:

1. Fix glitched`
2. make a PR (pull request)`
3. Document in `documentation/`
4. Expose to Lua if needed
5. Add menu integration

---

## 📄 License

MIT



## ⚡ Performance Tips

- Use local variables in Lua scripts
- Avoid heavy calculations in `OnPaint`
- Cache frequently accessed data
- Unload unused Lua scripts
- Optimize model material files

---

## 🎯 Roadmap

- [x] Lua scripting system
- [x] Model changer with arms support
- [x] Resolver API for Lua
- [x] Advanced anti-aim
- [x] Material detection system
- [ ] Lua debugger integration
- [ ] Visual Lua script editor
- [ ] More resolver examples
- [ ] Skin changer functionality

---
