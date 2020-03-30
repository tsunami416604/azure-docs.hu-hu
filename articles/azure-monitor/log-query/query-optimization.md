---
title: Naplólekérdezések optimalizálása az Azure Monitorban
description: Gyakorlati tanácsok a naplólekérdezések optimalizálásához az Azure Monitorban.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 02/28/2019
ms.openlocfilehash: c32731ce2de2b0f886a1e21ee8ccad3996e395eb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79480266"
---
# <a name="optimize-log-queries-in-azure-monitor"></a>Naplólekérdezések optimalizálása az Azure Monitorban
Az Azure Monitor Logs [az Azure Data Explorer (ADX)](/azure/data-explorer/) segítségével tárolja a naplóadatokat, és lekérdezéseket futtat az adatok elemzéséhez. Létrehozza, kezeli és karbantartja az ADX-fürtöket, és optimalizálja őket a naplóelemzési munkaterheléshez. Amikor egy lekérdezést futtat, az optimalizálva lesz, és a munkaterületi adatokat tároló megfelelő ADX-fürthöz irányítja. Az Azure Monitor Naplók és az Azure Data Explorer számos automatikus lekérdezésoptimalizálási mechanizmust használ. Bár az automatikus optimalizálás jelentős lökést ad, bizonyos esetekben jelentősen javíthatja a lekérdezés teljesítményét. Ez a cikk ismerteti a teljesítményszempontjait és számos technikát a javításukhoz.

A legtöbb technika gyakori a közvetlenül az Azure Data Explorer és az Azure Monitor naplók, bár számos egyedi Azure Monitor naplók szempontok, amelyek itt tárgyalt. Az Azure Data Explorer optimalizálási tippjeiről a [Lekérdezésajánlott eljárások](/azure/kusto/query/best-practices)című témakörben olvashat.

Az optimalizált lekérdezések:

- Gyorsabban fut, és csökkenti a lekérdezés végrehajtásának teljes időtartamát.
- Kisebb az esélye, hogy a fojtás vagy elutasított.

Különös figyelmet kell fordítania az ismétlődő és bursty használatra használt lekérdezésekre, például irányítópultokra, riasztásokra, Logic Apps és Power BI-ra. A hatástalan lekérdezés hatása ezekben az esetekben jelentős.

## <a name="query-performance-pane"></a>Lekérdezési teljesítmény ablaktábla
Miután futtatott egy lekérdezést a Log Analytics szolgáltatásban, kattintson a lekérdezés eredményei feletti lefelé mutató nyílra a lekérdezés teljesítményablakának megtekintéséhez, amely a lekérdezés több teljesítménymutatójának eredményeit jeleníti meg. Ezeket a teljesítménymutatókat a következő szakasz ismerteti.

![Lekérdezési teljesítmény ablaktábla](media/query-optimization/query-performance-pane.png)


## <a name="query-performance-indicators"></a>Teljesítménymutatók lekérdezése

A következő lekérdezési teljesítményjelzők érhetők el minden végrehajtott lekérdezéshez:

- [Teljes CPU:](#total-cpu)A lekérdezés feldolgozásához használt összes számítási folyamat az összes számítási csomóponton. A számítástechnikához, elemzéshez és adatbeolvasáshoz használt időt jelöli. 

- [Feldolgozott lekérdezéshez használt adatok:](#data-used-for-processed-query)A lekérdezés feldolgozásához elért összes adat. Befolyásolja a céltábla méretét, a használt időtartamot, az alkalmazott szűrőket és a hivatkozott oszlopok számát.

- [A feldolgozott lekérdezés időtartama](#time-span-of-the-processed-query): A lekérdezés feldolgozásához elért legújabb és legrégebbi adatok közötti rés. A lekérdezéshez megadott explicit időtartomány befolyásolja.

- [Feldolgozott adatok kora](#age-of-processed-data): A lekérdezés feldolgozásához elérhető adatok és a legrégebbi adatok közötti szakadék. Nagymértékben befolyásolja az adatok lehívásának hatékonyságát.

- [Munkaterületek száma:](#number-of-workspaces)Az implicit vagy explicit kijelölés miatt a lekérdezés feldolgozása során elért munkaterületek száma.

- [Régiók száma](#number-of-regions): Hány régió érhető el a lekérdezés feldolgozása során a munkaterületek implicit vagy explicit kiválasztása miatt. A több régióra kiterjedő lekérdezések sokkal kevésbé hatékonyak, és a teljesítménymutatók részleges lefedettséget mutatnak.

- [Párhuzamosság](#parallelism): Azt jelzi, hogy a rendszer mennyire tudta végrehajtani ezt a lekérdezést több csomóponton. Csak a magas cpu-fogyasztással rendelkező lekérdezések esetén releváns. Adott funkciók és operátorok használata befolyásolja.


## <a name="total-cpu"></a>Teljes CPU
A tényleges számítási CPU, amely a lekérdezés feldolgozásához az összes lekérdezésfeldolgozó csomópontok feldolgozása. Mivel a legtöbb lekérdezés nagy számú csomóponton kerül végrehajtásra, ez általában sokkal nagyobb lesz, mint a lekérdezés tényleges végrehajtásának időtartama. 

A lekérdezés feldolgozási ideje a következő:
- Adatbeolvasás – a régi adatok visszakeresése több időt fog igénybe vesz, mint a legutóbbi adatok visszakeresése.
- Adatfeldolgozás – az adatok logikája és értékelése. 

A lekérdezésfeldolgozási csomópontokban töltött időn kívül az Azure Monitor naplók további időt fordítanak a következőkre: hitelesítsék a felhasználót, és ellenőrizzék, hogy jogosultak-e hozzáférni ezekhez az adatokhoz, megkeresni az adattárat, elemezni a lekérdezést, és lefoglalni a lekérdezésfeldolgozást Csomópontok. Ez az idő nem szerepel a lekérdezés teljes CPU-idő.

### <a name="early-filtering-of-records-prior-of-using-high-cpu-functions"></a>A rekordok korai szűrése a magas CPU-függvények használata előtt

A lekérdezési parancsok és függvények némelyike nagy a CPU-felhasználásban. Ez különösen igaz a JSON és XML elemzése vagy összetett reguláris kifejezések kinyerése parancsaira. Az ilyen elemzés történhet explicit módon keresztül [parse_json()](/azure/kusto/query/parsejsonfunction) vagy [parse_xml()](/azure/kusto/query/parse-xmlfunction) függvények vagy implicit módon, amikor dinamikus oszlopokra hivatkozva.

Ezek a függvények a processzort az általuk feldolgozott sorok számával arányosan használják fel. A leghatékonyabb optimalizálás az, hogy adja hozzá, ahol a feltételek korai a lekérdezés, amely kiszűri a lehető legtöbb rekordot, mielőtt a CPU-igényes függvény végrehajtása.

Például a következő lekérdezések pontosan ugyanazt az eredményt, de a második messze a leghatékonyabb, mint az, [ahol](/azure/kusto/query/whereoperator) állapot elemzése előtt kizárja sok rekordot:

```Kusto
//less efficient
SecurityEvent
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" and FilePath !startswith "%SYSTEM32"  // Problem: irrelevant results are filtered after all processing and parsing is done
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 8002 //Only this event have FileHash
| where EventData !has "%SYSTEM32" //Early removal of unwanted records
| extend Details = parse_xml(EventData)
| extend FilePath = tostring(Details.UserData.RuleAndFileData.FilePath)
| extend FileHash = tostring(Details.UserData.RuleAndFileData.FileHash)
| summarize count() by FileHash, FilePath
| where FileHash != "" // No need to filter out %SYSTEM32 here as it was removed before
```

### <a name="avoid-using-evaluated-where-clauses"></a>Kerülje az értékelt záradékok helyének használatát

Az adatkészletben fizikailag jelen lévő oszlopok helyett a kiértékelt [oszlopban](/azure/kusto/query/whereoperator) lévő záradékokat tartalmazó lekérdezések hatékonysága megtörténik. A kiértékelt oszlopok szűrése megakadályozza a rendszer bizonyos optimalizálását nagy adathalmazok kezelése esetén.
Például a következő lekérdezések pontosan ugyanazt az eredményt eredményezik, de a második hatékonyabb, mivel a [feltétel](/azure/kusto/query/whereoperator) a beépített oszlopra hivatkozik.

```Kusto
//less efficient
Heartbeat 
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| where IPRegion == "WestCoast"
| summarize count() by Computer
```
```Kusto
//more efficient
Heartbeat 
| where RemoteIPLongitude  < -94
| extend IPRegion = iif(RemoteIPLongitude  < -94,"WestCoast","EastCoast")
| summarize count() by Computer
```

### <a name="use-effective-aggregation-commands-and-dimmentions-in-summarize-and-join"></a>Hatékony összesítési parancsok és dimmentions használata összegzésben és illesztésben

Míg egyes aggregációs parancsok, mint [a max()](/azure/kusto/query/max-aggfunction), [sum()](/azure/kusto/query/sum-aggfunction), [count()](/azure/kusto/query/count-aggfunction)és [avg()](/azure/kusto/query/avg-aggfunction) alacsony CPU-hatást gyakorolnak a logikájuk miatt, mások összetettebbek, és heurisztikát és becsléseket tartalmaznak, amelyek lehetővé teszik a hatékony végrehajtást. A [dcount()](/azure/kusto/query/dcount-aggfunction) például a HyperLogLog algoritmust használja a nagy adathalmazok különböző számának szoros becslésére anélkül, hogy ténylegesen megszámolná az egyes értékeket; a percentilis függvények hasonló közelítéseket végeznek a legközelebbi rangpercentilis algoritmus használatával. A parancsok közül több nem kötelező paramétereket is tartalmaz, hogy csökkentse azok hatását. Például a [makeet()](/azure/kusto/query/makeset-aggfunction) függvény rendelkezik egy választható paraméterrel a maximális beállított méret meghatározásához, amely jelentősen befolyásolja a processzort és a memóriát.

[A parancsok összeillesztése](/azure/kusto/query/joinoperator?pivots=azuremonitor) és [összegzése](/azure/kusto/query/summarizeoperator) nagy mennyiségű adat feldolgozása esetén magas processzorkihasználtságot okozhat. Összetettségük közvetlenül kapcsolódik a lehetséges értékek számához, a továbbiakban *számosság* `by` , az oszlopok, amelyek használják a in summarize vagy a join attribútumok. Az illesztés és az összegzés magyarázatát és optimalizálását a dokumentációs cikkekben és optimalizálási tippekben találja.

A következő lekérdezések például pontosan ugyanazt az eredményt eredményezik, mivel a **CounterPath** mindig egy az egyhez lesz leképezve **a CounterName** és **az ObjectName objektumnévre.** A második hatékonyabb, mivel az összesítési dimenzió kisebb:

```Kusto
//less efficient
Perf
| summarize avg(CounterValue) 
by CounterName, CounterPath, ObjectName
```
```Kusto
//make the group expression more compact improve the performance
Perf
| summarize avg(CounterValue), any(CounterName), any(ObjectName) 
by CounterPath
```

A cpu-felhasználást befolyásolhatja az is, hogy hol vannak az intenzív számítást igénylő körülmények vagy kiterjesztett oszlopok. Minden triviális karakterlánc-összehasonlításnak, például [egyenlő ==](/azure/kusto/query/datatypes-string-operators) és [indításnak](/azure/kusto/query/datatypes-string-operators) nagyjából ugyanaz a CPU hatása, míg a speciális szövegegyezések nagyobb hatást fejtenek ki. Pontosabban, a [van](/azure/kusto/query/datatypes-string-operators) üzemeltető hatékonyabb, hogy a [tartalmaz](/azure/kusto/query/datatypes-string-operators) üzemeltető. A karakterlánc-kezelési technikák nak köszönhetően hatékonyabb négy karakternél hosszabb karakterláncokat keresni, mint a rövid karakterláncok.

A következő lekérdezések például hasonló eredményeket hoznak, a számítógép-elnevezési házirendtől függően, de a második hatékonyabb:

```Kusto
//less efficient – due to filter based on contains
Heartbeat
| where Computer contains "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//less efficient – due to filter based on extend
Heartbeat
| extend MyComputer = Computer
| where MyComputer startswith "Production" 
| summarize count() by ComputerIP 
```
```Kusto
//more efficient
Heartbeat
| where Computer startswith "Production" 
| summarize count() by ComputerIP 
```

> [!NOTE]
> Ez a mutató csak a közvetlen fürt processzorát mutatja be. A többrégiós lekérdezésben csak az egyik régiót képviselné. Előfordulhat, hogy a többmunkaterületet lekérdező lekérdezés nem tartalmazza az összes munkaterületet.


## <a name="data-used-for-processed-query"></a>Feldolgozott lekérdezéshez használt adatok

A lekérdezés feldolgozásának kritikus tényezője a lekérdezés feldolgozásához beolvasott és használt adatok mennyisége. Az Azure Data Explorer agresszív optimalizálást használ, amely jelentősen csökkenti az adatmennyiséget más adatplatformokhoz képest. A lekérdezésben mégis vannak olyan kritikus tényezők, amelyek hatással lehetnek a használt adatmennyiségre.

Az Azure Monitor naplók, a **TimeGenerated** oszlop az adatok indexelésének módjaként. Az **időgenerált** értékek nek a lehető legszűkebb tartományra való korlátozása jelentősen javítja a lekérdezési teljesítményt azáltal, hogy jelentősen korlátozza a feldolgozandó adatok mennyiségét.

### <a name="avoid-unnecessary-use-of-search-and-union-operators"></a>Kerülje a keresési és szakszervezeti operátorok szükségtelen használatát

Egy másik tényező, amely növeli az adatokat, amelyek a folyamat használata nagy számú táblák. Ez általában `search *` akkor `union *` fordul elő, ha parancsokat használnak. Ezek a parancsok arra kényszerítik a rendszert, hogy kiértékelje és bekéselje az adatokat a munkaterület összes táblájából. Bizonyos esetekben több száz tábla lehet a munkaterületen. Próbálja meg elkerülni, amennyire csak lehetséges a "keresés *", vagy bármilyen keresés nélkül hatóköre, hogy egy adott táblázatot.

Például a következő lekérdezések pontosan ugyanazt az eredményt, de az utolsó messze a leghatékonyabb:

```Kusto
// This version scans all tables though only Perf has this kind of data
search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This version scans all strings in Perf tables – much more efficient
Perf
| search "Processor Time" 
| summarize count(), avg(CounterValue)  by Computer
```
```Kusto
// This is the most efficient version 
Perf 
| where CounterName == "% Processor Time"  
| summarize count(), avg(CounterValue)  by Computer
```

### <a name="add-early-filters-to-the-query"></a>Korai szűrők hozzáadása a lekérdezéshez

Egy másik módszer az adatmennyiség csökkentésére, hogy [a](/azure/kusto/query/whereoperator) lekérdezés korai szakaszában a feltételeket. Az Azure Data Explorer platform tartalmaz egy gyorsítótárat, amely lehetővé teszi, hogy tudja, mely partíciók tartalmaznak adatokat, amelyek relevánsak egy adott, ahol feltétel. Ha például egy `where EventID == 4624` lekérdezés tartalmazza, akkor a lekérdezést csak olyan csomópontoknak osztja el, amelyek egyező eseményekkel rendelkező partíciókat kezelnek.

A következő példa lekérdezések pontosan ugyanazt az eredményt, de a második hatékonyabb:

```Kusto
//less efficient
SecurityEvent
| summarize LoginSessions = dcount(LogonGuid) by Account
```
```Kusto
//more efficient
SecurityEvent
| where EventID == 4624 //Logon GUID is relevant only for logon event
| summarize LoginSessions = dcount(LogonGuid) by Account
```

### <a name="reduce-the-number-of-columns-that-is-retrieved"></a>A beolvasott oszlopok számának csökkentése

Mivel az Azure Data Explorer egy oszlopos adattár, minden oszlop lekérése független a többitől. A beolvasott oszlopok száma közvetlenül befolyásolja a teljes adatmennyiséget. Csak azokat az oszlopokat vegye fel a kimenetbe, amelyekre az eredmények [összegzéséhez](/azure/kusto/query/summarizeoperator) vagy az adott oszlopok [kivetítéséhez](/azure/kusto/query/projectoperator) szükség van. Az Azure Data Explorer számos optimalizálással rendelkezik a beolvasott oszlopok számának csökkentése érdekében. Ha azt állapítja meg, hogy nincs szükség egy oszlopra, például ha az [összegző](/azure/kusto/query/summarizeoperator) parancs nem hivatkozik rá, akkor nem fogja beolvasni.

A második lekérdezés például háromszor több adatot dolgozhat fel, mivel nem egy oszlopot, hanem hármat kell lekérnie:

```Kusto
//Less columns --> Less data
SecurityEvent
| summarize count() by Computer  
```
```Kusto
//More columns --> More data
SecurityEvent
| summarize count(), dcount(EventID), avg(Level) by Computer  
```

## <a name="time-span-of-the-processed-query"></a>A feldolgozott lekérdezés időtartama

Az Azure Figyelő naplók összes naplója particionálva a **TimeGenerated** oszlop szerint. Az elért partíciók száma közvetlenül kapcsolódik az időtartamhoz. Az időtartomány csökkentése a leghatékonyabb módja a gyors lekérdezés végrehajtásának biztosításához.

Az időtartomány az Időtartomány-választóval állítható be a Log Analytics képernyőn, az [Azure Monitor Log Analytics naplótartományában](scope.md#time-range)leírtak szerint. Ez az ajánlott módszer, mivel a kiválasztott időtartomány a lekérdezés metaadatainak használatával kerül átadásra a háttérrendszernek. 

Egy másik módszer explicit módon szerepel [egy, ahol](/azure/kusto/query/whereoperator) feltétel **timegenerated** a lekérdezésben. Ezt a módszert kell használnia, mivel biztosítja, hogy az időtartam rögzített, még akkor is, ha a lekérdezést más felületről használja.
Győződjön meg arról, hogy a lekérdezés minden része **rendelkezik TimeGenerated** szűrőkkel. Ha egy lekérdezés különböző táblákból vagy ugyanabból a táblából származó adatokat bekérő részlekérdezésekkel rendelkezik, mindegyiknek tartalmaznia kell a saját [where](/azure/kusto/query/whereoperator) feltételét.

### <a name="make-sure-all-sub-queries-have-timegenerated-filter"></a>Győződjön meg arról, hogy minden allekérdezés rendelkezik TimeGenerated szűrővel

A következő lekérdezésben például, míg a **Perf** tábla csak az utolsó napra lesz beolvasva, a **Szívverés** tábla az összes előzményét megvizsgálja, ami akár két év is lehet:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize IPs = makeset(ComputerIP, 10) by  Computer
) on Computer
```

Gyakori eset, amikor ilyen hiba fordul elő, amikor [arg_max()](/azure/kusto/query/arg-max-aggfunction) a legújabb előfordulás keresésére használják. Példa:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    //No time span filter in this part of the query
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Ez könnyen korrigálható időszűrő hozzáadásával a belső lekérdezésben:

```Kusto
Perf
| where TimeGenerated > ago(1d)
| summarize avg(CounterValue) by Computer, CounterName
| join kind=leftouter (
    Heartbeat
    | where TimeGenerated > ago(1d) //filter for this part
    | summarize arg_max(TimeGenerated, *), min(TimeGenerated)   
by Computer
) on Computer
```

Egy másik példa erre a hibára, amikor az időhatókör-szűrést közvetlenül egy több tábla [egyesítése](/azure/kusto/query/unionoperator?pivots=azuremonitor) után hajtja végre. Az egyesítés végrehajtásakor minden allekérdezést hatókörrel kell végezni. A [let](/azure/kusto/query/letstatement) utasítás használatával biztosíthatja a hatókör konzisztenciáját.

A következő lekérdezés például nem csak az elmúlt 1 napban, hanem a *Szívverés* és a *Perf* táblák összes adatát is megvizsgálja:

```Kusto
Heartbeat 
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | summarize arg_min(TimeGenerated,*) by Computer) 
| where TimeGenerated > ago(1d)
| summarize min(TimeGenerated) by Computer
```

Ezt a lekérdezést a következőképpen kell rögzíteni:

```Kusto
let MinTime = ago(1d);
Heartbeat 
| where TimeGenerated > MinTime
| summarize arg_min(TimeGenerated,*) by Computer
| union (
    Perf 
    | where TimeGenerated > MinTime
    | summarize arg_min(TimeGenerated,*) by Computer) 
| summarize min(TimeGenerated) by Computer
```

### <a name="time-span-measurement-limitations"></a>Az időtartam mérési korlátai

A mérés mindig nagyobb, mint a ténylegesen megadott idő. Ha például a lekérdezés szűrője 7 nap, a rendszer 7,5 vagy 8,1 napot kaszthat. Ennek az az oka, hogy a rendszer változó méretű adattömbökre particionálja az adatokat. Annak biztosítása érdekében, hogy az összes releváns rekordot beolvassa, megvizsgálja a teljes partíciót, amely több órát, sőt több mint egy napot fedhet le.

Számos olyan eset van, amikor a rendszer nem tudja pontosan mérni az időtartományt. Ez a legtöbb olyan esetben fordul elő, amikor a lekérdezés egy napnál rövidebb ideig tart, vagy több munkaterületi lekérdezésekben.


> [!IMPORTANT]
> Ez a mutató csak a közvetlen fürtben feldolgozott adatokat mutatja be. A többrégiós lekérdezésben csak az egyik régiót képviselné. Előfordulhat, hogy a többmunkaterületet lekérdező lekérdezés nem tartalmazza az összes munkaterületet.

## <a name="age-of-processed-data"></a>A feldolgozott adatok kora
Az Azure Data Explorer számos tárolási szintet használ: a memóriában, a helyi SSD-lemezeket és a sokkal lassabb Azure Blobokat. Minél újabb az adatok, annál nagyobb az esélye, hogy egy kisebb késleltetéssel rendelkező, nagyobb teljesítményrétegben tárolja, csökkentve a lekérdezés időtartamát és a PROCESSZORt. Magán az adaton kívül a rendszer rendelkezik a metaadatok gyorsítótárával is. Minél régebbiek az adatok, annál kisebb az esélye annak, hogy a metaadatai a gyorsítótárban lesznek.

Míg egyes lekérdezések régi adatok használatát igénylik, vannak olyan esetek, amikor a régi adatokat véletlenül használják. Ez akkor fordul elő, ha a lekérdezések végrehajtása időtartomány megadása nélkül történik a metaadatokban, és nem minden táblahivatkozás tartalmaz szűrőt a **TimeGenerated** oszlopban. Ezekben az esetekben a rendszer a táblában tárolt összes adatot beszkad. Ha az adatmegőrzés hosszú, akkor hosszú időtartományokat, és így az adatok, amelyek olyan régi, mint az adatmegőrzési időszak.

Az ilyen esetek lehetnek például:

- Nem állítja be az időtartományt a Log Analytics egy al-lekérdezés, amely nem korlátozott. Lásd a fenti példát.
- Az API használata az időtartomány választható paraméterei nélkül.
- Olyan ügyfél használata, amely nem kényszerít egy időtartományt, például a Power BI-összekötőt.

Tekintse meg a példákat és megjegyzéseket a pervious szakaszban, mivel ezek ebben az esetben is relevánsak.

## <a name="number-of-regions"></a>Régiók száma
Több olyan helyzet van, amikor egyetlen lekérdezés hajtható végre különböző régiókban:

- Ha több munkaterület explicit módon szerepel a listában, és különböző régiókban találhatók.
- Amikor egy erőforrás-hatókörű lekérdezés adatokat hív be, és az adatokat több, különböző régiókban található munkaterületen tárolja.

Régióközi lekérdezés-végrehajtás megköveteli, hogy a rendszer szerializálja és továbbítsa a háttérrendszer ben a köztes adatok nagy adattömbjeit, amelyek általában sokkal nagyobbak, mint a lekérdezés végeredménye. Azt is korlátozza a rendszer képes elvégezni optimalizálás, heurisztika, és használja a gyorsítótárak.
Ha nincs valódi ok a területek bevizsgálatára, módosítsa a hatókört úgy, hogy kevesebb régiót fedjen le. Ha az erőforrás-hatókör kis méretű, de továbbra is sok régióban használják, előfordulhat, hogy helytelen konfiguráció miatt. Például a naplónaplók és a diagnosztikai beállítások különböző régiók különböző munkaterületeire kerülnek, vagy több diagnosztikai beállítási konfiguráció van. 

> [!IMPORTANT]
> Ha egy lekérdezés több régióban fut, a processzor- és adatmérések nem lesznek pontosak, és csak az egyik régióban fogják képviselni a mérést.

## <a name="number-of-workspaces"></a>Munkaterületek száma
A munkaterületek olyan logikai tárolók, amelyek a naplók adatainak elkülönítésére és felügyeletére szolgálnak. A háttérprogram optimalizálja a munkaterületi elhelyezéseket a kijelölt területen belüli fizikai fürtökön.

Több munkaterület használata a következőkből eredhet: 

- Ahol több munkaterület explicit módon szerepel a listában.
- Amikor egy erőforrás-hatókörű lekérdezés adatokat hív be, és az adatokat több munkaterületen tárolja.
 
A lekérdezések régiók közötti és fürtközi végrehajtásához a rendszernek szerializálnia és kell átvinnie a háttérrendszerben a köztes adatok nagy adattömbjeit, amelyek általában sokkal nagyobbak, mint a lekérdezés végeredménye. Azt is korlátozza a rendszer képes elvégezni optimalizálás, heurisztikus és felhasználásával cache.

> [!IMPORTANT]
> Egyes többmunkaterületi forgatókönyvek esetén a processzor- és adatmérések nem lesznek pontosak, és csak néhány munkaterületre fogják képviselni a mérést.

## <a name="parallelism"></a>Párhuzamosság
Az Azure Monitor Logs az Azure Data Explorer nagy fürtjeit használja a lekérdezések futtatásához, és ezek a fürtök skálázata változó, és potenciálisan akár több tucat számítási csomópontot is elérhet. A rendszer automatikusan méretezi a fürtöket a munkaterület elhelyezési logikája és kapacitása szerint.

A lekérdezés hatékony végrehajtásához particionált és elosztott számítási csomópontok alapján a feldolgozáshoz szükséges adatokat. Vannak olyan helyzetek, amikor a rendszer ezt nem tudja hatékonyan megtenni. Ez a lekérdezés hosszú időtartamához vezethet. 

A párhuzamosság csökkentésére alkalmas lekérdezési viselkedések a következők:

- Szerializálási és ablakfunkciók, például a [szerializálási operátor,](/azure/kusto/query/serializeoperator) [next()](/azure/kusto/query/nextfunction), [prev()](/azure/kusto/query/prevfunction)és [sorfüggvények](/azure/kusto/query/rowcumsumfunction) használata. Az idősorozatok és a felhasználói elemzési funkciók néhány ilyen esetben használhatók. Nem hatékony szerializálás akkor is előfordulhat, ha a következő operátorokat nem használják a lekérdezés végén: [tartomány](/azure/kusto/query/rangeoperator), [rendezés](/azure/kusto/query/sortoperator), [sorrend](/azure/kusto/query/orderoperator), [top](/azure/kusto/query/topoperator), [top-hitters](/azure/kusto/query/tophittersoperator), [getschema](/azure/kusto/query/getschemaoperator).
-    A [dcount()](/azure/kusto/query/dcount-aggfunction) aggregációs függvény használata arra kényszeríti a rendszert, hogy a különböző értékeket központi másolattal készítsen. Ha az adatok skálája magas, fontolja meg a dcount függvény választható paramétereinek használatát a kisebb pontosság érdekében.
-    Sok esetben az [illesztési](/azure/kusto/query/joinoperator?pivots=azuremonitor) operátor csökkenti az általános párhuzamosságokat. Vizsgálja meg a véletlen sorrendű illesztést alternatívaként, ha a teljesítmény problematikus.
-    Az erőforrás-hatókör lekérdezések, a végrehajtás előtti RBAC-ellenőrzések idővel olyan helyzetekben, ahol nagyon nagy számú RBAC-hozzárendelések. Ez hosszabb ellenőrzésekhez vezethet, ami alacsonyabb párhuzamossághoz vezetne. Például egy lekérdezés végrehajtása egy olyan előfizetésen, ahol több ezer erőforrás van, és minden erőforrás nak sok szerepkör-hozzárendelése van az erőforrás szintjén, nem pedig az előfizetésen vagy az erőforráscsoporton.
-    Ha egy lekérdezés kis adattömböket dolgoz fel, a párhuzamosság alacsony lesz, mivel a rendszer nem osztja el sok számítási csomópont között.



## <a name="next-steps"></a>További lépések

- [A Kusto lekérdezési nyelvreferencia-dokumentációja](/azure/kusto/query/).
