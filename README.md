# Application-Specific-Buck-Converter
A 120W application-specific 24V-to-12V synchronous buck converter with 98.5% efficiency
 
## Problem statement
 
A **wide input range** forces every component to be sized for the **worst case**
(ripple ∝ `Vin`, transition loss ∝ `Vin²`), which means oversized parts and more
loss. Fixing the input at **24 V** lets the design be tuned for **efficiency**, not
worst-case margin.
 
---
 
## Target
 
| `Vin` | `Vout` | `Iout` | `fsw` | `D` |
| --- | --- | --- | --- | --- |
| 24 V | 12 V | 10 A | 300 kHz | 0.5 |
 
---
 
## Why 300 kHz
 
Low `fsw` → less switching loss but a bigger inductor. High `fsw` → smaller parts.
There's no middle optimum, just a floor set by magnetics size. **300 kHz** keeps the
inductor a tidy 6.8 µH while holding switching loss low.
 
---
 
## Calculations
 
**Inductor**
```
L = Vout·(Vin − Vout) / (Vin·ΔI_L·fsw) = (12·12)/(24·3·300k) = 6.67 µH → 6.8 µH
```
→ `XGL1010-682` — 6.8 µH · DCR 6.2 mΩ · Isat 22 A · Irms 21.2 A
 
**Input cap**
```
Cin ≥ Iout·D(1−D)/(fsw·ΔVin) = 34.7 µF
```
→ 3× `C3225X7R1H226M250AC` — 22 µF · 50 V · ESR 1 mΩ (0.33 mΩ parallel)
 
**Output cap**
```
Cout ≥ ΔIstep²·L/(2·(Vin−Vout)·ΔVdroop) = 71 µF
```
→ 3× `C5750X7R1E476M230KB` — 47 µF · 25 V · ESR 1.5 mΩ (0.5 mΩ parallel)
 
**MOSFETs**
```
P_gate = 2·Qg·VINTVCC·fsw = 2·49nC·5V·300k = 150 mW
```
→ 2× `BSC010N04LS6` — 40 V · Rds 1 mΩ · Qg 49 nC · Coss 1900 pF
 
**Controller**  

→ `LTC3855` — 4.5–38 V in · 0.6–12.5 V out · DCR or RSENSE · 250–770 kHz
 
---
 
## Efficiency
 
```
P_loss = I²·DCR + P_extvcc + P_sw + P_coss + P_gate + I²·Rds
```
 
| Loss | Formula | mW |
| --- | --- | --- |
| Inductor DCR | `I²·DCR` | 620 |
| EXTVCC dropper | `(Vin−5)·I_bias` | 315 |
| Switching | `½·Vin·I·(tr+tf)·fsw` | 290 |
| Coss | `½·Coss·Vin²·fsw` | 165 |
| Gate drive | `2·Qg·VINTVCC·fsw` | 150 |
| Conduction | `I²·Rds` | 130 |
 
```
η = Pout/(Pout + P_loss) = 120/(120 + 1.77) ≈ 98.5 %
```

## Schematic
<img width="541" height="381" alt="image" src="https://github.com/user-attachments/assets/4366d3f8-02e5-4919-b7cf-2b4373c88228" />

## Board
<img width="476" height="185" alt="image" src="https://github.com/user-attachments/assets/05e1604e-68d2-487f-a719-949b01d266cb" />
<img width="422" height="185" alt="image" src="https://github.com/user-attachments/assets/2d87a277-1e71-43d8-9d8b-10937f2100b1" />






