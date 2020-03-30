---
title: Szöveges adatok elemzése az Azure Monitor naplóiban | Microsoft dokumentumok
description: A naplóadatok Azure Monitor-rekordokban történő elemzésének különböző lehetőségeit ismerteti, amikor az adatokat beolvasják, és amikor lekérik egy lekérdezésben, összehasonlítva az egyes adatok relatív előnyeit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 12/04/2018
ms.openlocfilehash: d7a37d51c411488231205fd036f9a287f5206ce5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "77672446"
---
# <a name="parse-text-data-in-azure-monitor-logs"></a>Szöveges adatok elemzése az Azure Monitor naplóiban
Az Azure Monitor által gyűjtött egyes naplóadatok több adatot tartalmaznak egyetlen tulajdonságban. Ha ezeket az adatokat több tulajdonságra is elemzi, könnyebben használható a lekérdezésekben. Gyakori példa egy [egyéni napló,](../../log-analytics/log-analytics-data-sources-custom-logs.md) amely egy teljes naplóbejegyzést gyűjt több értékkel egyetlen tulajdonságba. Ha külön tulajdonságokat hoz létre a különböző értékekhez, mindegyiken kereshet és összesíthet.

Ez a cikk ismerteti a különböző lehetőségeket a naplóadatok elemzéséhez az Azure Monitorban, amikor az adatok at, és amikor lekéri a lekérdezésben, összehasonlítva az egyes relatív előnyeit.


## <a name="parsing-methods"></a>Elemzési módszerek
Az adatokat az adatok gyűjtésekor történő betöltéskor, vagy lekérdezéskor elemezheti, amikor az adatokat lekérdezéssel elemzi. Minden stratégiának egyedi előnyei vannak az alábbiakban leírtak szerint.

### <a name="parse-data-at-collection-time"></a>Adatok elemzése gyűjtéskor
Amikor adatgyűjtéskor elemzi az adatokat, konfigurálja [azokat az egyéni mezőket,](../../log-analytics/log-analytics-custom-fields.md) amelyek új tulajdonságokat hoznak létre a táblában. A lekérdezéseknek nem kell elemzési logikát tartalmazniuk, és egyszerűen csak a tábla bármely más mezőjéhez kell használniuk ezeket a tulajdonságokat.

Ennek a módszernek az előnyei a következők:

- Könnyebb lekérdezni az összegyűjtött adatokat, mivel nem kell elemzési parancsokat felvenni a lekérdezésbe.
- Jobb lekérdezési teljesítmény, mivel a lekérdezés nem kell elemzés.
 
Ennek a módszernek a hátrányai a következők:

- Előre meg kell határozni. Nem lehet olyan adatokat megadni, amelyek már összegyűjtöttek.
- Ha módosítja az elemzési logikát, az csak az új adatokra vonatkozik.
- Kevesebb elemzési lehetőség, mint a lekérdezésekben elérhető.
- Növeli az adatgyűjtés késési idejét.
- A hibákat nehéz lehet kezelni.


### <a name="parse-data-at-query-time"></a>Adatok elemzése lekérdezési időben
Amikor lekérdezéskor elemezzük az adatokat, a lekérdezésben logika is szerepel az adatok több mezőben való elemzéséhez. Maga a tényleges tábla nincs módosítva.

Ennek a módszernek az előnyei a következők:

- Minden adatra vonatkozik, beleértve a már összegyűjtött adatokat is.
- A logika változásai azonnal alkalmazhatók az összes adatra.
- Rugalmas elemzési lehetőségek, beleértve az adott adatstruktúrák előre definiált logikáját.
 
Ennek a módszernek a hátrányai a következők:

- Összetettebb lekérdezéseket igényel. Ez csökkenthető a [függvények](#use-function-to-simulate-a-table)használatával a tábla szimulálásához .
- Több lekérdezésben kell replikálnia az elemzési logikát. Megoszthatja néhány logika a funkciókon keresztül.
- Összetett logika futtatásakor nagyon nagy rekordhalmazok (több milliárd rekord) futtatásával többletterhelést hozhat létre.

## <a name="parse-data-as-its-collected"></a>Adatok elemzése az összegyűjtött adatok között
Az adatgyűjtés során az adatok elemzésével kapcsolatos részletekért tekintse meg az Egyéni mezők létrehozása az [Azure Monitorban](../platform/custom-fields.md) című témakört. Ez olyan egyéni tulajdonságokat hoz létre a táblában, amelyeket a lekérdezések ugyanúgy használhatnak, mint bármely más tulajdonságot.

## <a name="parse-data-in-query-using-patterns"></a>Adatok elemzése lekérdezésben minták használatával
Ha az elemezni kívánt adatokat a rekordok között ismétlődő minta azonosítja, a [Kusto lekérdezési nyelv](/azure/kusto/query/) különböző operátorai segítségével kinyerheti az adott adatrészletet egy vagy több új tulajdonságba.

### <a name="simple-text-patterns"></a>Egyszerű szövegminták

A [lekérdezésben](/azure/kusto/query/parseoperator) az elemzési operátorral hozzon létre egy vagy több egyéni tulajdonságot, amely kinyerhető egy karakterlánc-kifejezésből. Megadhatja az azonosítandó mintát és a létrehozandó tulajdonságok nevét. Ez különösen hasznos a _key=value_-hoz hasonló méretű kulcsértékű karakterláncokkal rendelkező adatok esetében.

Fontolja meg az egyéni napló adatokat a következő formátumban.

```
Time=2018-03-10 01:34:36 Event Code=207 Status=Success Message=Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
Time=2018-03-10 01:33:33 Event Code=208 Status=Warning Message=Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
Time=2018-03-10 01:35:44 Event Code=209 Status=Success Message=Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
Time=2018-03-10 01:38:22 Event Code=302 Status=Error Message=Application could not connect to database
Time=2018-03-10 01:31:34 Event Code=303 Status=Error Message=Application lost connection to database
```

A következő lekérdezés ezeket az adatokat egyedi tulajdonságokra elemzi. A _projekttel_ rendelkező sor csak a számított tulajdonságokat adja vissza, a _RawData_nem, amely az egyéni napló teljes bejegyzését tároló egyetlen tulajdonság.

```Kusto
MyCustomLog_CL
| parse RawData with * "Time=" EventTime " Event Code=" Code " Status=" Status " Message=" Message
| project EventTime, Code, Status, Message
```

A következő egy másik példa, amely kibontja _AzureActivity_ a felhasználónevét egy upn az AzureActivity-táblában.

```Kusto
AzureActivity
| parse  Caller with UPNUserPart "@" * 
| where UPNUserPart != "" //Remove non UPN callers (apps, SPNs, etc)
| distinct UPNUserPart, Caller
```


### <a name="regular-expressions"></a>Reguláris kifejezések
Ha az adatok reguláris kifejezéssel azonosíthatók, használhat [reguláris kifejezéseket használó függvényeket](/azure/kusto/query/re2) az egyes értékek kinyeréséhez. A következő példa [kivonat](/azure/kusto/query/extractfunction) használatával bontsa ki az _UPN-mezőt_ az _AzureActivity-rekordokból,_ majd adja vissza a különböző felhasználók.

```Kusto
AzureActivity
| extend UPNUserPart = extract("([a-z.]*)@", 1, Caller) 
| distinct UPNUserPart, Caller
```

A hatékony, nagy méretű elemzés érdekében az Azure Monitor a reguláris kifejezések re2 verzióját használja, amely hasonló, de nem azonos a többi reguláris kifejezés változatával. A részleteket a [re2 kifejezés szintaxisa](https://aka.ms/kql_re2syntax) című dokumentumban találja.


## <a name="parse-delimited-data-in-a-query"></a>Lekérdezésben tagolt adatok elemzése
A tagolt adatok elválasztják a közös karakterű mezőket, például a CSV-fájlban lévő vesszőt. A [felosztási](/azure/kusto/query/splitfunction) függvénnyel a megadott határolójel használatával elemezheti a körülhatárolt adatokat. Ezt az [operátor kihosszabbításával](/azure/kusto/query/extendoperator) együtt az adatok összes mezőjének visszaadására vagy a kimenetben szerepeltetni kívánt mezők megadására használhatja.

> [!NOTE]
> Mivel a felosztás dinamikus objektumot ad vissza, előfordulhat, hogy az eredményeket explicit módon kell elosztani az operátorokban és szűrőkben használandó adattípusokra, például karakterláncokra.

Fontolja meg egy egyéni napló adatokkal a következő CSV formátumban.

```
2018-03-10 01:34:36, 207,Success,Client 05a26a97-272a-4bc9-8f64-269d154b0e39 connected
2018-03-10 01:33:33, 208,Warning,Client ec53d95c-1c88-41ae-8174-92104212de5d disconnected
2018-03-10 01:35:44, 209,Success,Transaction 10d65890-b003-48f8-9cfc-9c74b51189c8 succeeded
2018-03-10 01:38:22, 302,Error,Application could not connect to database
2018-03-10 01:31:34, 303,Error,Application lost connection to database
```

A következő lekérdezés elemzi ezeket az adatokat, és a számított tulajdonságok közül kettővel összesíti. Az első sor a _RawData_ tulajdonságot karakterlánctömbre osztja fel. A következő sorok mindegyike nevet ad az egyes tulajdonságoknak, és függvények segítségével adja hozzá őket a kimenethez, hogy azokat a megfelelő adattípusra konvertálja.

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

## <a name="parse-predefined-structures-in-a-query"></a>Előre definiált struktúrák elemzése lekérdezésben
Ha az adatok ismert struktúrában vannak formázva, a [Kusto lekérdezési nyelv](/azure/kusto/query/) egyik függvényét használhatja az előre definiált struktúrák elemzéséhez:

- [JSON](/azure/kusto/query/parsejsonfunction)
- [Xml](/azure/kusto/query/parse-xmlfunction)
- [IPv4](/azure/kusto/query/parse-ipv4function)
- [Url](/azure/kusto/query/parseurlfunction)
- [URL-lekérdezés](/azure/kusto/query/parseurlqueryfunction)
- [Elérési út](/azure/kusto/query/parsepathfunction)
- [Felhasználói ügynök](/azure/kusto/query/parse-useragentfunction)
- [Verziókarakterlánc](/azure/kusto/query/parse-versionfunction)

A következő példa lekérdezés elemzi a _Tulajdonságok_ mező az _AzureActivity_ tábla, amely json-ban strukturált. Az eredményeket egy _parsedProp_nevű dinamikus tulajdonságba menti, amely tartalmazza a JSON névvel ellátott értéket. Ezek az értékek a lekérdezés eredményeinek szűrésére és összegzésére szolgálnak.

```Kusto
AzureActivity
| extend parsedProp = parse_json(Properties) 
| where parsedProp.isComplianceCheck == "True" 
| summarize count() by ResourceGroup, tostring(parsedProp.tags.businessowner)
```

Ezek az elemzési függvények processzorigényesek lehetnek, ezért csak akkor használhatók, ha a lekérdezés a formázott adatokból több tulajdonságot használ. Ellenkező esetben az egyszerű mintaegyeztetés gyorsabb lesz.

A következő példa a TGT Preauth tartományvezérlő típusának bontását mutatja be. A típus csak az EventData mezőben létezik, amely egy XML-karakterlánc, de ebből a mezőből nincs szükség más adatokra. Ebben az esetben [az elemzés](/azure/kusto/query/parseoperator) a szükséges adatok kiválasztására szolgál.

```Kusto
SecurityEvent
| where EventID == 4768
| parse EventData with * 'PreAuthType">' PreAuthType '</Data>' * 
| summarize count() by PreAuthType
```

## <a name="use-function-to-simulate-a-table"></a>Táblázat szimulálása függvény használatával
Előfordulhat, hogy több lekérdezése is van, amelyek egy adott tábla azonos elemzését hajtják végre. Ebben az esetben [hozzon létre egy függvényt,](functions.md) amely az egyes lekérdezésekben az elemzési logika replikálása helyett adja vissza az elemzési adatokat. Ezután használhatja a függvény alias helyett az eredeti tábla más lekérdezések.

Tekintsük a vesszővel tagolt egyéni napló fenti példa. Az elemzési adatok több lekérdezésben való használatához hozzon létre egy függvényt a következő lekérdezéssel, és mentse azokat a _MyCustomCSVLog_aliassal.

```Kusto
MyCustomCSVLog_CL
| extend CSVFields = split(RawData, ',')
| extend DateTime  = tostring(CSVFields[0])
| extend Code      = toint(CSVFields[1]) 
| extend Status    = tostring(CSVFields[2]) 
| extend Message   = tostring(CSVFields[3]) 
```

Most már használhatja a _MyCustomCSVLog_ aliast a tényleges táblanév helyett a következő lekérdezésekben.

```Kusto
MyCustomCSVLog
| summarize count() by Status,Code
```


## <a name="next-steps"></a>További lépések
* Ismerje meg a [naplólekérdezéseket](log-query-overview.md) az adatforrásokból és megoldásokból gyűjtött adatok elemzéséhez.
