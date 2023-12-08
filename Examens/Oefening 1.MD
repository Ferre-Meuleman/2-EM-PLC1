# Examen PLC
## Oefening 1
### Opgaven:
Als de PLC opstart (Stop -> Run) en het systeem in de begintoestand komt, verloopt het proces verder in functie van de toestand van schakelaar **S10**.
<br>Indien bij het opstarten **S10** open staat en even gesloten en terug geopend wordt, dient **Lgr1** gedurende 3s aan te gaan; d.w.z. **Lgr1** mag pas oplichten op het moment dat **S10** terug geopend wordt. Tijdens deze 3s moet in **iDisplay1** (op het operator panel) de waarde 1 komen.
<br>Indien bij het opstarten **S10** dicht staat, dient **Lre1** te knipperen (5 Hz) en staat in **iDisplay1** de waarde 2.
<br>
Vanaf het moment dat **S10** geopend wordt, dan dooft **Lre1** en dient **Lgr1** gedurende 3s aan te gaan en moet in **iDisplay1** (op het operator panel) de waarde 1 komen.
<br>
Nadat Lgr1 gedoofd is moet Lor1 knipperen met f = 1 Hz, in **iDisplay1** moet tegelijkertijd de waarde 3 te staan.
Wordt er op **S0** gedrukt, moet het systeem terug in de begintoestand komen. In de begintoestand staat in **iDispay1** de waarde 0.

### Statemachine:
![](https://i.ibb.co/BK9qw46/oefening-1.jpg)
### Code:
``` 
//Opstart
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

//Flankdetecties S10
"PosFl".S10 := "S10" AND NOT "NR".S10_VorigeCycl;
"NegFl".S10 := NOT "S10" AND "NR".S10_VorigeCycl;
"NR".S10_VorigeCycl := "S10";

//Posositeive Flankdetectie S0
"PosFl".S0 :="S0" AND NOT "NR".S0_VorigeCycl;
"NR".S0_VorigeCycl := "S0";

//State Idle
IF "NR".Opstart OR "PosFl"."S0" THEN
    "State".Idle := TRUE;
    "State".Rood := FALSE;
    "State".Groen := FALSE;
    "State".Oranje := FALSE;
    "State".Tussenstand := FALSE;
END_IF;

//State Rood
IF "State".Idle AND "S10" THEN
    "State".Idle := FALSE;
    "State".Rood := TRUE;
    "State".Groen := FALSE;
    "State".Oranje := FALSE;
    "State".Tussenstand := FALSE;
END_IF;

//State Groen
IF "State".Rood AND NOT "S10"
    OR("State".Tussenstand AND "NegFl".S10) THEN
    "State".Idle := FALSE;
    "State".Rood := FALSE;
    "State".Groen := TRUE;
    "State".Oranje := FALSE;
    "State".Tussenstand := FALSE;
END_IF;

//State Oranje
IF "State".Groen AND "Timeup" THEN
    "State".Idle := FALSE;
    "State".Rood := FALSE;
    "State".Groen := FALSE;
    "State".Oranje := TRUE;
    "State".Tussenstand := FALSE;
END_IF;

IF "State".Idle AND NOT "S10" THEN
    "State".Idle := FALSE;
    "State".Rood := FALSE;
    "State".Groen := FALSE;
    "State".Oranje := FALSE;
    "State".Tussenstand := TRUE;
END_IF;

//Timer 
"IEC_Timer_0_DB".TON(IN := "State".Groen,
                     PT := T#3s,
                     Q => "Timeup");

//Lampen controleren
"Lre1" := "State".Rood AND "PG5Hz";
"Lor1" := "State".Oranje AND "PG1Hz";
"Lgr1" := "State".Groen;

//Display 1 instellen
IF "State".Rood THEN
    "iDisplay1" := 2;
END_IF;

IF "State".Groen THEN
    "iDisplay1" := 1;
END_IF;

IF "State".Oranje THEN
    "iDisplay1" := 3;
END_IF;


```
### Controle:

1. **Opstart PLC en S10 open:**
   - Opstart PLC en open S10.
   - Na dicht-open-actie van S10 is Lgr1 gedurende 3s aan!
   - iDisplay1 = 1

2. **Opstart PLC en S10 dicht:**
   - Lre1 knippert (5 Hz).
   - iDisplay1 = 2

3. **Als S10 geopend wordt:**
   - Lre1 dooft.
   - Lgr1 gedurende 3s aan.
   - iDisplay1 = 1

4. **Na 3s oplichten Lgr1:**
   - Lor1 knippert met f=1Hz.
   - iDisplay1 = 3

5. **Terug op SO drukken:**
   - Terug naar beginsituatie.
   - iDisplay1 = 0
