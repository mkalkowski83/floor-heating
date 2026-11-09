# Schemat — moduł góra

Eksport schematu KiCad (arkusz A3, jeden moduł przy rozdzielaczu):

[floor-heating-manifold.pdf](floor-heating-manifold.pdf)

Źródło: [`kicad/floor-heating-manifold/floor-heating-manifold.kicad_pro`](kicad/floor-heating-manifold/floor-heating-manifold.kicad_pro). Ten sam układ dotyczy modułu dolnego.

## Legenda

| Oznaczenie | Element | Rola |
|---|---|---|
| J1 | Zacisk 230 V | Wejście sieci L / N |
| F1 | Bezpiecznik T1A | Tylko zasilacz HLK (PS1). Nie zasila siłowników |
| F2 | Bezpiecznik T2A | Faza obciążeń `L_OBC`: siłowniki zaworów i pompa |
| PS1 | HLK-10M05 | Zasilacz AC/DC, 5 V / 2 A. `+Vo` → `+5V`, `-Vo` → `GND` |
| U1 | ESP32 DevKit V1 | Sterownik. VIN z `+5V` HLK. Logika 3,3 V |
| U2 | Moduł 8× przekaźnik 5 V | K1–K8, zworka JD-VCC–VCC zdjęta |
| U3 | Moduł 2× przekaźnik 5 V | K9 pompa i K10 RT, zworka zdjęta |
| U4 | DS18B20 | Temperatura zasilania rozdzielacza, montaż przylgowy |
| U5 | DS18B20 | Temperatura powrotu rozdzielacza, montaż przylgowy |
| R1 | 4,7 kΩ | Pull-up magistrali 1-Wire do 3,3 V |
| J2–J9 | Zacisk 2-pin | ZAW1–ZAW8. Pin 1 (lewa) = faza z NO, pin 2 (prawa) = kabel do listwy N |
| J10 | Zacisk 2-pin | Pompa, rezerwa. Pin 1 = faza z NO, pin 2 = kabel do listwy N |
| J11 | Zacisk 2-pin | Suchy styk RT kotła. Pin 1 = NO, pin 2 = COM. Bez 230 V |
| J12–J19 | Zacisk śrubowy listwy N | Po jednym na zawór. Pin 1 = kabel do pinu 2 ZAW1–ZAW8, pin 2 = wspólna linia `N_OBC` |
| J20 | Zacisk śrubowy listwy N | Pin 1 = kabel do pinu 2 pompy, pin 2 = ta sama linia `N_OBC` |
| J21–J28 | Zacisk śrubowy listwy L | Po jednym na kanał K1–K8. Pin 1 = kabel na COM przekaźnika, pin 2 = wspólna linia `L_OBC` |
| J29 | Zacisk śrubowy listwy L | Pin 1 = kabel na COM pompy (K9), pin 2 = ta sama linia `L_OBC` |

## Sieci

| Nazwa | Znaczenie |
|---|---|
| `L` / `N` | Sieć na J1 |
| `AC/L` / `AC/N` | Wejście HLK za F1 |
| `L_OBC` | Faza za F2. Na listwie J21–J29 pin 2 jest zwarty w jedną linię, a z pinu 1 idzie osobny kabel na COM kanału K1–K9. Styk NO zostaje na pinie 1 zaworu albo pompy. RT (K10) nie jest na tej linii |
| `N_OBC` | Neutral obciążeń. Przy J1 schodzi na listwę J12–J20: pin 2 tych zacisków jest zwarty w jedną linię, a z pinu 1 idzie osobny kabel do zaworu albo pompy. Nie łączyć z `GND` |
| `+5V` | Wyjście HLK: VIN ESP i JD-VCC cewek |
| `+3V3` | Z ESP: VCC optoizolacji i pull-up DS18B20. Na GPIO nie podawać 5 V |
| `GND` | Masa logiki |

## GPIO

Aktywne stanem niskim: GPIO = 0 załącza przekaźnik.

| GPIO | Wyjście |
|---|---|
| 16, 17, 18, 19, 21, 22, 23, 25 | K1–K8, zawory 230 V NC |
| 26 | K9, pompa (w fazie 1 niepodłączona) |
| 27 | K10, suchy styk NO–COM na RT (Vaillant VC 15CS/1-5) |
| 4 | 1-Wire, U4 i U5 |

K1–K8: COM = faza, NO = siłownik, drugi przewód siłownika na `N_OBC`. Siłownik zasilony = zawór otwarty. K10 bez 230 V i bez zacisku Burner off.

## Notatki ze schematu

Notatki i komentarze przeniesione z arkusza KiCad (bez zmian treści).

**Tytuł arkusza:**

- ESP32 + HLK-10M05 + przekaźniki 8+2 + 2x DS18B20
- Zworka JD-VCC zdjeta. VCC logiki = 3,3 V. Cewki = 5 V.

**Przy przekaźnikach i RT:**

```
K10 RT: suchy styk.
Bez 230 V. Nie Burner off.
```

```
NC niepodłączone.
K1–K9: NO = faza załączana, drugi zacisk = N.
```

**Opis modułu:**

```
Moduł góra. Ten sam układ na moduł dół.
Zworka JD-VCC–VCC zdjęta na U2 i U3.
+5V (HLK +Vo) → VIN ESP i JD-VCC cewek.
VCC przy IN = +3V3 z ESP. Na GPIO nie podawać 5V.
Przy programowaniu nie łączyć USB z +5V naraz.
HLK w zamkniętej obudowie. F1 tylko dla HLK, F2 dla siłowników.
GND logiki nie łączy się z N sieci.

Aktywne LOW: GPIO = 0 załącza przekaźnik.
K1–K8 zawór 230V NC. COM = L, NO = siłownik, drugi przewód na N.
Siłownik zasilony = zawór otwarty.
K9 pompa, rezerwa, w fazie 1 niepodłączona.
K10 suchy styk NO–COM na zacisk RT (VC 15CS/1-5).
Nie podawać 230V. Nie używać Burner off
(zarezerwowany pod Maximalthermostat podłogówki).
Failsafe firmware, brak HA/Wi-Fi: zawory i pompa ON, HD OFF.

2× DS18B20, 1-Wire GPIO4, 4,7 kΩ do 3,3 V.
Zasilanie i powrót rozdzielacza, montaż przylgowy.
```
