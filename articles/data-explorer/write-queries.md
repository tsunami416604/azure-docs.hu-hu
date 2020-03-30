---
title: Lekérdezések írása az Azure Data Explorerhez
description: Ebben az útmutatóban megtudhatja, hogyan hajthat végre alapszintű és speciálisabb lekérdezéseket az Azure Data Explorerben.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68881182"
---
# <a name="write-queries-for-azure-data-explorer"></a>Lekérdezések írása az Azure Data Explorerhez

Ebben a cikkben megtudhatja, hogyan használhatja a lekérdezési nyelvet az Azure Data Explorer ben a leggyakoribb operátorokkal végzett alapvető lekérdezések végrehajtásához. Ön is kap expozíció néhány fejlettebb jellemzői a nyelvet.

## <a name="prerequisites"></a>Előfeltételek

A cikkben szereplő lekérdezéseket kétféleképpen futtathatja:

- Az Azure Data Explorer *segítségével fürt,* amely már beállított tanulási támogatás.
    [Jelentkezzen be a fürtbe](https://dataexplorer.azure.com/clusters/help/databases/samples) egy szervezeti e-mail fiókkal, amely az Azure Active Directory tagja.

- A saját fürtön, amely tartalmazza a StormEvents mintaadatokat. További információ: [Gyorsútmutató: Azure Data Explorer-fürt és-adatbázis létrehozása,](create-cluster-database-portal.md) valamint [mintaadatok betöltése az Azure Data Explorerbe című témakörben.](ingest-sample-data.md)

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>A lekérdezés nyelvének áttekintése

Az Azure Data Explorer lekérdezési nyelve egy írásvédett kérelem az adatok feldolgozására és az eredmények visszaadására. A kérelem egyszerű szövegként, egy adatfolyam-modell használatával történik, amely nek köszönhetően a szintaxis könnyen olvasható, szerző és automatizálható. A lekérdezés az SQL-hez hasonló hierarchiába rendezett sémaentitásokat használ: adatbázisokat, táblákat és oszlopokat.

A lekérdezés lekérdezési utasítások sorozatából áll, pontosvesszővel`;`( ), és legalább egy utasítás táblázatos kifejezésutasítás, amely olyan utasítás, amely az oszlopok és sorok táblaszerű hálójában rendezett adatokat hoz létre. A lekérdezés táblázatos kifejezésutasításai a lekérdezés eredményeit eredményezik.

A táblázatos kifejezésutasítás szintaxisa táblázatos adatáramlást mutat az egyik táblázatos lekérdezésoperátortól a másikig, kezdve az adatforrással (például egy adatbázis táblájával vagy egy adatokat előállító`|`operátorral), majd a cső ( ) határolóhasználata által összekötött adatátalakítási operátorok készletén keresztül áramlik.

A következő lekérdezés például egyetlen utasítással rendelkezik, amely egy táblázatos kifejezésutasítás. Az utasítás egy `StormEvents` nevű táblára való hivatkozással kezdődik (a táblát tartalmazó adatbázis itt implicit, és a kapcsolati adatok része). A tábla adatait (sorait) ezután az `StartTime` oszlop értéke, majd az `State` oszlop értéke szűri. A lekérdezés ezután a "túlélő" sorok számát adja vissza.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Ebben az esetben az eredmény:

|Darabszám|
|-----|
|   23|
| |

További információt a [Lekérdezés nyelvének hivatkozása című témakörben talál.](https://aka.ms/kustolangref)

## <a name="most-common-operators"></a>Leggyakoribb operátorok

Az ebben a szakaszban tárgyalt operátorok az Azure Data Explorer lekérdezéseinek megértéséhez. A legtöbb általa írt lekérdezés ek közül több operátort is tartalmazni fog.

Lekérdezések futtatásához a súgófürtön: **kattintson ide a lekérdezés futtatásához** az egyes lekérdezések felett.

Lekérdezések futtatása saját fürtön:

1. Másolja az egyes lekérdezéseket a webalapú lekérdezési alkalmazásba, majd jelölje ki a lekérdezést, vagy helyezze a kurzort a lekérdezésbe.

1. Az alkalmazás tetején válassza a **Futtatás lehetőséget.**

### <a name="count"></a>count

[**Count**](https://docs.microsoft.com/azure/kusto/query/countoperator): A táblázat sorainak számát adja eredményül.

A következő lekérdezés a StormEvents tábla sorainak számát adja vissza.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>venni

[**take**](https://docs.microsoft.com/azure/kusto/query/takeoperator): A megadott számú adatsort adja eredményül.

A következő lekérdezés öt sort ad vissza a StormEvents táblából. A *kulcsszókorlát* a *take aliasa.*

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Nincs garancia arra, hogy mely rekordokat adja vissza a rendszer, kivéve, ha a forrásadatok rendezésre vannak rendezve.

### <a name="project"></a>projekt

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Oszlopok egy részhalmazának kijelölése.

A következő lekérdezés egy adott oszlopkészletet ad vissza.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>Ebben a kódban:

[**where**](https://docs.microsoft.com/azure/kusto/query/whereoperator): A predikátumnak megfelelő sorok részhalmazára szűr egy táblázatot.

A következő lekérdezés a `EventType` `State`és a szerint szűri az adatokat.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>Rendezés

[**rendezés**](https://docs.microsoft.com/azure/kusto/query/sortoperator): A beviteli tábla sorait egy vagy több oszlop szerint rendezheti.

A következő lekérdezés az adatokat `DamageProperty`csökkenő sorrendben rendezi a szerint.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> A műveletek sorrendje fontos. Próbálja `take 5` meg `sort by`üzembe előtt . Más eredményeket kapunk?

### <a name="top"></a>felül

[**top**](https://docs.microsoft.com/azure/kusto/query/topoperator): Az első *N* rekordokat adja eredményül a megadott oszlopok szerint rendezve.

A következő lekérdezés ugyanazt az eredményt adja vissza, mint fent eggyel kevesebb operátorral.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Kiterjesztése

[**extend**](https://docs.microsoft.com/azure/kusto/query/extendoperator): A származtatott oszlopok számítása.

A következő lekérdezés új oszlopot hoz létre úgy, hogy minden sorban értéket számít ki.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

A kifejezések tartalmazhatják az összes szokásos operátort (+, -, *, /, %), és számos hasznos függvény thívhat.

### <a name="summarize"></a>Összefoglalni

[**összegzés:**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)Sorok csoportjainak összesítése.

A következő lekérdezés az események `State`számát adja vissza.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Az **összegző** operátor csoportosítja azokat a sorokat, amelyek a **by** záradékban azonos értékekkel rendelkeznek, majd az aggregációs függvény (például **a darabszám)** segítségével egyesíti az egyes csoportokat egyetlen sorba. Tehát ebben az esetben minden államhoz van egy sor, és egy oszlop az adott állapotban lévő sorok számára.

Van egy sor összesítési függvények, és több közülük egy **összegző** operátor több számított oszlopok. Például, lehet kapni a viharok száma az egyes államok és az egyedi viharok száma államonként, majd használja **a tetején,** hogy a legtöbb vihar által érintett államok.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Az **összegző** művelet eredménye:

- Minden oszlop **by** által megnevezett

- Minden egyes kiszámított kifejezéshez oszlop

- A sorok az egyes kombinációk értékek

### <a name="render"></a>Render

[**render**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Az eredményeket grafikus kimenetként jeleníti meg.

A következő lekérdezés oszlopdiagramot jelenít meg.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

A következő lekérdezés egy egyszerű idődiagramot jelenít meg.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

A következő lekérdezés egy nap alatt számolja az eseményeket, és órákba ürítette az eseményeket, és egy idődiagramot jelenít meg.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

A következő lekérdezés több napi adatsort hasonlít össze egy idődiagramon.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> A **render** operátor inkább ügyféloldali funkció, mint a motor része. A könnyebb használat érdekében integrálva van a nyelvbe. A webalkalmazás a következő beállításokat támogatja: sávdiagram, oszlopdiagram, kördiagram, idődiagram és vonaldiagram. 

## <a name="scalar-operators"></a>Skaláris operátorok

Ez a rész a legfontosabb skaláris operátorokat ismerteti.

### <a name="bin"></a>raktárhely()

[**bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Az értékeket egy adott raktárhelyméret egész többszörösére kerekíti.

A következő lekérdezés egy nap számú vödröt számít ja ki.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>eset()

[**eset()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Kiértékeli a predikátumok listáját, és visszaadja az első eredménykifejezést, amelynek predikátuma teljesült, vagy a végső **más** kifejezést. Ezzel az operátorral kategorizálhatja vagy csoportosíthatja az adatokat:

A következő lekérdezés egy `deaths_bucket` új oszlopot ad vissza, és szám szerint csoportosítja a haláleseteket.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

```Kusto
StormEvents
| summarize deaths = sum(DeathsDirect) by State
| extend deaths_bucket = case (
    deaths > 50, "large",
    deaths > 10, "medium",
    deaths > 0, "small",
    "N/A")
| sort by State asc
```

### <a name="extract"></a>kivonat()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Egy szöveges karakterlánc reguláris kifejezésének egyezését kapja.

A következő lekérdezés kinyeri a nyomkövetés ből adott attribútumértékeket.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Ez a lekérdezés **let** utasítást használ, amely `MyData`egy nevet (ebben az esetben) egy kifejezéshez köt. A hatókör többi részében, amelyben a **let** utasítás megjelenik (globális hatókör vagy egy függvénytörzs hatókörében), a név a kötött értékére hivatkozik.

### <a name="parse_json"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Egy karakterláncot JSON-értékként értelmez, és az értéket dinamikusként adja eredményül. Ez jobb a **extractjson()** függvény használatával, ha egy összetett JSON objektum egynél több elemét kell kivonnia.

A következő lekérdezés kinyeri a JSON-elemeket egy tömbből.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

A következő lekérdezés kinyeri a JSON-elemeket.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

A következő lekérdezés dinamikus adattípussal bontja ki a JSON-elemeket.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ezelőtt()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): Kivonja a megadott időtartományt az aktuális UTC-időből.

A következő lekérdezés az elmúlt 12 óra adatait adja vissza.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>a hét kezdete()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): A hét kezdését adja eredményül, amely tartalmazza a dátumot, eltolással eltolva, ha meg van adva

A következő lekérdezés a hét kezdetét adja vissza különböző eltolásokkal.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Ez a lekérdezés a **tartományoperátort** használja, amely egyoszlopos értéktáblát hoz létre. Lásd még: [**startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**startofyear()**](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek()**](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)és [**endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>között()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Megfelel a befogadó tartományon belüli bemenetnek.

A következő lekérdezés egy adott dátumtartomány szerint szűri az adatokat.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

A következő lekérdezés szűri az adatokat egy adott dátumtartomány`3d`szerint, a kezdő dátumtól számított három napos ( ) kis eltéréssel.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Táblázatos operátorok

Kusto számos táblázatos szereplők, amelyek közül néhány szerepel más szakaszaiban ezt a cikket. Itt fogunk összpontosítani **elemzés**. 

### <a name="parse"></a>parse

[**elemzés:**](https://docs.microsoft.com/azure/kusto/query/parseoperator)Kiértékel egy karakterlánc-kifejezést, és egy vagy több számított oszlopra elemzi annak értékét. Három féleképpen lehet elemezni: egyszerű (az alapértelmezett), regex, és nyugodt.

A következő lekérdezés elemzi a nyomkövetést, és kinyeri a megfelelő értékeket, az egyszerű elemzés alapértelmezett használatával. A kifejezés (stringconstant) egy normál karakterlánc-érték, és az egyezés szigorú: a kiterjesztett oszlopoknak meg kell egyezniük a szükséges típusokkal.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse EventTrace with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previouLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previouLockTime
```

A következő lekérdezés elemzi a nyomkövetést, és `kind = regex`kinyeri a megfelelő értékeket a használatával. A StringConstant lehet reguláris kifejezés.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind = regex EventTrace with "(.*?)[a-zA-Z]*=" resourceName @", totalSlices=\s*\d+\s*.*?sliceNumber=" sliceNumber:long  ".*?(previous)?lockTime=" lockTime ".*?releaseTime=" releaseTime ".*?previousLockTime=" previousLockTime:date "\\)"  
| project resourceName , sliceNumber , lockTime , releaseTime , previousLockTime
```

A következő lekérdezés elemzi a nyomkövetést, és `kind = relaxed`kinyeri a megfelelő értékeket a használatával. A StringConstant egy normál karakterlánc-érték, és az egyezés nyugodt: a kiterjesztett oszlopok részben megfelelnek a szükséges típusoknak.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

```Kusto
let MyTrace = datatable (EventTrace:string)
[
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=23, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=15, lockTime=02/17/2016 08:40:00Z, releaseTime=02/17/2016 08:40:00Z, previousLockTime=02/17/2016 08:39:00Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=20, lockTime=02/17/2016 08:40:01Z, releaseTime=02/17/2016 08:40:01Z, previousLockTime=02/17/2016 08:39:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=22, lockTime=02/17/2016 08:41:01Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:01Z)',
'Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=27, sliceNumber=16, lockTime=02/17/2016 08:41:00Z, releaseTime=02/17/2016 08:41:00Z, previousLockTime=02/17/2016 08:40:00Z)'
];
MyTrace
| parse kind=relaxed "Event: NotifySliceRelease (resourceName=PipelineScheduler, totalSlices=NULL, sliceNumber=23, lockTime=02/17/2016 08:40:01, releaseTime=NULL, previousLockTime=02/17/2016 08:39:01)" with * "resourceName=" resourceName ", totalSlices=" totalSlices:long * "sliceNumber=" sliceNumber:long * "lockTime=" lockTime ", releaseTime=" releaseTime:date "," * "previousLockTime=" previousLockTime:date ")" *  
| project resourceName ,totalSlices , sliceNumber , lockTime , releaseTime , previousLockTime
```

## <a name="time-series-analysis"></a>Idősorozat-elemzések

### <a name="make-series"></a>make-sorozat

[**make-series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): összesíti a sorok csoportjait, [például az összegzést,](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)de egy (idő) sorozatvektort hoz létre az értékek minden kombinációjára.

A következő lekérdezés egy idősorozatot ad vissza a viharesemények napi számához. A lekérdezés minden állapotra három hónapos időszakot fed le, és a hiányzó tárolókat 0 állandóval tölti ki:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Miután létrehozta az (idő) sorozatokat, sorozatfüggvényeket alkalmazhat a rendellenes alakzatok, szezonális minták és még sok más észlelésére.

A következő lekérdezés kinyeri az első három állapotot, amely a legtöbb eseményt kapta az adott napon:

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

További információért tekintse át a [sorozatfüggvények](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions)teljes listáját.

## <a name="advanced-aggregations"></a>Speciális aggregátumok

Az alapvető összesítéseket, például **a számlálást** és **az összegzést**a cikk korábbi korábbi tagjaira vonatkoztattuk. Ez a szakasz speciálisabb beállításokat mutat be.

### <a name="top-nested"></a>felül beágyazott

[**felülbe ágyazott**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Hierarchikus felső eredményeket hoz létre, ahol minden szint az előző szintértékeken alapuló részletezés.

Ez az operátor irányítópult-vizualizációs forgatókönyvek esetén hasznos, vagy ha a következőhöz hasonló kérdésre kell válaszolni: "Keresse meg a K1 legfelső N értékeit (néhány összesítéssel); mindegyiknél keresse meg a K2 felső M értékeit (egy másik összesítés használatával); ..."

A következő lekérdezés egy hierarchikus táblát `State` ad `Sources`vissza a legfelső szinten, amelyet a követ.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot() beépülő modul

[**pivot() plugin**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): A táblázat elforgatása a beviteli táblázat egyik oszlopának egyedi értékeit a kimeneti táblázat több oszlopává alakításával. Az operátor összesítéseket hajt végre ott, ahol szükség van rájuk a végső kimenet ben fennmaradó oszlopértékeken.

A következő lekérdezés szűrőt alkalmaz, és a sorokat oszlopokba forgatja.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>dcount()

[**dcount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): A csoportban lévő kifejezés különböző értékeinek becsült számát adja eredményül. A [**count()**](https://docs.microsoft.com/azure/kusto/query/countoperator) segítségével számolja meg az összes értéket.

A következő lekérdezés `Source` `State`száma különbözik a rendszerhez.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): Azon sorok kifejezésének különböző értékeinek becsült értékét adja eredményül, amelyek esetében az állítmány igaz értéket ad eredményül.

A következő lekérdezés megszámolja `DamageProperty < 5000`a hely különböző értékeit. `Source`

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Kiszámítja a **dcount-ot** a HyperLogLog eredményekből [**(hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) vagy [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction)generálva.

A következő lekérdezés a HLL algoritmus t használja a számláló létrehozásához.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Megkeresi a csoport olyan sorát, amely maximalizálja a kifejezést, és egy másik kifejezés értékét adja vissza (vagy * a teljes sor visszaadására).

A következő lekérdezés az egyes állapotok utolsó árvízi jelentésének idejét adja vissza.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeet()

[**makeet()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): A kifejezés által a csoportba befogadott különböző értékek halmazának dinamikus (JSON) tömbjét adja eredményül.

A következő lekérdezés minden olyan alkalommal ad vissza, amikor az egyes állapotok árvízi értéket jelentettek, és különböző értékek készletéből hoz létre tömböt.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-kibontás

[**mv-expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): A többértékű gyűjtemény(ek) kibontása egy dinamikus beírt oszlopból, így a gyűjtemény minden egyes értéke külön sort kap. A kibontott sor összes többi oszlopa duplikálva van. Ez a makelist ellentéte.

A következő lekérdezés mintaadatokat hoz létre egy készlet létrehozásával, majd az **mv-kibontási** képességek bemutatására.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Egy kifejezés által meghatározott sokaság megadott [**legközelebbi százalékos percentilisének**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) becslését adja eredményül. A pontosság függ a sűrűsége a lakosság a régióban a percentilis. Csak az összesítés kontextusában használható [**az összegzésen**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)belül.

A következő lekérdezés kiszámítja a storm időtartamára vonatkozó százalékokat.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

A következő lekérdezés kiszámítja a vihar időtartamának százalékos arányát állapot szerint, és`5m`ötperces raktárhelyekkel ( normalizálja az adatokat ).

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Keresztadatkészlet

Ez a szakasz olyan elemeket ölel fel, amelyek lehetővé teszik összetettebb lekérdezések létrehozását, adatok összeillesztését táblák között, valamint lekérdezést adatbázisok és fürtök között.

### <a name="let"></a>Hagyja

[**legyen**](https://docs.microsoft.com/azure/kusto/query/letstatement): Javítja a modularitást és újrafelhasználja. A **let** utasítás lehetővé teszi, hogy egy potenciálisan összetett kifejezést több részre bontson, amelyek mindegyike egy névhez kötődik, és összeírja ezeket a részeket. A **let** utasítás felhasználó által definiált függvények és nézetek (olyan táblák kifejezései, amelyek eredményei új táblának tűnnek) létrehozására is használhatók. A **let** utasítással kötött kifejezések lehetnek skaláris típusúak, táblázatos típusúak vagy felhasználó által definiált függvény (lambdas).

A következő példa egy táblázatos típusú változót hoz létre, és azt egy későbbi kifejezésben használja.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

```Kusto
let LightningStorms =
StormEvents
| where EventType == "Lightning";
let AvalancheStorms =
StormEvents
| where EventType == "Avalanche";
LightningStorms
| join (AvalancheStorms) on State
| distinct State
```

### <a name="join"></a>csatlakozás

[**illesztés**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Két tábla sorainak egyesítése új tábla létrehozásához az egyes táblák megadott oszlopainak értékeivel. Kusto támogatja a teljes körű illesztéstípusok: **fullouter**, **belső**, **innerunique**, **leftanti**, **leftanti, leftanti**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter**, **rightsemi**.

A következő példa két táblát egyesít egy belső illesztéssel.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

```Kusto
let X = datatable(Key:string, Value1:long)
[
    'a',1,
    'b',2,
    'b',3,
    'c',4
];
let Y = datatable(Key:string, Value2:long)
[
    'b',10,
    'c',20,
    'c',30,
    'd',40
];
X 
| join kind=inner Y on Key
```

> [!TIP]
> A **where** and **project** operátorok segítségével az illesztés előtt csökkentheti a bemeneti táblák sorainak és oszlopainak számát. Ha az egyik tábla mindig kisebb, mint a másik, használja az illesztés bal oldali (vezetékes) oldalaként. Az illesztési egyezés oszlopainak ugyanazzal a névvel kell rendelkezniük. Szükség esetén használja a **projektoperátort** az egyik tábla oszlopának átnevezéséhez.

### <a name="serialize"></a>Serialize

[**szerializálás :**](https://docs.microsoft.com/azure/kusto/query/serializeoperator)Szerializálja a sorkészletet, így olyan függvényeket használhat, amelyekszerített adatokat igényelnek, például **row_number()**.

A következő lekérdezés sikeres, mert az adatok szerializáltak.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

A sorkészlet akkor is szerializáltnak minősül, ha **rendezési**, **felső**vagy **tartományoperátorok** eredménye, opcionálisan **projekt,** **projekt-el**, **kiterjesztés**, **ahol**, **elemzés**, **mv-kibontás**vagy **operátorok.**

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Adatbázisközi és fürtközi lekérdezések

[Adatbázisközi és fürtközi lekérdezések](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Az adatbázist ugyanazon a fürtön lekérdezheti, ha a . `database("MyDatabase").MyTable` Az adatbázist lekérdezheti egy távoli fürtön, ha a rendszerre `cluster("MyCluster").database("MyDatabase").MyTable`hivatkozik.

A következő lekérdezés tegy fürtből `MyCluster` hívják meg, és adatokat kérdez le a fürtből. A lekérdezés futtatásához használja a saját fürtnevét és adatbázisnevét.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Felhasználói elemzés

Ez a szakasz olyan elemeket és lekérdezéseket tartalmaz, amelyek bemutatják, hogy milyen egyszerű a felhasználói viselkedések elemzése a Kusto-ban.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics plugin

[**activity_counts_metrics plugin:**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin)Kiszámítja a hasznos tevékenység metrikák (összesszám értékek, különböző számértékek, különböző száma az új értékek, és az összesített eltérő szám). A mérőszámok at minden egyes időszakablakhoz kiszámítják, majd összehasonlítják, és összesítik az összes korábbi időablakhoz.

A következő lekérdezés a napi tevékenységszám kiszámításával elemzi a felhasználók elfogadását.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

```Kusto
let start=datetime(2017-08-01);
let end=datetime(2017-08-04);
let window=1d;
let T = datatable(UserId:string, Timestamp:datetime)
[
'A', datetime(2017-08-01),
'D', datetime(2017-08-01),
'J', datetime(2017-08-01),
'B', datetime(2017-08-01),
'C', datetime(2017-08-02),
'T', datetime(2017-08-02),
'J', datetime(2017-08-02),
'H', datetime(2017-08-03),
'T', datetime(2017-08-03),
'T', datetime(2017-08-03),
'J', datetime(2017-08-03),
'B', datetime(2017-08-03),
'S', datetime(2017-08-03),
'S', datetime(2017-08-04),
];
T
| evaluate activity_counts_metrics(UserId, Timestamp, start, end,
window)
```

### <a name="activity_engagement-plugin"></a>activity_engagement plugin

[**activity_engagement plugin:**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin)Kiszámítja a tevékenység aktivitási arányalapján azonosító oszlop egy csúszó idővonal ablakban. **activity_engagement plugin** használható a DAU, WAU és MAU (napi, heti és havi aktív felhasználók) kiszámításához.

A következő lekérdezés adja vissza az alkalmazás naponta egy alkalmazást használó összes különböző felhasználók aránya az alkalmazás használatával hetente, egy mozgó hét napos ablakban.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> A DAU/MAU számításakor módosítsa a záró adatokat és a mozgó ablakidőszakot (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>activity_metrics plugin

[**activity_metrics plugin:**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin)Kiszámítja a hasznos tevékenység metrikák (különböző számértékek, különböző száma az új értékek, megőrzési arány, és lemorzsolódási arány) alapján az aktuális időszak ablak és az előző időszak ablak.

A következő lekérdezés kiszámítja a lemorzsolódási és megőrzési arány egy adott adatkészlet.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="new_activity_metrics-plugin"></a>new_activity_metrics plugin

[**new_activity_metrics plugin:**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin)Kiszámítja a hasznos tevékenység metrikák (különböző szám értékek, különböző száma az új értékek, megőrzési arány, és lemorzsolódási arány) a kohorsz az új felhasználók. A koncepció a plugin hasonló [**activity_metrics plugin**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), de elsősorban az új felhasználók számára.

A következő lekérdezés kiszámítja a megőrzési és lemorzsolódási arányt az új felhasználók kohorsz (az első héten érkezett felhasználók) hétről hétre időszakában.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mv-expand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="session_count-plugin"></a>session_count plugin

[**session_count plugin:**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin)Kiszámítja a munkamenetek számát az azonosító oszlop alapján egy idővonalon.

A következő lekérdezés a munkamenetek számát adja vissza. Egy munkamenet akkor tekinthető aktívnak, ha egy felhasználói azonosító legalább egyszer megjelenik egy 100-idős időből álló időkereten belül, míg a munkamenet visszatekintési ablaka 41-es időtároló.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

```Kusto
let _data = range Timeline from 1 to 9999 step 1
| extend __key = 1
| join kind=inner (range Id from 1 to 50 step 1 | extend __key=1) on __key
| where Timeline % Id == 0
| project Timeline, Id;
// End of data definition
_data
| evaluate session_count(Id, Timeline, 1, 10000, 100, 41)
| render linechart
```

### <a name="funnel_sequence-plugin"></a>funnel_sequence plugin

[**funnel_sequence plugin**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Kiszámítja a különböző száma a felhasználók, akik vették egy sor államok; azon korábbi és következő állapotok eloszlását mutatja, amelyek a sorozathoz vezettek vagy követték őket.

A következő lekérdezés bemutatja, milyen esemény történik a 2007-es Tornádó események előtt és után.

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion plugin

[**funnel_sequence_completion plugin:**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin)Kiszámítja a tölcsért a befejezett szekvencia lépéseket különböző időszakokban.

A következő lekérdezés ellenőrzi a szekvencia befejezési tölcsért: `Hail -> Tornado -> Thunderstorm -> Wind` egy órás, négy órás és egy napos ( ) "teljes" időpontban.`[1h, 4h, 1d]`

**\[**[**Ide kattintva futtathat lekérdezést**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Functions

Ez [**a**](https://docs.microsoft.com/azure/kusto/query/functions)szakasz a kiszolgálón tárolt újrafelhasználható lekérdezéseket ismerteti. A függvényeket lekérdezések és egyéb függvények hívhatják meg (a rekurzív függvények nem támogatottak).

> [!NOTE]
> Nem hozhat létre függvényeket a súgófürtön, amely írásvédett. Ehhez a részhez használja a saját tesztfürtjét.

A következő példa létrehoz egy függvényt, amely egy állapotnevet (`MyState`) argumentumként vesz fel.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

A következő példa megad egy függvényt, amely texasi állapot adatait kapja meg.

```Kusto
MyFunction ("Texas")
| summarize count()
```

A következő példa törli az első lépésben létrehozott függvényt.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>További lépések

[Kusto lekérdezési nyelv – referencia](https://aka.ms/kustolangref)
