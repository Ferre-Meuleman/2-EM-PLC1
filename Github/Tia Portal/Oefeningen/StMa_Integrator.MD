# StMa_Integrator
## Opgaven:
Indien **S10** True wordt, dient **iOut** (MW16) in functie van de tijd m.a.w. per 500 ms met 1
geïncrementeerd te worden. Wanneer **S10** = 0 moet het incrementeren stoppen.
Indien **S14** False wordt, moet **iOut** op nul komen.

## Statemachine:
![]()
## Code:
```SCL
//opstart
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

// Flankdetectie S14
"PosFl".S10 := "S10" AND NOT "NR".S10_VorigeCycl;
"NegFl".S10 := NOT "S10" AND "NR".S10_VorigeCycl;
"NR".S10_VorigeCycl := "S10";

"PosFl".S14 := "S14" AND NOT "NR".S14_VorigeCycl;
"NegFl".S14 := NOT "S14" AND "NR".S14_VorigeCycl;
"NR".S14_VorigeCycl := "S14";

"PosFl".PG2HZ := "PG2Hz" AND NOT "NR".PG2HZ_VorigeCycl;

"NR".PG2HZ_VorigeCycl := "PG2Hz";


IF "NR".Opstart
    OR ("State".Action_on AND "NegFl".S10)
    OR ("State".Reset AND "PosFl".S14) THEN
    "State".Idle := TRUE;
    "State".Action_on := FALSE;
    "State".Reset := FALSE;
END_IF;

IF "State".Idle AND "PosFl".S10 THEN
    "State".Idle := FALSE;
    "State".Action_on := TRUE;
    "State".Reset := FALSE;
END_IF;

IF ("State".Idle OR "State".Action_on) AND "NegFl".S14 THEN
    "State".Idle := FALSE;
    "State".Action_on := FALSE;
    "State".Reset := TRUE;
END_IF;

IF "State".Action_on AND "PosFl".PG2HZ THEN
    "Iout" := "Iout" + 1;
END_IF;

IF "State".Reset THEN
    "Iout" := 0;
END_IF;

```
