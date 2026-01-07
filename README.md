<p align="center">
  <img src="https://i.imgur.com/rbdudpA.png">
</p>

**Moonlite** is a light-weight, runtime player for sequences created in [**Moon Animator 2**](https://www.roblox.com/library/4725618216/Moon-Animator-2), a plugin developed by [@xsixx](https://twitter.com/xsixx).<br/>This project is actively developed and maintained by [@MaximumADHD](https://twitter.com/MaximumADHD).

<br/>

# Installation
If you use Wally for managing packages, you can install it using the following line:
```console
Moonlite = "maximumadhd/moonlite@0.9.0"
```

If you don't use Wally, you can install it from [Moonlite.rbxm](https://github.com/MaximumADHD/Moonlite/blob/main/Moonlite.rbxm) file.

# Basic Usage
Create a `LocalScript` in `StarterPlayerScripts` with the following code:
```luau
local Moonlite = require(game.ReplicatedStorage.Moonlite)

local save = game.ReplicatedStorage.MoonliteSaves.my_save
local MoonTrack = Moonlite.CreatePlayer(save)

MoonTrack:Play()
```
The `save` is a StringValue normally stored in `game.ServerStorage.MoonAnimator2Saves`, but you'll want to use it on the client, so it's moved to `game.ReplicatedStorage.MoonliteSaves` for example.

# Contribution
When syncing by default with rojo, the module can be found directly in the `ReplicatedStorage` service.

# APIs

### CreatePlayer
```luau
Moonlite.CreatePlayer(save: StringValue, root: Instance?, options: CreateOptions?) -> MoonTrack
```
Loads the provided save file to be played back. The `save` is a StringValue normally stored in `game.ServerStorage.MoonAnimator2Saves`, but you'll need to store it elsewhere to play the sequence back on the client.

The `options` parameter accepts a table with the following fields:
- `ShouldCompileOnCreate: boolean?` - If set to `false`, the track will not be compiled immediately. Defaults to `true`.

## MoonTrack
```luau
type MoonTrack = Moonlite.MoonTrack
```

`MoonTrack` is the main type exported when requiring the `Moonlite` module. It represents a loaded animation sequence.

### Play
```luau
MoonTrack:Play() -> ()
```
Starts playing the track's elements.
>**Note:** Has no effect if the track is already playing.

### Stop
```luau
MoonTrack:Stop() -> ()
```
Stops all playing track elements.
>**Note:** Has no effect if the track is already stopped.

### Reset
```luau
MoonTrack:Reset() -> boolean
```
Manually resets  all element's properties to their expected defaults. Returns `true` if the reset was successful.

>**Note:** This will only work if the track is not playing.

### IsPlaying
```luau
MoonTrack:IsPlaying() -> boolean
```
Returns `true` if the track has any elements playing.

### GetElements
```luau
MoonTrack:GetElements() -> { Instance }
```
Returns an array of instances that can be modified by this track during playback.

### LockElement
```luau
MoonTrack:LockElement(inst: Instance?, lock: any? = "Default") -> boolean
```
Adds a mutex lock to the provided element, disabling it from being modified by the track's playback. Returns `true` if the element is valid and successfully locked.

>**Note:** If provided, the value of `lock` must be a truthy type (i.e. not `false` or `nil`), otherwise it will fallback to `"Default"`.
>**Warning:** Calling this while the track is playing won't take effect until the track plays again.

### UnlockElement
```luau
MoonTrack:UnlockElement(inst: Instance?, lock: any? = "Default") -> boolean
```

Removes a mutex lock from the provided element, enabling it to be modified again if there are no other locks on it. Returns `true` if the element is valid and no longer has the provided lock.

>**Note:** If provided, the value of `lock` must be a truthy type (i.e. not `false` or `nil`), otherwise it will fallback to `"Default"`.
>**Warning:** Calling this while the track is playing won't take effect until the track plays again.

### IsElementLocked
```luau
MoonTrack:IsElementLocked(inst: Instance?) -> boolean
```

Returns `true` if there are any locks on the provided element.

### FindElement
```luau
MoonTrack:FindElement(name: string) -> Instance?
```
Returns the first element in this track whose name matches the provided name, if one can be found. Otherwise returns `nil`.

>**Warning:** The result of this function depends on the order of elements in the authored sequence. If there are multiple elements with the same name, this may produce unexpected behavior.

### FindElementOfType

```luau
MoonTrack:FindElementOfType(typeName: string): Instance?
```

Returns the first element in this track which satisfies `element:IsA(typeName)`, or `nil` if no such element can be found.

>**Warning:** The result of this function depends on the order of elements in the authored sequence. If there are multiple elements that satisfy `element:IsA(typeName)`, this may produce unexpected behavior.

### ReplaceElementByPath
```luau
MoonTrack:ReplaceElementByPath(path: string, replacement: Instance) -> boolean
```

Attempts to replace an element by its defined absolute path with a specific Instance. Returns `true` if the element was successfully replaced.

### SetRelativeTransform
```luau
MoonTrack:SetRelativeTransform(inst: Instance, initialCFrame: CFrame) -> ()
```

Sets the initial CFrame for relative transform calculations. When set, CFrame, Position, and Orientation animations will be played relative to the specified initial CFrame instead of using absolute values from the animation.

This is useful when the animation was created at a different world position than where it needs to be played in-game.

### ClearRelativeTransform
```luau
MoonTrack:ClearRelativeTransform(inst: Instance) -> ()
```

Removes the relative transform setting for the specified instance, reverting to absolute animation values.

### GetRelativeTransform
```luau
MoonTrack:GetRelativeTransform(inst: Instance) -> CFrame?
```

Returns the initial CFrame set for relative transform, or nil if not set.

### GetTimeLength
```luau
MoonTrack:GetTimeLength() -> number
```
Returns the total length of the track in seconds.

### GetMarkerReachedSignal
```luau
MoonTrack:GetMarkerReachedSignal(marker: string) -> RBXScriptSignal
```
Returns a signal that fires when the specified marker is reached during playback. The signal passes the target instance and marker data as parameters.

### GetMarkerEndedSignal
```luau
MoonTrack:GetMarkerEndedSignal(marker: string) -> RBXScriptSignal
```
Returns a signal that fires when the specified marker ends during playback. The signal passes the target instance and marker data as parameters.

### GetSetting
```luau
MoonTrack:GetSetting(name: string) -> any
```
Retrieves a custom setting value stored in the track's scratchpad.

### SetSetting
```luau
MoonTrack:SetSetting(name: string, value: any) -> ()
```
Stores a custom setting value in the track's scratchpad.

### Destroy
```luau
MoonTrack:Destroy() -> ()
```
Cleans up the track by destroying all internal signals and clearing data. Call this when you're done with the track to prevent memory leaks.

### Looped
```luau
MoonTrack.Looped: boolean
```
If set to `true`, this track's playback will loop on completion. This defaults to the value specified by the author of this sequence, and should be set explicitly if expected to behave one way or the other.

### Frames
```luau
MoonTrack.Frames: number
```
The total number of frames in the track. This is read-only and set when the track is created.

### FrameRate
```luau
MoonTrack.FrameRate: number
```
The frame rate (FPS) of the track. This defaults to the value specified in the sequence, typically 60 FPS. This is read-only.

### TimePosition
```luau
MoonTrack.TimePosition: number
```
The current playback position in seconds. This can be read to get the current position or written to seek to a specific time.

### RestoreDefaults
```luau
MoonTrack.RestoreDefaults: boolean
```
If set to `true`, the track will restore element properties to their original values when stopped. This defaults to `true`.

---

### Completed
```luau
MoonTrack.Completed: RBXScriptSignal
```
Fires upon all of the track's elements completing their playback. Passes a `PlaybackState` enum value indicating how the track completed (Completed, Cancelled, or Playing).

---
