# KiCad — moduł ogrzewania podłogowego

Schemat połączeń modułu górnego: ESP32 DevKit V1, HLK-10M05, przekaźniki 8+2 i dwa DS18B20.

Legenda oznaczeń i PDF: [README-schemat.pl.md](README-schemat.pl.md).

## Otwarcie

`docs/kicad/floor-heating-manifold/floor-heating-manifold.kicad_pro`

Biblioteka: `symbols/floor-heat.kicad_sym`.

## Połączenia

+5 V z HLK-10M05 zasila pin 5V/VIN ESP oraz JD-VCC cewek obu płytek. Zworka JD-VCC–VCC jest zdjęta. Pin VCC przy wejściach IN dostaje 3,3 V z ESP, więc na GPIO nie ma 5 V.

Wejścia są aktywne stanem niskim. K1–K8 to siłowniki 230 V NC (COM = faza, NO = siłownik). K9 to rezerwa pompy. K10 to suchy styk NO na zacisk RT kotła Vaillant VC 15CS/1-5, bez 230 V i bez zacisku Burner off.

GPIO: 16, 17, 18, 19, 21, 22, 23, 25 zawory; 26 pompa; 27 HD; 4 magistrala 1-Wire.
