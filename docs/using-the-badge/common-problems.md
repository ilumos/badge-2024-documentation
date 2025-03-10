---
title: Common problems
weight: 5
---

## Detecting hexpansions

Normally, hexpansions are detected immediately on being plugged in, indicated by a light illuminating them. Sometimes they may not be detected automatically. Rebooting the badge can help with this, but there is an additional method on firmware v1.6.0 and above.

If you press and hold the boop button, then press the edge button to the left of the hexpansion port you've inserted something in to, it will force a detection.

For example, if you plug a hexpansion into the port on the right-hand side of the badge and it doesn't detect, you can press boop and button B at the same time to trigger detection. Once you've finished with this hexpansion, pressing boop again will trigger a removal.

## Unable to update

If your badge fails the update, try moving to a spot with better WiFi. If the updates continues to fail, you may have to follow the steps to [flash your badge](./end-user-manual.md#flash-your-badge).

## Broken components

See [Replacing badge components](./replacing-parts.md).

## Turn off badge with broken screen

### Option 1: Buttons only

1. Restart the badge by pressing the **Reboop** button until the light pattern restarts.
2. Press **D** (down) three times.
3. Press **C** (confirm) twice.

### Option 2: `mpremote`

[Install `mpremote`](https://docs.micropython.org/en/latest/reference/mpremote.html) and follow the steps in [Debug your app on your badge](../tildagon-apps/run-on-badge.md#debug-your-app-on-your-badge) to get a shell on your badge and then run the following commands:

```sh
import machine
import bq25895

bq25895.bq25895(machine.I2C(7)).disconnect_battery()
```

After running the command, disconnect your badge from the USB cable and your badge will be turned off.

## I entered my name wrong

If you want to retain your data, you can [install `mpremote`](https://docs.micropython.org/en/latest/reference/mpremote.html) and follow the steps in [Debug your app on your badge](../tildagon-apps/run-on-badge.md#debug-your-app-on-your-badge) to get a shell on your badge and then change the setting by running the following commands, replacing `YourNewName` with your name:

```sh
import settings
settings.set("name", "YourNewName")
settings.save()
```

If you don't have any important data on your badge you can [flash your badge](./end-user-manual.md#flash-your-badge) which will erase all your data.