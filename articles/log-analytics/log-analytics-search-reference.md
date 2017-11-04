---
title: "Az Azure Naplóelemzés keresési referencia |} Microsoft Docs"
description: "A Naplóelemzési keresési hivatkozás ismerteti a keresési nyelv és az általános lekérdezés szintaxisát lehetőségeket is használ az adatok a Keresés és szűrés kifejezések segítségével szűkítse a keresést."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.assetid: 402615a2-bed0-4831-ba69-53be49059718
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/11/2017
ms.author: bwren
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 264ea071dc0b15964af07c68cbf0dee896b07a3e
ms.sourcegitcommit: 5735491874429ba19607f5f81cd4823e4d8c8206
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/16/2017
---
# <a name="log-analytics-search-reference"></a>A Naplóelemzési hivatkozás keresése

>[!NOTE]
> Ez a cikk ismerteti a napló keresések Naplóelemzési az örökölt lekérdezési nyelv használatával.  Ha a munkaterületet lett frissítve a [új Log Analytics lekérdezési nyelv](log-analytics-log-search-upgrade.md), majd tekintse át [a nyelvi referencia az új nyelv](https://go.microsoft.com/fwlink/?linkid=856079).

A következő útmutató szakaszban vonatkozó keresési nyelvi használatos általános lekérdezési szintaxis beállításokat ismerteti az adatok a Keresés és szűrés kifejezések segítségével szűkítse a keresést. Azt is bemutatja, parancsok, amelyek segítségével művelet végrehajtása a lekért adatok.

A keresés által visszaadott mezők olvashat, és a Decimal típushoz, amelyek segítenek részletesebben hasonló kategóriák adatok, a a [keresőmezőt és dimenzió hivatkozási szakasz](#search-field-and-facet-reference).

## <a name="general-query-syntax"></a>Általános lekérdezés szintaxisa
Az általános lekérdezése szintaxisa a következő:

```
filterExpression | command1 | command2 …
```

A szűrőkifejezés (`filterExpression`) határozza meg, a "where" a lekérdezés feltételét. A parancsok a lekérdezés által adott eredmények vonatkozik. Több parancs a függőleges vonal (|) kell elválasztani.

### <a name="general-syntax-examples"></a>Általános szintaxispéldáival
Példák:

```
system
```

A lekérdezés által visszaadott eredmények szót tartalmazó *rendszer* minden mezőben, amely a teljes szöveges indexelés vagy keresési feltételeket.

> [!NOTE]
> Nem minden mező így indexeli ugyan, de a legáltalánosabb szöveges mezők (például a leírásokat és nevek) általában vannak.
>
>

```
system error
```

A lekérdezés által visszaadott eredmények, amelyek tartalmazzák a szavakat *rendszer* és *hiba*.

```
system error | sort ManagementGroupName, TimeGenerated desc | top 10
```

A lekérdezés által visszaadott eredmények, amelyek tartalmazzák a szavakat *rendszer* és *hiba*. Azt, majd rendezi az eredményeket a *ManagementGroupName* mezőjét (növekvő sorrendben), és a majd a *TimeGenerated* mezőjét (csökkenő sorrendben). Csak az első 10 eredmények vesz igénybe.

> [!IMPORTANT]
> A mezőnevek és a következő karakterlánc és a szöveges mezők értékének különbözőnek számítanak a kis.
>
>

## <a name="filter-expressions"></a>Szűrési kifejezésekben
Az alábbi alszakaszok a szűrőkifejezéseket ismertetik.

### <a name="string-literals"></a>A szövegkonstansok
Egy szöveges karakterlánc, amely nem ismeri fel az elemző kulcsszó vagy egy előre definiált adattípus (például szám vagy dátum) bármilyen karakterlánc.

Példák:

```
These all are string literals
```

A lekérdezési eredmények, amelyek tartalmazzák az összes öt szavak előfordulását keresi. Összetett karakterlánc keresést végezni, tegye idézőjelek közé a karakterláncot. Példa:

```
"Windows Server"
```

Ez az csak a pontos egyezés az eredményeket ad vissza *Windows Server*.

### <a name="numbers"></a>Számok
Az elemző támogatja a decimális egész és lebegőpontos szám szintaxis numerikus mezők.

Példák:

```
Type:Perf 0.5
```

```
HTTP 500
```

### <a name="dates-and-times"></a>Dátum és idő
A rendszer minden adat rendelkezik egy *TimeGenerated* tulajdonság, amely az eredeti dátum és idő a rekord jelenti. Bizonyos típusú adatok lehetnek a további dátumot és időt tartalmazó mezőiben (például *LastModified*).

Az idősor **diagram és az időt** választó az Azure Naplóelemzés (szerint az aktuális lekérdezés futtatása) időbeli eredmények terjesztési jeleníti meg. Ez a alapul a *TimeGenerated* mező. Dátum-és egy adott karakterlánc-formátum, amelyek segítségével a lekérdezésekben korlátozza a lekérdezést egy adott időkeretet rendelkezik. Szintaxis segítségével is relatív (például "közötti intervallumok 3 nappal ezelőtt és 2 óra telt el") hivatkozik.

Az alábbiakban szintaxis érvényes formáinak a dátum és idő:

```
yyyy-mm-ddThh:mm:ss.dddZ
```

```
yyyy-mm-ddThh:mm:ss.ddd
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm:ss
```

```
yyyy-mm-ddThh:mm
```

```
yyyy-mm-dd
```


Példa:

```
TimeGenerated:2013-10-01T12:20
```

Az előző parancs csak rekordokat adja vissza egy *TimeGenerated* pontosan 12:20 2013. októberi 1 értéket.

Az elemző is támogatja a mnemonikus dátum/idő értéket, most. (Nem valószínű, hogy az így kapott eredmények, mert az adatok nem teszik, hogy gyors rendszeren keresztül.)

Ezekben a példákban a építőelemei relatív és abszolút dátumok használata. A következő három alszakaszokat hogyan használhatja ezeket a speciális szűrők, példákkal relatív dátumtartományok használó látható.

### <a name="datetime-math"></a>Matematikai dátum és idő
A dátum/idő matematikai operátorok használatával eltolás vagy a dátum/idő értékeknek ciklikus egyszerű dátum/idő számítások használatával.

Szintaxis:

```
datetime/unit
```

```
datetime[+|-]count unit
```

| Operátor | Leírás |
| --- | --- |
| / |A megadott egység kerekítés dátum/idő. Ha például most / nap az aktuális dátum/idő az aktuális nap éjféli kerekít. |
| + vagy - |Dátum és idő, a megadott számú egység tolódnak. Például most + 1 óra tolódnak az aktuális dátum/idő egy órával előre. 2013-10-01T12:00-10 nap eltolja a Date értéknek 10 nap által. |

A dátum/idő matematikai operátorok együtt is láncában találhatók. Példa:

```
NOW+1HOUR-10MONTHS/MINUTE
```

A következő táblázat a támogatott dátum/idő egység.

| Dátum és idő egység | Leírás |
| --- | --- |
| ÉV, ÉV |Aktuális év kerekítés vagy tolódnak el a megadott számú éven belülre esik. |
| HÓNAP, HÓNAP |Az aktuális hónap kerekít, vagy tolódnak el a megadott számú hónappal. |
| NAPJA, NAP, DÁTUM |Aktuális napot a hónapban kerekítés vagy tolódnak el a megadott számú nap óta. |
| ÓRA, ÜZEMIDEJE (ÓRA) |Jelenlegi órán kerekítés vagy tolódnak el a megadott számú óra. |
| PERC, A PERC |Jelenlegi percen kerekítés vagy tolódnak el a megadott számú perc. |
| MÁSODIK, MÁSODPERCBEN |Második aktuális kerekítés vagy tolódnak el a megadott számú másodpercnél tovább. |
| EZREDMÁSODPERCES, EZREDMÁSODPERC, MILLI, MILLIS |Aktuális ezredmásodperces kerekítés vagy tolódnak el a megadott számú ezredmásodperc. |

### <a name="field-facets"></a>A mező facets
Mező értékkorlátozás használatával adhatja meg a keresési feltétel mezőiről és a pontos értékek. Ez eltér az index egész különböző feltételek "szabad szöveg" lekérdezések írásáról. Ezzel a módszerrel több előző példákban már láthatta. A következő példák összetettebb.

**Szintaxis**

```
field:value
```

```
field=value
```

**Leírás**

Megkeresi az adott mezőt. Az érték lehet egy karakterlánc, szám vagy dátum és idő.

Példa:

```
TimeGenerated:NOW
```

```
ObjectDisplayName:"server01.contoso.com"
```

```
SampleValue:0.3
```

**Szintaxis**

*a mező > értéke*

*a mező < érték*

*a mező > = érték*

*a mező < érték =*

*a mező! érték =*

**Leírás**

Itt összehasonlítást.

Példa:

```
TimeGenerated>NOW+2HOURS
```

**Szintaxis**

```
field:[from..to]
```

**Leírás**

Tartomány értékkorlátozás biztosít.

Példa:

```
TimeGenerated:[NOW..NOW+1DAY]
```

```
SampleValue:[0..2]
```

### <a name="in"></a>IN
A **IN** kulcsszó lehetővé teszi, hogy az értékek listájának választását. Attól függően, hogy a szintaxist használ ez lehet egy egyszerű, adjon meg értékeket, vagy összesítést értékeinek listáját.

1. szintaxis:

```
field IN {value1,value2,value3,...}
```

Ez a szintaxis lehetővé teszi egy egyszerű listában fel az összes értéket.



Példák:

```
EventID IN {1201,1204,1210}
```

```
Computer IN {"srv01.contoso.com","srv02.contoso.com"}
```

2. szintaxis:

```
(Outer query) (Field to use with inner query results) IN {Inner query | measure count() by (Field to send to outer query)} (rest  of outer query)  
```

Ez a szintaxis összesítést létrehozását teszi lehetővé. Az értékek listája is majd hírcsatornát, hogy az adott összesítési be egy másik külső (elsődleges) keresési, amely megkeresi az adott értékkel rendelkező események. Ehhez a belső keresési befoglaló zárójelek között, és az eredményeket, a lehetséges értékek a külső keresési mező az IN operátor használatával.

Belső lekérdezés példa: *jelenleg hiányzó biztonsági frissítéseket számítógépek* a következő összesítési lekérdezést:

```
Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer
```    

Az utolsó lekérdezési megtalált *számítógépek jelenleg hiányzó biztonsági frissítéseket az összes Windows-események* a következőhöz hasonló:

```
Type=Event Computer IN {Type:Update Classification="Security Updates"  UpdateState=needed TimeGenerated>NOW-25HOURS | measure count() by Computer}
```

### <a name="contains"></a>Contains
A **Contains** kulcsszó lehetővé teszi, hogy lehet szűrni rögzíti a megadott karakterlánc tartalmazó mezőt. Ez a nagybetűk különbözőnek számítanak, csak a karakterlánc típusú működik, és nem tartalmazhatnak egyetlen escape-karakter.

Szintaxis:

```
field:contains("string")
```

Példa:

```
Type:contains("Event")
```

"Esemény" Ez visszaadja a karakterláncot tartalmazó típusú rekordok. Példák **esemény**, **SecurityEvent**, és **ServiceFabricOperationEvent**.



### <a name="regular-expressions"></a>Reguláris kifejezések
A reguláris kifejezéssel, mező a keresési feltételt is megadhat a **Regex** kulcsszó. A szintaxis reguláris kifejezéseket is használhat teljes leírását lásd: [reguláris kifejezésekkel napló megkeresi a Naplóelemzési szűrése](log-analytics-log-searches-regex.md).

Szintaxis:

```
field:Regex("Regular Expression")
```

Példa:

```
Computer:Regex("^C.*")
```

### <a name="logical-operators"></a>Logikai operátorok
A lekérdezés nyelveket támogatja a logikai operátorok (*és*, *vagy*, és *nem*) és a C-stílusú (*&&*,  *||* , és *!*, illetve). Zárójelek segítségével ezen operátorok csoport.

Példák:

```
system OR error

```

```
Type:Alert AND NOT(Severity:1 OR ObjectId:"8066bbc0-9ec8-ca83-1edc-6f30d4779bcb8066bbc0-9ec8-ca83-1edc-6f30d4779bcb")
```

A legfelső szintű szűrő argumentumok logikai operátor, akkor kihagyhatja. Ebben az esetben az és operátorral feltételezi.

| Szűrőkifejezés | Egyenértékű |
| --- | --- |
| Rendszerhiba: |a rendszer és hiba |
| a rendszer "Windows Server" vagy Súlyosság: 1 |rendszer- és ("Windows Server" vagy Súlyosság: 1) |

### <a name="wildcarding"></a>Helyettesítő karakterek használata
A lekérdezési nyelv támogatja a ( \* ) karaktert egy értéket a lekérdezés egy vagy több karakter helyettesítéséhez.

Példa:

 Az "SQL" nevét, például a "Redmond-SQL" számítógépek megkeresése.

```
Type=Event Computer=*SQL*
```

> [!NOTE]
> Jelenleg nem helyettesítő ajánlatok belül. Például az üzenet `"*This text*"` figyelembe veszi a (\*) literálként használt (\*) karaktert.


## <a name="commands"></a>Parancsok


A parancsok a lekérdezés által visszaadott eredmények vonatkozik. A függőleges vonal (|) segítségével egy parancs alkalmazása a lekért eredmények. Több parancs a függőleges vonal kell elválasztani.

> [!NOTE]
> Parancs nevek nagybetű vagy kisbetű, ellentétben a mezőnevek és az adatok is beírhatók.
>
>

### <a name="sort"></a>Rendezés
Szintaxis:

    sort field1 asc|desc, field2 asc|desc, …

Adott mezők szerint rendezi. A növekvő vagy csökkenő sorrendben az eredmények rendezéséhez növekvő/csökkenő utótag megadása nem kötelező. Ha ki van hagyva, a *asc* rendezési sorrend feltételezi. Az a **TimeGenerated** mezőben *desc* rendezési sorrend feltételezi, így az eredményeket ad vissza a legutóbbi először alapértelmezés szerint.

### <a name="toplimit"></a>Felső/korlátot
Szintaxis:

    top number


    limit number
A legfontosabb N eredmények válasz korlátozza.

Példa:

    Type:Alert errors detected | top 10

A felső 10 megfelelő eredményeket ad vissza.

### <a name="skip"></a>Kihagyás
Szintaxis:

    skip number

Kihagyja a felsorolt eredmények számát.

Példa:

    Type:Alert errors detected | top 10 | skip 200

200 eredmény kezdődő felső 10 megfelelő eredményeket ad vissza.

### <a name="select"></a>Válassza ezt:
Szintaxis:

    select field1, field2, ...

Korlátozzák az eredményeket a mezők választja.

Példa:

    Type:Alert errors detected | select Name, Severity

Korlátozza a visszaadott eredményeken mezők *neve* és *súlyossági*.

### <a name="measure"></a>mértékcsoport
A *mérték* parancs segítségével statisztikai függvények alkalmazása a nyers keresési eredmények között. Ez nagyon fontos a beolvasandó *csoportosítási* nézetek keresztül az adatok. A mérték parancs használatakor a Naplóelemzési keresési összesített eredmények tábla jeleníti meg.

**Szintaxis:**

    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]] by groupField1 [, groupField2 [, groupField3]]  [interval interval]


    measure aggregateFunction1([aggregatedField]) [as fieldAlias1] [, aggregateFunction2([aggregatedField2]) [as fieldAlias2] [, ...]]  interval interval



Összesíti az eredményeket *csoportosítási mező*, és az összesített mérték értékek alapján számítja ki *aggregatedField*.

| Mérték statisztikai függvény | Leírás |
| --- | --- |
| *aggregateFunction* |Az összesítő függvény (kis-és nagybetűket) neve. A következő összesítő függvények támogatottak: száma, MAX, MIN, SUM, AVG, szórás, COUNTDISTINCT, percentilis ## vagy PCT ## (## 1 és 99 közötti érték). |
| *aggregatedField* |A mező, amely összesítve van folyamatban. Ez a mező nem kötelező megadni a COUNT összesítő függvényben, de lehet egy meglévő számmező SUM, MAX, MIN, AVG, szórás, percentilis ## vagy PCT ## (## 1 és 99 közötti érték). A aggregatedField bármelyikét is lehet a **bővítése** támogatott függvényeket. |
| *fieldAlias* |A számított összesített értéket (nem kötelező) aliasneve. Ha nincs megadva, a mező értéke **AggregatedValue**. |
| *csoportosítási mező* |A mező az eredmény beállított neve szerint vannak csoportosítva. |
| *Időköz* |Az időtartam, a formátumban:**nnnNAME**. **nnn**az a pozitív egész szám. **NÉV** időköz neve. Támogatott intervallumnál nevek kis-és nagybetűket, és a következőket tartalmazzák: másodperc [S], [S] EZREDMÁSODPERCES perc [S], [S] ÓRÁBAN nap [S], [S] hónap és év [S]. |

Az intervallum-beállítás csak akkor használható dátum/idő csoport mezőket (például *TimeGenerated* és *TimeCreated*). Jelenleg ez a szolgáltatás nem kényszeríti ki, de a háttérben átadott dátum/idő nélküli mező futásidejű hibát okoz. A sémaérvényesítés megadásuk után a szolgáltatás API elutasítja a lekérdezéseket, amelyekkel a dátum/idő nélkül mezők időköz összesítés céljából. Az aktuális *mérték* implementációja időköz csoportosítási támogat minden összesítő függvény.

Ha a BY záradék meg van adva, de időközönkénti van megadva (második szintaxis), a *TimeGenerated* alapértelmezés szerint feltételezett mező.

Példák:

**1. példa**

    Type:Alert | measure count() as Count by ObjectId

Csoportosítja a riasztásokat *ObjectID*, és kiszámítja az egyes riasztások számát. Az összesített értéket adja vissza a rendszer a *száma* mező (másodlagos).

**2. példa**

    Type:Alert | measure count() interval 1HOUR

Csoportosítja a riasztásokat 1 órás időközönként használatával a *TimeGenerated* mezőben, majd minden egyes időszakban riasztások számát adja vissza.

**3. példa**

    Type:Alert | measure count() as AlertsPerHour interval 1HOUR

Ugyanaz, mint az előző példában, de összesített mező alias (*AlertsPerHour*).

**4. példa**

    * | mérték count() TimeCreated időköz 5DAYS által

Csoportosítja az eredményeket 5 napos időközönként használatával a *TimeCreated* mezőben, majd minden egyes időszakban eredmények számát adja vissza.

**5. példa**

    Type:Alert | measure max(Severity) by WorkflowName

A riasztások munkaterhelés név szerint csoportosítja, és minden munkafolyamat riasztás súlyossága maximális értékét adja vissza.

**6. példa**

    Type:Alert | measure min(Severity) by WorkflowName

Ugyanaz, mint az előző példában, de a *min* függvény összesíteni.

**7 – példa**

    Type:Perf | measure avg(CounterValue) by Computer

Számítógép által telj csoportok, és kiszámítja az átlagos (átlag).

**8 – példa**

    Type:Perf | measure sum(CounterValue) by Computer

Ugyanaz, mint az előző példában, de használja *sum*.

**Példa 9**

    Type:Perf | measure stddev(CounterValue) by Computer

Ugyanaz, mint az előző példában, de használja *stddev*.

**10 – példa**

    Type:Perf | measure percentile70(CounterValue) by Computer

Ugyanaz, mint az előző példában, de használja *percentile70*.

**11 – példa**

    Type:Perf | measure pct70(CounterValue) by Computer

Ugyanaz, mint az előző példában, de használja *pct70*. Vegye figyelembe, hogy *PCT ##* aliasneve csak *PERCENTILIS ##* függvény.

**Példa 12**

    Type:Perf | measure avg(CounterValue) by Computer, CounterName

A Teljesítményfigyelő először csoportosítása számítógép majd CounterName, és kiszámítja az átlagos (átlag).

**13 – példa**

    Type:Alert | measure count() as Count by WorkflowName | sort Count desc | top 5

Lekérdezi a riasztások maximális számának felső öt munkafolyamatok.

**14 – példa**

    * | mérték countdistinct(Computer) típus szerint

Megjeleníti az egyedi jelentés az egyes számítógépek számát.

**15 – példa**

    * | mérték countdistinct(Computer) időköz 1 óra

Megjeleníti az óránkénti jelentett egyedi számítógépek számát.

**16 – példa**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total” | measure avg(CounterValue) by Computer Interval 1HOUR
```

Csoportosítja a számítógép a processzoridő %, és minden átlagát adja vissza.

**17 – példa**

    Type:W3CIISLog | measure max(TimeTaken) by csMethod Interval 5MINUTES

W3CIISLog csoportok módszerrel, és legfeljebb 5 percenként adja vissza.

**18 – példa**

```
Type:Perf CounterName=”% Processor Time” InstanceName=”_Total”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer Interval 1HOUR
```

Processzoridő %-a számítógép csoportosítja, és adja vissza a minimális, átlagos, 75. percentilis és maximális óránkénti.

**19 – példa**

```
Type:Perf CounterName=”% Processor Time”  | measure min(CounterValue) as MIN, avg(CounterValue) as AVG, percentile75(CounterValue) as PCT75, max(CounterValue) as MAX by Computer, InstanceName Interval 1HOUR
```

Csoportosítja a százalékos processzoridő először számítógépet, majd példány nevét, és adja vissza a minimális, átlagos, 75. percentilis és maximális óránkénti.

**20 – példa**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | measure max(product(CounterValue,60)) as MaxDWPerMin by InstanceName Interval 1HOUR
```

Kiszámítja a maximális Lemezírások / perc minden lemez a számítógépen.

### <a name="where"></a>Ha
Szintaxis:

```
**where** AggregatedValue>20
```

Csak akkor használható után egy *mérték* további szűréséhez a összesített parancs eredménye, hogy a *mérték* aggregátumfüggvény által előállított.

Példák:

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) as MAXCPU by Computer

    Type:Perf CounterName:"% Total Run Time" | Measure max(CounterValue) by Computer | where (AggregatedValue>50 and AggregatedValue<90)



### <a name="dedup"></a>A Deduplikáció
Szintaxis:

    Dedup FieldName

Minden egyedi értéket a megadott mező található első dokumentumát adja vissza.

Példa:

    Type=Event | Dedup EventID | sort TimeGenerated DESC

Ez a példa egy esemény (az utolsó esemény) / EventID adja vissza.

### <a name="join"></a>Csatlakozás
Egyetlen eredménykészletet kialakításához két lekérdezések eredményeit csatlakozik.  Támogatja a következő táblázatban ismertetett többféle illesztési típust.

| Összekapcsolás típusa | Leírás |
|:--|:--|
| belső | Mindkét lekérdezések csak rögzíti a megfelelő érték visszaadása. |
| külső | Mindkét lekérdezések összes rekordját adja vissza.  |
| balra  | Bal oldali lekérdezés és az adatokat a megfelelő lekérdezés összes rekordját adja vissza. |


- Illesztések jelenleg nem támogatja lekérdezések, amelyek tartalmazzák a **IN** kulcsszóval, a **mérték** parancs vagy a **bővítése** parancsot, ha irányul-e a mező a jobb oldali lekérdezésből.
- Csak egy mező jelenleg szerepel a csatlakozzon.
- Egyetlen keresés nem tartozhat egynél több illesztési.

**Szintaxis**

```
<left-query> | JOIN <join-type> <left-query-field-name> (<right-query>) <right-query-field-name>
```

**Példák**

Bemutatják a különböző csatlakozási típusok, fontolja meg egy egyéni napló MyBackup_CL hívása szívverése az egyes számítógépek csatlakoztatása adattípust gyűjtött.  Ezek eltérőek a következő adatokkal rendelkeznek.

`Type = MyBackup_CL`

| TimeGenerated | Computer | LastBackupStatus |
|:---|:---|:---|
| 4/20/2017 01:26:32.137 AM | Srv01.contoso.com | Sikeres |
| 4/20/2017 02:13:12.381 AM | srv02.contoso.com | Sikeres |
| 4/20/2017 02:13:12.381 AM | srv03.contoso.com | Hiba |

`Type = Hearbeat`(Csak megjelenjen egy része)

| TimeGenerated | Computer | ComputerIP |
|:---|:---|:---|
| 4/21/2017 12:01:34.482 PM | Srv01.contoso.com | 10.10.100.1 |
| 4/21/2017 12:02:21.916 PM | srv02.contoso.com | 10.10.100.2 |
| 4/21/2017 12:01:47.373 PM | srv04.contoso.com | 10.10.100.4 |

#### <a name="inner-join"></a>belső illesztés

`Type=MyBackup_CL | join inner Computer (Type=Heartbeat) Computer`

Ha a számítógép mező megegyezik a mindkét adattípusok adja vissza a következő rekordokat.

| Computer| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Sikeres | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Szívverés |
| srv02.contoso.com | 4/20/2017 02:13:12.381 AM | Sikeres | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Szívverés |


#### <a name="outer-join"></a>külső illesztés

`Type=MyBackup_CL | join outer Computer (Type=Heartbeat) Computer`

A következő rekordok mindkét adattípusok adja vissza.

| Computer| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Sikeres  | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Szívverés |
| srv02.contoso.com | 4/20/2017 02:14:12.381 AM | Sikeres  | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Szívverés |
| srv03.contoso.com | 4/20/2017 01:33:35.974 AM | Hiba  | 4/21/2017 12:01:47.373 PM | | |
| srv04.contoso.com |                           |          | 4/21/2017 12:01:47.373 PM | 10.10.100.2 | Szívverés |



#### <a name="left-join"></a>bal oldali illesztés

`Type=MyBackup_CL | join left Computer (Type=Heartbeat) Computer`

A szívverés megfelelő mezőiben MyBackup_CL a következő rekordokat adja vissza.

| Computer| TimeGenerated | LastBackupStatus | TimeGenerated_joined | ComputerIP_joined | Type_joined |
|:---|:---|:---|:---|:---|:---|
| Srv01.contoso.com | 4/20/2017 01:26:32.137 AM | Sikeres | 4/21/2017 12:01:34.482 PM | 10.10.100.1 | Szívverés |
| srv02.contoso.com | 4/20/2017 02:13:12.381 AM | Sikeres | 4/21/2017 12:02:21.916 PM | 10.10.100.2 | Szívverés |
| srv03.contoso.com | 4/20/2017 02:13:12.381 AM | Hiba | | | |


### <a name="extend"></a>Bővíthető
A lekérdezések futásidejű mezők létrehozását teszi lehetővé. Vegye figyelembe, hogy futásidőben mezők nem használható a mérték paranccsal összesítési végrehajtásához.

**1. példa**

    Type=SQLAssessmentRecommendation | Extend product(RecommendationScore, RecommendationWeight) AS RecommendationWeightedScore
Jeleníti meg súlyozott SQL-kiértékelés ajánlásai javaslat pontszámot.

**2. példa**

    Type=Perf CounterName="Private Bytes" | EXTEND div(CounterValue,1024) AS KBs | Select CounterValue,Computer,KBs
A számláló értéke mutatja KB bájt helyett.

**3. példa**

    Type=WireData | EXTEND scale(TotalBytes,0,100) AS ScaledTotalBytes | Select ScaledTotalBytes,TotalBytes | SORT TotalBytes DESC
Arányosan WireData TotalBytes értékét úgy, hogy minden eredmény 0 és 100 közötti legyenek.

**4. példa**

```
Type=Perf CounterName="% Processor Time" | EXTEND if(map(CounterValue,0,50,0,1),"HIGH","LOW") as UTILIZATION
```
A Teljesítményfigyelő számlálók értékeit kisebb, mint 50 %-a alacsony, és más, magas címkéket.

**5. példa**

```
Type= Perf CounterName="Disk Writes/sec" Computer="BaconDC01.BaconLand.com" | Extend product(CounterValue,60) as DWPerMin| measure max(DWPerMin) by InstanceName Interval 1HOUR
```
Kiszámítja a maximális Lemezírások / perc minden lemez a számítógépen.

**Támogatott funkciók**

| Függvény | Leírás | Szintaxispéldáival |
| --- | --- | --- |
| ABS |A megadott érték vagy függvény abszolút értékét adja vissza. |`abs(x)` <br> `abs(-5)` |
| ARCCOS |Arkusz koszinusz az értékre, vagy egy olyan függvényt ad vissza. |`acos(x)` |
| és |Igaz értéket ad eredményként, csak ha összes operandus igaz értéked ad vissza. |`and(not(exists(popularity)),exists(price))` |
| ARCSIN |Arkusz szinusz az értékre, vagy egy olyan függvényt ad vissza. |`asin(x)` |
| Atan |Arkusz tangens az értékre, vagy egy olyan függvényt ad vissza. |`atan(x)` |
| ARCTAN2 |A téglalap alakú koordináták átalakításából a szöget adja vissza x, y koordinátáit. |`atan2(x,y)` |
| cbrt |Adatkocka gyökere. |`cbrt(x)` |
| ceil |Egész számra kerekít. |`ceil(x)`  <br> `ceil(5.6)`6 beolvasása |
| Cos |Az szög koszinuszát adja vissza. |`cos(x)` |
| COSH |A szög koszinusz hiperbolikuszát adja vissza. |`cosh(x)` |
| def |Alapértelmezett rövidítése. A "mező" mező értékét adja vissza. Ha a mező nem létezik, a megadott alapértelmezett értéket ad vissza, és az első olyan értéket eredményez ahol: `exists()==true`. |`def(rating,5)`. A def() függvény adja vissza a minősítése, vagy ha minősítés nélküli van megadva, a dokumentum a adja vissza az 5. <br> `def(myfield, 1.0)`egyenértékű `if(exists(myfield),myfield,1.0)`. |
| fok |Radiánban fokban megadva. |`deg(x)` |
| DIV |`div(x,y)`osztja x, y által. |`div(1,y)` <br> `div(sum(x,100),max(y,1))` |
| ELOSZLÁS |Két veszélyének, (pont) n dimenziós térben közötti távolság visszaadása. A teljesítmény, valamint két vagy több, ValueSource példányát veszi, és kiszámítja a két vektorok közötti. Minden egyes ValueSource számnak kell lennie. Az átadott ValueSource példányok páros számú kell lennie, és a módszer feltételezi, hogy az első félig felel meg az első vektort, és a második fele határoz meg a második vektor. |`dist(2, x, y, 0, 0)`Kiszámítja a Euclidean távolsága (0,0) és (x, y) minden dokumentumhoz. <br> `dist(1, x, y, 0, 0)`Kiszámítja a Manhattan (taxicab) távolsága (0,0) és (x, y) minden dokumentumhoz. <br> `dist(2,,x,y,z,0,0,0)`(0,0,0) Euclidean távolságát és (x, y, z) minden dokumentumhoz.<br>`dist(1,x,y,z,e,f,g)`Manhattan közötti távolság (x, y, z) és (e, f, g), ahol minden levél-e a mező nevét. |
| létezik-e |IGAZ, ha bármelyik tag értéket ad vissza a mező létezik-e. |`exists(author)`Bármely dokumentum, amely a "Szerző" mező értéke IGAZ értéket ad vissza.<br>`exists(query(price:5.00))`Eredménye IGAZ, ha a megfelelő "price", "5,00". |
| Exp |X hatványra emelve Euler tartozó számát adja vissza. |`exp(x)` |
| Emelet |Egész számra lefelé kerekítése. |`floor(x)`  <br> `floor(5.6)`5 beolvasása |
| hypo |Sqrt(sum(pow(x,2),pow(y,2))) köztes túlcsordulás vagy alulcsordulás történt nélkül adja vissza. |`hypo(x,y)`  <br> ` |
| Ha |Feltételes függvény lekérdezéseket tesz lehetővé. A `if(test,value1,value2)`, tesztelési vagy hivatkozik egy logikai érték vagy kifejezés, amely egy logikai (IGAZ vagy hamis) értéket ad vissza. `value1`az értéket ad vissza a függvény tesztelése, amelynek eredménye igaz. `value2`az értéket ad vissza a függvény vizsgálat hamis adja eredményül. Egy kifejezés lehet bármely funkció, amely a logikai értékek kimenete. Egy függvény numerikus értékeket, amelyben esetértéket 0 hamis kerül értelmezésre, vagy visszaadó karakterláncok, mely esetben üres karakterláncot a értelmezi hamis értéket ad vissza is lehet. |`if(termfreq(cat,'electronics'),popularity,42)`Ez a függvény ellenőrzi, ügyeljen rá, ha a kifejezés "electronics" cat mezőjében tartalmaz. Ha igen, majd a népszerűségét mező értékének ad vissza. Ellenkező esetben a 42 értéket adja vissza. |
| lineáris |Megvalósítja `m*x+c`, ahol m és c állandók és a x egy tetszőleges függvényben. Ez `sum(product(m,x),c)`, de nagyobb jelentőséggel hatékony, mint egy funkcióval megvalósuló. |`linear(x,m,c) linear(x,2,4)`adja vissza`2*x+4` |
| ln |A megadott függvény természetes napló adja vissza. |`ln(x)` |
| Napló |A napló a megadott függvény alap 10 adja vissza. |`log(x)   log(sum(x,100))` |
| térkép |A Maps bármely egy bemeneti függvény x minimális és maximális, a határokat is beleértve, a megadott cél belül eső értékeket. Az argumentumok minimális és maximális állandónak kell lennie. A cél argumentumok és az alapértelmezett lehet állandók vagy funkciók. Ha x értéke nem tartozik a minimális és maximális között, majd az x értéket adja vissza, vagy egy alapértelmezett értéket adja vissza, ha meg van adva az 5. argumentuma. |`map(x,min,max,target) map(x,0,0,1)`Módosítja az értékeket a 0 és 1 közé esnek. Ez az alapértelmezett 0 értékek kezelése hasznos lehet.<br> `map(x,min,max,target,default)    map(x,0,100,1,-1)`0 és 100-1 közötti értékeket, és minden más helyen módosítja a-1.<br>  `map(x,0,100,sum(x,599),docfreq(text,solr))`A kifejezés "solr" szövegmezőjében gyakorisága 0 és 100 x + 599 és egyéb értékek közötti értékeket módosításait. |
| maximális |Több beágyazott függvények vagy az állandókat, mint szerepkör argumentumokban megadott legnagyobb numerikus értéket adja vissza: `max(x,y,...)`. A max függvény is hasznos lehet a "bottoming" egy másik függvényt, vagy néhány mező megadott konstans.  Használja a `field(myfield,max)` szintaxis egy többértékű mező maximális értékét kijelöléséhez. |`max(myfield,myotherfield,0)` |
| perc |A több beágyazott függvények az állandók, mint szerepkör argumentumokban megadott minimális numerikus értéket adja vissza: `min(x,y,...)`. A min függvénnyel is hasznos lehet az állandó használatával függvényt a "felsőé" biztosít. Használja a `field(myfield,min)` szintaxis egy többértékű mező minimális értékét kijelöléséhez. |`min(myfield,myotherfield,0)` |
| MOD |Kiszámítja a függvény x függvény y modulus. |`mod(1,x)` <br> `mod(sum(x,100), max(y,1))` |
| MS |Az argumentumok között különbséget ezredmásodperc adja vissza. Dátumok van a Unix vagy POSIX idő epoch, éjféli, 1970. január 1 UTC viszonyítva. Argumentumok előfordulhat, hogy egy indexelt TrieDateField vagy állandó dátum alapján dátum matematikai nevét vagy most. `ms()`egyenértékű `ms(NOW)`, a epoch óta idő ezredmásodpercben. `ms(a)`az argumentum jelölő kor alapidőpontjának korábban óta ideje (MS) számát adja vissza. `ms(a,b)`ezredmásodperc számát adja vissza az b előtt következik be, amely `a - b`. |`ms(NOW/DAY)`<br>`ms(2000-01-01T00:00:00Z)`<br>`ms(mydatefield)`<br>`ms(NOW,mydatefield)`<br>`ms(mydatefield,2000-01-01T00:00:00Z)`<br>`ms(datefield1,datefield2)` |
| nem |A burkolt függvény logikailag megkereséséhez értéke. |`not(exists(author))`IGAZ, csak ha `exists(author)` értéke "false". |
| vagy |Logikai vagy műveletet. |`or(value1,value2)`Értéke TRUE, ha bármelyik value1 és value2 értéke true. |
| Pow |A megadott alapban megadott hatványra emel. `pow(x,y)`riasztást x, y hatványra. |`pow(x,y)`<br>`pow(x,log(y))`<br>`pow(x,0.5)`Ugyanaz, mint sqrt. |
| A termék |Több érték vagy függvények, amelyek egy vesszővel elválasztott listában megadott szorzatát adja vissza. `mul(...)`használható aliasként ennél a függvénynél. |`product(x,y,...)`<br>`product(x,2)`<br>`product(x,y)`<br>`mul(x,y)` |
| recip |Kölcsönös végzi a `recip(x,m,a,b)` végrehajtási `a/(m*x+b)`, ahol m, a, b állandók és x az önkényesen összetett függvényeket. Ha a és b egyenlő és x > = 0, a függvény, amely elutasítja azokat a növekszik x 1 maximális értéke. Az érték növelésével a és b együtt eredményezi, a teljes működnek, mint a görbe laposabb része egy mozgása. Ezek a Tulajdonságok hozzárendelheti ezt az ideális függvény kiemelése legfrissebb, ha x `rord(datefield)`. |`recip(myfield,m,a,b)`<br>`recip(rord(creationDate),1,1000,1000)` |
| RAD |Konvertálja radiánban megadott szög fokban megadva. |`rad(x)` |
| Nyomtatás |A legközelebbi egész számra kerekít. |`rint(x)`  <br> `rint(5.6)`6 beolvasása |
| EG |Egy szög szinuszát adja vissza. |`sin(x)` |
| SINH |A szög szinusz hiperbolikuszát adja vissza. |`sinh(x)` |
| Skála |A függvény x értékek arányosan, úgy, hogy a megadott minTarget és a határokat is beleértve maxTarget közötti tartoznak. A jelenlegi megvalósításától halad át az beszerzése a minimális és maximális, így kiválaszthatja a megfelelő méretezés függvény értékei. Jelenlegi implementációja nem különbözteti meg, amikor dokumentumok törölték, vagy dokumentumok érték nélküli. Ezekben az esetekben 0.0 értékeket használ. Ez azt jelenti, hogy értékek nagyobbak általában minden 0,0, ha egy is továbbra is végül 0,0 leképezni a minimális értékként. Ezekben az esetekben, egy megfelelő `map()` függvény használható megoldás 0,0 módosítsa a valódi közé eső értékre, az itt látható:`scale(map(x,0,0,5),1,2)` |`scale(x,minTarget,maxTarget)`<br>`scale(x,1,2)`Arányosan, az x értékek úgy, hogy az összes értékei 1 és 2 között lehet. |
| Sqrt |A megadott érték vagy függvény négyzetgyökét adja vissza. |`sqrt(x)`<br>`sqrt(100)`<br>`sqrt(sum(x,100))` |
| strdist |Két karakterláncok közötti távolságot számítja ki. A Lucene helyesírás-ellenőrző futtatásához, StringDistance felületet használja, és az összes elérhető csomag megvalósítások támogatja. Is lehetővé teszi az alkalmazások, hogy csatlakoztassák a saját, Solr tartozó erőforrás keresztül betöltése képességeit. strdist veszi `(string1, string2, distance measure)`. Távolság mérték lehetséges értékei:<ul><li>jw: Jaro-Winkler</li><li>Szerkesztés: Levenstein vagy Szerkesztés távolság</li><li>ngram: A NGramDistance, ha meg van adva, opcionálisan teljen ngram mérete túl. Alapértelmezett érték a 2.</li><li>FQN: Teljesen minősített osztály nevét a StringDistance felület megvalósítása. Nem-arg konstruktorral kell rendelkezniük.</li></ul> |`strdist("SOLR",id,edit)` |
| Sub |Visszaadja az x-y `sub(x,y)`. |`sub(myfield,myfield2)`<br>`sub(100,sqrt(myfield))` |
| Sum |Több érték vagy függvények, amelyek egy vesszővel elválasztott listában megadott összegét adja vissza. `add(...)`Előfordulhat, hogy használható aliasként ennél a függvénynél. |`sum(x,y,...)`<br>`sum(x,1)`<br>`sum(x,y)`<br>`sum(sqrt(x),log(y),z,0.5)`<br>`add(x,y)` |
| termfreq |A kifejezés megjelenik a mező a dokumentum alkalommal számát adja vissza. |termfreq(Text,'memory') |
| Tan |Az szög tangensét adja vissza. |`tan(x)` |
| TANH |A szög tangens hiperbolikuszát adja vissza. |`tanh(x)` |

## <a name="search-field-and-facet-reference"></a>Keresési mező, és a dimenzió hivatkozási
Naplófájl-keresési adatok kereséséhez használatakor eredményekben különböző mező és értékkorlátozást. Az adatok egy részét nem jelenhet meg nagyon leíró. Az alábbi információk segítségével megismerheti az eredményeket.

| Mező | Keresés típusa | Leírás |
| --- | --- | --- |
| A TenantId |Összes |Partíció adatok használatával. |
| TimeGenerated |Összes |Az idősor timeselectors (keresési és egyéb képernyők) használja. Azt jelöli, amikor a adat (általában az ügynökön) jött létre. Az idő ISO-formátumban van kifejezve, és mindig UTC. Esetén a meglévő instrumentation (Ez azt jelenti, hogy a napló események) alapuló típusú Ez az általában a valós idejű, a bejegyzés/sor/naplóbejegyzés naplózott. Az egyes felügyeleti csomagok segítségével vagy a felhőben (például javaslatok vagy riasztások) gyártott más típusú idő képviseli különböző. Ez az gyűjtötte a program az új adat valamilyen konfigurálása egy pillanatképet, és a javaslat riasztások előállítása azt alapján. |
| Eseményazonosító |Esemény |A Windows eseménynaplóban eseményazonosító. |
| Az eseménynaplóban talál |Esemény |Ha az esemény naplózásának Windows eseménynaplóba. |
| EventLevelName |Esemény |Kritikus vagy figyelmeztetési/információk/sikeres |
| EventLevel |Esemény |A kritikus vagy figyelmeztetési/információk/sikeres számérték (inkább EventLevelName könnyebb/olvashatóbb lekérdezések). |
| SourceSystem |Összes |Ha az adatok származik (a csatolása mód a szolgáltatás). Ilyen például a Microsoft System Center Operations Manager és az Azure Storage. |
| ObjectName |PerfHourly |Windows teljesítményobjektum nevét. |
| Példánynév |PerfHourly |Windows teljesítményszámláló példány neve. |
| CounteName |PerfHourly |Windows teljesítményszámláló neve. |
| ObjectDisplayName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |A teljesítményadat-gyűjtési szabály az Operations Manager által megcélzott objektum megjelenített neve. Az Operational insights vagy szemben, amelyek a riasztás generálása felderített objektum megjelenített neve is lehet. |
| RootObjectName |PerfHourly, ConfigurationAlert, ConfigurationObject, ConfigurationObjectProperty |A szülő (kettős birtokosi kapcsolat) egy teljesítménygyűjtési szabály az Operations Manager által megcélzott objektum szülőjének megjelenítendő nevét. Az Operational insights vagy szemben, amelyek a riasztás generálása felderített objektum megjelenített neve is lehet. |
| Computer |A legtöbb |Számítógép neve, amely az adatok tartozik. |
| Eszköznév |ProtectionStatus |Az adatok számítógép neve (megegyezik az "Számítógép") tartozik. |
| DetectionId |ProtectionStatus | |
| ThreatStatusRank |ProtectionStatus |Fenyegetés állapot dimenziószáma fenyegetés állapotát interfésztípus numerikus megjelölése. A HTTP válaszkódot hasonlóan a rangsorolási rendelkezik a számok között lévő hézagokat (ezért nem fenyegetések nem 150 és nem 100 vagy 0), hagyja a hely az új állapotok hozzáadása. Fenyegetések és védelmi állapota összesítését a levelezéshez, hogy megjelenítése a legrosszabb állapota, amely a számítógép nem történt a kiválasztott időszakra vonatkozóan. A számok rangsorolja a különböző állapotok, így megtekintheti a rekord a legmagasabb számát. |
| ThreatStatus |ProtectionStatus |A maps 1:1 ThreatStatusRank ThreatStatus, leírása. |
| TypeofProtection |ProtectionStatus |A számítógépen észlelt kártevőirtó termék: none, a Microsoft kártevő-eltávolító eszköz, a Forefront, és így tovább. |
| ScanDate |ProtectionStatus | |
| SourceHealthServiceId |ProtectionStatus, RequiredUpdate |A számítógép ügynök Állapotfigyelő szolgáltatás azonosítója. |
| HealthServiceId |A legtöbb |A számítógép ügynök Állapotfigyelő szolgáltatás azonosítója. |
| ManagementGroupName |A legtöbb |Az ügynököt az Operations Manager csatlakoztatott felügyeleti csoport neve. Ellenkező esetben is null vagy üres. |
| Objektumtípus |ConfigurationObject |Írja be (ahogy az Operations Manager felügyeleti csomag/osztálya) ehhez az objektumhoz, Log Analyticshez konfigurációelemzés által felderített. |
| UpdateTitle |RequiredUpdate |A frissítés talált nincs telepítve a neve. |
| PublishDate |RequiredUpdate |Ha a frissítés közzététele a Microsoft Update. |
| Kiszolgáló |RequiredUpdate |Az adatok számítógép neve (megegyezik az "Számítógép") tartozik. |
| Product |RequiredUpdate |Termék, amely a frissítés vonatkozik. |
| UpdateClassification |RequiredUpdate |Frissítés (például kumulatív frissítést vagy szervizcsomagot) típusú. |
| KBID |RequiredUpdate |KB cikk azonosítója, amely leírja a bevált gyakorlat vagy frissítés. |
| WorkflowName |ConfigurationAlert |A szabály vagy figyelő, amely a riasztás előállított neve. |
| Súlyosság |ConfigurationAlert |A riasztás súlyossága. |
| Prioritás |ConfigurationAlert |A riasztás prioritását. |
| IsMonitorAlert |ConfigurationAlert |Ez a riasztás generálja (true) figyelőt vagy szabályt (hamis)? |
| AlertParameters |ConfigurationAlert |A Naplóelemzési riasztás paraméterekkel rendelkező XML. |
| Környezet |ConfigurationAlert |A Naplóelemzési riasztás kontextusú XML. |
| Számítási feladat |ConfigurationAlert |Technológia vagy munkaterhelés –, hogy a riasztás hivatkozik. |
| AdvisorWorkload |Ajánlás |Technológia vagy terhelést, az ajánlás hivatkozik. |
| Leírás |ConfigurationAlert |Riasztás leírása (rövid). |
| DaysSinceLastUpdate |UpdateAgent |Hány nappal ezelőtt (viszonyítva TimeGenerated a rekord) volt az ügynök telepítése minden frissítés a Windows Server Update Service (WSUS) vagy a Microsoft Update? |
| DaysSinceLastUpdateBucket |UpdateAgent |DaysSinceLastUpdate, az idő gyűjtők, hogy mennyivel számítógép utolsó telepített bármilyen frissítést a WSUS vagy a Microsoft Update szolgáltatással a kategorizálási alapján. |
| AutomaticUpdateEnabled |UpdateAgent |Automatikus frissítés ellenőrzése engedélyezve van-e az ügynökön? |
| AutomaticUpdateValue |UpdateAgent |Automatikus frissítés ellenőrzi automatikusan letölti és telepíti, csak letöltéséhez vagy csak ellenőrizze set? |
| WindowsUpdateAgentVersion |UpdateAgent |A Microsoft Update-ügynök verziószáma. |
| WSUSServer |UpdateAgent |Melyik WSUS-kiszolgáló a Windows update agent célzott? |
| OSVersion |UpdateAgent |Az operációs rendszer verziója a Windows update agent fut. |
| Név |A javaslat, ConfigurationObjectProperty |Neve/cím a javaslat, vagy a Naplóelemzési konfigurációelemzés tulajdonságának nevével. |
| Érték |ConfigurationObjectProperty |A Naplóelemzési konfigurációelemzés tulajdonság értéke. |
| KBLink |Ajánlás |A Tudásbázis következő cikkét, amely leírja a bevált gyakorlat vagy frissítés URL-címe. |
| RecommendationStatus |Ajánlás |A javaslatok még rekordok módosul, a keresési indexhez nem csak hozzáadott néhány típusok közül. Ez az állapot változik-e a javaslat aktív/megnyitása, vagy ha Log Analyticshez észleli, hogy le van oldva. |
| RenderedDescription |Esemény |Megjelenített (feltöltött paraméterekkel újrafelhasznált szöveg) egy Windows esemény leírása. |
| ParameterXml |Esemény |XML-adatok szakaszában egy Windows-esemény (az eredményhalmaznak megfelelően Eseménynapló) paraméterekkel. |
| EventData |Esemény |A Windows-esemény (az eredményhalmaznak megfelelően Eseménynapló) teljes adatszakasza XML. |
| Forrás |Esemény |Az eseményt létrehozó eseménynapló-forrás. |
| EventCategory |Esemény |Az esemény, a Windows Eseménynapló-ről kategóriáját. |
| Felhasználónév |Esemény |A felhasználónév, a Windows-esemény (általában NT AUTHORITY\LOCALSYSTEM). |
| SampleValue |PerfHourly |Az óránkénti összesítés teljesítményszámláló átlagos értékét. |
| Perc |PerfHourly |Minimális értékének a teljesítmény számláló óránkénti összesítés számú óránként történik. |
| Maximum |PerfHourly |A teljesítmény számláló óránkénti összesítés óránkénti időszak maximális érték. |
| Percentile95 |PerfHourly |A teljesítmény számláló óránkénti összesítés a óránkénti alatt 95. percentilis. |
| SampleCount |PerfHourly |Hány nyers teljesítményszámláló-minták a óránkénti összesített rekord létrehozásához használt. |
| Fenyegetések |ProtectionStatus |Kártevő szoftver található neve. |
| StorageAccount |W3CIISLog |Az Azure Storage-fiók a napló be lett beolvasva. |
| AzureDeploymentID |W3CIISLog |A felhőszolgáltatás a napló az Azure-telepítés azonosítója, amelyhez tartozik. |
| Szerepkör |W3CIISLog |Az Azure felhőszolgáltatást a naplót a szerepkör tartozik. |
| RoleInstance |W3CIISLog |A naplófájl tartozik Azure szerepkör RoleInstance. |
| sSiteName |W3CIISLog |IIS-webhely, amely a naplófájl tartozik (metabázis jelöléssel); az eredeti napló s-sitename mező. |
| sComputerName |W3CIISLog |Az eredeti napló s-computername mező. |
| a sIP |W3CIISLog |Kiszolgáló IP-cím a HTTP kérelem küldtünk. Az eredeti napló s-ip mező. |
| csMethod |W3CIISLog |HTTP-metódus (például GET vagy POST) a HTTP-kérelmet az ügyfél által használt. A cs-method az eredeti tartalmaz. |
| cIP |W3CIISLog |Ügyfél IP-cím a HTTP kérelem érkezett. Az eredeti napló ügyfél IP-címe mező. |
| csUserAgent |W3CIISLog |Az ügyfél által megadott HTTP-ügynök (böngészőben vagy más módon). A cs-felhasználói ügynök az eredeti tartalmaz. |
| scStatus |W3CIISLog |Az ügyfél a kiszolgáló által visszaadott HTTP-állapotkód: (például 200/403-as/500). A cs-állapot az eredeti tartalmaz. |
| TimeTaken |W3CIISLog |Milyen hosszú (ezredmásodpercben), amely a kérelem végrehajtása befejeződik. Az eredeti napló timetaken mező. |
| csUriStem |W3CIISLog |Relatív URI-Azonosítót (gazdagép címe, amely van, / keresése), amely a kért. Az eredeti napló cs-lévő egyedi azonosítóhoz mező. |
| csUriQuery |W3CIISLog |URI-lekérdezésre. URI-lekérdezések szükségesek csak dinamikus lapok, például az ASP-lapok, így ez a mező általában statikus lapok kötőjellel tartalmazza. |
| sPort |W3CIISLog |Kiszolgáló portja, amelyek a HTTP-kérelem elküldve (és, hogy az IIS figyeli, mivel azt felvételre azt). |
| csUserName |W3CIISLog |Hitelesített felhasználó nevét, ha a kérés hitelesített, és nem névtelen. |
| csVersion |W3CIISLog |A HTTP protokoll verziója, amelyet a kérés (például HTTP/1.1). |
| csCookie |W3CIISLog |A cookie-adatok. |
| csReferer |W3CIISLog |A felhasználó által utoljára felkeresett helyet. Ez a hely szolgáltatta a hivatkozást a jelenlegi helyre. |
| csHost |W3CIISLog |Állomásfejléc (például www.mysite.com), amely a kért. |
| scSubStatus |W3CIISLog |A részállapot hibakód. |
| scWin32Status |W3CIISLog |Windows állapotkódját. |
| csBytes |W3CIISLog |Az ügyfél által az a kiszolgáló a kérelemben küldött bájtok. |
| scBytes |W3CIISLog |Vissza az ügyfél a kiszolgálótól kapott válasz visszaadott bájtok. |
| ConfigChangeType |Konfigurációváltozás |Olyan változást (például WindowsServices/szoftverfrissítési). |
| ChangeCategory |Konfigurációváltozás |A változás (módosított/hozzáadott/eltávolítva) típusa. |
| SoftwareType |Konfigurációváltozás |(Frissítés/alkalmazás) szoftver típusát. |
| SoftwareName |Konfigurációváltozás |A szoftverekhez (csak a szoftvermódosítások) neve. |
| Közzétevő |Konfigurációváltozás |Szállítói, aki közzéteszi a szoftverekhez (csak a szoftvermódosítások). |
| SvcChangeType |Konfigurációváltozás |Olyan változást, amely a Windows-szolgáltatás (állam/StartupType/útvonal/szolgáltatásfiók) lett alkalmazva. Ez a tulajdonság csak a Windows-szolgáltatás módosításait vonatkozik. |
| SvcDisplayName |Konfigurációváltozás |A módosított szolgáltatás megjelenített neve. |
| SvcName |Konfigurációváltozás |Szolgáltatás neve a megváltozott. |
| SvcState |Konfigurációváltozás |A szolgáltatás új (aktuális) állapotát. |
| SvcPreviousState |Konfigurációváltozás |Az előző (csak akkor érvényes, ha a szolgáltatás állapota megváltozott) szolgáltatás állapotának ismert. |
| SvcStartupType |Konfigurációváltozás |A szolgáltatás indítási típusa. |
| SvcPreviousStartupType |Konfigurációváltozás |Előző szolgáltatás indítási típusa (csak akkor érvényes, ha a szolgáltatás indítási típusának megváltoztatása). |
| SvcAccount |Konfigurációváltozás |A szolgáltatásfiók. |
| SvcPreviousAccount |Konfigurációváltozás |Előző szolgáltatásfiók (csak akkor érvényes, ha megváltozott a szolgáltatásfiók). |
| SvcPath |Konfigurációváltozás |A Windows-szolgáltatás a végrehajtható fájl elérési útja. |
| SvcPreviousPath |Konfigurációváltozás |Előző elérési utat a végrehajtható fájl, a Windows-szolgáltatás (csak akkor érvényes, ha megváltoztatta). |
| SvcDescription |Konfigurációváltozás |A szolgáltatás leírása. |
| Előző |Konfigurációváltozás |A szoftver (telepített/nem telepített/előző verzió) korábbi állapotába. |
| Aktuális |Konfigurációváltozás |Ez a szoftver (verzió: telepítve vagy nem telepített/current) aktuális állapotát. |

## <a name="next-steps"></a>Következő lépések
További információt a naplófájl-keresések feldolgozásához:

* A [naplókeresések](log-analytics-log-searches.md) megismerése a megoldások által összegyűjtött részletes információk megtekintéséhez.
* Használjon [Naplóelemzési az egyéni mezők](log-analytics-custom-fields.md) napló keresések kiterjeszteni.
