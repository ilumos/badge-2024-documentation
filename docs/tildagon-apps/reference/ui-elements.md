# UI widget overview

You can use the following premade [`app_components`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/menu.py) to create user interfaces:

## Menu

The [`Menu`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/menu.py) component, allows you to create menus with which you can scroll through a list of options and select an item.

### Example

```python
from app import App
from app_components import Menu, Notification, clear_background

main_menu_items = ["menu_item1", "menu_item2", "menu_item3"]

class MenuDemo(App):
    def __init__(self):
        self.menu = Menu(
            self,
            main_menu_items,
            select_handler=self.select_handler,
            back_handler=self.back_handler,
        )
        self.notification = None

    def select_handler(self, item):
        self.notification = Notification('You selected "' + item + '"!')

    def back_handler(self):
        self.minimise()

    def draw(self, ctx):
        clear_background(ctx)
        self.menu.draw(ctx)
        if self.notification:
            self.notification.draw(ctx)

    def update(self, delta):
        self.menu.update(delta)
        if self.notification:
            self.notification.update(delta)

__app_export__ = MenuDemo
```

You can see a more comprehensive example in the [`menu_demo.py`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/firmware_apps/menu_demo.py).

### Usage

To use a menu:

1. Import the `Menu` component:

    ```python
    from app_components import Menu
    ```

2. Define your menu items above your App class:

    ```python
    menu_items = [ "menu_item1", "menu_item2" ]
    ```

3. Initialize a variable to hold the menu in the `__init__` method of your app:

    ```python
    self.menu = Menu(
        self,
        menu_items,
        select_handler=self.select_handler,
        back_handler=self.back_handler,
    )
    ```

    To initialize the Menu use the following parameters:

    | Parameter | Type | Description |
    | --------- | ---- | ----------- |
    | `app` | `App` | The app to add the menu to. |
    | `menu_items` | `[str]` | A list of strings containing the menu items. |
    | `select_handler` | `method` | The method to be called when an item is selected and the confirm button is pressed. |
    | `back_handler` | `method` | The method to be called when the cancel button is pressed. |
    | `position` | `int` | _Optional_. The menu position to start at. Default: `0`. |
    | `speed_ms` | `int` | _Optional_. The speed to redraw the UI at. Default: `300`. |
    | `item_font_size` | `float` | _Optional_. The item line height. Default: [`tokens.ten_pt`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/tokens.py). |
    | `item_line_height` | `float` |_Optional_. The item line height. Default: [`tokens.label_font_size * tokens.line_height`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/tokens.py). |
    | `focused_item_font_size` | `float` | _Optional_. The font size of the focused item. Default: [`tokens.heading_font_size`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/tokens.py). |
    | `focused_item_margin` | `float` | _Optional_. The margin on the focused item. Default: `20`. |

4. If you are creating a multi-layered menu, also create a variable like `current_menu` in the `__init__` method of your app to store the users menu state:
    ```python
    self.current_menu = None
    ```

4. Define the `select_handler` and the `back_handler`:

    ```python
    def back_handler(self):
        # Do something. If in the topmost menu, minimize, otherwise move one menu up.
        if self.current_menu == "main":
            self.minimise()
        self.set_menu("main")

    def select_handler(self, item):
        # Do something based on the selection, like moving to a new menu or performing an action.

    ```

5. Add the following lines in your `draw()` method to draw the app's menu:

    ```python
    # in def draw(self, ctx):
    self.menu.draw(ctx)
    ```

6. Add the following lines in your `update()` method to update the state of the menu:

    ```python
    # in def update(self, delta):
    self.menu.update(delta)
    ```

### Methods

You can use the following methods on a `Menu` object:

| Method | Description | Parameter | Returns |
| ------ | ----------- | --------- | ------- |
| `up_handler()` | Manually moves you up one position in the menu. | None | None |
| `down_handler()` | Manually moves you down one position in the menu. | None | None |
| `update(delta)` | Update the menu as animations are happening. You need to call this method in your app's `update()` method. | `delta`: Time difference between the last update call and the current update call. | None |
| `draw(ctx)` | Add the menu to the screen. You need to call this method in your app's `draw()` method. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |

## Notification

The [`Notification`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/notification.py) component allows you to create pop up notifications.

### Example

This example app shows a notification for each button you press.

```python
from app import App
from app_components import Notification, clear_background
from events.input import Buttons, BUTTON_TYPES


class NotificationDemo(App):
    def __init__(self):
        self.notification = None
        self.button_states = Buttons(self)

    def update(self, delta):
        if self.button_states.get(BUTTON_TYPES["RIGHT"]):
            self.notification = Notification("right!")
        if self.button_states.get(BUTTON_TYPES["LEFT"]):
            self.notification = Notification("left!")
        if self.button_states.get(BUTTON_TYPES["UP"]):
            self.notification = Notification("up!")
        if self.button_states.get(BUTTON_TYPES["DOWN"]):
            self.notification = Notification("down!")
        if self.button_states.get(BUTTON_TYPES["CANCEL"]):
            self.notification = Notification("cancel!")
        if self.button_states.get(BUTTON_TYPES["CONFIRM"]):
            self.notification = Notification("confirm!")
        if self.notification:
            self.notification.update(delta)

    def draw(self, ctx):
        clear_background(ctx)
        if self.notification:
            self.notification.draw(ctx)

__app_export__ = NotificationDemo
```

### Usage

To use a notification:

1. Import the `Notification` component:

    ```python
    from app_components import Notification
    ```

2. Initialize the notification in the `__init__` method of your app:

    ```python
    self.notification = None
    ```

3. Set the notification text in your app's code:

    ```python
    self.notification = Notification("This is a notification!")
    ```

    `Notification()` supports the following parameters:

    | Parameter | Type | Description |
    | --------- | ---- | ----------- |
    | `message` | `str` | The notification message. |
    | `port` | `int` | _Optional_. The port from which the notification was issued. Default: `0`. |
    | `open` | `boolean` | _Optional_. Whether to open the notification. Default: `True`. |

4. Add the following lines in your `draw()` method to draw the notification when `self.notification` contains a notification:

    ```python
    # in def draw(self, ctx):
    if self.notification:
        self.notification.draw(ctx)
    ```

5. Add the following lines in your `update()` method to update the state of the notification:

    ```python
    # in def update(self, delta):
    if self.notification:
        self.notification.update(delta)
    ```

### Methods

You can use the following methods on a `Notification` object:

| Method | Description | Arguments | Returns |
| ------ | ----------- | --------- | ------- |
| `open()` | Manually open the notification. | None | None |
| `close()` | Manually close the notification. | None | None |
| `update(delta)` | Automatically display the notification for a period of 5 seconds. You need to call this method in your app's `update()` method. | `delta`: Time difference between the last update call and the current update call. | None |
| `draw(ctx)` | Add the notification to the screen. You need to call this method in your app's `draw()` method. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |

## Yes/No Dialog

The [`YesNoDialog`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/dialog.py) component allows you to create yes or no dialogues.

### Example

=== "Synchronous"

    This example app shows a dialog that asks you if it's a happy day and responds with an appropriate message. It calls the `YesNoDialog` in a synchronous way:

    ```python
    import app

    from app_components import YesNoDialog, clear_background


    class DialogDemo(app.App):
        def __init__(self):
            # Need to call to access overlays for dialog
            super().__init__()
            self.dialog = None
            self.answer = ""
            self.displayed = False

        def _no_handler(self):
            self.dialog._cleanup()
            self.answer = "I'm sorry"
            self.dialog = None

        def _yes_handler(self):
            self.dialog._cleanup()
            self.answer = "YAY!"
            self.dialog = None

        def update(self, delta):
            if not self.displayed:
                self.displayed = True
                self.dialog = YesNoDialog(
                    message="Is it a happy day?",
                    on_yes=self._yes_handler,
                    on_no=self._no_handler,
                    app=self,
                )

        def draw(self, ctx):
            clear_background(ctx)

            ctx.save()
            if self.answer:
                ctx.rgb(0,0,0.2).rectangle(-120,-120,240,240).fill()
                ctx.rgb(0,0,1).move_to(-80,0).text(self.answer)
            ctx.restore()

            if self.dialog:
                self.dialog.draw(ctx)

    __app_export__ = DialogDemo
    ```

=== "Asynchronous"

    This example app shows a dialog that asks you if it's a happy day and responds with an appropriate message. It overwrites the `run()` method to be able to use the `run()` method on the `YesNoDialog` which allows you to perform asynchronous actions based on the output of the dialog:

    ```python
    import app

    from app_components import YesNoDialog, clear_background


    class DialogDemo(app.App):
        def __init__(self):
            # Need to call to access overlays for dialog
            super().__init__()
            self.answer = None

        async def run(self, render_update):
            # Render initial state
            await render_update()

            # Create a yes/no dialogue, add it to the overlays
            dialog = YesNoDialog("Is it a happy day?", self)
            self.overlays = [dialog]
            # Wait for an answer from the dialogue.
            # if the answer was yes, the dialog.run() will return a true value
            # and we set an appropriate answer
            if await dialog.run(render_update):
                self.answer = "YAY!"
            else:
                self.answer = "I'm sorry"
            # Remove the dialogue and re-render
            self.overlays = []
            await render_update()


        def draw(self, ctx):
            clear_background(ctx)
            if self.answer:
                ctx.save()
                ctx.rgb(0,0,0.2).rectangle(-120,-120,240,240).fill()
                ctx.rgb(0,0,1).move_to(-80,0).text(self.answer)
                ctx.restore()
            self.draw_overlays(ctx)

    __app_export__ = DialogDemo
    ```

### Usage

To use the Yes/No dialog:

1. Import the `YesNoDialog` component:

    ```python
    from app_components import YesNoDialog
    ```

2. Add the following line to the `__init__` method of your app to initialize the inherited object:

    ```python
    super().__init__()
    ```

=== "Synchronous"

      3. Create the `YesNoDialog` from your `update()` method and pass in the `message` to display, as well as handlers for what to do when the user responds with yes or no. Since the `update()` method gets called repeatedly, make sure it only gets called once:

        ```python
        self.dialog = YesNoDialog(
            message="Is it a happy day?",
            on_yes=self._yes_handler,
            on_no=self._no_handler,
            app=self,
        )
        ```

=== "Asynchronous"

      3. Use the `run()` method which supports asynchronous calls. You need asynchronous calls to wait for the answer to the dialog:

          ```python
          async def run(self, render_update):
              # Render initial state
              await render_update()

              # Create a yes/no dialogue, add it to the overlays
              dialog = YesNoDialog("Is it a happy day?", self)
              self.overlays = [dialog]
              # Wait for an answer from the dialogue, and if it was yes, do something
              if await dialog.run(render_update):
                  # this sets a variable that can be used in the draw method
                  self.answer = "I'm sorry"
              else:
                  self.answer = "YAY!"
              # Remove the dialogue and re-render
              self.overlays = []
              await render_update()

          ```

`YesNoDialog()` supports the following parameters:

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| `message` | `str` | The dialog message. |
| `app` | `App` | The app opening the dialog. |
| `on_yes` | `method` | _Optional_. Call the provided handler method or return `True` if answer is yes and no handler is provided. Default: `None`. |
| `on_no` | `method` | _Optional_. Call the provided handler method or return `False` if answer is yes and no handler is provided. Default: `None`. |

=== "Synchronous"

      4. Add the following lines in your `draw()` method to draw the dialog's overlays:

          ```python
          # in def draw(self, ctx):
              if self.dialog:
                  self.dialog.draw(ctx)
          ```

=== "Asynchronous"

      4. Add the following lines in your `draw()` method to clear the background and draw the dialog's overlays:

          ```python
          # in def draw(self, ctx):
              clear_background(ctx)
              self.draw_overlays(ctx)
          ```

To make the dialog's answers have an effect you need to do something based on the input you have received from the `YesNoDialog`. Check the example for an idea.

### Methods

You can use the following methods on a `YesNoDialog` object:

| Method | Description | Arguments | Returns |
| ------ | ----------- | --------- | ------- |
| `run(render_update)` | Asynchronous. Open the dialog. You need to call this method to display the dialog. | `render_update`: The method that triggers a `draw()` call when updates are complete. | `True` or `False` |
| `draw_message(ctx)` | Helper method to add your message to the screen. This method is called by the `draw()` method. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |
| `draw(ctx)` | Add the dialog to the screen. You need to call this method in your app's `draw()` method. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |

## Text Dialog

The [`TextDialog`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/dialog.py) component allows you to create ask for text input.

### Example

=== "Synchronous"

    This example app shows a dialog that asks you for your name and then says hell:

    ```python
    import app
    from app_components import TextDialog, clear_background


    class TextDemo(app.App):
        def __init__(self):
            super().__init__()
            self.name = ""
            self.dialog = None
            self.displayed = False

        def _cancel_handler(self):
            self.name = "world!"
            self.dialog._cleanup()
            self.dialog = None

        def _complete_handler(self):
            self.name = self.dialog.text
            self.dialog._cleanup()
            self.dialog = None

        def update(self, delta):
            if not self.displayed:
                self.displayed = True
                self.dialog = TextDialog(
                    "What is your name?",
                    self,
                    masked=False,
                    on_complete=self._complete_handler,
                    on_cancel=self._cancel_handler)

        def draw(self, ctx):
            clear_background(ctx)

            ctx.save()
            if self.name:
                ctx.text_align = "center"
                ctx.gray(1).move_to(0, 0).text("Hello " + self.name)
            ctx.restore()

            if self.dialog:
                self.dialog.draw(ctx)

    __app_export__ = TextDemo
    ```

=== "Asynchronous"

    This example app shows a dialog that asks you for a name and then says hello. It overwrites the `run()` method to be able to use the `run()` method on the `TextDialog` which allows you to perform asynchronous actions based on the output of the dialog:

    ```python
    import app
    from app_components import TextDialog, clear_background


    class TextDemo(app.App):
        def __init__(self):
            super().__init__()
            self.name = "world!"

        async def run(self, render_update):
            await render_update()

            dialog = TextDialog("What is your name?", self)
            self.overlays = [dialog]

            if await dialog.run(render_update):
                self.name = dialog.text

            self.overlays = []
            await render_update()

        def draw(self, ctx):
            clear_background(ctx)

            ctx.save()
            ctx.text_align = "center"
            ctx.gray(1).move_to(0, 0).text("Hello " + self.name)
            ctx.restore()

            self.draw_overlays(ctx)

    __app_export__ = TextDemo
    ```

### Usage

To use the text dialog:

1. Import the `TextDialog` component:

    ```python
    from app_components import TextDialog
    ```

2. Add the following line to the `__init__` method of your app to initialize the inherited object:

    ```python
    super().__init__()
    ```

=== "Synchronous"

      3. Create the `TextDialog` from your `update()` method and pass in the `message` to display, whether to mask (that means obscure) the input while the suer is entering it, as well as handlers for what to do when the user completes their input or cancels entering input. Since the `update()` method gets called repeatedly, make sure it only gets called once:

        ```python
        self.dialog = TextDialog(
            "What is your name?",
            self,
            masked=False,
            on_complete=self._complete_handler,
            on_cancel=self._cancel_handler)
        ```

=== "Asynchronous"

      3. Use the `run()` method which supports asynchronous calls. You need asynchronous calls to wait for the answer to the dialog:

          ```python
          async def run(self, render_update):
              # Render initial state
              await render_update()

              # Create a text dialogue, add it to the overlays
              dialog = TextDialog("What is your name?", self)
              self.overlays = [dialog]
              # Wait for an answer from the dialogue, and if it was yes, do something
              if await dialog.run(render_update):
                  # this sets a variable that can be used in the draw method
                  self.name = dialog.text
              else:
                  # this is run when the user cancels the dialog
                  self.answer = "anonymous"
              # Remove the dialogue and re-render
              self.overlays = []
              await render_update()

          ```

`TextDialog()` supports the following parameters:

| Parameter | Type | Description |
| --------- | ---- | ----------- |
| `message` | `str` | The dialog message. |
| `app` | `App` | The app opening the dialog. |
| `masked` | `bool` | _Optional_. Whether to obscure the text buffer with asterisks (for example, for passwords). Default: `False`. |
| `on_complete` | `method` | _Optional_. Call the provided handler method or return the text entry if the text entry is confirmed and no handler is provided. Default: `None`. |
| `on_cancel` | `method` | _Optional_. Call the provided handler method or return `False` if answer is cancelled and no handler is provided. Default: `None`. |

=== "Synchronous"

      4. Add the following lines in your `draw()` method to draw the dialog's overlays:

          ```python
          # in def draw(self, ctx):
              if self.dialog:
                  self.dialog.draw(ctx)
          ```

=== "Asynchronous"

      4. Add the following lines in your `draw()` method to clear the background and draw the dialog's overlays:

          ```python
          # in def draw(self, ctx):
              clear_background(ctx)
              self.draw_overlays(ctx)
          ```

To make the dialog's answer have an effect you need to do something with the answer you received. The response from the dialog is saved in `text` on the dialog (for example `dialog.text`). Check the example for an idea.

### Methods

You can use the following methods on a `TextDialog` object:

| Method | Description | Arguments | Returns |
| ------ | ----------- | --------- | ------- |
| `run(render_update)` | Asynchronous. Open the dialog. You need to call this method to display the dialog. | `render_update`: The method that triggers a `draw()` call when updates are complete. | `True` or `False` |
| `draw_message(ctx)` | Helper method to add your message to the screen. This method is called by the `draw()` method. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |
| `draw(ctx)` | Add the dialog to the screen. You need to call this method in your app's `draw()` method. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |

## Layouts

You can componse menu layouts out of the following display layouts:

- [`TextDisplay`](./#textdisplay): The `TextDisplay` component allows you to display long texts.
- [`ButtonDisplay`](./#buttondisplay): The `ButtonDisplay` component allows you to display a button and register a handler for the button.
- [`DefinitionDisplay`](./#definitiondisplay): The `DefinitionDisplay` component allows you to display a label and a definition for the label.

### Example

This example creates what could be a small game menu with three parts:

- one text field that can be edited for the game player's name
- one selector field where you can select an option, in this case the difficulty level
- one field that displays a lot of text, in this case lorem ipsum text

```python
import app

from app_components import clear_background, TextDialog
from app_components.layout import TextDisplay, ButtonDisplay, DefinitionDisplay, LinearLayout
from events.input import BUTTON_TYPES, ButtonDownEvent
from system.eventbus import eventbus

DIFFICULTY_VALUES = ["easy", "normal", "hard"]

def string_formatter(value):
    if value is None:
        return "Default"
    else:
        return str(value)

class LayoutMenuDemo(app.App):
    def __init__(self):
        self.layout = LinearLayout(items=[DefinitionDisplay("", "")])
        self.dialog = None
        self.options = {
            ("text_setting", "Player Name", string_formatter, self.string_editor),
            ("button_selector", "Difficulty selector", string_formatter, None),
            ("text", "Game Instructions", string_formatter, None),
        }
        self.app_settings = {
            "text_setting": "Naomi",
            "button_selector": "easy",
            "instructions": "Lorem ipsum dolor sit amet, consectetur adipiscing elit, sed do eiusmod tempor incididunt ut labore et dolore magna aliqua. Ut enim ad minim veniam, quis nostrud exercitation ullamco laboris nisi ut aliquip ex ea commodo consequat. Duis aute irure dolor in reprehenderit in voluptate velit esse cillum dolore eu fugiat nulla pariatur. Excepteur sint occaecat cupidatat non proident, sunt in culpa qui officia deserunt mollit anim id est laborum."
        }
        eventbus.on_async(ButtonDownEvent, self._button_handler, self)

    async def string_editor(self, label, id, render_update):
        self.dialog = TextDialog(label, self)
        self.dialog._settings_id = id

    async def _button_handler(self, event):
        layout_handled = await self.layout.button_event(event)
        if not layout_handled:
            if BUTTON_TYPES["CANCEL"] in event.button:
                self.minimise()

    async def update_values(self):
        for item in self.layout.items:
            if isinstance(item, DefinitionDisplay):
                for id, label, formatter, _ in self.options:
                    if item.label == label:
                        if id in self.app_settings.keys():
                            value = self.app_settings[id]
                        else:
                            value = ""
                        item.value = formatter(value)

    async def run(self, render_update):
        while True:
            self.layout.items = []
            for id, label, formatter, editor in self.options:
                if id in self.app_settings.keys():
                    value = self.app_settings[id
                    ]
                else:
                   value = ""
                if editor:
                    async def _button_event(event, label=label, id=id, editor=editor):
                        if BUTTON_TYPES["CONFIRM"] in event.button:
                            await editor(label, id, render_update)
                            return True
                        return False

                    entry = DefinitionDisplay(
                        label, formatter(value), button_handler=_button_event
                    )
                else:
                    entry = DefinitionDisplay(label, formatter(value))
                self.layout.items.append(entry)
                if id == "text":
                    async def _button_event(event):
                        if BUTTON_TYPES["CONFIRM"] in event.button:
                            return True
                        return False

                    entry = TextDisplay(self.app_settings["instructions"])

                    self.layout.items.append(entry)

                if id == "button_selector":
                    async def _button_selector_event(event):
                        value = self.app_settings[id]
                        if BUTTON_TYPES["CONFIRM"] in event.button:
                            if value == DIFFICULTY_VALUES[0]:
                                self.app_settings[id] = DIFFICULTY_VALUES[1]
                            if value == DIFFICULTY_VALUES[1]:
                                self.app_settings[id] = DIFFICULTY_VALUES[2]
                            if value == DIFFICULTY_VALUES[2]:
                                self.app_settings[id] = DIFFICULTY_VALUES[0]
                            await self.update_values()
                            await render_update()
                            return True
                        return False

                    entry = ButtonDisplay(
                        "Change", button_handler=_button_selector_event
                    )
                    self.layout.items.append(entry)
            while True:
                await render_update()
                if self.dialog:
                    result = await self.dialog.run(render_update)
                    if (
                        result is not False
                    ):  #!= because we want to allow entering empty strings
                        self.app_settings[self.dialog._settings_id] = result
                    self.dialog = None
                    if result:
                        break


    def update(self, delta):
        return True

    def draw(self, ctx):
        clear_background(ctx)
        self.layout.draw(ctx)

__app_export__ = LayoutMenuDemo
```

A longer example of a layout is the [Settings app](https://github.com/emfcamp/badge-2024-software/blob/main/modules/firmware_apps/settings_app.py).

### Usage

To use layouts:

1. Import the different layouts:

    ```python
    from app_components.layout import *
    ```

1. Initialize a variable to hold the layouts in the `__init__` method of your app:

    ```python
    self.layout = LinearLayout(items=[DefinitionDisplay("", "")])
    ```

1. Add one or more layouts:

    === "DefinitionDisplay"

        The `DefinitionDisplay` component allows you to display a label and a definition for the label.

        Initialize the button_display in your `__init__` or in your `update` method:

        ```python
        definition_display = DefinitionDisplay("Label", "value")
        self.layout.items.append(definition_display)
        ```

        To initialize the definition display use the following parameters:

        | Parameter | Type | Description |
        | --------- | ---- | ----------- |
        | `label` | `str` | The text to display for the label. |
        | `value` | `str` | The text to display for the definition. |
        | `height` | `int` | The height at which to display the definition display. |
        | `button_handler` | `method` | The handler for button events. |

    === "TextDisplay"

        The [`TextDisplay`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/layout.py) component allows you to display long texts.

        Initialize the text_display in your `__init__` or in your `update` method and add it to the `self.layout.items` variable:

        ```python
        text_display = TextDisplay("My long text", font_size=8, rgb=(0,0,50))
        self.layout.items.append(text_display)
        ```

        To initialize the `TextDisplay` use the ?following parameters:

        | Parameter | Type | Description |
        | --------- | ---- | ----------- |
        | `text` | `str` | The long text to display. |
        | `font_size` | `int` | The font size to display the text in. |
        | `rgb` | `tuple` | The color to display the text in. |

    === "ButtonDisplay"

        The `ButtonDisplay` component allows you to display a button and register a handler for the button.

        Initialize the button_display in your `__init__` or in your `update` method:

        ```python
        button_display = ButtonDisplay(
            text="Select me",
            app=self,
            font_size=8,
            rgb=(50,0,0),
            button_handler=self.select_handler)
        self.layout.items.append(button_display)
        ```

        To initialize the button display use the following parameters:

        | Parameter | Type | Description |
        | --------- | ---- | ----------- |
        | `text` | `str` | The long text to display. |
        | `app` | `App` | The app to add the button display to. |
        | `font_size` | `int` | The font size to display the text in. |
        | `rgb` | `tuple` | The color to display the text in. |
        | `button_handler` | `method` | The handler for button events. |

        Create an asynchronous `select_handler` that does something when a button is pressed:

        ```python
            async def select_handler(event):
                if BUTTON_TYPES["CONFIRM"] in event.button:
                    # do something
                return False
        ```

1. Add the following lines in your `draw()` method to draw the app's layout:

    ```python
    # in def draw(self, ctx):
     self.layout.draw(ctx)
    ```

## Tokens

The [`Tokens`](https://github.com/emfcamp/badge-2024-software/blob/main/modules/app_components/tokens.py) component allows you to use:
    - functions for clearing the background and setting a color
    - constants for the display properties and colors

### Functions

| Method | Description | Arguments | Returns |
| ------ | ----------- | --------- | ------- |
| `clear_background(ctx)` | Clear the badge background. | `ctx`: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md). | None |
| `set_color(ctx, color)` | Set the color for the canvas. | <ul><li><code>ctx</code>: The canvas that let's you add graphics or texts. See [`ctx` library](../reference/ctx.md).</li><li><code>color</code>: The provided color as a string from the following options: `pale_green`, `mid_green`, `dark_green`, `colors.yellow`, `colors.orange`, `colors.pink`, `colors.blue`, `ui_colors.background`, `ui_colors.label`. </li></ul> | None |

#### Usage

```python
from app_components.tokens import clear_background, set_color

clear_background(ctx)
set_color(ctx, "label")
```

### Constants

To use the pre-defined constants, import them from the `Tokens` component, for example:

```python
from app_components.tokens import display_x
```

You can access the following variables:

For display:

- `display_x`
- `display_y`
- `display_height_inches`
- `ppi`

For fonts:

- `one_pt`
- `ten_pt`
- `twelve_pt`
- `eighteen_pt`
- `twentyfour_pt`
- `label_font_size`
- `heading_font_size`
- `line_height`

For colors:

- `colors.pale_green`
- `colors.mid_green`
- `colors.dark_green`
- `colors.yellow`
- `colors.orange`
- `colors.pink`
- `colors.blue`
- `ui_colors.background`
- `ui_colors.label`
