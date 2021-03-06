# Nukepayload2.Diagnostics.InputInjection
A .NET Standard Port of Windows.UI.Input.Preview.Injection

[Download on Nuget](https://www.nuget.org/packages/Nukepayload2.Diagnostics.InputInjection)

You can use it for software testing ~~or writing tools for cheating in games~~.

We provide wrapped undocumented APIs in the `Nukepayload2.Diagnostics.Preview` namespace.

## Commonly used types:
- Nukepayload2.Diagnostics.InputInjector
- Nukepayload2.Diagnostics.Interaction.SendTouch

## Requirements
- Windows 10 (Recommended), Windows 8.1, Windows 8
- .NET Standard 2.0 or .NET Framework 4.5

## Sample
#### Touch at 123, 456 for 789 milliseconds with SendTouch
__VB__
```vb
SendTouch(123, 456, 789)
```

__C#__
```vb
SendTouch(123, 456, 789);
```

#### Touch at 123, 456 for 789 milliseconds with InputInjection
__VB__
```vb
Dim injection = InputInjector.TryCreate
If injection Is Nothing Then MsgBox("Your system is too old to use touch injection.", vbExclamation, "Not supported")
injection.InitializeTouchInjection(InjectedInputVisualizationMode.Default)
Const PosX = 123
Const PosY = 456
Const DurationMilliseconds = 789

With injection
    Dim touch As New InjectedInputTouchInfo With {
        .PointerInfo = New InjectedInputPointerInfo With {
            .PointerId = CUInt(pointerId),
            .PixelLocation = New InjectedInputPoint With {
                .PositionX = PosX, ' Y co-ordinate of touch on screen
                .PositionY = PosY ' X co-ordinate of touch on screen
            },
            .PointerType = PointerInputType.Touch ' Must be set to PointerInputType.Touch. It's different from UWP.
        },
        .TouchParameters =
            InjectedInputTouchParameters.Contact Or
            InjectedInputTouchParameters.Orientation Or
            InjectedInputTouchParameters.Pressure, ' TouchMask
        .Orientation = 90,
        .Pressure = 1024
    }
    ' defining contact area (I have taken area of 30 x 30 pixel)
    Dim pxLoc As InjectedInputPoint = touch.PointerInfo.PixelLocation
    touch.Contact = New InjectedInputRectangle With {
        .Top = pxLoc.PositionY - 15,
        .Bottom = pxLoc.PositionY + 15,
        .Left = pxLoc.PositionX - 15,
        .Right = pxLoc.PositionX + 15
    }

    ' TouchDown
    touch.PointerInfo.PointerOptions =
        InjectedInputPointerOptions.PointerDown Or
        InjectedInputPointerOptions.InRange Or
        InjectedInputPointerOptions.InContact

    .InjectTouchInput(touch) ' Injecting the touch Down from screen

    ' Hold
    touch.PointerInfo.PointerOptions =
        InjectedInputPointerOptions.Update Or
        InjectedInputPointerOptions.InRange Or
        InjectedInputPointerOptions.InContact

    Dim timer As New Stopwatch
    timer.Start()
    Do While timer.ElapsedMilliseconds < DurationMilliseconds
        .InjectTouchInput(touch)
    Loop
    timer.Stop()
    ' TouchUp
    touch.PointerInfo.PointerOptions = InjectedInputPointerOptions.PointerUp
    .InjectTouchInput(touch) ' Injecting the touch Up from screen
End With
```

## Progress
- [x] Touch Injection (Wrap [user32] `InjectTouchInput` and [user32] `InitializeTouchInjection`)
- [x] Convenient Touch Injection for RAD scenario
- [ ] ~~Uninitialize Touch Injection~~ ([user32] `RemoveInjectionDevice` is undocumented)
- [ ] ~~Gamepad Injection~~ (Win32 module `XboxgipSynthetic.dll` is undocumented)
- [ ] ~~Convenient Gamepad Injection for RAD scenario~~
- [ ] ~~Uninitialize Gamepad Injection~~ ([XboxgipSynthetic] `SyntheticController_RemoveController` is undocumented)
- [ ] Pen Injection (Both [user32] `InjectPointerInput` and [user32] `InitializePointerDeviceInjection` are undocumented)
- [ ] ~~Convenient Pen Injection for RAD scenario~~
- [ ] ~~Uninitialize Pen Injection~~ ([user32] `RemoveInjectionDevice` is undocumented)
- [x] Mouse Injection (Preview, [user32] `InjectMouseInput` is undocumented)
- [ ] ~~Convenient Mouse Injection for RAD scenario~~
- [x] Keyboard Injection (Preview, [user32] `InjectKeyboardInput` is undocumented)
- [x] Convenient Keyboard Injection for RAD scenario
- [ ] ~~Shortcut Injection~~ (Duplicate of Keyboard Injection, [user32] `InjectKeyboardInput` is undocumented)
- [ ] ~~Convenient Shortcut Injection for RAD scenario~~
- [x] Xml Document for zh-CN locale
- [ ] Xml Document for en-US locale
