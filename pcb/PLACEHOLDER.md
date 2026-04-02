# PCB — KiCad Blade Design

Carrier board for 8 T1C MAAU chips.

## Specs
- 8-layer PCB (JLCPCB compatible)
- 8× MAAU BGA footprints (compute die, 0.5mm pitch)
- 8× I/O die LGA socket footprints
- 32× LPDDR5X chips (128-bit wide-bus)
- Dual PCIe Gen4 x8 connectors + retimer
- 25GbE NIC connector
- M.2 NVMe slot
- Dual STM32H7 controllers
- Star power topology (per MAAU VRM)

## Files Needed
- [ ] `blade_v1.kicad_pcb` — Main PCB layout
- [ ] `blade_v1.kicad_sch` — Schematic
- [ ] `blade_v1.kicad_pro` — Project file
- [ ] `gerbers/` — Fabrication files for JLCPCB

## Tools
- KiCad 8 (free) — kicad.org
- Order from JLCPCB — jlcpcb.com (~$80–150 for 8-layer)
