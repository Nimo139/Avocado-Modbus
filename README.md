# Avocado-Modbus
This repo provides insights from the Modbus registers of the Priwatts Avocado Orbit M.

> [!NOTE]
> Disclaimer: This library is not affiliated with Priwatt. It is an independent project developed to provide tools for interacting with Priwatt Avocado batteries
> featuring integrated WiFi. Any trademarks or product names mentioned are the property of their respective owners.
> This document is **not an official manual or documentation**.  
> All information provided here is based on observations and testing and may be incomplete or inaccurate.
>
> ⚠️ **Use at your own risk**:  
> - Writing to registers or changing parameters may affect system behavior.  
> - Incorrect settings could lead to malfunction or permanent damage to the device.  
> - The author assumes no liability for any damage, data loss, or other consequences resulting from the use of this document.  


# Modbus Register Reverse Engineered Documentation

This document provides an overview of the Modbus registers starting at address **48010**. The registers are structured in steps of **10** and represent configuration and status values for system operation. The slots for 48000-48009 are unidentified, after which the created schedules follow.

---

## Register Map

### Base Address: `48010`

| Offset | Register Address | Description | Value Range / Notes |
|--------|-----------------|-------------|----------------------|
| 0 | 48010 | **Active** | `0 = inactive`, `1 = active` |
| 1 | 48011 | **Start Time** | Encoded as `256 * hours + minutes` |
| 2 | 48012 | **End Time** | Encoded as `256 * hours + minutes` |
| 3 | 48013 | **Operation Mode** | `1 = Self-consumption`<br>`2 = Feed-in priority`<br>`6 = Forced charging`<br>`7 = Forced discharging` |
| 4 | 48014 | Unknown | Possible values: `21776`, `21780`, `22029` (exact meaning under review, related to feed-in priority?) |
| 5 | 48015 | **(FDSOC) Forced SOC** | `0–100 %` |
| 6 | 48016 | **(FDPwr) Forced Power** | `0–1200` (Watt) |
| 7 | 48017 | Unknown | — |
| 8 | 48018 | Unknown | — |
| 9 | 48019 | Unknown | — |

---

## Encoding Notes

- **Time Encoding (Registers 1 & 2):**  
  - Formula: `Value = 256 * hours + minutes`  
  - Example: `08:30 → 256*8 + 30 = 2078`

- **Operation Modes (Register 3):**  
  - Mode `1`: Optimize for self-consumption.  
  - Mode `2`: Prioritize feeding energy into the grid.  
  - Mode `6`: Force battery charging.  
  - Mode `7`: Force battery discharging.  

- **Register 4:**  
  - Observed values: `21776`, `21780`, `22029`  
  - Further testing required to confirm function.  

---

## Example Usage
Create a manual scheduler in the app
- To **activate schedule**: Write `1` to Register `48010`.  
- To **set schedule from 08:30 to 18:00**:  
  - Register `48011` = `2078`  
  - Register `48012` = `4608`  
- To **force charging with SOC 80% and power 1000**:  
  - Register `48013` = `6`  
  - Register `48015` = `80`  
  - Register `48016` = `1000`  

---

## Known Problems
> [!NOTE]
> If too much is read too quickly, the battery can get confused and return incorrect/shifted registers values.
> Until now, this could only be resolved by restarting the system.
  
