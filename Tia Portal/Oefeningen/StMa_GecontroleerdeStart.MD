# StMa_GecontroleerdeStart
## Opgaven:
Eis: maak een Start-Stopschakeling met **S1** (NO) als Startdrukknop en **S4** (NC) als Stopdrukknop.
Om te vermijden dat een machine onverwacht kan opstarten, als de bedrading van de Startdrukknop bv. kortgesloten is, mag de machine enkel starten als **S1** losgelaten wordt en niet
als ze ingedrukt wordt ! Gebruik als uitgang **Lgr1** !

## Statemachine:
![]()
## Code:
```SCL
//opstart
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

// Flankdetectie S1
"NegFl".S1 := NOT "S1" AND "NR".S1_VorigeCycl;
"NR".S1_VorigeCycl := "S1";

IF "NR".Opstart
    OR ("State".Action_on AND NOT "S4")  THEN
    "State".Idle := TRUE;
    "State".Action_on := FALSE;
END_IF;

IF "State".Idle AND "NegFl".S1 AND "S4" THEN
    "State".Idle := FALSE;
    "State".Action_on := TRUE;
END_IF;

IF "State".Action_on THEN
    "Lgr1" := TRUE;
ELSE
    "Lgr1" := FALSE;
END_IF;
```