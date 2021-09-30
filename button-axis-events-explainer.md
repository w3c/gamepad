## 🎮 GamePad API Button and Axis Events Explainer

### Objective

The [Gamepad API](https://www.w3.org/TR/gamepad/) provides access to button and axis inputs on connected gaming input devices. Applications can use the [getGamepads()](https://www.w3.org/TR/gamepad/#dom-navigator-getgamepads) method to retrieve a current snapshot of the state of all connected gamepads.

One major drawback of polling-based APIs is that applications do not receive a signal when the input state has changed. The Gamepad API specification considers it [best practice](https://www.w3.org/TR/gamepad/#practice-timing) to coordinate gamepad input polling with the requestAnimationFrame method, which will typically fire at 60 Hz. Polling at 60 Hz introduces average input latency equal to half the polling interval (half of 16.6 ms is 8.3 ms). The added latency can be mitigated by increasing the application's polling frequency at the cost of additional CPU.

Enabling applications to register for button and axis events would eliminate the need for a polling loop and would minimize input latency added by the application.

Extending the Gamepad API with an event interface would make the API more consistent with other event-driven input APIs like [Pointer Events](https://developer.mozilla.org/en-US/docs/Web/API/Pointer_events), [Touch Events](https://developer.mozilla.org/en-US/docs/Web/API/Touch_events), and [KeyboardEvent](https://developer.mozilla.org/en-US/docs/Web/API/KeyboardEvent).

### Events

Three new events are proposed that should be dispatched when new data is received from a gamepad. The browser should compare the new gamepad state with the previous state and fire the corresponding events.

**buttondown** and **buttonup** events are dispatched if a button's pressed attribute has changed. A separate event is dispatched for each changed button.
**rawgamepadchange** is dispatched if any button or axis state has changed. When multiple changes occur at the same time, one event is dispatched that encapsulates all the changes.

A fourth **gamepadchange** event is proposed that is created whenever a rawgamepadchange event would be dispatched and placed into an internal queue. The event may be dispatched immediately or delayed for performance reasons. Queued events must be dispatched before animation callbacks are run and before a buttondown or buttonup event is dispatched for that gamepad.

When a gamepadchange event would be dispatched and there are multiple queued events, a single gamepadchange event is dispatched that represents the union of all the changes that occurred in the combined events. The events that were combined to create the dispatched event are returned by the event's getCoalescedEvents() method.

### Proposed IDL

```WebIDL
[Exposed=Window, SecureContext]
interface Gamepad {
  // New attributes
  attribute EventHandler onbuttondown;
  attribute EventHandler onbuttonup;
  attribute EventHandler ongamepadchange;
  attribute EventHandler onrawgamepadchange;

  // Existing attributes
  readonly attribute DOMString id;
  readonly attribute long index;
  readonly attribute boolean connected;
  readonly attribute DOMHighResTimeStamp timestamp;
  readonly attribute GamepadMappingType mapping;
  readonly attribute FrozenArray<double> axes;
  readonly attribute FrozenArray<GamepadButton> buttons;
};
```
*Gamepad interface IDL with new EventHandlers*

New EventHandlers are added to the Gamepad interface for each of the added events.

```WebIDL
[Exposed=Window, SecureContext]
interface GamepadChangeEvent : Event {
  constructor(DOMString type, GamepadChangeEventInit eventInitDict);
  readonly attribute Gamepad gamepadSnapshot;
  readonly attribute FrozenArray<long> axesChanged;
  readonly attribute FrozenArray<long> buttonsChanged;
  readonly attribute FrozenArray<long> buttonsPressed;
  readonly attribute FrozenArray<long> buttonsReleased;
  sequence<GamepadChangeEvent> getCoalescedEvents();
};
```
*GamepadChangeEvent interface IDL, used for **gamepadchange** and **rawgamepadchange***

```WebIDL
[Exposed=Window, SecureContext]

interface GamepadButtonEvent: Event {
  constructor(DOMString type, GamepadButtonEventInit eventInitDict);
  readonly attribute long gamepadIndex;
  readonly attribute long buttonIndex;
  readonly attribute GamepadButton buttonSnapshot;
  readonly attribute DOMHighResTimeStamp gamepadTimestamp;
};
```
*GamepadButtonEvent interface IDL, used for **buttondown**, and **buttonup***

### Example

```Javascript
window.ongamepadconnected = connectEvent => {
  connectEvent.gamepad.ongamepadchange = changeEvent => {
    for (let axisIndex of changeEvent.axesChanged) {
      const axisValue = changeEvent.gamepadSnapshot.axes[axisIndex];
      console.log('axis ' + axisIndex +
                  ' on gamepad ' + changeEvent.gamepadSnapshot.index +
                  ' changed to value ' + axisValue);
    }
    for (let buttonIndex of changeEvent.buttonsChanged) {
      const buttonValue = changeEvent.gamepadSnapshot.buttons[buttonIndex].value;
      console.log('button ' + buttonIndex +
                  ' on gamepad ' + changeEvent.gamepadSnapshot.index +
                  ' changed to value ' + buttonValue);
    }
  };
  connectEvent.gamepad.onbuttondown = buttonEvent => {
    console.log('button ' + buttonEvent.buttonIndex +
                ' on gamepad ' + buttonEvent.gamepadIndex + ' pressed');
  };
  connectEvent.gamepad.onbuttonup = buttonEvent => {
    console.log('button ' + buttonEvent.buttonIndex +
                ' on gamepad ' + buttonEvent.gamepadIndex + ' released');
  };
};
```

### Links

Gamepad API spec issue: [Should fire events instead of using passive model #4](https://github.com/w3c/gamepad/issues/4)
PR for this extension: [Add algorithms for getGamepads and events #151](https://github.com/w3c/gamepad/pull/151)
Chrome Status entry: [Gamepad Button and Axis Events](https://chromestatus.com/features/5989275208253440)

### Related work

Firefox implemetns similar events:

* [GamepadAxisMoveEvent.webidl](https://searchfox.org/mozilla-central/source/dom/webidl/GamepadAxisMoveEvent.webidl)
* [GamepadButtonEvent.webidl](https://searchfox.org/mozilla-central/source/dom/webidl/GamepadButtonEvent.webidl)

A superb library that extends the Gamepad API with super powers: [gamepad-plus polyfill](https://github.com/MozillaReality/gamepad-plus/blob/master/README.md)