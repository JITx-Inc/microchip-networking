# Installation

In slm.toml add:
```
microchip-networking = { git = "JITx-Inc/microchip-networking", version = "0.6.0" }
```

# KSZ9563 3 Port Network Switch
## circuit
This module defines many of the supporting features for instantiating the [KSZ9563](https://www.microchip.com/en-us/product/ksz9563) network switch in a design, including:
- Configuration passives
- Bypass capacitors
- Power filters
- Crystal resonators

This module makes the underlying component instance `netsw` public so that the user can directly access ports as necessary.

The current implementation is supports RMII and MIIM for the configuration interface.
```
  inst switch : microchip-networking/components/KSZ9563/circuit(substrate)
```
### ports
```
  port VDD-1v2 : power ; 1.2V Power Rail input. This rail will be used for both the digital and analog 1.2V rails with a ferrite power filter instantiated for the analog.
  port VDD-3v3 : power ; 3.3V Power rail input. THis rail will be used for the VDD-IO and the Analog 3.3V power rail. A ferrite power filter is applied to source to the analog power rail.
  port mii : rmii-bus ; Media Independent Interface implemented as a RMII (Reduced Media Independent Interface)
  port management : miim ; Management interface, currently implemented as a MIIM interface.
  port power-evt-n ; Active low power event signal from the switch
  port irq-n ; Active low interrupt request signal from the switch
  port LEDs : pin[2][2] ; LED1 and LED2 for status signals
```
Supports
```
; We have to use a special bundle type that includes the LEDs so the proper LED control pins connected to the right ethernet connector.
require sw-mdi : microchip-networking/components/KSZ9563/MDI-1000BaseT-With-LEDs[2] from switch.netsw
```
### Parameters
- `substrate:Substrate` PCB Substrate where the vias are defined. This circuit requires at least one via that is `via-in-pad` capable.

--

- `make-oscillator:True|False` Instantiate a crystal oscillator circuit for the switch. Use `false` here to drive the clock input of the KSZ9563 directly with a logic level signal. Default value is `true`.
- `enable-in-band-mng:True|False` Enable the In-band management interface. This allows the user to make modifications to the switch via the ethernet interface. See section 4.10 in the datasheet. By default, this is deactivated.
- `enable-on-reset:True|False` Enable network switching to start on reset of the network switch. By default this is activated.
- `enable-gigabit:True|False` Configures Port 3 MAC as Gigabit. If deasserted, then the MAC operates at 100Mbps speed. Default is activated.
- `rmii-clock-mode:True|False` Configures the RMII directional flow of the clock. In normal mode, the RMII interface receives a clock from the PHY. In clock mode, the RMII interface generates a clock to drive the MAC on a MCU. By default, the clock mode is active. This flag only affects RMII mode.
