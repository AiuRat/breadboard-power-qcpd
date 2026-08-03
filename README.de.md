<p align="center">
  <a href="README.md">Русский</a> | <a href="README.en.md">English</a> | <b>Deutsch</b>
</p>

# Breadboard Power QC/PD

Kombiniertes Stromversorgungsmodul für lötfreie Steckplatinen (Breadboards) mit Unterstützung für die Schnellladeprotokolle **USB Power Delivery (PD)** und **Quick Charge (QC)**.

<p align="center">
  <img src="/production/images/assembled-pcb-top(pretty).jpg" width="850" alt="Breadboard Power QC/PD Assembled Board">
</p>

---

## 1. Projektbeschreibung

**Breadboard Power QC/PD** ist ein kompaktes, vielseitiges Gerät zur Lösung von Stromversorgungsproblemen bei Prototypen auf lötfreien Steckplatinen. Traditionelle Labornetzteile sind sperrig und erfordern manuelle Einstellungen, während billige chinesische Breadboard-Netzteilmodule auf starre Grenzen von $5\text{ V}$ und $3{,}3\text{ V}$ bei geringem Strom beschränkt sind.

Dieses Projekt kombiniert moderne Schnellladetechnologien über das **USB Type-C (QC/PD)**-Protokoll, die hohe Präzision von Linearreglern und die breiten Möglichkeiten von DC-DC-Schaltreglern. Das Modul wird direkt in die seitlichen Stromschienen der Steckplatine gesteckt und bietet dem Entwickler unabhängige Stromkanäle für digitale Logik, Mikrocontroller und analoge Schaltungen (symmetrische Spannungsversorgung von $\pm 15\text{ V}$ ).

---

## 2. Technische Hauptdaten

* **Eingangsschnittstelle:** USB Type-C (mit Unterstützung für PD 3.0 / QC 3.0)
* **Anschlussschnittstelle:** Zweireihige Stiftleisten mit einem Rastermaß von $2{,}54\text{ mm}$ (für Standard-Steckplatinenschienen)
* **Verfügbare Kanäle und Spannungen:**
  * Einstellbarer Hauptstromkanal (`VBUS`): **$5\text{ V}$ / $9\text{ V}$ / $12\text{ V}$ / $15\text{ V}$ / $20\text{ V}$** (wird durch den Trigger bestimmt)
  * Logik-Spannungskanal (`3.3Vout`): **$+3{,}3\text{ V}$** (rauscharmer LDO-Linearregler)
  * Negativer Spannungskanal (`-15out`): **$-15\text{ V}$** (invertierender Schaltregler)

---

## 3. Schaltungsentwurf und Gerätearchitektur

Vollständiger elektrischer Schaltplan des Moduls:

<p align="center">
  <img src="/docs/images/breadboard-power-qcpd-schematic.png" width="850" alt="Prinzipschaltbild des Breadboard Power QC/PD">
</p>

### Die Architektur des Moduls besteht aus drei Schlüsselkomponenten:
1. **Schnelllade-Trigger-Einheit:** Realisiert mit dem spezialisierten Chip **CH224K**. Er verhandelt mit der externen Stromquelle (Netzteil) über die `CC1/CC2`-Leitungen und stellt die gewünschte Spannung auf der `VBUS`-Schiene bereit. Die Konfiguration wird über den DIP-Schalter `SA2` eingestellt.
2. **LDO-Linearregler ($+3{,}3\text{ V}$):** Basiert auf dem Chip **LD1086DT33TR** (im TO-252-Gehäuse) mit Eingangs- und Ausgangsfilterkondensatoren von $10\text{ }\mu\text{F}$.
3. **Invertierender Schaltregler ($-15\text{ V}$):** Realisiert in invertierender Topologie auf dem PWM-Controller **MC34063AG-S08-R** ohne externen Transistor. Die Betriebsfrequenz des Oszillators wird durch die Kapazität $C_1 = 2{,}2\text{ nF}$ ($\approx 97{,}5\text{ kHz}$) bestimmt.

---

## 4. Konstruktion und 3D-Modellierung

Zum Schutz der Elektronik und für eine komfortable Nutzung wurde ein zweiteiliges Kunststoff-Schutzgehäuse in **KOMPAS-3D** entworfen (Verbindung der Teile über ein Nut-Feder-System):

<p align="center">
  <img src="/mechanical/images/breadboard-power-qcpd-assembly(1).png" width="650" alt="3D-Modell des Gehäuses - Ansicht 1">
</p>

*Die Gehäuseteile (Abdeckung und Bodenplatte) sind für die additive Fertigung mittels FDM-3D-Druck optimiert (die druckfertigen `.stl`-Dateien befinden sich im Ordner [`mechanical/enclosure/`](/mechanical/enclosure)).*

---

## 5. Labortests & Hardware-Verifizierung

Das Gerät wurde erfolgreich gelötet und getestet:

<p align="center">
  <img src="/production/images/assembled-pcb-test.jpg" width="600" alt="Physischer Test des bestückten Prototyps">
</p>

Während der Verifizierung mit einem digitalen Oszilloskop wurden die Betriebsstabilität des internen Oszillators des Wechselrichters und der kontinuierliche Betriebszustand der Leistungsinduktivität bestätigt:

<p align="center">
  <img src="/electronics/verification/images/mc34063-c1-waveform.png" width="380" alt="C1 Oszillogramm">
  &nbsp;&nbsp;&nbsp;&nbsp;
  <img src="/electronics/verification/images/mc34063-l1-waveform.png" width="380" alt="L1 Oszillogramm">
</p>

* **C1 Oszillogramm (links):** Bestätigt die Schaltfrequenz des Wandlers von $97{,}5\text{ kHz}$ (innerhalb des zulässigen Grenzwerts von $100\text{ kHz}$).
* **L1 Oszillogramm (rechts):** Bestätigt die richtige Wahl der Induktivität von $220\text{ }\mu\text{H}$, was einen stabilen kontinuierlichen Drosselstrom ohne Übergang in den lückenden Betrieb gewährleistet.

---

## 6. Konfigurationstabellen der Schalter

### 6.1. Auswahl der QC/PD-Eingangsspannung (Schalter `SA2`)

| `CFG1` | `CFG2` | `CFG3` | Ausgangsspannung `VBUS` |
| :---: | :---: | :---: | :---: |
| $1$ | $-$ | $-$ | **$5\text{ V}$** |
| $0$ | $0$ | $0$ | **$9\text{ V}$** |
| $0$ | $0$ | $1$ | **$12\text{ V}$** |
| $0$ | $1$ | $1$ | **$15\text{ V}$** |
| $0$ | $1$ | $0$ | **$20\text{ V}$** |

### 6.2. Umschalten der Steckplatinen-Stromschienen (Schalter `SA1`, `SA3`)

| `SA1`-Stellung | `SA3`-Stellung | Spannung an der linken Schiene `X2` | Spannung an der rechten Schiene `X3` |
| :---: | :---: | :---: | :---: |
| 1 | 1 | $+3{,}3\text{ В}$ | $-15\text{ В}$ |
| 0 | 1 | $+3{,}3\text{ В}$ | $+3{,}3\text{ В}$ |
| 0 | 0 | `VBUS` (vom Trigger) | `VBUS` (vom Trigger) |
| 1 | 0 | `VBUS` (vom Trigger) | $-15\text{ В}$ |

---

## 7. Projekt-Navigation

Das Repository hat eine klare modulare Struktur. Klicken Sie auf die unten stehenden Links, um zu den entsprechenden Abschnitten zu gelangen und die Quelldateien einzusehen:

* 📁 **[`/electronics`](/electronics)** — Hardware-Teil des Projekts. Enthält die Schaltplan- und Layoutdateien im EasyEDA-Format sowie den Unterordner **[`verification`](/electronics/verification)** mit detaillierten mathematischen Berechnungen zur Dimensionierung des Schaltreglers und Oszillogrammen der Tests.
* 📁 **[`/mechanical`](/mechanical)** — Mechanischer Teil und Gehäuse. Enthält die parametrischen Originalmodelle der Gehäuseteile in KOMPAS-3D (`.m3d`, `.a3d`) und fertige FDM-3D-Druckdateien (`.stl`).
* 📁 **[`/production`](/production)** — Dateien für die Fertigung und Bestellung. Enthält das Gerber-Dateien-Archiv für den Leiterplattenhersteller (`gerber.zip`), die Stückliste für den Bauteileeinkauf (`BOM.xlsx`) und PDF-Zeichnungen.
* 📁 **[`/docs`](/docs)** — Technische Dokumentation. Enthält eine ausführliche Beschreibung der physikalischen Funktionsweise der Schaltung, Schalter-Wahrheitstabellen und den Unterordner **[`datasheets`](/docs/datasheets)** mit Original-Datenblättern der verwendeten ICs.


## Lizenz

Copyright (c) 2026 Airat Yalaletdinov

Diese Quelle beschreibt Open Hardware (offene Hardware) und ist unter der CERN-OHL-P v2 lizenziert. 
Sie dürfen diese Quelle unter den Bedingungen der CERN-OHL-P v2 (https://ohwr.org/cern_ohl_p_v2.txt) 
weiterverbreiten, modifizieren und Produkte auf deren Grundlage herstellen.

Diese Quelle wird OHNE JEGLICHE AUSDRÜCKLICHE ODER STILLSCHWEIGENDE GEWÄHRLEISTUNG vertrieben, 
EINSCHLIESSLICH DER GEWÄHRLEISTUNG DER MARKTGÄNGIGKEIT, ZUFRIEDENSTELLENDEN QUALITÄT ODER EIGNUNG 
FÜR EINEN BESTIMMTEN ZWECK. Die geltenden Bedingungen entnehmen Sie bitte der CERN-OHL-P v2.
