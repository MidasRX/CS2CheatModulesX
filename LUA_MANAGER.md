# Lua Manager Module

The Lua Manager provides a complete scripting interface for extending cheat functionality through Lua scripts.

## 📋 Overview

The Lua scripting system allows users to create custom features without modifying the source code. Scripts can:
- Draw custom ESP and overlays
- Modify player movement and input
- Create custom resolvers
- Track game events
- Manipulate configuration
- Access player and world information

---

## 🏗️ Architecture

### Core Components

#### **LuaManager.h**
Defines the Lua API interface and callback system.

#### **LuaManager.cpp**
Implements script loading, execution, and API functions.

#### **Script Structure**
```lua
-- Global variables (persistent)
local myData = {}

-- Callback functions
function OnPaint()
    -- Drawing code
end

-- Register callbacks
RegisterCallback("OnPaint", OnPaint)

-- Initialization
Print("Script loaded!")
```

---

## 🔧 API Categories

### 1. Console Functions
```cpp
void Print(const std::string& text)
void PrintColor(int r, int g, int b, const std::string& text)
```

**Usage:**
```lua
Print("Hello from Lua!")
PrintColor(255, 0, 0, "Red text!")
```

### 2. Configuration
```cpp
bool GetBool(const std::string& path)
void SetBool(const std::string& path, bool value)
int GetInt(const std::string& path)
void SetInt(const std::string& path, int value)
float GetFloat(const std::string& path)
void SetFloat(const std::string& path, float value)
```

### 3. Player Information
```cpp
void* GetLocalPlayer()
bool IsLocalPlayerAlive()
int GetHealth(void* player)
int GetArmor(void* player)
int GetTeam(void* player)
bool IsAlive(void* player)
void GetOrigin(void* player, float& x, float& y, float& z)
void GetViewAngles(float& pitch, float& yaw, float& roll)
void SetViewAngles(float pitch, float yaw, float roll)
```

### 4. Weapon Information
```cpp
void* GetActiveWeapon(void* player)
std::string GetWeaponName(void* weapon)
int GetAmmoInClip(void* weapon)
```

### 5. Resolver / Ragebot
```cpp
void GetEyePosition(void* player, float& x, float& y, float& z)
void GetVelocity(void* player, float& x, float& y, float& z)
float GetSimulationTime(void* player)
int GetFlags(void* player)
bool IsOnGround(void* player)
void GetMissedShots(void* player, int& count)
void OverrideResolver(void* player, float yaw)
void ResetResolver(void* player)
```

### 6. Drawing
```cpp
void DrawLine(float x1, float y1, float x2, float y2, int r, int g, int b, int a)
void DrawRect(float x, float y, float w, float h, int r, int g, int b, int a)
void DrawFilledRect(float x, float y, float w, float h, int r, int g, int b, int a)
void DrawText(float x, float y, int r, int g, int b, int a, const std::string& text)
void DrawCircle(float x, float y, float radius, int r, int g, int b, int a)
```

### 7. World Functions
```cpp
void WorldToScreen(float x, float y, float z, float& screenX, float& screenY, bool& onScreen)
```

### 8. Math Utilities
```cpp
float Distance(float x1, float y1, float z1, float x2, float y2, float z2)
void AngleVectors(float pitch, float yaw, float& x, float& y, float& z)
```

---

## 🔄 Callback System

### Available Callbacks

#### **OnPaint**
Called every frame for drawing.
```lua
function OnPaint()
    DrawText(10, 10, 255, 255, 255, 255, "Frame rendered!")
end
RegisterCallback("OnPaint", OnPaint)
```

#### **OnCreateMove**
Called before sending user commands.
```lua
function OnCreateMove(cmd)
    -- Modify movement
end
RegisterCallback("OnCreateMove", OnCreateMove)
```

#### **OnFrameStageNotify**
Called at various frame stages.
```lua
function OnFrameStageNotify()
    -- Run per-frame logic
end
RegisterCallback("OnFrameStageNotify", OnFrameStageNotify)
```

#### **OnPlayerHurt**
Called when a player takes damage.
```lua
function OnPlayerHurt()
    PrintColor(255, 0, 0, "Player hurt!")
end
RegisterCallback("OnPlayerHurt", OnPlayerHurt)
```

#### **OnRoundStart**
Called at round start.
```lua
function OnRoundStart()
    Print("New round!")
end
RegisterCallback("OnRoundStart", OnRoundStart)
```

#### **OnRoundEnd**
Called at round end.
```lua
function OnRoundEnd()
    Print("Round over!")
end
RegisterCallback("OnRoundEnd", OnRoundEnd)
```

---

## 📂 File Structure

### Script Storage
Scripts are stored in `lua_scripts/` directory:
```
lua_scripts/
└── your_script.lua        # User scripts
```

### Script Metadata
```cpp
struct LuaScript {
    std::string name;          // Script name
    std::string filepath;      // Full path
    std::string content;       // Source code
    bool enabled;              // Execution state
    bool loaded;               // Load state
    std::string last_error;    // Error message
};
```

---

## 🚀 Usage Guide

### Creating a Script

1. **Create File**
   - Navigate to `lua_scripts/` folder
   - Create `my_script.lua`

2. **Write Script**
   ```lua
   function OnPaint()
       DrawText(10, 10, 255, 255, 255, 255, "My Script!")
   end
   
   RegisterCallback("OnPaint", OnPaint)
   Print("My script loaded!")
   ```

3. **Load Script**
   - Open cheat menu
   - Navigate to **Other → Lua Scripts**
   - Click **Refresh Scripts**
   - Enable your script

### Script Management

**Menu Features:**
- Enable/disable scripts with checkboxes
- Reload individual scripts
- Reload all scripts
- Unload scripts
- View error messages
- Open scripts folder

**Hot-Reload:**
Scripts can be reloaded without restarting the game.

---

## 💡 Example Scripts

### Simple Watermark
```lua
function OnPaint()
    DrawFilledRect(10, 10, 200, 30, 0, 0, 0, 200)
    DrawRect(10, 10, 200, 30, 255, 255, 255, 255)
    DrawText(20, 18, 255, 255, 255, 255, "My Cheat v1.0")
end

RegisterCallback("OnPaint", OnPaint)
```

### Velocity Display
```lua
function OnPaint()
    local player = GetLocalPlayer()
    if not player or not IsAlive(player) then return end
    
    local vx, vy, vz = GetVelocity(player)
    local speed = math.sqrt(vx*vx + vy*vy)
    
    local text = string.format("Speed: %.1f", speed)
    DrawText(10, 100, 255, 255, 0, 255, text)
end

RegisterCallback("OnPaint", OnPaint)
```

### Custom Resolver
```lua
local resolverData = {}

function ResolvePlayer(player)
    if not player or not IsAlive(player) then return end
    
    if not resolverData[player] then
        resolverData[player] = { misses = 0, lastUpdate = 0 }
    end
    
    local data = resolverData[player]
    local vx, vy, vz = GetVelocity(player)
    local speed = math.sqrt(vx*vx + vy*vy)
    
    local yaw = 0
    
    if speed < 5 then
        -- Standing resolver
        if data.misses == 0 then yaw = 0
        elseif data.misses == 1 then yaw = 60
        else yaw = -60 end
    else
        -- Moving resolver
        yaw = math.deg(math.atan2(vy, vx))
    end
    
    OverrideResolver(player, yaw)
end

function OnRoundStart()
    resolverData = {}
    PrintColor(0, 255, 255, "Resolver reset")
end

RegisterCallback("OnRoundStart", OnRoundStart)
```

---

## 🔧 Implementation Details

### Script Loading Process
1. Scan `lua_scripts/` directory
2. Read `.lua` files
3. Parse and store content
4. Create script metadata
5. Execute initialization code
6. Register callbacks

### Callback Execution
1. Game event occurs
2. LuaManager::RunCallback() called
3. Iterate enabled scripts
4. Execute registered callbacks
5. Handle errors gracefully

### Error Handling
- Syntax errors displayed in UI
- Runtime errors caught and logged
- Script automatically disabled on error
- Error messages shown per-script

---

## 🛠️ Advanced Features

### State Management
```lua
-- Persistent state across callbacks
local playerData = {}

function OnCreateMove(cmd)
    -- Access and modify playerData
end

function OnPaint()
    -- Same playerData available here
end
```

### Performance Optimization
```lua
-- Cache expensive calculations
local cachedData = nil
local lastUpdate = 0

function OnPaint()
    local currentTime = os.time()
    
    if currentTime - lastUpdate > 1 then
        cachedData = ExpensiveCalculation()
        lastUpdate = currentTime
    end
    
    -- Use cachedData
end
```

### Multi-Script Communication
Scripts run in isolated environments. To share data:
- Use config system: `SetInt()`, `GetInt()`
- Use console output: `Print()`, `PrintColor()`

---

## 📊 Performance Considerations

### Best Practices
- ✅ Use local variables
- ✅ Cache calculations
- ✅ Limit OnPaint operations
- ✅ Validate pointers
- ❌ Don't create tables in callbacks
- ❌ Don't do heavy math in OnPaint

### Optimization Tips
```lua
-- BAD: Creates table every frame
function OnPaint()
    local players = {} -- Don't do this
end

-- GOOD: Reuses table
local players = {}
function OnPaint()
    -- Use existing table
end
```

---

## 🐛 Debugging

### Console Output
```lua
Print("Debug: Variable value = " .. tostring(value))
PrintColor(255, 0, 0, "ERROR: Something failed")
```

### Validation
```lua
function OnPaint()
    local player = GetLocalPlayer()
    if not player then
        Print("ERROR: No local player!")
        return
    end
    
    -- Safe to use player
end
```

### Error Messages
Check the UI for script-specific errors:
- Syntax errors
- Runtime exceptions
- Nil pointer access
- Invalid function calls

---

## 🔗 Integration

### Menu Integration
Located in `NewMenu.cpp` under **Other → Lua Scripts** tab:
- Script list with checkboxes
- Refresh/reload buttons
- Open folder button
- Status display
- Error messages

### Backend Integration
Scripts integrate with:
- `Globals::m_pLocalPlayerPawn` - Local player
- `Draw::WorldToScreen()` - World to screen
- `CS2ChannelManager::Msg()` - Console output
- `ImGui::GetBackgroundDrawList()` - Drawing

---

## 📝 API Extension

To add new Lua functions:

1. **Add to LuaManager.h**
   ```cpp
   void MyNewFunction(int param);
   ```

2. **Implement in LuaManager.cpp**
   ```cpp
   void MyNewFunction(int param) {
       // Implementation
   }
   ```

3. **Document in README.md**
   ```markdown
   #### `MyNewFunction(param)` → `result`
   Description of the function.
   ```

4. **Add example script**
   ```lua
   function OnPaint()
       MyNewFunction(123)
   end
   ```

---

## 📚 Resources

- **Lua Reference**: https://www.lua.org/manual/5.4/

---

## ✨ Future Enhancements

- [ ] Lua debugger integration
- [ ] Visual script editor
- [ ] Script marketplace
- [ ] Performance profiler
- [ ] Auto-complete in editor
- [ ] Syntax highlighting
- [ ] Script templates

---

**Module Version**: 1.0  
**Last Updated**: November 29, 2025  
**Maintainer**: CS2CheatModulesX Team
