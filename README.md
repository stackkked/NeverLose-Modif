# Neverlose.cc UI Library вЂ” Full Reference

## Loading the Library

```lua
local NeverLose = loadstring(game:HttpGet("https://raw.githubusercontent.com/stackkked/NeverLose-Modif/refs/heads/main/neverlose.lua"))()
```

---

## 2. Global Configuration & Creating a Window

Before creating a window, you can configure global theme variables, including the main colors and fonts used across the entire UI.

```lua
-- Optional: Configure theme and fonts BEFORE creating the window
NeverLose.AccentColor = Color3.fromRGB(78, 127, 252)
NeverLose.MainFont = Enum.Font.Code      -- Replaces GothamMedium globally
NeverLose.MainFontBold = Enum.Font.Code  -- Replaces GothamBold globally

local Window = NeverLose:CreateWindow({
    Name = "My Script",          -- string: Window title (default: "Neverlose")
    Content = "Game Name",       -- string: Subtitle under the title (default: "Counter-Strike 2")
    Logo = "rbxassetid://...",   -- string: Logo image asset (default: built-in NL logo)
    Size = NeverLose.Scales.Default, -- UDim2: Window size (see Scales below)
    ConfigFolder = "MyConfigs",  -- string: Folder name for config saving (default: "NeverLoseConfigs")
    Keybind = "Insert",          -- string: Key to toggle UI visibility (default: "Insert")
    Enable3DRenderer = false,    -- boolean: 3D perspective rendering mode (default: false)
})
```

### Available Scales
```lua
NeverLose.Scales.Small   -- UDim2.fromOffset(540, 380)
NeverLose.Scales.Mobile  -- UDim2.fromOffset(640, 385)
NeverLose.Scales.Default -- UDim2.fromOffset(640, 480)
NeverLose.Scales.Large   -- UDim2.fromOffset(800, 600)
```

### Window Methods
```lua
Window:ToggleInterface()       -- Show/hide the window
Window:SetSize(UDim2)          -- Change window size
Window:Set3DRender(bool)       -- Enable/disable 3D render mode
Window:SetAccount({            -- Set user profile in sidebar
    Name = "Username",
    Profile = "rbxassetid://...",
    Expires = "Never"
})
Window:AddTabLabel("Section")  -- Add a non-clickable text label/separator between tabs
Window:_InitConfig()           -- Initialize the built-in config system (ConfigLib)
```

---

## 2. Adding Tabs

```lua
local Tab = Window:AddTab({
    Name = "Visuals",          -- string: Tab name shown in sidebar
    Icon = "eye"               -- string: Icon name from BuilderIcons font (see icon list)
})
```

Icon names use Roblox BuilderIcons. Append `-bold` for bold variant (e.g. `"gear-bold"`).

Common icons: `"eye"`, `"crosshairs"`, `"gear"`, `"shield-check"`, `"person"`, `"sword"`, `"flame"`, `"lightning-bolt"`, `"magnifying-glass"`, `"three-sliders-horizontal"`, `"bell"`, `"key"`.

---

## 3. Adding Sections

```lua
local Section = Tab:AddSection({
    Name = "ESP Settings",     -- string: Section header text
    Side = "Left"              -- string: "Left" or "Right" column placement
})
```

The `Section` object is an `idx` that supports: `AddLabel`, `AddButton`, `AddUserFrame`.

---

## 4. Adding Elements

All elements are added inside a Section. Each `AddLabel` call returns a `handle` object that can chain controls onto it.

### 4.1 Label (Row with controls)

```lua
local label = Section:AddLabel("Enable ESP")
-- Returns: handle object with methods to attach controls
```

The `handle` is the primary building block. You attach toggles, sliders, dropdowns, etc. to it:

### 4.2 Toggle

```lua
local toggle = label:AddToggle({
    Default = false,               -- boolean: initial state
    Flag = "esp_enabled",          -- string|nil: key in NeverLose.Flags for global access
    Callback = function(state)     -- function(boolean): called on toggle
        print("ESP:", state)
    end
})

-- Methods:
toggle:GetValue()          -- returns boolean
toggle:SetValue(true)      -- sets value and fires callback
```

### 4.3 Slider

```lua
local slider = label:AddSlider({
    Default = 50,                  -- number: initial value
    Min = 0,                       -- number: minimum
    Max = 100,                     -- number: maximum
    Rounding = 0,                  -- number: decimal places (0 = integers)
    Type = "%",                    -- string: suffix displayed after value (e.g. "%", "m", "ms")
    Size = 125,                    -- number: pixel width of the slider
    Nums = {},                     -- table: optional {[value] = "display name"} overrides
    Flag = "esp_distance",         -- string|nil: flag key
    Callback = function(value)     -- function(number)
        print("Distance:", value)
    end
})

-- Methods:
slider:GetValue()          -- returns number
slider:SetValue(75)        -- sets value and fires callback
```

### 4.4 Dropdown

```lua
-- Single select:
local dropdown = label:AddDropdown({
    Default = "Head",              -- string|nil: initial selection
    Values = {"Head", "Torso", "Nearest"}, -- table: list of options
    Multi = false,                 -- boolean: multi-select mode (default: false)
    AutoUpdate = false,            -- boolean: regenerate items when opened (for dynamic lists)
    Size = 100,                    -- number: pixel width
    Flag = "aim_hitbox",           -- string|nil: flag key
    Callback = function(value)     -- function(string|table)
        print("Selected:", value)
    end
})

-- Multi select:
local multi = label:AddDropdown({
    Default = {"Head", "Torso"},   -- table: initially selected items
    Values = {"Head", "Torso", "Arms", "Legs"},
    Multi = true,
    Callback = function(selected)  -- table: {Head = true, Torso = true, ...}
        -- keys with true are selected
    end
})

-- Methods:
dropdown:GetValue()              -- returns string or table
dropdown:SetValue("Torso")       -- sets value, fires callback
dropdown:SetValues({"A", "B"})   -- replace the options list
dropdown:Generate()              -- manually regenerate the dropdown items
```

### 4.5 Color Picker

```lua
local colorpicker = label:AddColorPicker({
    Default = Color3.fromRGB(255, 0, 0), -- Color3 or hex string "#FF0000"
    Callback = function(color)           -- function(Color3)
        print("Color:", color)
    end
})

-- Methods:
colorpicker:GetValue()          -- returns Color3
colorpicker:SetValue(Color3.fromRGB(0, 255, 0))
```

### 4.6 Keybind

```lua
local keybind = label:AddKeybind({
    Default = "F",                 -- string|nil: initial key name or nil for "None"
    Blacklist = {                  -- table: keys that cannot be bound
        Enum.KeyCode.W, Enum.KeyCode.A, Enum.KeyCode.S, Enum.KeyCode.D,
        "M1B"                      -- "M1B" = Mouse Button 1, "M2B" = Mouse Button 2
    },
    Flag = "aim_key",              -- string|nil
    Callback = function(keyName)   -- function(string): key name string
        print("Bound to:", keyName)
    end
})

-- Methods:
keybind:GetValue()               -- returns string (key name)
keybind:SetValue("G")            -- sets key and fires callback
```

### 4.7 Text Input

```lua
local textbox = label:AddTextInput({
    Default = "",                  -- string: initial text
    Placeholder = "Enter value",   -- string: placeholder text
    Numeric = false,               -- boolean: only allow numbers
    Size = 100,                    -- number: pixel width
    Flag = "custom_input",         -- string|nil
    Callback = function(text)      -- function(string|number)
        print("Input:", text)
    end
})

-- Methods:
textbox:GetValue()               -- returns string or number
textbox:SetValue("hello")        -- sets text and fires callback
```

### 4.8 Option Gear (Sub-menu)

```lua
local option = label:AddOption()  -- no argument or 1 (gear icon) or 2 (chevron)
-- Returns an option window (sub-panel) where you can add more labels/controls:
local subLabel = option:AddLabel("Sub Setting")
subLabel:AddToggle({ ... })
```

### 4.9 Button

```lua
local button = Section:AddButton({
    Name = "Teleport",             -- string: button text
    Icon = "location-pin",         -- string: icon name
    Callback = function()          -- function: called on click
        print("Clicked!")
    end,
    ToolTip = "Click to teleport"  -- string|nil: hover tooltip text
})

-- Methods:
button:SetText("New Text")
button:SetIcon("new-icon")
```

### 4.10 User Frame

```lua
local userframe = Section:AddUserFrame(
    "Username",                    -- string: display name
    "rbxassetid://...",            -- string: avatar image
    "Expires: Never"               -- string: status text
)

-- Methods:
userframe:SetUsername("NewName")
userframe:SetProfile("rbxassetid://...")
userframe:SetExpires("30 days")
```

---

## 5. Common handle Methods

Every `AddLabel` returns a `handle` with these shared methods:

```lua
handle:SetText("New Label")       -- change the row's label text
handle:SetVisible(false)          -- hide/show the entire row
handle:ToolTip("Description")     -- attach a hover tooltip
```

---

## 6. Watermark

```lua
local watermark = Window:Watermark()

local block = watermark:AddBlock({
    Name = "FPS",
    Value = "0"
})

watermark:SetRender(true)  -- show/hide the watermark
```

---

## 7. Notification System

```lua
local Notifier = NeverLose:CreateNotification()

Notifier.new({
    Type = "info",                 -- string: preset ("success", "error", "warning", "info")
    Content = "Something happened",-- string: notification body
    Logo = "rbxassetid://...",     -- string|nil: custom image override
    IconColor = Color3.new(1,1,1), -- Color3|nil: custom color override
    Duration = 5                   -- number: seconds before auto-dismiss
})
```

---

## 8. Logger

```lua
local Logger = NeverLose:CreateLogger()

Logger.new({
    Title = "Hit Log",
    Content = "Headshot on Player123",
    Logo = "rbxassetid://...",
    Duration = 3
})
```

---

## 9. Indicators

```lua
local Indicators = NeverLose:CreateIndicator()

local indicator = Indicators.new({
    Name = "Double Tap",
    Color = Color3.fromRGB(0, 255, 0)
})

-- Methods:
indicator:SetText("Active")
indicator:SetColor(Color3.fromRGB(255, 0, 0))
```

---

## 10. Keybinds List

```lua
local KeybindsList = Window:KeybindsList("CenterLeft") -- position string (TopLeft, TopRight, etc.)

-- Add a bind to the list
local AimbotBind = KeybindsList:AddBind("Aimbot", "Mouse2", "holding")

KeybindsList:SetRender(true)  -- show/hide the entire list

-- Update the bind dynamically:
AimbotBind:SetState("on")     -- changes state text and color (white for active, gray for inactive)
AimbotBind:SetKey("F")        -- updates the displayed key
AimbotBind:SetVisible(false)  -- hides just this specific bind from the list
```

---

## 11. Admin Presence Widget

```lua
NeverLose:AdminPresence(
    123456,                    -- number: Roblox Group ID
    255,                       -- number: minimum rank to flag as admin
    "Admin"                    -- string|table: rank display name, or {[rank] = "Name"} mapping
)
```

Creates a draggable overlay that auto-detects admins joining/leaving the server. Shows notifications and avatar cards. Supports double-click collapse.

---

## 12. Flag System

All elements with a `Flag` parameter are stored in `NeverLose.Flags`:

```lua
-- Access any flagged element:
local value = NeverLose.Flags["esp_enabled"]:GetValue()
NeverLose.Flags["esp_enabled"]:SetValue(true)
```

---

## 13. Unloading

```lua
NeverLose:Unload()  -- Destroys all UI and disconnects all signals
-- Only works if NeverLose.UnloadEnabled was set to true before creating elements
```

---

## 14. Utility Functions

```lua
NeverLose:KeyCodeToStr(Enum.KeyCode.F)   -- "F"
NeverLose:StrToKeyCode("F")              -- Enum.KeyCode.F
NeverLose.Base64Encode("hello")          -- base64 string
NeverLose.Base64Decode("aGVsbG8=")       -- "hello"
```

---

## Full Example

```lua
local NeverLose = loadstring(game:HttpGet("https://raw.githubusercontent.com/stackkked/NeverLose-Modif/refs/heads/main/neverlose.lua"))()

local Window = NeverLose:CreateWindow({
    Name = "My Script",
    Content = "SCP Roleplay",
    Size = NeverLose.Scales.Default,
    Keybind = "Insert"
})

local VisualsTab = Window:AddTab({ Name = "Visuals", Icon = "eye" })
local CombatTab = Window:AddTab({ Name = "Combat", Icon = "crosshairs" })
local MiscTab = Window:AddTab({ Name = "Misc", Icon = "gear" })

local EspSection = VisualsTab:AddSection({ Name = "ESP" })

local espToggle = EspSection:AddLabel("ESP Enabled")
espToggle:AddToggle({
    Default = false,
    Flag = "esp_on",
    Callback = function(v) print("ESP:", v) end
})

local espDist = EspSection:AddLabel("Max Distance")
espDist:AddSlider({
    Default = 500, Min = 50, Max = 2000,
    Type = "m", Rounding = 0,
    Flag = "esp_dist",
    Callback = function(v) print("Dist:", v) end
})

local espColor = EspSection:AddLabel("ESP Color")
espColor:AddColorPicker({
    Default = Color3.fromRGB(255, 0, 0),
    Callback = function(c) print("Color:", c) end
})

Window:ToggleInterface()
```
