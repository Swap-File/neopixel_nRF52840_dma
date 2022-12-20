# neopixel_nRF52840_dma

Under certain condition, on mbed nRF52840 boards (like the Seeed Studio XIAO nRF52840) I found that using Adafruit_NeoPixel and ArduinoBLE at the same time could cause the ArduinoBLE stack to lock up with the error "[Info] Unhandled event".
 
Specifically, I had a central device writing 15 byte payloads to a peripheral at 20hz, while also writing to a string of 20 neopixels at 50hz on the peripheral.

The lockup would occur when BLE data came in during a LED show(), but not always.

It seemed like the lockup would happen if BLE data came in while show() was configuring the PWM & DMA transfer.

Adafruit's show() function for the nRF52840 architecture reconfigures the PWM and DMA transfer every time it is called, which is not needed.

This hacked up version of the library configures the PWM and DMA transfer once, on begin().  It also does not wait for the DMA transfer to complete before the function returns.

This has removed all of my BLE lockups, and as a side benefit it allows the show() function to return much quicker.

Make sure your NeoPixel's begin() is called before BLE.begin().  updateLength() and updateType() are not supported.

Multi-string operation has not been tested.
