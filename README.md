# lofi window/door sensor

## Uses attiny84a + nrf24l01p + tpl5111 to achieve very low power.

This project was influenced by a LoFi board designed by
David Cook and entered in a hackaday contest.
See https://hackaday.io/project/1552-lofi

At the beginning, I used OshPark to make some of his
boards and hacked them to work with an NRF24l01+.

I have mated the lofi board with a nrf24l01+ instead of using
the 433 MHz transmitter described at the above link.

I'm using the nrf24 in its very basic form, using minimal
features.

I am using parts of xprintf.[ch] by ChaN (see xprintf.c for copyright)
I am using parts of nrf24.[ch] by <ihsan@ehribar.me>
(see nrf24.c for license)

The lofi+nrf24 combo draws ~2 uA in the idle mode.
It can monitor one reed switch, Vcc and internal temperature.
It uses the internal RC oscillator so timing is not very accurate.
The switch can be configured to generate an interrupt on Pin Change
which will trigger a transmission. Otherwise, it is only polled
and switch state will be transmitted at the polling frequency.
The polling period is configured in eeprom, to be a multiple
of the TPL5111 period.

A 10-bit incrementing count can also be enabled and will be
sent at a frequency determined by eeprom and TPL5111.

I am making my own board now that uses a TPL5111 to periodically
wake the ATtiny84. I do not power down the t84 but instead,
use a Pin Change function to monitor the TPL5111 DRVn pin.
So, I continuously draw about 2uA.
I chose this method, so that my original code did not have
to change much. I can still use SRAM between sleeps.
If I powered down the t84 between DRVn assertions, I would
have to send all messages each time. I don't think there
would be a method of only sending Vcc messages every n
DRV assertions.

I'm using spin loop delays. See util/delay basic.h.
_delay_loop_1 loop is three cycles and takes an 8-bit
loop counter. 3us * 256 is max delay.
_delay_loop_2 loop is four cycles and takes a 16-bit
loop counter. 4us * 65536 is max delay.

Configuration is stored in the eeprom starting at addr 0.
See config_t structure for details.

---

