# StMa_IntegratorMetOmkeerfucntie
## Opgaven:
Indien **S10** = 1, **iOut** (MW16) wordt geïncrementeerd met 1 per 500ms.
Indien **S10** = 0, incrementeren stopt.
Indien **S10** terug = 1 dient **iOut** gedecrementeerd te worden met 1 per 500 ms.
Enz…
De laagste waarde voor **iOut** = **iLimietMin** (MW18) , de hoogste waarde voor **iOut** = **iLimietMax** (MW20)

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
"NegFl".PG2HZ := NOT "PG2Hz" AND "NR".PG2HZ_VorigeCycl;
"NR".PG2HZ_VorigeCycl := "PG2Hz";

"PosFl".Action_on := "State".Action_on AND NOT "NR".Action_on_VorigeCycl;
"NR".Action_on_VorigeCycl := "State".Action_on;

IF "NR".Opstart
    OR ("State".Action_on AND "NegFl".S10)
    OR ("State".Reset AND "PosFl".S14) THEN
    "State".Idle := TRUE;
    "State".Action_on := FALSE;
    "State".Reset := FALSE;
END_IF;

IF "State".Idle AND "PosFl".S10
    OR ("State".Inc AND "NegFl".PG2HZ) THEN
    "State".Idle := FALSE;
    "State".Action_on := TRUE;
    "State".Reset := FALSE;
END_IF;

IF ("State".Idle OR "State".Action_on) AND "NegFl".S14 THEN
    "State".Idle := FALSE;
    "State".Action_on := FALSE;
    "State".Reset := TRUE;
END_IF;

IF "NR".Opstart THEN
    "ActionDec" := TRUE;
END_IF;

IF "PosFl".Action_on THEN
    "ActionInc" := NOT "ActionInc";
    "ActionDec" := NOT "ActionDec";
END_IF;

IF "State".Action_on AND "PosFl".PG2HZ AND "Iout" < "iLimietMax" AND "ActionInc" THEN
    "Iout" := "Iout" + 1;
END_IF;

IF "State".Action_on AND "PosFl".PG2HZ AND  "Iout" > "iLimietMin" AND "ActionDec" THEN
    "Iout" := "Iout" - 1;
END_IF;

IF "State".Reset THEN
    "Iout" := 0;
END_IF;

```
