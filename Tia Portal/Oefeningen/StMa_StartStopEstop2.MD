# StMa_StartStopEstop2
## Opgaven:
Programmeer een start-stop-schakeling (naam programma: StartStop) in LAD met een
NO-drukknop **S1** als start en een NC-drukknop **S4** als stop. JE MAG GEEN SET/RESETINSTRUCTIES GEBRUIKEN ! Baseer je op de schema's van hierboven !
Als uitgang neem je één van de LED's op je paneeltje. Probeer uit! + bijvoegen van een noodstop (gebruik de aanwezige noodstop)
Bij indrukken van noodstop valt uitgang onmiddellijk uit. Indien echter de noodstop
ontgrendeld (uitgetrokken) wordt, mag de uitgang pas terug aangeschakeld kunnen worden
indien er eerst op een NO-drukknop (Reset-knop) gedrukt wordt. + onderstaande aanvulling.
Indien de noodstop ingedrukt wordt, dient **Lre1** te knipperen aan 1 Hz. Indien de
noodstopknop ontgrendeld wordt dient Lre1 continu te branden totdat de Reset-knop **S2**
ingedrukt wordt.

## Statemachine:
![]()

## Code:
```SCL
//opstart
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

//Idle
IF "NR".Opstart
    OR ("State".Action_on & NOT "S4")
    OR ("State".Alarm & "S2") THEN
    
    "State".Idle := TRUE;
    "State".Action_on := FALSE;
    "State".Ems := FALSE;
    "State".Alarm := FALSE;
END_IF;

//Action_on
IF "State".Idle & "S1" & "S4" THEN
    
    "State".Idle := FALSE;
    "State".Action_on := TRUE;
    "State".Ems := FALSE;
    "State".Alarm := FALSE;
END_IF;

//Ems
IF "State".Idle & NOT "Estop"
    OR ("State".Action_on & NOT "Estop")
    OR ("State".Alarm & NOT "Estop")
THEN
    
    "State".Idle := FALSE;
    "State".Action_on := FALSE;
    "State".Ems := TRUE;
    "State".Alarm := FALSE;
END_IF;

//Alarm
IF "State".Ems & "Estop" THEN
    
    "State".Idle := FALSE;
    "State".Action_on := FALSE;
    "State".Ems := FALSE;
    "State".Alarm := TRUE;
END_IF;

IF "State".Ems THEN
    "Lre1" := "PG1Hz";
ELSE
    IF "State".Alarm THEN
        "Lre1" := TRUE;
    ELSE
        "Lre1" := FALSE;
    END_IF;

END_IF;

//acties
"Lgr1" := "State".Action_on;
```
