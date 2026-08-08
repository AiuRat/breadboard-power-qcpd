<p align="center">
  <a href="README.md">Русский</a> | <a href="README.en.md">English</a> | <b>Deutsch</b>
</p>

# Elektrische Berechnung & Verifizierung des -15 V Inverters

Hier wird die detaillierte Berechnung der externen Beschaltung des Schaltregler-ICs **MC34063AG** im invertierenden Betrieb dargestellt, zusammen mit den Ergebnissen der praktischen Tests mit einem digitalen Oszilloskop.

Der invertierende Betrieb ist erforderlich, um eine stabile negative Spannung von $-15\text{ V}$ zu erzeugen. Diese wird für die symmetrische Versorgung analoger Baugruppen (Operationsverstärker, Komparatoren) auf einer lötfreien Steckplatine verwendet.

---

## 1. Schaltplan der Baugruppe

Der theoretische Schaltplan für den Anschluss des ICs im invertierenden Betrieb ist unten dargestellt:

![Schaltplan der MC34063-Beschaltung](/electronics/verification/images/mc34063-inverting-schematic.png)

### Ausgangsparameter für die Berechnung:
* Maximale Eingangsspannung: $U_{\text{in(max)}} = 20\text{ V}$ (entspricht dem maximalen USB-Power-Delivery-Profil)
* Nominale Eingangsspannung: $U_{\text{in}} = 5\text{ V}$
* Erforderliche Ausgangsspannung: $U_{\text{out}} = -15\text{ V}$
* Maximaler Ausgangsstrom: $I_{\text{out}} = 100\text{ mA}$
* Zulässige Ausgangswelligkeit (Spannungsrippel): $U_{\text{ripple(peak-to-peak)}} = 10\text{ mV}$

---

## 2. Theoretische Berechnung der externen Bauteile

### 2.1. Bestimmung der Zeitintervalle und Frequenzwahl
Die Betriebsfrequenz des internen Oszillators wird durch die Kapazität des Kondensators $C_1$ (zwischen Pin `TC` und Masse) festgelegt. Um eine kompakte Bauweise des Geräts zu gewährleisten und die Abmessungen der Drossel zu minimieren, wird eine hohe Schaltfrequenz gewählt.

Anhand des Diagramms aus dem Datenblatt werden bei einer Kondensatorkapazität von $C_1 = 100\text{ pF}$ folgende Zeitintervalle gewählt:
* Einschaltzeit des Ausgangsschalters: $t_{\text{on}} = 4\ \mu\text{s}$
* Ausschaltzeit des Ausgangsschalters: $t_{\text{off}} = 1\ \mu\text{s}$

<p align="center">
  <img src="/electronics/verification/images/mc34063-timing-vs-capacitor.png" width="380" alt="Frequenz-Kapazitäts-Diagramm">
</p>
  
### 2.2. Berechnung des Sättigungsstroms der Drossel ($I_{\text{sat}}$)
Der Sättigungsstrom der Drossel $L_1$ wird nach folgender Formel berechnet:

$$I_{\text{sat}} = 2 \cdot I_{\text{out(max)}} \cdot \frac{t_{\text{on}} + t_{\text{off}}}{t_{\text{off}}}$$

Einsetzen der Werte:

$$I_{\text{sat}} = 2 \cdot 0{,}1 \cdot \frac{4 \cdot 10^{-6} + 1 \cdot 10^{-6}}{1 \cdot 10^{-6}} = 1\text{ A}$$

### 2.3. Berechnung der minimalen Induktivität der Drossel ($L_{\text{min}}$)
Die minimale Induktivität wird für den ungünstigsten Fall berechnet (bei maximaler Eingangsspannung):

$$L_{\text{min}} = \left( \frac{U_{\text{in(max)}} - U_{\text{out(sat)}}}{I_{\text{sat}}} \right) \cdot t_{\text{on(max)}}$$

Wobei $U_{\text{out(sat)}}$ die Sättigungsspannung des internen Ausgangstransistors des ICs bei einem Strom von $1\text{ A}$ ist. Laut Referenzdiagramm im Datenblatt beträgt dieser Wert $1{,}65\text{ V}$:

<p align="center">
  <img src="/electronics/verification/images/mc34063-saturation-vs-current.png" width="380" alt="Sättigungsspannungs-Diagramm">
</p>
  
Berechnung des Induktivitätswerts:

$$L_{\text{min}} = \left( \frac{20 - 1{,}65}{1} \right) \cdot 4 \cdot 10^{-6} \approx 73\ \mu\text{H}\ (73\ \mu\text{H})$$

*Bauteilauswahl:* Zur Erhöhung der Zuverlässigkeit, Reduzierung der Welligkeit und Gewährleistung des kontinuierlichen Betriebs wurde eine Standarddrossel mit einem Nennwert von **$220\ \mu\text{H}$** gewählt (Modell *SPQ105-560M* / *B82464G4224M*).

### 2.4. Berechnung des Strombegrenzungswiderstands ($R_3$)
Der Widerstand $R_3$ (verbunden mit Pin `IPK`) dient dem Überstromschutz des ICs:

$$R_3 = \frac{0{,}3}{I_{\text{sat}}} = \frac{0{,}3}{1} = 0{,}3\ \Omega\ (0{,}3\ \Omega)$$

*Auswahl des Nennwerts:* Es wurde ein niederohmiger Standardwiderstand mit **$0{,}22\ \Omega$** (oder $0{,}3\ \Omega$) gewählt.

### 2.5. Berechnung des Glättungskondensators ($C_3$)
Die Kapazität des Ausgangs-Glättungskondensators $C_3$ (in den Originalformeln der Abschlussarbeit als $C_1$ bezeichnet) wird wie folgt berechnet:

$$C_3 = \frac{I_{\text{out}} \cdot t_{\text{on}}}{U_{\text{ripple(peak-to-peak)}}} = \frac{0{,}1 \cdot 4 \cdot 10^{-6}}{10 \cdot 10^{-3}} = 40\ \mu\text{F}\ (40\ \mu\text{F})$$

*Bauteilauswahl:* Aus der Standardreihe E12 wurde ein Tantal-Kondensator mit einer Kapazität von **$47\ \mu\text{F}$** (Typ CASE-D) gewählt.

### 2.6. Theoretische Berechnung des Feedback-Teilers ($R_1$, $R_4$)
Zur Stabilisierung der Ausgangsspannung auf $-15\text{ V}$ wird der interne Komparator mit einer Referenzspannung von $1{,}25\text{ V}$ verwendet. Die theoretische Berechnung bei einem Teilerstrom von $I_{\text{div}} = 1\text{ mA}$ ergibt folgende Formeln:

$$R_1 = \frac{|U_{\text{out}}| - 1{,}25}{I_{\text{div}}} = \frac{15 - 1{,}25}{0{,}001} = 13750\ \Omega\ (13{,}75\text{ k}\Omega)$$

$$R_4 = \frac{1{,}25}{I_{\text{div}}} = \frac{1{,}25}{0{,}001} = 1250\ \Omega\ (1{,}25\text{ k}\Omega)$$

*Theoretische Auswahl aus der Reihe E24:* $R_1 = 13\text{ k}\Omega$, $R_4 = 1{,}2\text{ k}\Omega$.

---

## 3. Praktische Inbetriebnahme und Abgleich des Prototyps (Wichtig!)

Bei der Montage und dem ersten Einschalten des physischen Prototyps stellte sich heraus, dass die theoretischen Rückkopplungs- und Steuerkreiswerte aufgrund realer Spannungsabfälle an den Bauteilen und parasitärer Parameter angepasst werden müssen. 

### Änderungen in der finalen Version (v5):
1. **MC34063 Feedback-Teiler:** Um einen präzisen und stabilen Pegel von $-15\text{ V}$ auf der physischen Platine zu erhalten, wurde experimentell ein Teiler aus den Widerständen **$R_4 = 910\ \Omega$** und **$R_1 = 10\text{ k}\Omega$** (mit einem nachgeschalteten Trimmelement $R_5 = 750\ \Omega$) eingebaut. Dies kompensierte den Spannungsabfall an der Schottky-Gleichrichterdiode $VD_1$ und sicherte eine stabile Erzeugung des negativen Zweigs.
2. **CH224K Pull-up-Widerstände:** In den Konfigurationsschaltungen `CFG1–CFG3` des Schnelllade-Triggers wurden die Widerstandswerte von **$1\text{ k}\Omega$ auf $10\text{ k}\Omega$** geändert. Ein zu geringer Wert von $1\text{ k}\Omega$ hinderte das IC daran, die Logikpegel korrekt umzuschalten, um das gewünschte Spannungsprofil vom Netzteil anzufordern.

---

## 4. Ergebnisse der physischen Messungen (Verifizierung)

Zur Bestätigung der Stabilität der Schaltung wurde eine oszilloskopische Aufzeichnung der Signale an Schlüsselpunkten des Geräts durchgeführt.

### 4.1. Signalform am frequenzbestimmenden Kondensator $C_1$
Die tatsächliche Betriebsfrequenz des internen Generators betrug **$97{,}5\text{ kHz}$** (bei einem berechneten Maximum von $100\text{ kHz}$ für dieses IC). Die Spannungsform ist klassisch sägezahnförmig ohne Verzerrungen, was die Stabilität des internen Oszillators bestätigt.

<p align="center">
  <img src="/electronics/verification/images/mc34063-c1-waveform.png" width="600" alt="Oszillogramm C1">
</p>

### 4.2. Signalform an der Drossel $L_1$
Die Messung am Schaltknoten (Pin `SWE` / Anode der Diode `VD1`) bestätigt den Betrieb des Wandlers im berechneten Impulsmodus. Die Einschaltzeit beträgt $4\ \mu\text{s}$, die Ausschaltzeit $1\ \mu\text{s}$, was vollständig mit den berechneten Zeitparametern übereinstimmt. Die auftretenden hochfrequenten Schwingungen (Ringing) beim Schalten liegen innerhalb der zulässigen Grenzen für die ausgewählte Schottky-Diode $15MQ040N$.

<p align="center">
  <img src="/electronics/verification/images/mc34063-l1-waveform.png" width="600" alt="Oszillogramm L1">
</p>

---

## 5. Vergleich der berechneten und experimentellen Daten

| Parameter | Berechneter Wert | Tatsächlich (Prototyp) | Verifizierungsstatus |
| :--- | :---: | :---: | :--- |
| **Ausgangsspannung ($U_{\text{out}}$)** | $-14{,}57\text{ V}$ | $-15{,}02\text{ V}$ | **Bestanden** (Genauigkeit $\approx 3\%$) |
| **Betriebsfrequenz ($f$)** | $100\text{ kHz}$ (max) | $97{,}5\text{ kHz}$ | **Bestanden** (optimal für MC34063) |
| **Einschaltzeit ($t_{\text{on}}$)** | $4{,}0\ \mu\text{s}$ | $4{,}0\ \mu\text{s}$ | **Bestanden** (exakte Übereinstimmung) |
| **Ausschaltzeit ($t_{\text{off}}$)** | $1{,}0\ \mu\text{s}$ | $1{,}0\ \mu\text{s}$ | **Bestanden** (exakte Übereinstimmung) |
| **Welligkeitsamplitude (peak-to-peak)** | $< 100\text{ mV}$ | $\approx 85\text{ mV}$ | **Bestanden** (mit Reserve für Steckplatinen) |
