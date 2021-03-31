# DCS Keybind Injector by Quaggles

## Summary

A mod that allows you to configure custom keybind entries inside your `Saved Games/DCS/` folder instead of inside your game folder, when DCS is run these entries are merged in with the default aircraft entries. This method avoids having to manually merge your control changes into each aircrafts default controls when DCS updates.

## The goal of this mod

Commonly in DCS users with unique input systems will need to create custom keybinds to allow them to use certain aircraft functions with their HOTAS. Some examples are:

* Configuring 3 way switches on a Thrustmaster Warthog HOTAS to control switches the module developer never intended to be controlled by a 3 way switch
* Configuring actions that only trigger a cockpit switch while a button is held, for example using the trigger safety on a VKB Gunfighter Pro to turn on Master Arm while it's flipped down and then turn off Master Arm when flipped up
* Adding keybinds that the developer forgot, for example the Ka-50 has no individual "Gear Up" and Gear Down" binds, only a gear toggle

In my case, on my Saitek X-55 Throttle there is an airbrake slider switch that when engaged registers as a single button being held down, when the slider is disengaged the button is released. In DCS by default few aircraft support this type of input so a custom input is needed, in my case for the F/A-18C:

```lua
{down = hotas_commands.THROTTLE_SPEED_BRAKE, up = hotas_commands.THROTTLE_SPEED_BRAKE, cockpit_device_id = devices.HOTAS, value_down = -1.0, value_up = 1.0, name = 'Speed Brake Hold', category = {'Quaggles Custom'}},
```

Until now the solution was to find the control definition file `DCSWorld\Mods\aircraft\FA-18C\Input\FA-18C\joystick\default.lua` and insert your command somewhere inside of it, if you weren't using a mod manager then every time the game was updated your change would be erased and you'd need reinsert your commands into the files for every aircraft you changed. If you were using a mod manager such as OVGME if you reapplied your mod after an update and the developers had changed the control binds you wouldn't see them since your mod was setting a copy of the old version of the file.

## Installation

1. Go to the [latest release](https://github.com/Quaggles/dcs-keybind-injector/releases/latest)
2. Download `DCS-Keybind-Injector-Quaggles.zip`

### [OVGME (Recommended)](https://wiki.hoggitworld.com/view/OVGME)
3. Drop the zip file in your mod directory
4. Enable mod in OVGME
5. Reenable with each DCS update

### Manual
3. Extract the zip
4. Find the `DCS-Keybind-Injector-Quaggles/Scripts` folder
5. Move it into your `DCSWorld/` folder
6. Windows Explorer will ask you if you want to replace `Data.lua`, say yes
7. Repeat this process every DCS update, if you use OVGME you can just reenable the mod and it handles this for you

## Configuration

New commands are configured in the `Saved Games\DCS\QuagglesKeybindInjector` directory, lets go through how to configure a hold action for the speedbrake on the F/A-18C Hornet.

### Setting the folder structure

For the F/A-18C the default input files are located in `DCSWorld\Mods\aircraft\FA-18C\Input\FA-18C`, inside this directory are folders with the generic names of your input devices, these can include `joystick`, `keyboard`, `mouse`, `trackir` and `headtracker`. Each generic input folder contains `default.lua` which is the default set of keybinds the developer has configured, this is an important reference when making your own commands. It also contains many lua files for automatic binding of common hardware like the Thrustmaster Warthog HOTAS but these can be ignored.

The DCS input folder structure needs be duplicated so that the folders relative to `DCSWorld\Mods\aircraft` are placed in `Saved Games\DCS\QuagglesKeybindInjector`. The folder structure needs to match <b>EXACTLY</b> for each generic input device you want to add commands to. In my F/A-18C Speedbrake Hold example that means I will create the structure `Saved Games\DCS\QuagglesKeybindInjector\FA-18C\Input\FA-18C\joystick\`, for an F-14B in the RIO seat I would create `Saved Games\DCS\QuagglesKeybindInjector\F14\Input\F-14B-RIO\joystick`.

<b>IMPORTANT:</b> For some aircraft the 1st and 3rd folders have different names, for example `F14\Input\F-14B-Pilot` make sure this structure is followed correctly or your inputs won't be found

An example of the folder structure for some aircraft I have configured:

![image](https://user-images.githubusercontent.com/8382945/113179959-87230880-9293-11eb-9979-2c2e99b74920.png)

### Creating your custom commands

![image](https://user-images.githubusercontent.com/8382945/113173913-37414300-928d-11eb-91ad-6e09b6f64a8b.png)

Inside the generic input folder `Saved Games\DCS\QuagglesKeybindInjector\FA-18C\Input\FA-18C\joystick\` etc we will create a lua script called `default.lua`, paste in the following text, it contains the Speedbrake Hold example and some commented out templates for the general structure of commands

```lua
return {
	keyCommands = {
        {down = hotas_commands.THROTTLE_SPEED_BRAKE, up = hotas_commands.THROTTLE_SPEED_BRAKE, cockpit_device_id = devices.HOTAS, value_down = -1.0, value_up = 1.0, name = 'Speed Brake Hold', category = {'Quaggles Custom'}},
        {down = hotas_commands.THROTTLE_SPEED_BRAKE, up = hotas_commands.THROTTLE_SPEED_BRAKE, cockpit_device_id = devices.HOTAS, value_down = 1.0, value_up = -1.0, name = 'Speed Brake Inverted', category = {'Quaggles Custom'}},
        -- KeyCommand Template (Remove leading -- to uncomment)
		-- {down = CommandNameOnButtonDown, up = CommandNameOnButtonUp, name = 'NameForControlList', category = 'CategoryForControlList'},
	},
	axisCommands = {
        -- AxisCommand Template (Remove leading -- to uncomment)
		-- {action = CommandName, Device, name = 'NameForControlList', category = 'CategoryForControlList'},
	}
}
```

To work out what to put in these templates reference the developer provided default keybinds file, for the F/A-18C that is in `DCSWorld\Mods\aircraft\FA-18C\Input\FA-18C\joystick\default.lua`

I'd recommend setting a unique category name for your custom commands so that they are easy to find in the menu.

## Extras
### Hardlinking
If you want to have a set of commands bindable to both your HOTAS and your keyboard consider [hardlinking](https://schinagl.priv.at/nt/hardlinkshellext/linkshellextension.html) your `default.lua` from your `joystick` folder to your `keyboard` folder.

By hardlinking both files look like they are in different directories to Windows and DCS but they actually refer to the same file on the disk meaning if you modify one you automatically modify the other

### Request AWACS Nearest Bandit
Allows binding request bogey dope to your HOTAS, not every aircraft has this by default in DCS

```lua
{down = iCommandAWACSBanditBearing, name='Request AWACS Nearest Bandit', category = 'Quaggles Custom'},
```

### Ka-50 Gear Up/Down
```lua
{down = iCommandPlaneGearUp, name = 'Gear Up', category = 'Quaggles Custom'},
{down = iCommandPlaneGearDown, name = 'Gear Down', category = 'Quaggles Custom'},
```

### F/A-18C VKB Gunfighter Flip Trigger controls master arm
```lua		
{down = SMS_commands.MasterArmSw, up = SMS_commands.MasterArmSw, cockpit_device_id = devices.SMS, value_down = 1.0, value_up = 0.0, name = 'Master Arm Armed [else] Safe', category = {'Quaggles Custom'}},
{down = SMS_commands.MasterArmSw, up = SMS_commands.MasterArmSw, cockpit_device_id = devices.SMS, value_down = 0.0, value_up = 1.0, name = 'Master Arm Safe [else] Armed', category = {'Quaggles Custom'}},
```

### F-14 control TID range from front seat
Note: May get broken by Heatblur at any time and could be considered unscrupulous on Multiplayer servers
```lua
{down = device_commands.TID_range_knob, cockpit_device_id=devices.TID, value_down = -1.0, name = _('TID range: 25'), category = _('Quaggles Custom')},
{down = device_commands.TID_range_knob, cockpit_device_id=devices.TID, value_down = -0.5, name = _('TID range: 50'), category = _('Quaggles Custom')},
{down = device_commands.TID_range_knob, cockpit_device_id=devices.TID, value_down = 0.0, name = _('TID range: 100'), category = _('Quaggles Custom')},
{down = device_commands.TID_range_knob, cockpit_device_id=devices.TID, value_down = 0.5, name = _('TID range: 200'), category = _('Quaggles Custom')},
{down = device_commands.TID_range_knob, cockpit_device_id=devices.TID, value_down = 1.0, name = _('TID range: 400'), category = _('Quaggles Custom')},
```

# FAQ
## My new control bindings aren't showing up ingame
First look at `Saved Games\DCS\Logs\dcs.log` at the bottom is likely an error telling you what went wrong in your code, for finding syntax errors in lua I would recommend Visual Studio Code with the vscode-lua extension, it should highlight them all in red for you making it easy to find that missing comma 😄

If you have no errors open the mod version of `Scripts\Input\Data.lua` and find the line `local quagglesLoggingEnabled = false` and set it to `true` you will get outputs in the `Saved Games\DCS\Logs\dcs.log` file as the script tries to handle every lua control file, it will tell you the path to the file it is trying to find in your Saved Games folder so you can ensure your folder structure is correct. Remember `../` in a path means get the parent directory.

## HELP MY CONTROLS MENU IS BLANK/MISSING
Don't worry, this doesn't mean you've lost all your keybinds, it means there was an error somewhere in the code loading the keybinds, usually my injector catches any errors in the `default.lua` and reports them `Saved Games\DCS\Logs\dcs.log`. If you see nothing there it could mean that DCS has been updated and changed the format of the `Scripts/Input/Data.lua` file the mod changes, simple uninstall the mod and the game should work as normal, then wait for an updated version of the mod.

## Disclaimer
I am not responsible for any corrupted keybinds when you use this mod, I've personally never had an issue with this method but I recommend <b>always</b> keeping backups of your keybinds (`Saved Games\DCS\Config\Input`) if you value them.
