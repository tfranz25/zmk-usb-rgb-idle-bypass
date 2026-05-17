# ZMK USB RGB Idle Bypass (`zmk-usb-rgb-idle-bypass`)

A lightweight, standalone ZMK module that prevents your keyboard's RGB underglow from turning off when connected via USB, while maintaining power-saving idle timeouts when running on battery (Bluetooth). **Fully compatible with both standard ZMK underglow features, as well as the `zmk-rgb-plus` custom lighting module.**

## 🤖 AI Disclosure

This ZMK module was built with the assistance of AI tools. The code has been reviewed, tweaked, and tested on [TibbyPad](https://github.com/tfranz25/tibbypad-module), but as with all AI-assisted firmware, please review the configurations before flashing it to your device.

## 💡 Why use this?
In native ZMK, enabling `CONFIG_ZMK_RGB_UNDERGLOW_AUTO_OFF_IDLE=y` turns off the underglow on inactivity regardless of how the keyboard is powered. 

This module bypasses that limitation, giving you the best of both worlds:
- 🔋 **On Battery (Bluetooth):** RGB turns off automatically after your configured idle timeout to conserve battery.
- 🔌 **On USB Power:** RGB stays beautifully illuminated indefinitely, ignoring the idle timeout.

---

## 🚀 Installation

### 1. Update `west.yml`
Add this module to your ZMK user config's `config/west.yml` under the `projects` section:

```yaml
manifest:
  projects:
    # ... your existing projects ...
    - name: zmk-usb-rgb-idle-bypass
      url: https://github.com/tfranz25/zmk-usb-rgb-idle-bypass
      revision: main
```

### 2. Configure your board's `.conf` file
Remove the native ZMK idle config and enable the module's custom flag:

```kconfig
# Disable native ZMK idle underglow off behavior
# CONFIG_ZMK_RGB_UNDERGLOW_AUTO_OFF_IDLE=y (REMOVE or set to n)

# Enable Bluetooth-only idle RGB auto-off
CONFIG_ZMK_BT_IDLE_ONLY=y
```

---

## ⚙️ Configuration Options

| Feature | Kconfig Flag | Default | Description |
|---|---|---|---|
| **Bluetooth Idle Only** | `CONFIG_ZMK_BT_IDLE_ONLY` | `n` | Enables the USB-bypass logic. Auto-turns off RGB only when idle on battery. |

---

## 🛠️ How it works
This module intercepts the `zmk_activity_state_changed` and `zmk_usb_conn_state_changed` events natively. 

If it detects the keyboard has gone idle, it checks the USB power status (`zmk_usb_is_powered()`). If USB is active, it preserves the active lighting. If the keyboard is running on Bluetooth, it safely saves your lighting settings and turns off the LEDs, restoring them automatically as soon as you wake the board up.

Depending on your configuration, the module automatically compiles with and communicates with either:
- **Classic ZMK Underglow** (`CONFIG_ZMK_RGB_UNDERGLOW`): Calls native `zmk_rgb_underglow_on()` / `zmk_rgb_underglow_off()`.
- **ZMK RGB Plus Advanced Lighting** (`CONFIG_ZMK_RGB_PLUS`): Calls custom `zmk_rgb_plus_on()` / `zmk_rgb_plus_off()` with full work loop suspension for optimal battery efficiency.
