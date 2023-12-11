# StMa_StartStopEstop1
## Opgaven:
Programmeer een start-stop-schakeling (naam programma: StartStop) in LAD met een
NO-drukknop **S1** als start en een NC-drukknop **S4** als stop. JE MAG GEEN SET/RESETINSTRUCTIES GEBRUIKEN ! Baseer je op de schema's van hierboven !
Als uitgang neem je één van de LED's op je paneeltje. Probeer uit! + bijvoegen van een noodstop (gebruik de aanwezige noodstop)
Bij indrukken van noodstop valt uitgang onmiddellijk uit. Indien echter de noodstop
ontgrendeld (uitgetrokken) wordt, mag de uitgang pas terug aangeschakeld kunnen worden
indien er eerst op een NO-drukknop (Reset-knop) gedrukt wordt.
## Statemachine:
![](https://i.ibb.co/5Y0gBD5/Oefeningen-St-Ma-Start-Stop-Estop1.jpg)
## Code:
```SCL
//opstart
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

//Idle
IF "NR".Opstart
    OR ("State".Action_on & NOT "S4")
    OR ("State".Ems & "S2" & "Estop") THEN
    
    "State".Idle := TRUE;
    "State".Action_on := FALSE;
    "State".Ems := FALSE;
END_IF;

//Action_on
IF "State".Idle & "S1" & "S4" THEN
    
    "State".Idle := FALSE;
    "State".Action_on := TRUE;
    "State".Ems := FALSE;
END_IF;

//Ems
IF "State".Idle & NOT "Estop"
    OR ("State".Action_on & NOT "Estop") THEN

    "State".Idle := FALSE;
    "State".Action_on := FALSE;
    "State".Ems := TRUE;
END_IF;


//acties
"Lgr1" := "State".Action_on;
```
