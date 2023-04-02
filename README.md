I reverse engineered the serial protocol of my standing table, these are my findings.

# Model

## Table frame

Assmann DIGITUS Electric height-adjustable stand/ sit desk frame, 90° angle-shaped (L-shaped)  
Product number: DA-90386  
EAN: 4016032456445

However, the controller is probably also used in other models.

## Controller

Manufacturer: Hangzhou Winner Intelligent Technology Co., Ltd  
Model: WN17CM3

# RJ45 Pinout

| Pin | Color | Function |
| - | - | - |
| 1 | | n.c. |
| 2 | blue | RX |
| 3 | black | GND |
| 4 | yellow | TX |
| 5 | red | 5V |
| 6 | | n.c. |
| 7 | | n.c. |
| 8 | | n.c. |

# UART Settings
Baud Rate: 19200  
Data Bits: 8  
Stop Bits: 0  
Parity: None

All data is sent as strings.

# Message Structure
```
:D71.10B;
```

| Char | Description |
| - | - |
| `:` | Message Start |
| `D71.1` | Command |
| `0B` | Checksum as Hex String |
| `;` | Message End |

## Checksum Calculation
Lower byte of the sum of data bytes  

```
D71.1 -> 0x44 + 0x37 + 0x31 + 0x2E + 0x31 -> 0x0B
```

## Commands

| Command | Description | Sent by | Example |
| - | - | - | - |
| `WN18H32.V2.00.1918.001` | Sent on boot | remote | `:WN18H32.V2.00.1918.001BF;` |
| `A` | Confirm last command | both | `:A41;` |
| `D[3-4 chars]` | Display `xxx` (comma lights up if contained in text) remote responds with `A` | controller | `:D71.10B;` |
| `D[3-4 chars],F600` | Like previous command but with flashing screen | controller | `:D73.0,F60014;` |
| `D` | Turn off display, remote responds with `A` | controller | `:D44;` |
| `K[key][action]` | Key event, controller responds with `A` | remote | `:KUAM2E;` |
| `R` | Request pressed key, remote responds with `K[key][action]` | controller | `:R52;` |

### Command `K[key][action]`

`[action]`: `M` = pressed, `B` = released

| `[key]` | Key | Example |
| - | - | - |
| `UA` | Up | `:KUAM2E;` - pressed |
| `DA` | Down | `:KDAB12;` - released |
| ` 1` | 1 | `:K 1ME9;` - pressed |
| ` 2` | 2 | `:K 2MEA;` - pressed |
| ` 3` | 3 | `:K 3MEB;` - pressed |
| ` 4` | 4 | `:K 4MEC;` - pressed |
| ` S` | M | `:K SM0B;` - pressed |
