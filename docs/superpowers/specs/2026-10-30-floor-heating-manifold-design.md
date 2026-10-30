# Floor heating manifold control — design

Date: 2026-10-30 · Revised: 2026-10-30 (temp., radio, Vaillant HD/pompa)  
Repo: `floor-heating` (new) · Prior art: ESPHome + HA (`tank-level`, `GageGateSens`), eBUS kotła  
BOM (faza 1): [`../../bom/floor-heating-manifold-phase1.xlsx`](../../bom/floor-heating-manifold-phase1.xlsx)  
Kocioł: Vaillant ecoTEC plus **VC 15CS/1-5** (15 kW, 0010024646) + systemregler eBUS + ebusd

## Goal

Sterowanie pętlami ogrzewania podłogowego: moduł ESP przy rozdzielaczach (faza 1: góra), gotowe regulatory pokojowe przez HA, drugi identyczny moduł (dół) później.

## Decisions

- **Architektura:** cienki aktor ESPHome + mózg w HA; dwa moduły (góra/dół), ten sam kontrakt wyjść
- **Regulatory (faza 1):** gotowe (Zigbee/Wi‑Fi itd.) → HA `climate` → `switch` zaworu; lokalny setpoint na termostacie przez HA
- **Ścieżka C (później):** nie blokować bezpośredniego linku pokój↔moduł; nie implementować teraz
- **Radio modułu:** **Wi‑Fi** na osobnej sieci IoT **bez WAN** (tylko do HA); Zigbee na module — nie
- **Siłowniki:** 230 V AC NC; moduł **8 kanałów** od razu; faza 1: 3–4 szt. testowo
- **Przekaźniki:** kupiony moduł **8+2** (8× zawór + 1× pompa + 1× HD); reszta DIY + schemat KiCad połączeń
- **Heat demand:** HA → **eBUS** (główna ścieżka; krzywa/nieobecność zostają na systemreglerze) **oraz** suchy styk NO z modułu jako **bramka CO** (zacisk RT / uzgodniony przy montażu — **nie** Burner off)
- **Burner off:** zarezerwowany pod Maximalthermostat podłogówki (osobno od HD)
- **Failsafe (ESP, brak HA/Wi‑Fi):** zawory ON, HD OFF (styk + eBUS wg HA gdy wróci); po montażu pompy — pompa ON w tym trybie
- **Pompa:** 1× wyjście na moduł jako **rezerwa**; faza 1 bez zakupu/montażu pompy i sprzęgła (osobna decyzja z hydraulikiem; docelowo wtórna ON przy ≥1 zaworze)
- **Temperatura:** 2× DS18B20 na **wspólne** zasilanie i powrót rozdzielacza (nie per pętla); montaż **przylgowy** (bazowo)
- **Opcja A (później):** 2× tuleja zanurzeniowa (np. G½″) + te same DS18B20; potwierdzić gwint; nie stracić spustu belki
- **Ochrona temp.:** HA (alerty, ΔT, ochrona dzienna → HD OFF, zawory bez zmian) + **twardy wyższy próg na ESP** → HD OFF
- **Firmware:** ESPHome; any-valve→pompa/HD w HA

## Scope / out of scope

**In:** moduł góra (ESPHome, 8+2, KiCad, failsafe, HD eBUS+styk, 2× DS18B20 przylgowo, sieć IoT Wi‑Fi); nazewnictwo pod „dół”

**Out (teraz):** moduł dół; montaż pompy/sprzęgła; Zigbee na module; studzienki (opcja w BOM); bezpośredni link regulator↔ESP; projekt uprawniony 230 V / hydraulika uprawniona

## Acceptance criteria

1. Z HA: każdy z 8 kanałów ON/OFF; 3–4 podłączone siłowniki reagują fizycznie
2. ≥1 zawór ON → styk HD zwarty **i** żądanie eBUS zgodne z polityką HA; wszystkie OFF → styk rozwarty + eBUS bez żądania stref
3. Odcięcie HA/Wi‑Fi → zawory ON, HD OFF; po powrocie steruje HA
4. Schemat KiCad: ESP ↔ 8+2 ↔ zaciski zaworów / pompy / HD + 1-Wire DS18B20; nota HD vs Burner off dla VC 15CS/1-5
5. YAML + encje HA kopiowalne 1:1 na moduł dół bez zmiany kontraktu wyjść
6. HA widzi temp. zasilania/powrotu i ΔT; alerty + ochrona HD OFF; ESP ma twardy próg HD OFF
