# Gamepad Feature Requests

This document includes feature requests and new requirements for the next version of 
the [Gamepad](https://w3c.github.io/gamepad/) specification; 
that is features that are not included in the first version of the spec.

* Vibration/Rumble
  * Most modern gamepads contain vibration motors
  * Discussed several times on public-webapps
    * http://lists.w3.org/Archives/Public/public-webapps/2013JulSep/0245.html
    * http://lists.w3.org/Archives/Public/public-webapps/2014JanMar/0722.html
  * Related work
    * [WebVibration](http://www.w3.org/TR/vibration/)
    * [Vibration API spec editor is open to the idea of working together](http://lists.w3.org/Archives/Public/public-device-apis/2014Apr/0002.html)
    * [Firefox platform bug (with WIP patch)](https://bugzilla.mozilla.org/show_bug.cgi?id=680289)
    * [Chromium platform bug](https://bugs.chromium.org/p/chromium/issues/detail?id=581515)

* Accelerometer+Gyro
  * Supported in some modern controllers (DualShock 3/4?, Wii remote)

* Better mapping support
  * https://www.w3.org/Bugs/Public/show_bug.cgi?id=21387
  * Firefox
    * Various bugs on file for incorrect mappings (https://bgz.la/952773, https://bgz.la/1027207, https://bgz.la/1152937)
    * [Platform bug to implement gamepad remapping](https://bugzilla.mozilla.org/show_bug.cgi?id=855364)
  * Chromium
    * Various bugs on file for incorrect mappings (https://crbug.com/360427, https://crbug.com/421833, https://crbug.com/180141, https://crbug.com/421833)

* Event-based update mechanism
  * Discussed on public-webapps: http://lists.w3.org/Archives/Public/public-webapps/2012AprJun/0469.html
  * Firefox contains a prototype implementation of `ButtonEvent`/`AxisMoveEvent` behind a pref (`dom.gamepad.non_standard_events.enabled`)

* Capture/lock feature
  * Similar to mouse lock / pointer lock API - ability to capture gamepad inputs before they're mapped to (emulated) mouse/keyboard controls by user agent
  * Mentioned on public-webapps, with use case already for Mozilla/Ouya
    * http://lists.w3.org/Archives/Public/public-webapps/2014JanMar/0515.html
    * http://lists.w3.org/Archives/Public/public-webapps/2014JanMar/0685.html
