# InkTime - Hardware Design

## Overview

Acest repository contine partea hardware pentru proiectul InkTime, realizat in Fusion 360 Electronics in jurul microcontroller-ului nRF52840.

Dispozitivul include:
- microcontroller nRF52840
- alimentare din baterie Li-Po
- incarcare prin USB-C
- fuel gauge pentru monitorizarea bateriei
- convertor DC/DC
- display E-Paper
- IMU
- driver haptic
- interfata SWD pentru programare si debug

Scopul proiectului a fost realizarea schemei electrice, a PCB-ului, a fisierelor de fabricatie si a documentatiei tehnice pentru review.

---

## Block Diagram

Diagrama bloc a proiectului este inclusa in folderul Images.

<img width="1024" height="624" alt="Captură de ecran din 2026-04-18 la 20 21 12" src="https://github.com/user-attachments/assets/95d13a8a-de4c-462c-b12a-ba429076ce94" />


Pe scurt, sistemul functioneaza astfel:
USB-C asigura alimentarea si incarcarea bateriei.
BQ25180 gestioneaza partea de charging si power-path.
RT6160 genereaza rail-ul necesar pentru logica sistemului.
MAX17048 masoara starea bateriei.
nRF52840 este controlerul principal.
Display-ul E-Paper este controlat de microcontroller prin semnale dedicate.
IMU-ul si DRV2605 sunt conectate la MCU prin I2C si GPIO-uri de control.
Antena si reteaua de matching asigura partea RF pentru nRF52840.

---

## Repository Structure

Repository-ul este organizat astfel:

Hardware contine fisierele de schema si PCB.  
Manufacturing contine fisierele Gerber, BOM si CPL.  
Mechanical este rezervat pentru fisiere mecanice, daca este cazul.  
Images contine capturi, randari si diagrama bloc.  
README.md contine documentatia proiectului.  
LICENSE contine licenta open-source.

---

## BOM

Componentele principale folosite in proiect sunt urmatoarele:

Microcontrollerul principal este nRF52840. Acesta gestioneaza toate functiile dispozitivului, inclusiv controlul display-ului, comunicatia cu perifericele, debug-ul si partea RF. Componenta poate fi cautata pe JLCPCB dupa “nRF52840”, iar datasheet-ul oficial este disponibil pe site-ul Nordic Semiconductor.

Circuitul de incarcare si power-path este BQ25180YBGR. Acesta gestioneaza incarcarea bateriei Li-Po si distributia alimentarii in sistem. Componenta poate fi cautata pe JLCPCB dupa “BQ25180”, iar datasheet-ul este disponibil pe site-ul Texas Instruments.

Convertorul DC/DC este RT6160AWSC. Acesta genereaza rail-ul principal de alimentare pentru logica placii. Componenta poate fi cautata pe JLCPCB dupa “RT6160”, iar datasheet-ul este disponibil pe site-ul Richtek.

Fuel gauge-ul este MAX17048G+T10. Acesta monitorizeaza starea bateriei si furnizeaza informatii utile pentru firmware. Componenta poate fi cautata pe JLCPCB dupa “MAX17048”, iar datasheet-ul este disponibil pe site-ul Analog Devices.

IMU-ul folosit este BMA423. Acesta este conectat la microcontroller prin I2C si este folosit pentru detectia miscarii. Componenta poate fi cautata pe JLCPCB dupa “BMA423”, iar datasheet-ul este disponibil pe site-ul Bosch Sensortec.

Driverul haptic este DRV2605YFZR. Acesta controleaza actuatorul de vibratie si este comandat de microcontroller. Componenta poate fi cautata pe JLCPCB dupa “DRV2605”, iar datasheet-ul este disponibil pe site-ul Texas Instruments.

Conectorul USB-C este KH-TYPE-C-16P. Este folosit pentru alimentare si conectivitate USB. Componenta poate fi cautata pe JLCPCB dupa “KH-TYPE-C-16P”, iar datasheet-ul este disponibil pe site-ul furnizorului.

Protectia ESD pentru USB este USBLC6-2SC6Y. Aceasta protejeaza liniile USB impotriva descarcarilor electrostatice. Componenta poate fi cautata pe JLCPCB dupa “USBLC6-2SC6Y”, iar datasheet-ul este disponibil pe site-ul STMicroelectronics.

Conectorul display-ului E-Paper este 503480-2400. Acesta este conectorul principal dintre placa si display. Componenta poate fi cautata pe JLCPCB dupa “503480-2400”, iar datasheet-ul este disponibil pe site-ul Molex.

Antena RF este 2450AT18B100E. Este folosita pentru partea BLE a nRF52840. Componenta poate fi cautata pe JLCPCB dupa “2450AT18B100E”, iar datasheet-ul este disponibil pe site-ul Johanson Technology.

Conectorul SWD este TC2030-IDC. Acesta este folosit pentru programare si debug. Datasheet-ul si documentatia sunt disponibile pe site-ul Tag-Connect.

Cristalul de 32 MHz este folosit ca clock principal RF pentru nRF52840.  
Cristalul de 32.768 kHz este folosit pentru RTC si modul low-power.

In plus, proiectul foloseste:
- rezistente 0201 si 0402 pentru pull-up, configurare, feedback si bias
- condensatoare 0201, 0402 si 0603 pentru decuplare, filtrare, matching RF si charge-pump EPD
- inductoare pentru matching RF si convertorul DC/DC
- diode Schottky si MOSFET-uri in zona de drive a display-ului E-Paper

---

## Hardware Functionality

### Power subsystem

Alimentarea dispozitivului este facuta dintr-o baterie Li-Po, care poate fi incarcata prin USB-C.

Blocurile principale implicate sunt:
- J4, conectorul USB-C
- D3, protectia ESD pentru USB
- IC1, BQ25180
- U3, MAX17048
- IC9, RT6160

VBUS intra prin conectorul USB-C. Liniile USB sunt protejate prin D3. BQ25180 gestioneaza partea de incarcare a bateriei si power-path-ul. MAX17048 monitorizeaza tensiunea si starea bateriei. RT6160 genereaza tensiunea de lucru pentru sistem, adica 3V3.

In aceasta zona sunt importante traseele de alimentare mai late, condensatoarele de decuplare apropiate de pini si mentinerea unei alimentari curate pentru partea digitala si RF.

### MCU si RF

Microcontrollerul folosit este nRF52840. Acesta controleaza display-ul E-Paper, citeste IMU-ul, comanda driverul haptic, comunica cu fuel gauge-ul si charger-ul, permite debug prin SWD si gestioneaza partea RF pentru BLE.

Blocul RF include:
- antena 2450AT18B100E
- reteaua de matching cu inductoare si condensatoare
- cristalul de 32 MHz
- cristalul de 32.768 kHz

Interfetele principale folosite de MCU sunt:
- SPI pentru E-Paper
- I2C pentru IMU, fuel gauge, charger si haptic driver
- SWD pentru programare si debug
- GPIO-uri pentru butoane, reset, busy si interrupt-uri

### E-Paper

Display-ul E-Paper este conectat prin conectorul J1 si printr-un bloc dedicat de drive.

Semnalele principale sunt:
- EPD_CS
- EPD_RST
- EPD_DC
- EPD_BUSY
- MOSI
- SCK

Pe foaia a doua a schemei apare si blocul de drive pentru display, care contine inductoare, diode, MOSFET-uri, rezistente si condensatoare dedicate. Acest bloc genereaza si comuta rail-urile necesare pentru functionarea display-ului.

### IMU

IMU-ul folosit este BMA423.

Acesta este conectat la:
- 3V3
- GND
- SDA
- SCL
- un pin de interrupt catre MCU

Rolul lui este detectia miscarii si suportul pentru functii low-power sau evenimente bazate pe miscare.

### Haptic driver

Driverul haptic este DRV2605.

Acesta este conectat la:
- alimentare
- GND
- magistrala I2C
- un semnal de control / enable
- iesirea catre actuatorul haptic

Rolul lui este generarea de feedback prin vibratie.

### Buttons

Placa include trei butoane:
- SW_UP
- SW_ENT
- SW_DN

Fiecare buton are rezistenta de pull-up si condensator de filtrare/debouncing. Butoanele sunt conectate la GPIO-uri dedicate ale microcontrollerului.

### Debug si test

Pentru programare, testare si debugging sunt disponibile:
- J2, conectorul SWD
- test pads pentru SWDIO, SWDCLK, RESET, SWO, 3V3, GND, SDA, SCL, VREG, VBAT si iesirile driverului haptic

Acest lucru ajuta la bring-up si depanare dupa asamblarea placii.

---

## nRF52840 Pin Usage

Semnalele importante conectate la nRF52840 sunt urmatoarele:

Pinii XL1 si XL2 sunt folositi pentru cristalul de 32.768 kHz, necesar pentru RTC si modurile low-power.

Pinii XC1 si XC2 sunt folositi pentru cristalul de 32 MHz, necesar pentru functionarea precisa a blocului RF.

Pinul ANT este conectat la reteaua de matching si apoi la antena, pentru comunicatia BLE.

Semnalele SWDIO si SWDCLK sunt scoase la conectorul J2 si la test pads, pentru programare si debug.

Semnalul RESET este scos la headerul de debug si la test pad pentru reset extern.

Semnalele EPD_CS, EPD_RST, EPD_DC si EPD_BUSY sunt folosite pentru controlul display-ului E-Paper.

Semnalele SCK si MOSI sunt folosite pentru comunicatia SPI cu display-ul.

Semnalele SDA si SCL sunt folosite ca magistrala I2C comuna pentru IMU, fuel gauge, charger si haptic driver.

Semnalul IMU_INT este folosit pentru intreruperi de la BMA423.

Semnalul PMIC_INT este folosit pentru semnalizarea evenimentelor charger/PMIC.

Semnalul HAPTIC_EN este folosit pentru controlul driverului haptic.

Semnalele SW_UP, SW_ENT si SW_DN sunt folosite pentru interfata cu utilizatorul.

Semnalul ALERT este folosit pentru evenimente de la fuel gauge.

Semnalele de enable pentru partea EPD sunt folosite pentru controlul alimentarii display-ului.

Pinii au fost alesi astfel incat:
- pinii dedicati de debug sa ramana disponibili pentru SWD
- pinii dedicati cristalelor sa fie folositi conform functiei lor
- semnalele SPI sa fie grupate logic
- I2C-ul sa fie folosit ca magistrala comuna pentru periferice low-speed
- GPIO-urile simple sa fie folosite pentru butoane si interrupt-uri

---

## Communication Interfaces

Interfetele folosite in proiect sunt:
- SPI intre nRF52840 si display-ul E-Paper
- I2C intre nRF52840 si BMA423, MAX17048, BQ25180 si DRV2605
- SWD intre nRF52840 si headerul J2
- GPIO pentru butoane si semnale de control

---

## Energy and Power Considerations

Consumul exact depinde de firmware si de duty-cycle, dar hardware-ul este proiectat pentru un sistem portabil pe baterie.

Sursele principale de consum sunt:
- nRF52840
- refresh-ul display-ului E-Paper
- driverul haptic in timpul vibratiei
- fuel gauge-ul si PMIC-ul
- IMU-ul in mod activ sau low-power

Observatii importante:
- E-Paper consuma in principal in timpul refresh-ului, nu in afisare statica
- IMU-ul poate functiona in mod low-power
- fuel gauge-ul are consum mic, dar permanent
- BLE si activitatea MCU influenteaza puternic autonomia

Pentru o baterie de capacitate C_bat in mAh si un curent mediu estimat I_avg in mA, autonomia teoretica se poate aproxima cu relatia:

T = C_bat / I_avg

Exemplu:
Pentru o baterie de 150 mAh si un consum mediu de 3 mA, autonomia estimata este de aproximativ 50 de ore.

Aceasta este doar o estimare orientativa. Consumul real depinde de frecventa refresh-ului E-Paper, activitatea BLE, utilizarea vibratorului si strategia de citire a senzorilor.

---

## PCB Design Notes

PCB-ul a fost realizat pe doua straturi.

Deciziile principale de layout au fost:
- MCU-ul si zona RF au fost plasate in partea dreapta-sus
- conectorul E-Paper si blocul sau de drive au fost grupate in partea stanga
- butoanele au fost plasate pe marginea inferioara
- USB-C este pe muchia superioara
- test pads-urile au fost plasate in zone accesibile pentru debugging

Observatii:
- antena este tinuta la marginea placii
- matching network-ul este plasat intre MCU si antena
- decuplarile sunt distribuite in jurul blocurilor active
- PCB-ul este in stadiul livrabil pentru review si verificare

---

## Design Log

Problemele principale intalnite au fost:
- organizarea componentelor pe o placa mica
- densitatea mare in jurul nRF52840
- rutarea in zona RF si in zona de power
- pozitionarea test pads-urilor fara a bloca rutarea

Modificari facute dupa review:
- componentele au fost mutate in interiorul conturului placii
- placement-ul a fost reorganizat pe blocuri functionale
- au fost generate fisierele de fabricatie: Gerber, BOM si CPL
- documentatia a fost completata cu BOM si descriere hardware

---

## Manufacturing Outputs

Repository-ul include <img width="1024" height="624" alt="Captură de ecran din 2026-04-18 la 20 21 12" src="https://github.com/user-attachments/assets/b6b02721-9697-4aae-825f-35f4814cf29d" />
:
- gerbers.zip
- bom.csv
- cpl.csv

Aceste fisiere permit fabricarea PCB-ului, asamblarea automata si verificarea componentelor si footprint-urilor.

---

