---
title: Az Azure Log Analyticsben szöveges adatok elemzése |} A Microsoft Docs
description: A Log Analytics-rekordok adatok elemzésekor, amikor az adatok be lett töltve és lekérdezés lekéri azt, amikor a relatív előnyeit összehasonlítása az egyes különböző lehetőségeit ismerteti.
documentationcenter: ''
author: bwren
manager: carmonm
editor: tysonn
ms.service: log-analytics
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/04/2018
ms.author: bwren
ms.openlocfilehash: 0d589156824c7b9f3f6a8c31591d69479d11780a
ms.sourcegitcommit: e7312c5653693041f3cbfda5d784f034a7a1a8f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/11/2019
ms.locfileid: "54214130"
---
# <a name="parse-text-data-in-log-analytics"></a>A Log Analytics szöveges adatok elemzése
Egyes Log Analytics által gyűjtött adatok egy-egy tulajdonság több információt tartalmazni fogja. Ezek az adatok elemzése több tulajdonságot be megkönnyíti a lekérdezések használata. Ilyenek például a [egyéni napló](../../log-analytics/log-analytics-data-sources-custom-logs.md) , amely egy teljes naplóbejegyzés több értékkel rendelkező gyűjti be egy-egy tulajdonság. Hozzon létre külön tulajdonságait a különböző értékeket, kereshet és az egyes összesített.

Ez a cikk ismerteti az adatok a Log Analytics-rekordok elemzésekor, amikor az adatok betöltött és lekérdezés lekéri azt, amikor a relatív előnyeit összehasonlítása az egyes különböző lehetőségeket.


## <a name="parsing-methods"></a>Elemzési módszer
Betöltési idő, amikor az adatgyűjtés akár lekérdezés értelmezni tudja az adatokat meg, amikor az adatok elemzése az egy lekérdezést. Minden egyes stratégia rendelkezik egyedi előnyökkel, az alább ismertetett.

### <a name="parse-data-at-collection-time"></a>Adatok elemzése a gyűjtemény időpontban
Adatok elemezni a gyűjtemény időpontban, amikor konfigurálja [egyéni mezők](../../log-analytics/log-analytics-custom-fields.md) új tulajdonságok létrehozása, amely a táblában. Lekérdezések nem kell minden olyan elemzési logikát tartalmazó egyszerűen, és használja ezeket a tulajdonságokat a táblázat bármely más mező.

Ez a módszer előnyei a következők:

- A lekérdezés óta nem kell tartalmaznia az összegyűjtött adatok elemzése a lekérdezésben szereplő parancsok könnyebben.
- A lekérdezés óta jobb lekérdezési teljesítmény-elemzés végrehajtásához nem szükséges.
 
Ezt a módszert hátrányai a következők:

- Meg kell határozni előre. Nem tartalmazzák a korábban gyűjtött adatokat.
- Ha módosítja az elemzési logikát, azt csak az új adatokra vonatkozik.
- Kevesebb elemzési lehetőségeket, mint amennyi a lekérdezésekben.
- Várakozási idő adatok gyűjtésére szolgáló nő.
- Hibák kezelése nehézkes lehet.


### <a name="parse-data-at-query-time"></a>Adatok elemzése a lekérdezéskor
Amikor adatainak elemzése a lekérdezéskor, logikát tartalmazó adatok elemzése több mezőkbe a lekérdezésben. Maga a tényleges táblázat nem módosul.

Ez a módszer előnyei a következők:

- Minden olyan adatot, beleértve a korábban gyűjtött adatok is vonatkozik.
- Logikai módosítások azonnal alkalmazhatók összes adatot.
- A rugalmas elemzési lehetőségeket adott datové struktury előre meghatározott logikát.
 
Ezt a módszert hátrányai a következők:

- Több összetett lekérdezések szükségesek. Ez a enyhíthető [funkciók szimulálása a tábla](#Use-function-to-simulate-a-table).
- Több lekérdezés elemzés logikáját kell replikálni. Megoszthat egy logikai függvényeken keresztül.
- Terhelés hozhat létre, ha nagyon nagy rekord összetett logikát futtat csoportokat (több milliárd rekordon,).

## <a name="parse-data-as-its-collected"></a>Mivel a begyűjtésük adatainak elemzése
Lásd: [egyéni mezőket hozhat létre a Log Analytics](../../log-analytics/log-analytics-custom-fields.md) adatok elemzésekor, mivel a begyűjtésük részleteiért. Ez a tábla, csakúgy, mint minden más tulajdonság lekérdezések által használható egyéni tulajdonságok hoz létre.

## <a name="parse-data-in-query-using-patterns"></a>A minták használatával lekérdezés adatainak elemzése
Elemezni kívánt adatokat a rekordok között ismétlődő minta segítségével azonosítható, ha a különböző operátorok használhatja a [adatkezelő lekérdezési nyelv](/azure/kusto/query/) be egy vagy több új tulajdonságok a konkrét adat kibontásához.

### <a name="simple-text-patterns"></a>Egyszerű szöveges mintákkal

Használja a [elemezni](/azure/kusto/query/parseoperator) operátor szerepel a lekérdezés egy vagy több egyéni tulajdonságot, amely egy karakterlánc-kifejezés kinyerésének létrehozásához. A minta azonosítását és a Tulajdonságok létrehozása a nevét kell megadni. Ez különösen hasznos adatokhoz hasonló módon a kulcs-érték karakterláncokkal _kulcs = érték_.

Fontolja meg egy egyéni naplót a következő formátumban adatokkal.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A következő lekérdezés az egyes tulajdonságok szeretné elemezni ezeket az adatokat. Vonal rajzolása _projekt_ bekerül a visszaadandó egyetlen számított tulajdonságait, és nem _RawData_, amely az egyéni napló, az egész bejegyzésre egyetlen tulajdonságra.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

Az alábbiakban a egy másik példa, amely megtöri a felhasználó neve, az egyszerű felhasználónév a _AzureActivity_ tábla.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguláris kifejezések
Ha az adatok egy reguláris kifejezéssel azonosíthatók, használhatja [reguláris kifejezéseket használó függvények](/azure/kusto/query/re2) egyéni értékeket kibontásához. Az alábbi példában [kinyerése](/azure/kusto/query/extractfunction) kezdetét vette az a _UPN_ mezőt _AzureActivity_ rögzíti, és visszatér a különböző felhasználók.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

Ahhoz, hogy hatékony elemzési nagy méretekben Log Analytics használja re2 verziója reguláris kifejezések, amely hasonló, de nem egyeznek, amelyek a reguláris kifejezés variantní hodnoty. Tekintse meg a [re2 kifejezés szintaxisa](https://aka.ms/kql_re2syntax) részleteiről.


## <a name="parse-delimited-data-in-a-query"></a>A lekérdezésben tagolt adatainak elemzése
Tagolt adatok mezőket egy vesszőt a CSV-fájl például közös karakterrel elválasztó. Használja a [felosztása](/azure/kusto/query/splitfunction) függvény elemezni a tagolt adatok egy Ön által megadott elválasztó használatával. Ez a [kiterjesztése](/azure/kusto/query/extendoperator) üzemeltető minden mező vissza az adatokat, vagy adja meg a kimenetben szereplő egyedi mezők.

> [!NOTE]
> Split egy dinamikus objektumot ad vissza, mert az eredményeket kell explicit módon konvertálható adattípusok karakterlánc például operátorok és szűrőkben használható.

Fontolja meg egy egyéni naplót a következő CSV formátumban adatokkal.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A következő lekérdezés az adatok elemzése és két a számított tulajdonságokat foglalják össze. Az első sor bontja a _RawData_ tulajdonság be egy karakterlánc-tömbben. A következő sorokban minden egyes nevet ad egyes tulajdonságokat, és hozzáadja őket a kimeneti függvények használata alakíthatja át őket a megfelelő adattípust.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields  = split(RawData, ',')
| extend EventTime  = todatetime(CSVFields[0])
| extend Code       = toint(CSVFields[1]) 
| extend Status     = tostring(CSVFields[2]) 
| extend Message    = tostring(CSVFields[3]) 
| where getyear(EventTime) == 2018
| summarize count() by Status,Code
```

## <a name="parse-predefined-structures-in-a-query"></a>A lekérdezésben előre meghatározott struktúrák elemzése
Ha az adatok ismert szerkezetben vannak formázva, akkor lehet az a funkciók egyikét használhatják a [adatkezelő lekérdezési nyelv](/azure/kusto/query/) előre meghatározott struktúrák elemzéséhez:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [XML](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [URL-cím](/azure/kusto/query/parseurlfunction)
- [URL-lekérdezés](/azure/kusto/query/parseurlqueryfunction)
- [Elérési út](/azure/kusto/query/parsepathfunction)
- [Felhasználói ügynök](/azure/kusto/query/parse-useragentfunction)
- [Verzió karakterlánca](/azure/kusto/query/parse-versionfunction)

A következő példalekérdezés elemzi a _tulajdonságok_ mezőjében a _AzureActivity_ táblában, amely a JSON-ban van strukturálva. Az eredményeket elmenti azt egy dinamikus nevű tulajdonsággal _parsedProp_, amely tartalmazza az egyes megnevezett érték a JSON-fájlban. Ezekkel az értékekkel szűrése és összegzése a lekérdezés eredményeit.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Ezen elemzési funkciók lehet processzorigényes, így azok kell használni, csak akkor, amikor a lekérdezést használ több tulajdonságot a formázott adatokból. Ellenkező esetben egyszerű a feldolgozás egyező minta gyorsabbak lesznek.

Az alábbi példa a tartományvezérlő TGT előhitelesítési típus bontását mutatja. A típus csak a EventData mező, amely XML-karakterlánc, létezik, de nem más, ebben a mezőben van volt szükség adatok. Ebben az esetben [elemezni](/azure/kusto/query/parseoperator) szolgál ki a szükséges adatot kivételezési.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Függvény használatával egy tábla szimulálása
Előfordulhat, hogy több lekérdezés, amely egy adott tábla ugyanazon elemzés végrehajtásához. Ebben az esetben [függvény létrehozása](functions.md) , amelyek az elemzési lekérdezés logikai ahelyett elemzett adatait adja vissza. Ezután a függvény aliasa az eredeti tábla helyett használhatja más lekérdezések.

Fontolja meg a fenti példában az egyéni napló vesszővel tagolt. Több lekérdezés az elemzett adatok használatához hozzon létre egy függvényt, használja a következő lekérdezést, és mentse az alias _MyCustomCSVLog_.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Most már használhatja az alias _MyCustomCSVLog_ helyett a tényleges tábla neve a következőhöz hasonló lekérdezést.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>További lépések
* Ismerje meg [lekérdezések naplózását](log-query-overview.md) az adatforrások és megoldások gyűjtött adatok elemzéséhez.