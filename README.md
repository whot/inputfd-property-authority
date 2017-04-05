inputfd-property-authority
==========================

**This is a draft only, this repository's contents will change
significantly**

inputfd is a ***DRAFT*** wayland protocol to pass file descriptors to input
devices directly to a wayland client. For more details, see
[this blog post](http://who-t.blogspot.com.au/2017/04/inputfd-protocol-for-direct-access-to.html)
and the [thread around initial patch series](https://lists.freedesktop.org/archives/wayland-devel/2017-March/033626.html).

Part of this protocol draft is a "property" event with a key/value pair for
information about the device. The protocol leaves the content of this
key/value pair unspecified and defers to an external authority for
standardization. **inputfd-property-authority** is this 
authority.

The goal of this repository is to specify a common set of properties that
are recognised and used by the Wayland compositor and the Wayland client.

Property Format
---------------

All properties are in a key/value format.

The requirements for property keys are:
* UTF-8 encoded zero-terminated strings with a maximum length of 1024 bytes,
including the terminating zero.
* keys are prefixed with ```IFDA_``` ("inputfd authority")
* key naming format ```IFDA_UPPERCASE_UNDERSCORE```

The requirements for property values are:
* Free-form keys are zero-terminated strings. Free-form keys are any
  user-visible key or keys obtained from third-party sources (e.g. a device
  name)
* Restricted-value-set keys are in format```lowercase-hyphen```
* The list separator for multiple entries is a semicolon (;) without
  preceding or subsequent spaces. A list of multiple entries
  **must** terminate in a semicolon. e.g. a property may be defined
  as: ```CARDINAL_DIRECTIONS``` with the value ```north;south;south-west;```
* Where the property is a pure number, no whitespace is permitted.
  * a hexadecimal number is always in format 0xab12..., i.e. prefixed by
    "0x" and using the lowercase letters a-f.
  * an octal number is always in format 012..., i.e. prefixed by "0" 
  * a binary number is always in format 0b101..., i.e. prefixed by "0b"
  * a floating point number always uses '.' as the decimal separator
    character and provides at least one floating point and at least one
    integer digit, i.e. the integer number 10 is "10.0" and the fraction .5
    is "0.5"
  Sequences of numbers are whitespace-less, separated by semicolons, e.g.
  "0xab;0.10;50;"

These requirements are chosen for readability and to make parsers as
restrictive as possible, don't read too much into it otherwise.

Properties
----------

In alphabetical order.

* **```IFDA_BUSTYPE```**:
  The bus type of this device. Permitted values are
  * **```bluetooth```**
  * **```usb```**
* **```IFDA_DEVICE_GROUP```**:
  If set, this device may be identified as one of a set of multiple logical
  devices that represent the same physical device. For example, a controller
  may expose a gamepad and a touchpad on the same physical device but
  provide separate devices for it otherwise.

  The value is an **arbitrary length string of characters [a-f0-9]** and may
  only be used for string comparison with other devices that have a device
  group set.
* **```IFDA_ID_PRODUCT```**:
  A 16-bit hexadecimal number representing the USB product ID
* **```IFDA_ID_VENDOR```**:
  A 16-bit hexadecimal number representing the USB vendor ID
* **```IFDA_INTEGRATION_TYPE```**:
  Describes how the device is integrated into the host system. Permitted
  values are
  * **```external```**: the device is an external, removable device (e.g.
    a USB device)
  * **```internal```**: the device is built into the host system and cannot
    be removed (e.g. most touchpads)
  If this value is not set, a client should assume ```external```.
* **```IFDA_JOYSTICK_TYPE```**:
  If set, this device is a gaming device of some type. Permitted values are
  * **```joystick```**: a joystick-like device
  * **```gamepad```**: a gamepad-like device
  * **```wheel```**: a steering wheel
  * **```footpedal```**: a foot pedal
  * **```touchpad```**: a touchpad input device
* **```IFDA_VERSION```**:
  A decimal number denoting the IFDA version supported by this sender. This
  is an incremental version like e.g. systemd uses, **not** a semantic version.
