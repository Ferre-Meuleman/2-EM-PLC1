# StMa_Teleruptor_Tim
## Opgaven:
Indien de lamp aangeschakeld is (via **S0**), kan deze gedurende 3s niet uitgeschakeld
worden. M.a.w. de lamp moet min. 3s aanblijven, ook al druk je tijdens deze 3s op de
drukknop **S0**.

## Statemachine:
![](https://i.ibb.co/Mpv9qcT/Oefeningen-St-Ma-Teleruptor-Tim.jpg)

## Code:
```SCL
//opstart
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

// Flankdetectie S1
"PosFl".S0 := "S0" AND NOT "NR".S0_VorigeCycl;
"NegFl".S0 := NOT "S0" AND "NR".S0_VorigeCycl;
"NR".S0_VorigeCycl := "S0";

IF "NR".Opstart
    OR ("State".Action_on AND "PosFl".S0 AND "Timeup") THEN
    "State".Idle := TRUE;
    "State".Action_on := FALSE;
    "PosFl".S0 := False;
END_IF;

IF "State".Idle AND "PosFl".S0 THEN
    "State".Idle := FALSE;
    "State".Action_on := TRUE;
END_IF;



IF "State".Action_on THEN
    "Lgr1" := TRUE;
ELSE
    "Lgr1" := FALSE;
END_IF;

"IEC_Timer_0_DB".TON(IN := "State".Action_on,
                     PT := T#3s,
                     Q => "Timeup");
```
