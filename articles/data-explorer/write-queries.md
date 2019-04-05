---
title: Az Azure Data Explorer lekérdezéseket írni
description: Ebben az útmutatóban megismerheti, hogyan hajthat végre alapszintű és speciális lekérdezéseket az Azure Data Explorer.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 6a764c5051aad9123c605ae51807117ef75a7047
ms.sourcegitcommit: 8313d5bf28fb32e8531cdd4a3054065fa7315bfd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2019
ms.locfileid: "59048486"
---
# <a name="write-queries-for-azure-data-explorer"></a>Az Azure Data Explorer lekérdezéseket írni

Ebből a cikkből elsajátíthatja a lekérdezési nyelv használata az Azure Data Explorer hajthat végre alapszintű lekérdezéseket a leggyakoribb szereplőkkel. Akkor is ajánlhassuk, a nyelv speciális funkcióit.

## <a name="prerequisites"></a>Előfeltételek

A lekérdezések a cikkben szereplő kétféleképpen futtathatja:

- Az Azure Data Explorer *súgófürtben* , hogy azt állította be, ezzel elősegítve a tanulást.
    [Jelentkezzen be a fürt](https://dataexplorer.azure.com/clusters/help/databases/samples) , amely tagja az Azure Active directory szervezeti e-mail fiókkal.

- Egy saját fürtöt, amely tartalmazza a StormEvents mintaadatok. További információkért lásd: [a rövid útmutató: Hozzon létre egy Azure Data Explorer fürt és az adatbázis](create-cluster-database-portal.md) és [mintaadatok betöltése az Azure Data Explorer](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>A lekérdezési nyelv áttekintése

Egy lekérdezési nyelvet, az Azure Data Explorer olvasási kérést adatok feldolgozására, és adja vissza az eredményeket. A kérelem egy adatfolyam-modell számára, hogy könnyen olvasható, hozhat létre és automatizálja a szintaxis használatával egyszerű szövegként van megadva. A lekérdezés használja, amely hasonló az SQL-hierarchiában vannak rendszerezve séma entitások: adatbázisok, táblákat és oszlopokat.

A lekérdezés áll egy feladatütemezési lekérdezés utasítások, pontosvesszővel elválasztva (`;`), egy táblázatos kifejezés utasítás folyamatban van legalább egy utasítással, azaz olyan utasításban, amely egy táblázat-szerű háló sorok és oszlopok rendezett adatok eredményez. A lekérdezés táblázatos kifejezés utasítások készít a lekérdezés eredményeit.

A táblázatos kifejezés utasítás szintaxisa a következő táblázatos adatokat tartalmaz a másikra, kezdve az adatforrás (például egy tábla egy adatbázisban, vagy azokból adatokat előállító szolgáltatások operátor), és majd halad végig az Adatátalakítási folyamatot egy táblázatos operátor operátorok vannak kötve együttes használatával a függőleges vonal (`|`) elválasztó karaktert.

Például a következő lekérdezést, egy utasítás, amely egy táblázatos kifejezés utasítást. Az utasítás egy hivatkozást a következő táblába kezdődik `StormEvents` (az ebben a táblában üzemeltető adatbázis az implicit itt, és a kapcsolati adatok egy részét). Az adatokat (sor), hogy a táblázat majd szűrve értékét a `StartTime` oszlopot, majd értéke szerint szűrt és a `State` oszlop. A lekérdezés majd visszaadja a "fennmaradó" sorok száma.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA)**\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Ebben az esetben az eredmény a következő:

|Darabszám|
|-----|
|   23|
| |

További információ: a [lekérdezési nyelvi leírása](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Leggyakoribb operátorok

Az ebben a szakaszban ismertetett operátorok ismertetése a lekérdezéseket az Azure Data Explorer építőkövei. A legtöbb lekérdezések írása ezen operátorok számos tartalmazza.

A súgófürtben lekérdezések futtatására: válassza ki **kattintson ide a lekérdezés futtatásához** minden lekérdezés felett.

Lekérdezések futtatása a saját fürtön:

1. Minden egyes lekérdezés másolása a lekérdezés webes alkalmazásba, majd válassza ki a lekérdezést, vagy vigye a kurzort a lekérdezésben.

1. Válassza ki az alkalmazás tetején **futtatása**.

### <a name="count"></a>count

[**száma**](https://docs.microsoft.com/azure/kusto/query/countoperator): A táblázatban a sorok számát adja vissza.

A következő lekérdezést a StormEvents táblában adja vissza a sorok száma.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA)**\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>hajtsa végre a megfelelő

[**igénybe**](https://docs.microsoft.com/azure/kusto/query/takeoperator): A megadott számú sornyi adatot adja vissza.

A következő lekérdezést a StormEvents tábla öt sort adja vissza. A kulcsszó *korlát* aliasneve *igénybe vehet.*

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d)**\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Nincs garancia arra, hogy mely rekordok lesznek visszaadva, kivéve, ha a forrásadatok van rendezve.

### <a name="project"></a>Projekt

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Oszlopok kijelölése.

A következő lekérdezés az oszlopok egy adott készletét adja vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA)**\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>ahol

[**ahol**](https://docs.microsoft.com/azure/kusto/query/whereoperator): A predikátum megfelelő sorokat részhalmazát tábla szűrése.

A következő lekérdezés szűri az adatokat `EventType` és `State`.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>Rendezés

[**Rendezés**](https://docs.microsoft.com/azure/kusto/query/sortoperator): A bemeneti tábla sorainak rendezése sorrendben be egy vagy több oszlop szerint.

A következő lekérdezés rendezi az adatokat csökkenő sorrend szerint `DamageProperty`.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> A műveletek sorrendje akkor fontos. Próbáljon üzembe `take 5` előtt `sort by`. Más eredményt kapunk?

### <a name="top"></a>felső

[**felső**](https://docs.microsoft.com/azure/kusto/query/topoperator): Visszaadja az első *N* rekordok a megadott oszlopok alapján rendezi.

A következő lekérdezés ugyanazokat az eredményeket, fent egy kisebb operátorral adja vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>Kiterjesztése

[**kiterjesztheti**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Számítási erőforrások származtatott oszlopokat.

A következő lekérdezés egy olyan új oszlop szerint számítástechnika minden sor egy értéket hoz létre.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

Kifejezések tartalmazhatnak a szokásos operátorok (+, -, *, /, %), és nincs a számos hasznos funkció, amelyeket meghívhat.

### <a name="summarize"></a>Összegzés

[**Összegzés**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Összesíti a sorcsoportra.

A következő lekérdezés visszaadja az események száma `State`.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA)**\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

A **összefoglalója** operátor összevonja a ugyanazokat az értékeket tartalmazó sorok a **által** záradék esetében, majd az összesítő függvény (például **száma**) úgy, hogy minden csoport egy egyetlen sorra. Tehát ebben az esetben van egy az egyes állapotokhoz, oszlopok és az abban az állapotban lévő sorok száma.

Nincs a, összesítési függvények széles, és a egy is használhatja őket számos **összefoglalója** előállításához több operátor számított oszlopokat. Például, hogy sikerült lekérni vihar az egyes állapotokhoz és állapot szerint vihar egyedi száma, majd a **felső** beolvasni a leginkább a storm-érintett állapotok.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d)**\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Eredménye egy **összefoglalója** műveletet tartalmaz:

- Minden oszlop **szerint**

- Minden oszlop számított kifejezés

- Egy sor minden értékek kombinációjának

### <a name="render"></a>renderelési

[**leképezési**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Ez a beállítás eredmények egy grafikus kimenetként.

A következő lekérdezést egy oszlopdiagramot jelenít meg.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

A következő lekérdezés egyszerű idő diagramot jelenít meg.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA)**\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

A következő lekérdezés események száma a modulus egy nap, ideje, az óra binned, és időt diagramot jelenít meg.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

A következő lekérdezést a diagram egy több napi sorozatát hasonlítja össze.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA)**\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> A **render** operátor a motor része egy ügyféloldali szolgáltatás helyett. Integrálva van a könnyű használatra nyelvre. A webes alkalmazás támogatja a következő beállításokat: barchart, columnchart, piechart, idődiagramját, és linechart. 

## <a name="scalar-operators"></a>Skaláris operátorok

Ez a szakasz ismerteti a legfontosabb skaláris operátorok némelyike.

### <a name="bin"></a>bin()

[**Bin()**](https://docs.microsoft.com/azure/kusto/query/binfunction): Értékek az egész számra kerekít egy adott doboz méretét többszöröse.

A következő lekérdezést a szám egy nap gyűjtőbe mérettel számítja ki.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>case()

[**Case()**](https://docs.microsoft.com/azure/kusto/query/casefunction): Predikátumok listája kiértékeli, és visszaadja az első eredmény kifejezés, amelynek predikátum teljesül, vagy az utolsó **más** kifejezés. Ez az operátor használatával kategorizálását, vagy a csoport adatokat:

A következő lekérdezés visszaadja az egy olyan új oszlop `deaths_bucket` és a halála száma alapján csoportosítja.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d)**\]**

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

### <a name="extract"></a>extract()

[**extract()**](https://docs.microsoft.com/azure/kusto/query/extractfunction): Egyezés reguláris kifejezést olvas be egy szöveges karakterlánc.

A következő lekérdezést a nyomkövetési kigyűjti a meghatározott attribútumértékek.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d)**\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Ez a lekérdezés használ egy **teszik** utasítás, amely lekötné a nevét (ebben az esetben `MyData`) kifejezést. A hatókör, amelyben a többi a **lehetővé teszik** utasítás jelenik meg (globális hatókör vagy egy függvény törzsében hatókörében), a neve is használható az kötött értékére hivatkozunk.

### <a name="parsejson"></a>parse_json()

[**parse_json()**](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Egy karakterláncot egy JSON-értékként értelmezi, és dinamikusként értékét adja vissza. A felső szintű a **extractjson()** függvényt, amikor szüksége van egy összetett JSON-objektum egynél több elem kibontásához.

A következő lekérdezést a JSON-elemek kiolvassa a tömböt.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d)**\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

A következő lekérdezést a JSON-elemek adja eredményül.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA)**\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

A következő lekérdezés kibontása egy dinamikus adattípusú JSON-elemek.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA)**\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ago()**](https://docs.microsoft.com/azure/kusto/query/agofunction): A megadott időtartam a jelenlegi UTC idő a időpontból.

A következő lekérdezés számára az elmúlt 12 órában adatokat ad vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA)**\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mvexpand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek()

[**startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): A hét, a dátumot, ha meg van adva egy eltolás megjelenítjük tartalmazó kezdetét adja vissza.

A következő lekérdezést a hét különböző eltolású kezdetét adja vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d)**\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Ez a lekérdezés használ a **tartomány** operátor szerinti szűrése, amely értékeket tartalmazó egyoszlopos táblázatot hoz létre. Lásd még: [ **startofday()**](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [ **startofweek()**](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [ **startofyear()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [ **startofmonth()**](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [ **endofday()**](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [ **endofweek()**  ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [ **endofmonth()**](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction), és [ **endofyear()**](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between()

[**between()**](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Megegyezik a bemenet, amely a határokat is beleértve tartományon belül van.

A következő lekérdezés az adatok egy adott dátumtartomány szerint szűrik.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

A következő lekérdezés szűri az adatokat egy adott időtartományban, a három nap enyhe változata szerint (`3d`), a kezdő dátum.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d)**\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Táblázatos operátorok

Kusto rendelkezik, ez a cikk többi szakasza tárgyalja, amelyek sok táblázatos operátor. Itt az fogunk összpontosítani **elemezni**. 

### <a name="parse"></a>elemzése

[**elemezni**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Egy karakterlánc-kifejezés kiértékeli, és elemzi az értékét egy vagy több számított oszlopokat. Elemezni három módja van: egyszerű (alapértelmezett), a következő reguláris kifejezésre, és könnyített.

A következő lekérdezést a nyomkövetési elemzi, és kinyeri a megfelelő, egyszerű elemzése az alapértelmezett értékeket. A kifejezés (néven StringConstant) rendszeres karakterlánc-érték és az egyezés szigorú: kiterjesztett oszlopok egyeznie kell a szükséges típusokat.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d)**\]**

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

A következő lekérdezést a nyomkövetési elemzi, és kinyeri a megfelelő értékek, használatával `kind = regex`. A StringConstant reguláris kifejezés is lehet.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA)**\]**

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

A következő lekérdezést a nyomkövetési elemzi, és kinyeri a megfelelő értékek, használatával `kind = relaxed`. A StringConstant rendszeres karakterlánc-érték és az egyezés Könnyített: kiterjesztett oszlopok részben egyeznek meg a szükséges típusokat.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d)**\]**

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

### <a name="make-series"></a>Gyártmány-sorozat

[**Gyártmány sorozat**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): összesíti együtt például sorcsoportra [összefoglalója](https://docs.microsoft.com/azure/kusto/query/summarizeoperator), létrehoz egy (idő) sorozat, de vektor / értékek kombinációjának minden.

A következő lekérdezést a száma, a storm-események naponta idősorozat készletét adja vissza. A lekérdezés a három hónapos időszak, az egyes állapotokhoz hiányzó bins kitöltését az a konstans 0 mutatja be:

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Miután létrehozott egy (idősorozat) készletét, észlelheti a rendellenes alakzatokat, szezonális mintázatokat, és sokkal több sorozat funkciókat is alkalmazhat.

A következő lekérdezés kinyeri a felső három állapot, amely a legtöbb esemény volt az adott napon:

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d)**\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

További információkért tekintse át a teljes listáját [sorozat funkciók](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions).

## <a name="advanced-aggregations"></a>Speciális aggregátumok

Megismerte, egyszerű összesítés, mint **száma** és **összefoglalója**, ez a cikk elején. Ez a szakasz bemutatja a Speciális beállítások.

### <a name="top-nested"></a>top-nested

[**felső – beágyazott**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Hierarchikus eredmények létrehozása; felső, ahol minden szintje a Lehatolás előző szintű értékek alapján.

Ez az operátor az irányítópult vizualizációs forgatókönyvek esetén, vagy ha a következőhöz hasonló kérdések megválaszolása szükséges: "A felső N értékek K1 (bizonyos összesítési használatával); keresése mindegyikük számára keresse meg, Mik azok a top-M értékek K2 (használatával egy másik összesítő); ..."

A következő lekérdezést a hierarchikus táblázatot ad vissza `State` a legfelső szinten követ `Sources`.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>Pivot() beépülő modul

[**Pivot() beépülő modul**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Egy tábla elforgatása az egyik oszlopának egyedi értékeket kapcsolja a bemeneti tábla a kimeneti tábla több oszlopba. Az operátor elvégzi az aggregációt. Ha szüksége van rájuk minden fennmaradó oszlop értékeit a végeredmény.

A következő lekérdezés olyan szűrőt, és pivots a sorokat oszlopokra.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d)**\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>DCount()

[**DCount()**](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): Egy kifejezés egyedi értékek számának becslése a csoporthoz adja vissza. Használat [ **count()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) számát az összes értéket.

A következő lekérdezés megszámlálja a különböző `Source` által `State`.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d)**\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif()**](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): A kifejezés, amelynek a predikátum kiértékeli a sorok esetében eltérő értékek számának becslése igaz értéket adja vissza.

A következő lekérdezés megszámlálja a különböző értékeket `Source` ahol `DamageProperty < 5000`.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d)**\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcounthll"></a>dcount_hll()

[**dcount_hll()**](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Kiszámítja a **dcount** HyperLogLog eredményekből (által generált [**hll**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) vagy [**hll_merge** ](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction).

A következő lekérdezést a HLL algoritmust használja a count létrehozásához.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA)**\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="argmax"></a>arg_max()

[**arg_max()**](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Egy sor megkeresi a lehető legnagyobbra növeli a kifejezés, és a egy másik kifejezés értékét adja vissza csoportban (vagy * az egész sort vissza).

A következő lekérdezés az idő az elmúlt elárasztó jelentés az egyes állapotokban adja vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset()**](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): A csoport, amely egy kifejezés egyedi értékek tömbje dinamikus (JSON) adja vissza.

Az alábbi lekérdezés minden esetben amikor áramlik egyes által jelentett, és létrehoz egy tömböt a készletből az eltérő értékeket ad vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA)**\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mvexpand"></a>mvexpand

[**mvexpand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): A dinamikus típusmegadású oszlop több értéket próbaidőszakában kibővíti az, hogy a gyűjteményben szereplő összes értékhez beolvas egy külön sort. Egy kibontott sorban az összes többi oszlop ismétlődik. Fontos makelist ellentéte.

A következő lekérdezést hoz létre egy csoportot, és oldalméretről bemutatása mintaadatok állít elő a **mvexpand** képességeket.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA)**\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mvexpand FloodReports
```

### <a name="percentiles"></a>percentiles()

[**percentiles()**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Visszaadja a megadott becsült [**legközelebbi rang PERCENTILIS**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) a lakosság kifejezés által meghatározott. Pontossága attól függ, hogy a régióban a PERCENTILIS sokaságát sűrűsége. Csak abban a környezetben, az összesítés belül használható [**összefoglalója**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator).

A következő lekérdezést. percentilisei storm időtartamának számítja ki.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

A következő lekérdezés alapján számítja ki. percentilisei storm időtartamának állapota, és normalizálja az adatokat öt perces bins (`5m`).

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d)**\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Adatbázisközi adatkészlet

Ez a szakasz ismertet, amelyek lehetővé teszik, hogy az összetett lekérdezések létrehozása, adatok táblák és a lekérdezés adatbázisok és -fürtök csatlakozás elemeket.

### <a name="let"></a>lehetővé teszik

[**lehetővé teszik**](https://docs.microsoft.com/azure/kusto/query/letstatement): Javítja a modularitás és újbóli felhasználáshoz. A **teszik** utasítás lehetővé teszi, hogy egy meglehetősen összetett kifejezést felosztása több részből, minden egyes nevét kötve, és a compose együtt a részeket. A **teszik** utasítás is használható, felhasználó által definiált függvények és -nézetek (táblák, amelyek eredményei egy új táblát kinéznie keresztül expressions) létrehozásához. Kifejezések kötelezőnek egy **teszik** utasítás táblázatos típusa, vagy a felhasználó által definiált függvény (lambdas) skaláris típusú is lehet.

Az alábbi példa létrehoz egy táblázatos típusú változót és annak egy későbbi kifejezésben.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d)**\]**

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

[**Csatlakozás**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Az új tábla kialakításához, a megadott oszlopoknak az egyes táblákból egyező értékei alapján két tábla sorainak egyesítése. Kusto széles skáláját illesztési típust támogat: **fullouter**, **belső**, **innerunique**, **leftanti**, **leftantisemi **, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter **, **rightsemi**.

Az alábbi példa két tábla belső illesztést csatlakozik.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==)**\]**

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
> Használat **ahol** és **projekt** operátorok csökkentése érdekében a sorok és a csatlakozás előtt, a bemeneti tábla oszlopainak számát. Ha egy tábla minden esetben kisebb, mint a másik, az legyen az illesztés bal oldali (védőeszközön) oldalán. Az oszlopok az illesztési egyezéssel ugyanazzal a névvel kell rendelkeznie. Használja a **projekt** operátor, ha szükséges, nevezze át az egyik táblázat oszlopában.

### <a name="serialize"></a>szerializálása

[**szerializálható**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): A sorhalmaz, így használhatja a szerializált adatok, például átadhatók függvényt visszaadó szerializálja **row_number()**.

A következő lekérdezés sikeresen létrejön, mert a szerializált adatok.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d)**\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

A sor-készlet is figyelembe veszi, ha eredménye szerializált: **rendezési**, **felső**, vagy **tartomány** operátorok, követheti **projekt**, **projekt távolléti**, **kiterjesztése**, **ahol**, **elemezni**, **mvexpand**, vagy **igénybe** operátorok.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA)**\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Adatbázisközi és a fürt közötti lekérdezéseket.

[Adatbázisközi és a fürt közötti lekérdezések](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Úgy lekérdezheti, ha egy adatbázis ugyanazon a fürtön, hivatkozó `database("MyDatabase").MyTable`. Úgy lekérdezheti, ha egy adatbázis egy távoli fürtön rá, `cluster("MyCluster").database("MyDatabase").MyTable`.

A következő lekérdezés az egyik fürtről nevezik, és lekéri az adatokat `MyCluster` fürt. A lekérdezés futtatására használja a saját fürt neve és az adatbázis nevét.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>A felhasználói elemzéséhez

Ez a szakasz tartalmazza az elemeket, és a lekérdezések, amelyek bemutatják, mennyire egyszerű is Kusto a felhasználói viselkedés elemzését.

### <a name="activitycountsmetrics-plugin"></a>activity_counts_metrics beépülő modul

[**activity_counts_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Kiszámítja a hasznos tevékenységi metrikák (értékek teljes száma, eltérők darabszáma értékek, új értékeket darabszáma és összesített eltérők darabszáma). Metrikák kiszámítása minden egyes időtartomány, majd képest, és összesítve, és az összes korábbi időablakokat.

A következő lekérdezés elemzi a felhasználói bevezetésére szerint kiszámításának napi tevékenységek számát.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d)**\]**

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

### <a name="activityengagement-plugin"></a>activity_engagement beépülő modul

[**activity_engagement beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Tevékenység engagement arány keresztül egy idővonalon csúszóablakban azonosító oszlop alapján számítja ki. **activity_engagement beépülő modul** aktív felhasználók Naponta WAU és (napi, heti és havi aktív felhasználók) MAU kiszámításához használható.

A következő lekérdezés olyan alkalmazást, naponta, hetente, az alkalmazás használatával egy 7 napos mozgó időablak az egyedi felhasználók teljes képest egyedi felhasználók teljes aránya adja vissza.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-01);
let _end = datetime(2017-01-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+100*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
// Calculate DAU/WAU ratio
| evaluate activity_engagement(['id'], _day, _start, _end, 1d, 7d)
| project _day, Dau_Wau=activity_ratio*100
| render timechart
```

> [!TIP]
> Aktív felhasználók Naponta/MAU kiszámításakor, módosítsa a végfelhasználók adatokat és a mozgó ablak időszak (OuterActivityWindow).

### <a name="activitymetrics-plugin"></a>activity_metrics beépülő modul

[**activity_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Hasznos tevékenységi metrikák (értékek eltérők száma, új értékeket, megtartási aránya és lemorzsolódási rátához darabszáma), és az előző időszak ablak az aktuális időszak ablak alapján számítja ki.

A következő lekérdezést egy adott adatkészlet és a megőrzési díjaival számítja ki.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-01-02);
let _end = datetime(2017-05-31);
range _day from _start to _end step 1d
| extend d = tolong((_day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
| where _day > datetime(2017-01-02)
| project _day, id
// Calculate weekly retention rate
| evaluate activity_metrics(['id'], _day, _start, _end, 7d)
| project _day, retention_rate*100, churn_rate*100
| render timechart
```

### <a name="newactivitymetrics-plugin"></a>new_activity_metrics beépülő modul

[**new_activity_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Hasznos tevékenységi metrikák (értékek eltérők száma, új értékeket, megtartási aránya és lemorzsolódási rátához darabszáma) kiszámítja az új felhasználók kohorsz. Ez a beépülő modul koncepciójuk hasonló [**activity_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), azonban az új felhasználók összpontosít.

A következő lekérdezést egy megőrzési és adatváltozás aránya egy over-hét hét ablak kiszámítja az új felhasználók kohorsz (az első héten a érkező felhasználók).

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d)**\]**

```Kusto
// Generate random data of user activities
let _start = datetime(2017-05-01);
let _end = datetime(2017-05-31);
range Day from _start to _end step 1d
| extend d = tolong((Day - _start)/1d)
| extend r = rand()+1
| extend _users=range(tolong(d*50*r), tolong(d*50*r+200*r-1), 1)
| mvexpand id=_users to typeof(long) limit 1000000
// Take only the first week cohort (last parameter)
| evaluate new_activity_metrics(['id'], Day, _start, _end, 7d, _start)
| project from_Day, to_Day, retention_rate, churn_rate
```

### <a name="sessioncount-plugin"></a>session_count beépülő modul

[**session_count beépülő modul**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Kiszámítja az azonosító oszlop alapján keresztül ütemterv munkamenetek száma.

A következő lekérdezés visszaadja a munkamenetek száma. A munkamenet aktív, ha a felhasználói azonosító megjelenik legalább egyszer egy 100-időszeletek időkeretét közben a munkamenet meg visszaírt ablak 41 időszeletek számít.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d)**\]**

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

### <a name="funnelsequence-plugin"></a>funnel_sequence beépülő modul

[**funnel_sequence beépülő modul**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Kiszámítja a felhasználók, akik állapotok; egy feladatütemezés végrehajtása darabszáma előző és a következő állapotok vezettek, vagy a feladatütemezés követte elosztását mutatja.

A következő lekérdezés jeleníti meg, milyen esemény előtt és után tornádó eseményeit a 2007 történik.

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA)**\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnelsequencecompletion-plugin"></a>funnel_sequence_completion beépülő modul

[**funnel_sequence_completion beépülő modul**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Kiszámítja a tölcsér befejezett feladatütemezési lépések belül különböző időszakokra.

A következő lekérdezés ellenőrzi a feladatütemezés, a befejezési tölcsér: `Hail -> Tornado -> Thunderstorm -> Wind` az egy órás, négy óra és a egy nap "teljes" időtartama (`[1h, 4h, 1d]`).

**\[**[**Kattintson a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA)**\]**

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

Ez a szakasz ismertet [ **funkciók**](https://docs.microsoft.com/azure/kusto/query/functions): újrafelhasználható, amelyek a kiszolgálón tárolt lekérdezések. Lekérdezések és egyéb funkciók (rekurzív funkciók nem támogatottak) funkciókat is hivatkozhat.

> [!NOTE]
> A csak olvasható súgófürtben függvények nem hozhatók létre. Ez a rész a saját tesztfürt használja.

Az alábbi példa létrehoz egy függvényt, amely egy állam neve (`MyState`) argumentumként.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

Az alábbi példa meghívja egy függvény, amely az adatok beolvasása a Texas állapota.

```Kusto
MyFunction ("Texas")
| summarize count()
```

A következő példa törli a függvényt, amely az első lépésben jött létre.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>További lépések

[Kusto lekérdezési nyelv leírása](https://aka.ms/kustolangref)
