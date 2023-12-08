# StMa_MijnVar
## Opgaven:
Maak een DB12 aan genaamd 'MijnVar'.
Maak in deze DB volgende variabelen aan:
- iVar1 init-waarde 0
- iVar2 init-waarde 0
- iVar3 init-waarde 10
- iVar4 init-waarde 20
- bOneBit init-waarde True
- bZeroBit init-waarde False
### Functionele beschrijving:
Als op **K4** (op het touch panel) gedrukt wordt, dient op **iDaisplay1** de waarde van **iVar3** te
verschijnen. Tegelijkertijd dient in **iVar1** de waarde te komen die je eerst via **iEntry1** hebt
ingegeven.
Ook moet **Lgr1** aan gaan via **bOneBit**.
Als op **K5** gedrukt wordt dient op **iDispalay1** de waarde van **iVar4** te verschijnen.
Tegelijkertijd dient in **iVar1** de waarde te komen die je ingegeven hebt op **iEntry2**.
Ook moet **Lgr2** aan gaan via **bOneBit**.
Als op **K6** gedrukt wordt, dienen **iVar1 … iVar4** in de DB gewist te worden en moeten **Lgr1** en
**Lgr2** via **bZeroBit** uitgaan.

## Statemachine:
![]()
## Code:
```SCL
    "PosFl".K4 := "K4" AND NOT "NR".K4_VorigeCycl;
    "NegFl".K4 := NOT "K4" AND "NR".K4_VorigeCycl;
    "NR".K4_VorigeCycl := "K4";
    
    "PosFl".K5 := "K5" AND NOT "NR".K5_VorigeCycl;
    "NegFl".K5 := NOT "K5" AND "NR".K5_VorigeCycl;
    "NR".K5_VorigeCycl := "K5";
    
    "PosFl".K6 := "K6" AND NOT "NR".K6_VorigeCycl;
    "NegFl".K6 := NOT "K6" AND "NR".K6_VorigeCycl;
    "NR".K6_VorigeCycl := "K6";
    
    //opstart
    "NR".Opstart := NOT "NR"."Zero@Start";
    "NR"."Zero@Start" := TRUE;
    
    IF "NR".Opstart
        OR("State".Action1 AND "NegFl".K4)
        OR("State".Action2 AND "NegFl".K5)
        OR("State".Reset AND "NegFl".K6) THEN
        "State".Idle := TRUE;
        "State".Action1 := FALSE;
        "State".Action2 := FALSE;
        "State".Reset := FALSE;
    END_IF;
    
    IF "State".Idle AND "PosFl".K4 THEN
        "State".Idle := FALSE;
        "State".Action1 := TRUE;
        "State".Action2 := FALSE;
        "State".Reset := FALSE;
    END_IF;
    
    IF "State".Idle AND "PosFl".K5 THEN
        "State".Idle := FALSE;
        "State".Action1 := FALSE;
        "State".Action2 := TRUE;
        "State".Reset := FALSE;
    END_IF;
    
    IF "PosFl".K6 THEN
        "State".Idle := FALSE;
        "State".Action1 := FALSE;
        "State".Action2 := FALSE;
        "State".Reset := TRUE;
    END_IF;
    
    IF "State".Action1 THEN
        "iDisplay1" := "MijnVar".iVar3;
        "MijnVar".iVar1 := "iEntry1";
        "Lgr1" := "MijnVar".bOneBit;
    END_IF;
    
    IF "State".Action2 THEN
        "iDisplay2" := "MijnVar".iVar4;
        "MijnVar".iVar2 := "iEntry2";
        "Lgr2" := "MijnVar".bOneBit;
    END_IF;
    
    IF "State".Reset THEN
        "MijnVar".iVar1 := 0;
        "MijnVar".iVar2 := 0;
        "MijnVar".iVar3 := 0;
        "MijnVar".iVar4 := 0;
        "Lgr1" := "MijnVar".bZeroBit;
        "Lgr2" := "MijnVar".bZeroBit;
    END_IF;
```
