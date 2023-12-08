# StMa_Parkeerplaats<br><br> <span style="color: Red"> **!! Code werkt nog niet !!** </span>
## Opgaven:
Bij een op- en afrit van een parking staan sensoren om te detecteren dat een voertuig de
parking op- en/of afrijdt. <br>
Je mag ervan uitgaan dat bij opstart de parking leeg is.
De sensor **S0** (NO) staat bij de oprit en detecteert een auto die de parking oprijdt.<br>
De sensor **S4** (NC) staat bij de uitrit en detecteert een auto die de parking verlaat. <br><br>
Indien de sensoren geen wagen detecteren staan ze in de stand Idle !<br><br>
Bij de oprit bevinden zich eveneens twee lampen om de toestand op de parking weer te
geven. Een groene lamp **Lgr1** dat er nog vrije plaatsen zijn; een rode lamp **Lre1** dat
de parking vol is.<br><br>
Op het OP (Operator Panel) moeten het aantal vrije plaatsen weergegeven worden via de
variabele **iDisplay1** ! Om iets naar **iDisplay1** te sturen gebruik je de MOVE-instructie !<br><br>
Bij het testen van je programma ga je uit van een parking met max. 5 plaatsen.
Je moet bij het testen dan ook niet verder tellen dan 5!<br><br>
Maak ook gebruik van een drukknop (bv. **S2**) om je teller te kunnen resetten bij het testen
van je programma ! De PV- en S-ingang dienen niet gebruikt te worden !<br><br>
Opgelet: belangrijke opmerking
De uitgang Q van de teller kan je niet gebruiken om te testen dat de **tellerwaarde** bv. gelijk
is aan 5 ! Kijk na via help wanneer deze Q-uitgang dan wel 1 of 0 is !<br><br>
Om dus te testen dat de **tellerwaarde** = 5 , moet bij deze opgave gebruik gemaakt worden
van instructies om de **tellerwaarde** te vergelijken met 5 en dan hierop gepast te reageren.
Dit zijn o.a. volgende instructies:
- instructies om INT-waarden te vergelijken (in de map Comparator) en
- instructies om rekenkundige bewerkingen (+, -, …) met INT-waarden uit te voeren
(in map Integer Function)

## Statemachine:
![]()

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