## Gamepad Touch Extension
### Overview
Modern gamepads support touch functionality, e.g the touchpad on the Sony DualShock®4 controller and the trackpads on the Steam Controller. The Standard Gamepad specification should be updated to include touchpad functionality, and the Gamepad API should be extended to allow developers to get touchpad values in their apps.

#### Standard Gamepad
The Standard Gamepad specification describes the positions of up to 17 buttons and 4 axes that are found on a typical gamepad. To support getting touchpad values, the standard gamepad will be extended to specify multiple touches found on a modern gamepad.
 
The touchpad, if present, will be assumed to be capable of capturing multiple touches. This configuration is found on many popular gamepads including the DualShock®4. When this configuration is present, it should be exposed as the touchEvents on the corresponding gamepad.

The Gamepad Extensions draft defines a GamepadTouch which represents a single touch event on a gamepad device that supports touch input and adds an optional touchEvents attribute to the Gamepad interface.
Examples
Below is an example of how this API could be used to get touches from the gamepad at index 0.
```
var gamepads = navigator.getGamepads();
if (gamepads.length > 0) {
    var gamepad = gamepads[0];
    if (gamepad.touchEvents) {
        for (var i = 0; i < gamepad.touches.length; i++) {
            var touchEvent = gamepad.touchEvents[i];
            console.log("----touch #" + i + ":touchId = " + 
                        touchEvent.touchId);
            console.log("----touch #" + i + ":surfaceId = " +
                        touchEvent.surfaceId);
            console.log("----touch #" + i + ":position[0] = " +
                        touchEvent.position[0]);
            console.log("----touch #" + i + ":position[1] = " +
                        touchEvent.position[1]);
            console.log("----touch #" + i + ":surfaceDimension[0] = " +
                        touchEvent.surfaceDimension[0]);
            console.log("----touch #" + i + ":surfaceDimension[1] = " +
                        touchEvent.surfaceDimension[1]);
        }
    }
}
```



### Links
PR for Gamepad Extensions draft: [Enhance Gamepad interface description for Touch #168](https://github.com/w3c/gamepad/pull/168)
