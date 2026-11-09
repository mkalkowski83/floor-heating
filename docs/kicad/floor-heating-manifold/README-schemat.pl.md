# Schemat — moduł góra

Eksport schematu KiCad (arkusz A3, jeden moduł przy rozdzielaczu):

[floor-heating-manifold.pdf](floor-heating-manifold.pdf)

Źródło: [`floor-heating-manifold.kicad_pro`](floor-heating-manifold.kicad_pro). Ten sam układ dotyczy modułu dolnego.

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
| J2–J9 | Zacisk 2-pin | ZAW1–ZAW8. Pin 1 (lewa) = faza z NO, pin 2 (prawa) = `N_OBC` |
| J10 | Zacisk 2-pin | Pompa, rezerwa. Pin 1 = faza z NO, pin 2 = `N_OBC` |
| J11 | Zacisk 2-pin | Suchy styk RT kotła. Pin 1 = NO, pin 2 = COM. Bez 230 V |

## Sieci

| Nazwa | Znaczenie |
|---|---|
| `L` / `N` | Sieć na J1 |
| `AC/L` / `AC/N` | Wejście HLK za F1 |
| `L_OBC` | Faza za F2, na COM przekaźników K1–K9 |
| `N_OBC` | Neutral obciążeń, pin 2 zaworów i pompy. Nie łączyć z `GND` |
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
