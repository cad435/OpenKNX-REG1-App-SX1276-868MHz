# REG1-RP2040-SX1276-1x

LoRa-Funkerweiterung (Semtech **SX1276**) für den **OpenKNX RP2040 REG1**-Controller.

Das Add-on bindet ein **SX1276-Funkmodul** per **SPI** an den RP2040 an und führt die
Antenne auf eine **SMA-Buchse**. Damit lässt sich ein REG1-Gerät um LoRa-Funk
(z. B. 433 / 868 / 915 MHz) erweitern.

---

## Funktion

Der SX1276 wird vollständig über **SPI + drei Steuerleitungen** angesprochen:

- **SPI:** SCK, MOSI, MISO, NSS — gemappt auf die **Hardware-SPI1** des RP2040 (GPIO26–29)
- **RESET** (active low)
- **DIO0** — Interrupt (TxDone / RxDone), für den Betrieb zwingend
- **DIO1** — optionaler Interrupt (RxTimeout, LoRaWAN, RadioLib)

Versorgung **3,3 V** (SX1276 ist 3,3-V-only; RP2040 ebenfalls → keine Pegelwandler nötig).
Die Antenne läuft über eine **50-Ω-Leitung** zur SMA-Buchse.

---

## Anschluss J1 (1×10 Stiftleiste zum REG1-Controller / J107)

| Pin | Netz (Add-on) | RP2040 GPIO | SX1276 | Funktion |
|----:|---------------|-------------|--------|----------|
| 1   | VCC2          | – (Vcc2)    | –      | nicht genutzt |
| 2   | 3V3           | 3V3         | VCC    | 3,3 V Versorgung |
| 3   | GND_KNX       | GND         | GND    | Masse |
| 4   | SPI_CSN       | GPIO29      | NSS    | SPI1 Chip-Select |
| 5   | SPI_MISO      | GPIO28      | MISO   | SPI1 RX |
| 6   | SPI_MOSI      | GPIO27      | MOSI   | SPI1 TX |
| 7   | SPI_SCK       | GPIO26      | SCK    | SPI1 SCK |
| 8   | SX_DIO1       | GPIO18      | DIO1   | IRQ (optional) |
| 9   | SX_RST        | GPIO17      | RESET  | Reset (active low) |
| 10  | SX_DIO0       | GPIO16      | DIO0   | IRQ (TxDone/RxDone) |

Genutzt werden **3V3, GND, GPIO16/17/18 und GPIO26–29**; Pin 1 (Vcc2) bleibt unbelegt.

---

## Stromversorgung & TX-Hinweise

Der Funk wird aus der **3,3-V-Schiene der NanoBCU** versorgt. Beim Senden zieht der
SX1276 **bis ~120 mA für die gesamte Sendedauer** (LoRa „Time on Air": ~40 ms bei SF7
bis ~1,15 s bei SF12) — kein kurzer Spike, sondern Dauerstrom.

Empfehlungen für sicheren Betrieb:

- An der NanoBCU (NCN5130) den **2. DC-DC (DC2) auf 3,3 V** für den Funk nutzen und den
  **Bus-Strom hoch einstellen** (FANIN/R6 → 40 mA Buslast; in ETS entsprechend anmelden).
- **TX-Leistung ≤ +17 dBm (~90 mA)** wählen — bleibt unter der DC2-Überstromschwelle (~100 mA).
- Lokal am Modul **100 nF + 10–47 µF** Entkopplung; der eigentliche Burst-Puffer ist der
  VFILT-Stützkondensator der NanoBCU.

---

## Antennen-Routing

- **SMA-Buchse**, Antennenleitung als **50-Ω Grounded Coplanar Waveguide (CBCPW)**.
- 2-Lagen-FR4 (Dk ≈ 4,4 @ 1 GHz), durchgehende GND-Fläche auf Bottom.
- Trace **1,5 mm**, Gap zur Coplanar-GND **0,45 mm**, beidseitiges **Via-Stitching** zur
  Bottom-GND. Leitung kurz und gerade zum SMA-Innenleiter.

---

## Inhalt des Repositories

| Datei | Beschreibung |
|-------|--------------|
| `REG1-RP2040-SX1276-1x_V0.1-Schematic.PDF` | Schaltplan |
| `REG1-RP2040-SX1276-1x_V0.1_Assembly.PDF`  | Bestückungsplan (Top/Bottom, realistische Ansicht) |
| `BOM-REG1-RP2040-SX1276-1x(V0.1).pdf`      | Stückliste |
| `JLC_PCBA_BOM-REG1-RP2040-SX1276-1x(V0.1).xlsx` | BOM für JLCPCB-Bestückung |
| `JLC_PCBA_CPL-REG1-RP2040-SX1276-1x(V0.1).xlsx` | Pick-&-Place / CPL für JLCPCB |
| `REG1-RP2040-SX1276-1x_Gerber.zip`         | Gerber-Daten zur Fertigung |
| `REG1-RP2040-SX1276-1x(V0.1).step`         | 3D-Modell (STEP) |

---

## Fertigung

Vorbereitet für **JLCPCB** (2-Lagen-FR4, 1,6 mm): Gerber-ZIP für die Leiterplatte,
dazu `JLC_PCBA_BOM…` und `JLC_PCBA_CPL…` für die SMD-Bestückung. Die
Antennenleitung ist als 50-Ω-CBCPW ausgelegt (siehe „Antennen-Routing").

---

## Status

Version **V0.1** – in Entwicklung.

## Autor / Lizenz

Hardware-Design © **4R3Ngineering**.
Veröffentlicht als Open-Source-Hardware-Add-on für das [OpenKNX](https://github.com/OpenKNX)-Ökosystem.

Lizenziert unter der **CERN-OHL-S v2** (CERN Open Hardware Licence Version 2 – Strongly Reciprocal) – siehe [`LICENSE`](LICENSE).
