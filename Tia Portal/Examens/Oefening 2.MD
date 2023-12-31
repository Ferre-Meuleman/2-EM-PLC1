# Examen PLC
## Oefening 2
### Opgaven:
***
Als de PLC opstart (Stop -> Run) en het systeem in de begintoestand komt, dient **Lor1** aan te gaan. In **iDisplay1** (op het operator panel) moet de waarde 1 komen. <br>
Als op **S0** gedrukt wordt, dient **Lor1** uit te gaan en **Lgr1** te knipperen met volgende sequentie: 1s aan, 0,5s uit. Gedurende het knipperen moet in **iDisplay1** de waarde 2 staan.<br><br>
Als tijdens het knipperen op **S0** gedrukt wordt, dient het knipperen ONMIDDELLIJK te stoppen, m.a.w. **Lgr1** moet DIRECT uit gaan en de begintoestand wordt terug actief (Lor1 aan).<br><br>
In **iDisplay2** moet het aantal keren dat **Lgr1** is aan geweest (tijdens het knipperen) weergegeven worden. JE MAG HIERVOOR GEEN COUNTER GEBRUIKEN! <br><br>
Met **S4** kan de waarde in **iDisplay2** op 0 gezet worden, maar enkel indien de PLC zich in de begintoestand bevindt.

### Statemachine:
***
![](https://i.ibb.co/1bqCSWy/Examen-Oefening2.jpg)


### Code:
***
``` s
//Opstarten
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

//Flanken op S0
"PosFl".S0 := "S0" AND NOT "NR".S0_VorigeCycl;
"NR".S0_VorigeCycl := "S0";

//Flanken op Lgr1
"PosFl".Lgr1 := "Lgr1" AND NOT "NR".Lgr1_VorigeCycl;
"NR".Lgr1_VorigeCycl := "Lgr1";


//IdleExamen2
IF "NR".Opstart OR
("State".Lgr1_1sec_aan AND "PosFl".S0) OR
("State".Lgr1_0_5Sec_uit AND "PosFl".S0)
THEN
"State".IdleExamen2 := TRUE;
"State".Lgr1_0_5Sec_uit := FALSE;
"State".Lgr1_1sec_aan := FALSE;
"PosFl".S0 := FALSE;
END_IF;

IF ("State".IdleExamen2 AND "PosFl".S0) OR
("State".Lgr1_0_5Sec_uit AND) #Timer05sec
THEN
"State".IdleExamen2 := FALSE;
"State".Lgr1_1sec_aan := TRUE;
"State".Lgr1_0_5Sec_uit := FALSE;
"PosFl".S0 := FALSE;
END_IF;

IF "State".Lgr1_1sec_aan AND #Timer1sec THEN
"State".Lgr1_1sec_aan := FALSE;
"State".IdleExamen2 := FALSE;
"State".Lgr1_0_5Sec_uit := TRUE;
END_IF;


//Timer voor Lgr1 1 seconden aan te laten
"IEC_Timer_0_DB_3".TON(IN := "State".Lgr1_1sec_aan,
PT := T#1s,
Q => #Timer1sec);

//TImer Voor Lgr1 0,5 seconden uit te laten
"IEC_Timer_0_DB_4".TON(IN := "State".Lgr1_0_5Sec_uit,
PT := T#0.5s,
Q => #Timer05sec);

//IDsiplay2 op nul zetten
IF "State".IdleExamen2 AND "S4" THEN
"iDisplay2" := 0;
END_IF;

IF "PosFl".Lgr1 THEN
"iDisplay2" := "iDisplay2" + 1;
END_IF;

IF "State".IdleExamen2 THEN
"iDisplay1" := 1;
END_IF;

//Leds aanzetten
"Lor1" := "State".IdleExamen2;
"Lgr1" := "State".Lgr1_1sec_aan;
```
### Controle:
***
1. **Opstart PLC:**
   - Lor1 aan
   - iDisplay1 = 1

2. **Op SO drukken:**
   - Lor1 uit
   - Lgr1 knippert 1s aan / 0,5s uit
   - iDisplay1 = 2

3. **Terug op SO drukken:**
   - Lgr1 onmiddellijk uit
   - Lor1 aan
   - iDisplay1 = 1 (begintoestand)

4. **In iDisplay2 eller ok / geen teller gebruikt**
5. **Teller kan enkel gereset worden in begintoestand ...**

