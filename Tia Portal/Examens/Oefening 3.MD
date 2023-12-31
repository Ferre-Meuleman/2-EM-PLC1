# Examen PLC
## Oefening 3
### Opgaven:
***
Indien de PLC opgestart wordt, en het systeem in de begintoestand komt, moet **Lre1** knipperen (**f= 1 HZ**).
<br>
<br>
Dan zijn er 2 mogelijkheden om met de machine te werken:
<br>
Je mag er van uitgaan dat beide schakelaars nooit tezamen dicht zijn.

- Manuele mode (**S10** dicht):
<br>
   **Lre1** dooft, **Lgr2** knippert (**f = 2Hz**). <br>
   **Lor1** en **Lgr1** kunnen nu aangestuurd worden door respectievelijk **S1** en    **S2**, <br>
   dus **Lor1** is aan zolang **S1** ingedrukt wordt en idem met **Lgr1** en **S2**.<br>
   Op het moment dat **S10** in Idle gezet wordt → systeem terug in
   begintoestand.

- Automatische mode (**S11** dicht).<br>
   **Lre1** dooft, **Lgr2** continu aan.<br>
   Bij een dalende flank op **S0**, gaat **Lor1** aan en 1,5s later gaat **Lgr1** mee aan, 1,5s later → beide uit. <br>
   Via **S0** kan een nieuwe cyclus gestart worden.<br> 
   Op het moment dat **S11** in Idle gezet wordt systeem terug in begintoestand. <br>
   Indien S11 in Idle gezet wordt, mag de cyclus eerst wel afgewerkt worden.<br>
   <br>

Je moet dus geen rekening houden met het feit dat **S10** en **S11** samen dicht kunnen  zijn. Bij het testen wel slechts de ene sluiten als de andere open is.


### Statemachine:
***
![](https://i.ibb.co/VYqkhPb/Oefening-1-Oefening3.jpg")


### Code:
***
``` S
//Opstarten 
"NR".Opstart := NOT "NR"."Zero@Start";
"NR"."Zero@Start" := TRUE;

//Flanken op S0
"NegFl".S0 := NOT "S0" AND "NR".S0_VorigeCycl;
"NR".S0_VorigeCycl := "S0";

//state idleEx3
IF "NR".Opstart OR ("State".Automatisch AND NOT "S11") OR
("State".Manueel AND NOT "S10") THEN
"State".IdleEx2 := TRUE;
"State".Automatisch := FALSE;
"State".Lor1_aan := FALSE;
"State".Lor1_en_Lgr1_aan := FALSE;
"State".Manueel := FALSE;
END_IF;

IF ("State".IdleEx2 AND "S11") OR
("State".Lor1_en_Lgr1_aan AND #Timerdos) THEN
"State".Automatisch := TRUE;
"State".IdleEx2 := FALSE;
"State".Lor1_aan := FALSE;
"State".Lor1_en_Lgr1_aan := FALSE;
END_IF;

IF "State".Automatisch AND "NegFl".S0 THEN
"State".Lor1_aan := TRUE;
"State".Automatisch := FALSE;
"State".IdleEx2 := FALSE;
"State".Lor1_en_Lgr1_aan := FALSE;
END_IF;

IF "State".Lor1_aan AND #Timeruno THEN
"State".Lor1_en_Lgr1_aan := TRUE;
"State".Lor1_aan := FALSE;
"State".Automatisch := FALSE;
"State".IdleEx2 := FALSE;
END_IF;

//Eerste Timer voor lor1 aan voor 1,5 seconden
"IEC_Timer_0_DB_1".TON(IN := "State".Lor1_aan,
PT := T#1.5s,
Q => #Timeruno);

//Tweede Timer voor lor1 en lgr1 aan voor 1,5 seconden
"IEC_Timer_0_DB_2".TON(IN := "State".Lor1_en_Lgr1_aan,
PT := T#1.5s,
Q => #Timerdos);

//State manueel
IF "State".IdleEx2 AND "S10" THEN
"State".IdleEx2 := FALSE;
"State".Manueel := TRUE;
"State".Automatisch := FALSE;
END_IF;


//Leds aanzetten
"Lor1" := "State".Lor1_aan OR "State".Lor1_en_Lgr1_aan OR ("State".Manueel AND "S1");
"Lgr1" := "State".Lor1_en_Lgr1_aan OR ("State".Manueel AND "S2");
"Lgr2" := "State".Manueel AND "PG2Hz";
"Lre1" := "State".IdleEx2 AND "PG1Hz";
```


### Controle:
***
**Geen idee geen fotos van**

