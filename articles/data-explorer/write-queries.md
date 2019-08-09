---
title: Írási lekérdezések az Azure Adatkezelő
description: Ebben a útmutatóban megismerheti, hogyan végezheti el az Azure Adatkezelő alapszintű és fejlettebb lekérdezéseit.
author: orspod
ms.author: orspodek
ms.reviewer: mblythe
ms.service: data-explorer
ms.topic: conceptual
ms.date: 04/07/2019
ms.openlocfilehash: 80d3eaaf7e588766d62f5e5885d75e61c590970e
ms.sourcegitcommit: aa042d4341054f437f3190da7c8a718729eb675e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68881182"
---
# <a name="write-queries-for-azure-data-explorer"></a>Írási lekérdezések az Azure Adatkezelő

Ebből a cikkből megtudhatja, hogyan használhatja az Azure Adatkezelő lekérdezési nyelvét a leggyakoribb operátorokkal rendelkező alapszintű lekérdezések végrehajtásához. Emellett a nyelv fejlettebb funkcióinak is kikerülnek.

## <a name="prerequisites"></a>Előfeltételek

Az ebben a cikkben szereplő lekérdezéseket kétféleképpen futtathatja:

- Az Azure Adatkezelő *súgójában* , amelyet a tanuláshoz hoztunk létre.
    [Jelentkezzen be a fürtbe](https://dataexplorer.azure.com/clusters/help/databases/samples) egy olyan szervezeti e-mail-fiókkal, amely az Azure Active Directory tagja.

- A StormEvents-mintaadatok részét képező saját fürtön. További információ: gyors útmutató [: Hozzon létre egy Azure adatkezelő-](create-cluster-database-portal.md) fürtöt és-adatbázist, és a mintaadatok betöltését [Az Azure Adatkezelőba](ingest-sample-data.md).

    [!INCLUDE [data-explorer-storm-events](../../includes/data-explorer-storm-events.md)]

## <a name="overview-of-the-query-language"></a>A lekérdezés nyelvének áttekintése

Az Azure Adatkezelő lekérdezési nyelve egy írásvédett kérelem, amely az adatfeldolgozást és az eredményeket jeleníti meg. A kérelem egyszerű szövegként van megadva, egy adatáramlási modell használatával, amely a szintaxis egyszerű elolvasását, elkészítését és automatizálását teszi lehetővé. A lekérdezés olyan séma-entitásokat használ, amelyek az SQL-hez hasonló hierarchiába vannak rendezve: adatbázisok, táblák és oszlopok.

A lekérdezés egy pontosvesszővel (`;`) tagolt lekérdezési utasítások sorozatát tartalmazza, amelynek legalább egy táblázatos kifejezési utasítása egy táblázatos kifejezés, amely egy tábla – például oszlopokból és sorokból álló hálóba rendezett adatokat eredményez. A lekérdezés táblázatos kifejezésének utasításai a lekérdezés eredményét eredményezik.

A táblázatos kifejezés utasítás szintaxisa táblázatos adatfolyamattal rendelkezik az egyik táblázatos lekérdezési operátorról a másikra, az adatforrástól kezdve (például egy adatbázis táblájában vagy egy adatokat előállító operátoron), majd egy Adatátalakítási csoporton keresztül áramlik. a pipe (`|`) elválasztójának használatával kötött operátorok.

Például a következő lekérdezés egyetlen utasítással rendelkezik, amely egy táblázatos kifejezési utasítás. Az utasítás a (z) nevű `StormEvents` táblára mutató hivatkozással kezdődik (a táblát üzemeltető adatbázis ebben az esetben implicit, a kapcsolati adatok részét képezi). Ezután az `StartTime` oszlop értékével szűri az adott tábla adatsorait, majd az `State` oszlop értéke alapján szűri. A lekérdezés ezután a "túlélő" sorok számát adja vissza.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWws1VISSxJLQGyNYwMDMx1DQ11DQw1FRLzUpBU2aArMgIpQjGvJFXB1lZByc3HP8jTxVFJQQEkm5xfmlcCAHoR9euCAAAA) **\]**

```Kusto
StormEvents
| where StartTime >= datetime(2007-11-01) and StartTime < datetime(2007-12-01)
| where State == "FLORIDA"  
| count
```

Ebben az esetben az eredmény a következő:

|Count|
|-----|
|   23|
| |

További információért lásd a [lekérdezés nyelvi referenciáját](https://aka.ms/kustolangref).

## <a name="most-common-operators"></a>Leggyakoribb operátorok

Az ebben a szakaszban szereplő operátorok az Azure Adatkezelő lekérdezések megismerésére szolgáló építőelemek. A legtöbb írási lekérdezés több ilyen operátort is tartalmaz.

Lekérdezések futtatása a Súgó fürtön: **kattintson a kattintás gombra az** egyes lekérdezések feletti lekérdezés futtatásához.

Lekérdezések futtatása a saját fürtön:

1. Másolja az egyes lekérdezéseket a web-alapú lekérdezési alkalmazásba, majd válassza ki a lekérdezést, vagy helyezze a kurzort a lekérdezésbe.

1. Az alkalmazás tetején válassza a **Futtatás**lehetőséget.

### <a name="count"></a>count

[**darabszám**](https://docs.microsoft.com/azure/kusto/query/countoperator): A tábla sorainak számát adja vissza.

A következő lekérdezés a StormEvents tábla sorainak számát adja vissza.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRSM4vzSsBALU2eHsTAAAA) **\]**

```Kusto
StormEvents | count
```

### <a name="take"></a>take

[**igény**](https://docs.microsoft.com/azure/kusto/query/takeoperator): Az adatok megadott számú sorát adja vissza.

A következő lekérdezés a StormEvents tábla öt sorát adja vissza. A kulcsszó *korlátja* a Take aliasa *.*

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwBQDEz2b8FAAAAA%3d%3d) **\]**

```Kusto
StormEvents | take 5
```

> [!TIP]
> Nincs garancia arra, hogy mely rekordok lesznek visszaadva, kivéve, ha a forrásadatok rendezése megtörtént.

### <a name="project"></a>project

[**projekt**](https://docs.microsoft.com/azure/kusto/query/projectoperator): Kijelöli az oszlopok egy részhalmazát.

A következő lekérdezés az oszlopok egy adott halmazát adja vissza.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUShJzE5VMAWxCorys1KTSxSCSxKLSkIyc1N1FFzzUiAMoFgJiA%2fSFlJZAGS6JOYmpqcGFOUXpBaVVAKlCjKL81NS%2fRKLihJLMstSAY%2buIINnAAAA) **\]**

```Kusto
StormEvents
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="where"></a>ahol

[**hol**](https://docs.microsoft.com/azure/kusto/query/whereoperator): Kiszűr egy táblázatot a predikátumot teljesítő sorok részhalmazára.

Az alábbi lekérdezés a és `EventType` `State`a alapján szűri az adathalmazokat.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAEWMPQvCMBCGd8H%2fcFuWro4dBOvHkgoJOB%2fm0KjJhetRKfjjNe3g9n49r1OW1I2UdVivPvC%2bkxDM3k%2bFoG3B7F%2fMwQDmAE5Rl%2fCydceTPfjemsopPgk2VRXhB121TkV9TNRAl8MiZrz53zeww4Q3OgsXEp1%2bVYkDB7IoghpH%2bgI9OH8WnwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="sort"></a>Rendezés

[**Rendezés**](https://docs.microsoft.com/azure/kusto/query/sortoperator): A bemeneti tábla sorait egy vagy több oszlop szerint rendezheti sorrendbe.

A következő lekérdezés csökkenő sorrendben `DamageProperty`rendezi az adathalmazokat.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NPQvCMBCGd8H%2fcFuXrI4dBOvHEoUGnM%2fm0KjphctRKfjjNe0guL0fvM%2fbKktsBuo1LxdveN1ICCbvxkRQ11Btn8y%2bAuw9tIo6h%2bd1uz%2fYnTvaquwyi8JlhA1GvNJJOJHoCJ5yV2rFB8GqqCR8p04LSdSFSAaa3s9iopvfu%2fnDfasUMnuyKIIaBvoAtvGMsb4AAAA%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| sort by DamageProperty desc
| take 5
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

> [!NOTE]
> A műveletek sorrendje fontos. Próbálja megismételni a következőt:. `sort by` `take 5` Különböző eredményekhez juthat?

### <a name="top"></a>Top

[**felül**](https://docs.microsoft.com/azure/kusto/query/topoperator): Az első *N* rekordot adja vissza a megadott oszlopok szerint rendezve.

A következő lekérdezés ugyanazt az eredményt adja vissza, mint a fenti egy kevesebb operátorral.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2NOwvCMBSFd8H%2fcLcsWR07CNbHkgoJOMfmohGTG24vlYA%2fXtsOgtt5cL5jhTi1I2YZ1qs3vO7ICLN3tSA0Daj9kygo8DmAFS9LeNna48kcXGfUtBMqsIFrhZ1P%2foZnpoIsFQIO%2fdQXpgf2MgFYXEyooc1hETNU%2f071H%2bRblThQQOOZvcQRP1rSng21AAAA) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| project StartTime, EndTime, State, EventType, DamageProperty, EpisodeNarrative
```

### <a name="extend"></a>kiterjesztése

[**kiterjesztés**](https://docs.microsoft.com/azure/kusto/query/extendoperator): Származtatott oszlopok számítása.

A következő lekérdezés egy új oszlopot hoz létre az egyes sorokban lévő értékek kiszámításával.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OvQ7CMAyEdyTewVuWMDJ2QGr5WQJSKzGHxoIiEkeuKVTi4WmooBKbfXeffaUQ%2b6LDIO189oLHBRnhs1d9RMgyUOsbkVNgg4NSrIzicVVud2ZT7Y1KnFCEJZx6yK23ZzwwRWTpwWFbJx%2bfggOf39lKQwEyKIKrGo%2bwSEdZ0pyCkemKtUyi%2fib1j9ZjDz311H9%2fBys2LTk0lhPT4RvwA3pn6AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where EventType == 'Flood' and State == 'WASHINGTON'
| top 5 by DamageProperty desc
| extend Duration = EndTime - StartTime
| project StartTime, EndTime, Duration, State, EventType, DamageProperty, EpisodeNarrative
```

A kifejezések tartalmazhatják az összes szokásos operátort (+,-, *,/,%), és számos hasznos függvényt hívhatnak meg.

### <a name="summarize"></a>Összegzés

[**Összegzés**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator): Sorok összesítése.

A következő lekérdezés az események `State`számát adja vissza.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pUo2CqAaQ1NhaRKheCSxJJUAB%2fedDI3AAAA) **\]**

```Kusto
StormEvents
| summarize event_count = count() by State
```

Az **összegző** operátor csoportosítja azokat a sorokat, amelyek azonos értékekkel rendelkeznek a **by** záradékban, majd az összesítési függvényt (például **darabszám**) használják az egyes csoportok egyetlen sorba való összekapcsolásához. Így ebben az esetben minden állapothoz tartozik egy sor, valamint egy oszlop az adott állapotban lévő sorok számához.

Számos aggregációs függvény létezik, és több számított oszlop létrehozásához használhatja őket egy **Összefoglaló** operátorban. Például megszerezheti az egyes állapotokban lévő viharok számát és az egyes állapotokban az egyedi Storms számot, majd a **Top** használatával beszerezhetik a legtöbb Storm által érintett állapotot.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIBkk455fmlSjYKiSDaA1NHYWQyoJU%2fzSwXDFQPAUiAdYPktJUSKoE6kwsSQUZVpJfoGAKEYGblZJanAwAgbFb73QAAAA%3d) **\]**

```Kusto
StormEvents
| summarize StormCount = count(), TypeOfStorms = dcount(EventType) by State
| top 5 by StormCount desc
```

Egy **Összefoglaló** művelet eredménye:

- Minden, a **által** megnevezett oszlop

- Egy oszlop az egyes számított kifejezésekhez

- Egy sor az egyes értékek szerinti kombinációhoz

### <a name="render"></a>renderelési

[**Megjelenítés**](https://docs.microsoft.com/azure/kusto/query/renderoperator): Az eredményeket grafikus kimenetként jeleníti meg.

Az alábbi lekérdezés egy oszlopdiagram megjelenítését jeleníti meg.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWMsQ7CQAxDdyT%2bIWMrdSgbSxmQ2Nj6Aei4Ru0hkqA0VwTi49uUBRZL9rPdmiidJmQbt5sPjJkoaHojoGeXKJmtWbUoK6DUQQNh6osj9onPwUq4vqC1YLjORc2Dpef2OaD%2bPcEBdvu6dvZQuWG077b6LTlV5A4VotwzcRyC2gxU6ktSqQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize event_count=count(), mid = avg(BeginLat) by State
| sort by mid
| where event_count > 1800
| project State, event_count
| render columnchart
```

A következő lekérdezés egy egyszerű idődiagramot jelenít meg.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIBYnFJ%2beX5pXYgkkNTYWkSoWkzDyN4JLEopKQzNxUHQXDFE2QtqLUvJTUIoUSoFhyBlASAAyXWQJWAAAA) **\]**

```Kusto
StormEvents
| summarize event_count=count() by bin(StartTime, 1d)
| render timechart
```

A következő lekérdezés egy nap időkerete szerint számítja ki az eseményeket, dobozolni órákig, és megjeleníti az idődiagramot.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEADWNQQqDMBRE90LvMBtBwY0HcNkT2L2k8UuEJh9%2bfqSWHt4k4GZghpk3s7L450FB46P5g75KYYXjJJiwfZilm9WIvnZPaDGuGDC6vnRj8t7I%2fiNQ2S%2bWU9CpatfjfVZKLbLo7WGiLZnkGxJoxlqX%2bRf81ZbyiAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend hour = floor(StartTime % 1d , 1h)
| summarize event_count=count() by hour
| sort by hour asc
| render timechart
```

A következő lekérdezés összehasonlítja a napi adatsorozatokat egy idődiagramon.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACWPSwvCMBCE74L%2fYSgIFXrpD%2bihaKzxkUBTXyeputKCbSCmvvDHm9TL7gwzsN8qq03DHtTa%2b3DwBb0stRdUujMJrjetTQhlS2OLuiGMEF8QIa7GvvusyJBPLaFuEQbZZjWDnGHN9nwigyhYp1wwt7c8z7jgqZM7riZSKC6cFjIv5pimS1n4SLAdFixX7OCMzFkmRdAfundNU5r6QyAPejzrrrVJP8MxTu8eN%2fqT%2bL5xL5CBdcjnyrH%2fALPTSKnkAAAA) **\]**

```Kusto
StormEvents
| extend hour= floor( StartTime % 1d , 1h)
| where State in ("GULF OF MEXICO","MAINE","VIRGINIA","WISCONSIN","NORTH DAKOTA","NEW JERSEY","OREGON")
| summarize event_count=count() by hour, State
| render timechart
```

> [!NOTE]
> A **Render** operátor egy ügyféloldali szolgáltatás, amely nem része a motornak. Az egyszerű használat érdekében integrálva van a nyelvbe. A webalkalmazás a következő beállításokat támogatja: barchart, columnchart, piechart, idődiagramját és linechart. 

## <a name="scalar-operators"></a>Skaláris operátorok

Ez a szakasz a legfontosabb skaláris operátorokat ismerteti.

### <a name="bin"></a>bin ()

[**bin ()** ](https://docs.microsoft.com/azure/kusto/query/binfunction): Egy adott raktárhely méretének többszörösére kerekíti az értékeket.

A következő lekérdezés kiszámítja a darabszámot egy nap gyűjtői méretével.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVWwU0hJLEktATI1jAwMzHUNjHQNTTQVEvNSkBTZYCoyMtQEGVdcmpubWJRZlaqQCrIiPjm%2fNK9EwVYBTGtoKiRVKiRl5mnAjdJRMEzRBABIhjnmkwAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime > datetime(2007-02-14) and StartTime < datetime(2007-02-21)
| summarize event_count = count() by bin(StartTime, 1d)
```

### <a name="case"></a>eset ()

[**eset ()** ](https://docs.microsoft.com/azure/kusto/query/casefunction): Kiértékeli a predikátumok listáját, és az első eredmény kifejezést adja vissza, amelynek predikátuma teljesül, vagy az utolsó **Else** kifejezés. Az operátor használatával kategorizálhatja vagy csoportosíthatja az adatcsoportot:

A következő lekérdezés egy új oszlopot `deaths_bucket` ad vissza, és szám szerint csoportosítja a haláleseteket.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOwQrCQAxE74X%2bQ9hTCwX14FFBaK9e%2bgGS7gZdbFrYZEXFj7dbqgfNbfJmhml1DNzcaFDJsxdIZMbgnwSOUC8Cu%2fQq6lnUPpDVEroHtIpKKUB3pcEt7lMX7ZV0ClkUgiLPYLqlaQ%2fbdQWmx3AmU%2f2gTUJMzkf%2bYwkJY99%2fiDmuDqac545Bv3MAxb4Bic1Oy88AAAA%3d) **\]**

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

### <a name="extract"></a>Extract ()

[**extract ()** ](https://docs.microsoft.com/azure/kusto/query/extractfunction): Egy szöveges karakterlánc reguláris kifejezésének egyezését kapja meg.

A következő lekérdezés kibontja a megadott attribútum-értékeket egy nyomkövetésből.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQrCMBBE74X%2bw9BTojHYagSVHJRevXkrHqJdpVBbSVew4McbFYungeXtvKmJsetzxw4WZQh2x5og9t6daIWOfdVcJIpkY1OFrc0U8rt3XLWNTbOZnhultU4UfoD5A4zRmVkovInDOo6%2bojh6gh5MTTmQwR0uQckiGb5FMZ0s9WEsQ3uo%2fixSccT9jdqz8ORqKTECV1cSaSdfq2k6L8oAAAA%3d) **\]**

```Kusto
let MyData = datatable (Trace: string) ["A=1, B=2, Duration=123.45,...", "A=1, B=5, Duration=55.256, ..."];
MyData
| extend Duration = extract("Duration=([0-9.]+)", 1, Trace, typeof(real)) * time(1s)
```

Ez a lekérdezés egy **let** utasítást használ, amely egy nevet (ebben az esetben `MyData`) egy kifejezéshez köti. Annak a hatókörnek a többi részén, amelyben az " **let** " utasítás megjelenik (globális hatókör vagy a függvény törzsének hatóköre), a név használható a megkötött értékre való hivatkozáshoz.

### <a name="parse_json"></a>parse_json()

[**parse_json()** ](https://docs.microsoft.com/azure/kusto/query/parsejsonfunction): Egy karakterláncot JSON-értékként értelmez, és dinamikusként adja vissza az értéket. A **extractjson ()** függvény használata akkor jobb, ha egy összetett JSON-objektum egynél több elemét kell kinyerni.

A következő lekérdezés kibontja a JSON-elemeket egy tömbből.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAHWPQQuCQBCF74L%2fYdmLBSJ6EGKjU17r1E0kJh1C2XZlHc0w%2f3ur1s1O896bB%2fONRGKnVwIE7MAKOwhuEtnmYiBHwRoypbpvXSf1Bl60BqjUiot04B3IFrmIol0Q%2bpPLdauIi3iyj9KWojCcNfRWx7NuqEiw48KaMRu9bO86y3HXeTPsCVXBzvg8amlpajANXqtGq4VmO5VqoyvM6dsKfkhpmAUzkf9nM9OtLi3reg79ar788AEVX8GkOAEAAA%3d%3d) **\]**

```Kusto
let MyData = datatable (Trace: string)
['{"duration":[{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}]}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.duration[0].value, NewCol.duration[0].valcount, NewCol.duration[0].min, NewCol.duration[0].max, NewCol.duration[0].stdDev
```

A következő lekérdezés kibontja a JSON-elemeket.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAE2OwQqCQBCG74LvsOzFBBE9CLHRKa916hYRkw6RbLuyO5pRvXvrGtZpvn9m4P8kEts%2bSiBga1a7QXCWyBZ7AxUKZslc1SVmh%2bjJe5AdcpHnyzRLxlTpThEXxRhvV%2bVOWeYZBseFZ0t1iT0XLryj4yoMprIweDEcCFXNdnjfaOnaWzAWT43VamqPx6fW6AYr%2bn6l3iH5S95hXjiLH8Mw82TxAQvJEB%2fsAAAA) **\]**

```Kusto
let MyData = datatable (Trace: string) ['{"value":118.0,"valcount":5.0,"min":100.0,"max":150.0,"stdDev":0.0}'];
MyData
| extend NewCol = parse_json(Trace)
| project NewCol.value, NewCol.valcount, NewCol.min, NewCol.max, NewCol.stdDev
```

A következő lekérdezés dinamikus adattípussal kibontja a JSON-elemeket.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAD2NMQvCMBBG90D%2bw5GphVLSoSARt65ubuJwJjdU0lZiWlrU%2f25MotO9x8H7LHk4bh16hAOYcDxeLUFxcqhJgdlGHHpdcnbOWDzFgnYmoZpmV8tK6GkePTmh2q8N%2fRg%2bUkbGNXAb%2beFNR4tQQd7lZc9ZGuXsBXc33Uh7iJN1jFdZcvunIf5HXCvOEqf2BwXmDCnKAAAA) **\]**

```Kusto
let MyData = datatable (Trace: dynamic)
[dynamic({"value":118.0,"counter":5.0,"min":100.0,"max":150.0,"stdDev":0.0})];
MyData
| project Trace.value, Trace.counter, Trace.min, Trace.max, Trace.stdDev
```

### <a name="ago"></a>ago()

[**ezelőtt ()** ](https://docs.microsoft.com/azure/kusto/query/agofunction): Kivonja a megadott TimeSpan az aktuális UTC időpontból.

A következő lekérdezés az elmúlt 12 órában szolgáltatja az adatok értékét.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA1WOQQ6CQAxF9yTc4S8hQcmQuNSNR4ALTKQyJDAlnSIuPLwzJGrctM3v+7+t684R7qMEhW6MafQUMJAnsUoIdl4mQm/VVrC+h0Z6shFOINZAIc/qOql24KIEL8nIAuWYohC6sfQB9yjtPtPA8SrhmGeLjF7RjTO1Gu+cIdYPVHjeisOpLyukKTbjYml5piuvXknwIU1lGlPm2Qvzg55L+u+b9udIyOZI6LfHZf/YNK58Ay2HrbAEAQAA) **\]**

```Kusto
//The first two lines generate sample data, and the last line uses
//the ago() operator to get records for last 12 hours.
print TimeStamp= range(now(-5d), now(), 1h), SomeCounter = range(1,121)
| mv-expand TimeStamp, SomeCounter
| where TimeStamp > ago(12h)
```

### <a name="startofweek"></a>startofweek ()

[**startofweek ()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction): A dátumot tartalmazó hét kezdetét adja vissza, eltolással eltolt, ha meg van megadva

A következő lekérdezés a hét kezdetét adja vissza eltérő eltolásokkal.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEACtKzEtPVchPSytOLVFIK8rPVdA1VCjJVzBUKC5JLVAw5OWqUSgoys9KTS5RKE9NzQ4uSSwqUbAFygLp%2fDSQkEZefrmGpg7UEE0dCA0AdE3lv1kAAAA%3d) **\]**

```Kusto
range offset from -1 to 1 step 1
| project weekStart = startofweek(now(), offset),offset
```

Ez a lekérdezés a **tartomány** operátort használja, amely az értékek egyoszlopos tábláját hozza létre. Lásd még: [**startofday ()** ](https://docs.microsoft.com/azure/kusto/query/startofdayfunction), [**startofweek ()** ](https://docs.microsoft.com/azure/kusto/query/startofweekfunction), [**STARTOFYEAR ()** ](https://docs.microsoft.com/azure/kusto/query/startofyearfunction)), [**STARTOFMONTH ()** ](https://docs.microsoft.com/azure/kusto/query/startofmonthfunction), [**endofday ()** ](https://docs.microsoft.com/azure/kusto/query/endofdayfunction), [**endofweek ()** ](https://docs.microsoft.com/azure/kusto/query/endofweekfunction), [**ENDOFMONTH ()** ](https://docs.microsoft.com/azure/kusto/query/endofmonthfunction)és [**ENDOFYEAR ()** ](https://docs.microsoft.com/azure/kusto/query/endofyearfunction).

### <a name="between"></a>between()

[**között ()** ](https://docs.microsoft.com/azure/kusto/query/betweenoperator): Megfelel a befogadó tartományon belüli bemenetnek.

Az alábbi lekérdezés egy adott Dátumtartomány alapján szűri az adathalmazt.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp4ChrixgaYmyKTk%2fNK8EgBluyagXgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. datetime(2007-07-30))
| count
```

A következő lekérdezés egy adott Dátumtartomány alapján szűri az adatait, a kezdő dátumtól számított három nap (`3d`) kis változatával.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQguSSwqCcnMTVVISi0pT03NU9BISSxJLQGKaBgZGJjrApGRuaaCnp6CcYomSF9yfmleCQCGAqjRTAAAAA%3d%3d) **\]**

```Kusto
StormEvents
| where StartTime between (datetime(2007-07-27) .. 3d)
| count
```

## <a name="tabular-operators"></a>Táblázatos operátorok

A Kusto számos táblázatos operátorral rendelkezik, amelyek némelyikét a cikk más részei tárgyalják. Itt az elemzésre fogunkösszpontosítani. 

### <a name="parse"></a>elemezni

[**elemzés**](https://docs.microsoft.com/azure/kusto/query/parseoperator): Kiértékel egy karakterlánc-kifejezést, és egy vagy több számított oszlopba elemzi az értékét. Háromféle módon elemezhető: egyszerű (alapértelmezett), regex és nyugodt.

A következő lekérdezés egy nyomkövetést elemez, és Kinyeri a megfelelő értékeket az egyszerű elemzés alapértelmezett értékének használatával. A kifejezés (más néven StringConstant) egy reguláris karakterlánc, és a egyezés szigorú: a kiterjesztett oszlopoknak meg kell egyezniük a szükséges típusokkal.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UTU%2fDMAyG75X6H6xcxlCkpRlsUNQjN6gQ2wnEoevMFsiaKk2HJvHjabqvlI91l11QLrH12vETW5Zo4H411kmKEME0MdWZSISz2yVmpvaHhdEim3V979n3OrU%2fhFgZ8boaSZHiI0pMiipEY6FKnWKcLDB6EDlKkeEoneO0lKgpGGUSWYcUER9SKOw1LhcT1BHvU5AqfR%2bLKpbxXjDscRYMgF2FFyxkwRMFvX7ngCLXuBSqLO5%2bT9S%2ftrJuh54OI7g8iMFaMdhxGOy0GJz9i25w%2fjdG0IoRHNWNNe1ph2pwEKNlqI7HsEPley83vrfZCL73CXmiq%2fr32wA%2bhJnDOZAGEQHXBNIEIq4VSpXNbAIXkbjAO8UOmuz4bWoXlrhWWO0vqyA2%2bAcw2f7B1rORd60calat3jA1TRbq1A6NxsC%2bLdCoCuj3p74AKTs4pmcFAAA%3d) **\]**

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

A következő lekérdezés egy nyomkövetést elemez, és Kinyeri a megfelelő `kind = regex`értékeket a használatával. A StringConstant lehet reguláris kifejezés.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2UQU%2fCQBCF7036HyZ7gWKRbVHQmgY9eNPGCCcoh9KOsLK0ZLtFMf54l6LQBgUuXEyTTbP7pt3vvclwlPC47IkgRHAhCqR6Rhyher%2fAWOb7TioFi8eGrg10rZLvO%2bAlkr0su5yF%2bIwcg1SVCEyTTIToBTN0n9gcOYuxG04wyjgKE2QiA56XpK7dNiFdvXrZbITCtZsm8CSc9piqpXbDajdsarWAXjkX1KFW3wSx%2fs8exVzggiVZ%2bvD7h5rXK5lRMU%2bHYV3uxaAHMehxGPS0GDb9F2nY9t8Y1kEM66g01rSnbarWXowDTXU8xqqpdG14o2vfE0HXPmEeCHX%2fKYsjNR8EjvEdtqMB3picAKme1zrGIKh%2f3NX7w5pLoEgLt6SM56c1PzpTq6oqYpIitMOTeAxAlKb6c3Wjs3GBbAzJJUV8UjQjP91BJztuOGryKbHvGwQgxxbJK4ayTFKKBbahQCkA2DX7C29veJJmBQAA) **\]**

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

A következő lekérdezés egy nyomkövetést elemez, és Kinyeri a megfelelő `kind = relaxed`értékeket a használatával. A StringConstant egy reguláris karakterlánc-érték, és a egyezés nyugodt: a kiterjesztett oszlopok részben megegyeznek a szükséges típusokkal.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAN2US0%2fCQBDH7036HSZ7wZpN2BYFrenRGzZG4KLxUNoRVpYu2W5REj%2b83fKw9QE1kYvppTOZx%2f%2b3MxmBGm5WQxXFCAEkkS6%2bsUA4uV5iqku%2fn2nF04ljWw%2b21Sr9PoRS86fVQPAY71BglBUpCjOZqxjDaI7BLV%2bg4CkO4ikmuUBFQUsdiTIlC7wehcz8hvl8jCrwOhSEjGdDXuQyr%2b322h5zu8Au%2fDPmM%2feeglr32ROxULjkMs%2f63xfqXJowp0WPh%2bGe78VgBzFYMwx2XAyP%2fYtpeN7PGO5BDLfRNNa0x12q7l6MA0vVHMMslW09XtnW5iLY1hssIlXon%2fE0CYom0SsmQP6IMxz1%2b7%2b7AnXQdX6TNXMIvHA9hVMgNYEEqiaQuj5StXwh04kpUNVLqup3ETsCsoMxpavSSdXyi7NrIohJ%2foJDtoRbzybcMeFQjkjJZ4x1nYVWtEPtleHjjaGmCujnVu%2fWU75tHgYAAA%3d%3d) **\]**

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

### <a name="make-series"></a>sorozat készítése

[**make-Series**](https://docs.microsoft.com/azure/kusto/query/make-seriesoperator): összesíti a sorok csoportjait, [](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)például az összesítést, de az értékek alapján minden kombinációban létrehoz egy (Time) adatsorozat-vektort.

A következő lekérdezés egy idősorozatot ad vissza a Storm-események napi számának megadásához. A lekérdezés az egyes állapotokhoz tartozó három hónapos időszakot fedi le, és a hiányzó raktárhelyeket kitölti a 0 konstanssal:

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUchNzE7VLU4tykwtVsizTc4vzSvR0FRISU1LLM0psTVQyM9TCC5JLCoJycxNVcjMUyhKzEtP1UhJLEktAYpoGBkYmOsaGAKRpo4CmqixrjFI1DBFUyGpEmRKSSoAazsM0n0AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
```

Miután létrehozta a (Time) adatsorozatot, alkalmazhatja az adatsorozat-függvényeket a rendellenes alakzatok, a szezonális mintázatok és még sok egyéb észlelésére.

A következő lekérdezés kinyeri az első három olyan állapotot, amely a legtöbb eseményt adott napon belül:

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAF2OsQoCMRBEe8F%2f2DIBAzmvsLrSLzj7EC%2brBs3mSPbkBD%2feLDYibPVmZmdGziUdn0hct5s3JH9HU7FErEDDlBdipSHgxS8PHixkgpF94VNMCJGgeLqiCp6RG1F7aw%2fGdu30Dv5ob3qhXdBwfskXRmnElZECfDtdbbgq0qJwnqEX76%2fmyCW%2ftkV1Ek9pWSwgNdOt7foAJIuybs8AAAA%3d) **\]**

```Kusto
StormEvents
| make-series n=count() default=0 on StartTime in range(datetime(2007-01-01), datetime(2007-03-31), 1d) by State
| extend series_stats(n)
| top 3 by series_stats_n_max desc
| render timechart
```

További információkért tekintse át az adatsorozat- [függvények](https://docs.microsoft.com/azure/kusto/query/scalarfunctions#series-processing-functions)teljes listáját.

## <a name="advanced-aggregations"></a>Speciális aggregátumok

Az alapszintű összesítések, például a **darabszám** és az **Összefoglalás**a jelen cikk korábbi részében szerepelnek. Ez a szakasz további speciális lehetőségeket mutat be.

### <a name="top-nested"></a>szigorúan beágyazott

[**Top-nested**](https://docs.microsoft.com/azure/kusto/query/topnestedoperator): Hierarchikus legfelső szintű eredményeket hoz létre, ahol az egyes szintek részletezése az előző szint értékei alapján történik.

Ez az operátor az irányítópult-vizualizációs forgatókönyvek esetében hasznos, vagy ha a következőhöz hasonló kérdéssel kell válaszolnia: "A K1 első N értékének megkeresése (néhány összesítés használatával); mindegyiknél keresse meg a K2 legfontosabb értékeit (egy másik összesítés használatával); ..."

A következő lekérdezés egy hierarchikus táblát ad vissza `State` , amely a legfelső szinttel `Sources`, majd ezt követi:.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjJL9DNSy0uSU1RMFLIT1MILkksSVVIqlQoLs3VcEpNz8zzSSzR1OHlQlJoDFaYX1qUTEilIUila16KT35yYklmfh6GcgDrXwk5jgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| top-nested 2 of State by sum(BeginLat),
top-nested 3 of Source by sum(BeginLat),
top-nested 1 of EndLocation by sum(BeginLat)
```

### <a name="pivot-plugin"></a>pivot () beépülő modul

[**pivot () beépülő modul**](https://docs.microsoft.com/azure/kusto/query/pivotplugin): Elforgat egy táblát úgy, hogy a bemeneti tábla egyik oszlopának egyedi értékeit több oszlopba helyezi a kimeneti táblában. Az operátor olyan összesítéseket hajt végre, amelyeken a végső kimenetben lévő többi oszlop értékeire szükség van.

A következő lekérdezés egy szűrőt alkalmaz, és a sorokat oszlopokba helyezi.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSgoys9KTS5RCC5JLEnVUQBLhFQWpILkyjNSi1IhMgrFJYlFJcXlmSUZCkqOPkoIabgOhYzEYgWl8My8FLBsalliTilIZ0FmWX6JBtgUTQDlv21NfQAAAA%3d%3d) **\]**

```Kusto
StormEvents
| project State, EventType
| where State startswith "AL"
| where EventType has "Wind"
| evaluate pivot(State)
```

### <a name="dcount"></a>DCount ()

[**DCount ()** ](https://docs.microsoft.com/azure/kusto/query/dcount-aggfunction): A csoportban található kifejezés különböző értékeinek becsült értékét adja vissza. Az összes érték számlálásához használja a [**Count ()** ](https://docs.microsoft.com/azure/kusto/query/countoperator) függvényt.

A következő lekérdezési számok `Source` különböznek egymástól: `State`.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlUIzi8tSk4tVrBVSEnOL80r0YAIaCokVSoElySWpAIAFKgSBDoAAAA%3d) **\]**

```Kusto
StormEvents
| summarize Sources = dcount(Source) by State
```

### <a name="dcountif"></a>dcountif()

[**dcountif ()** ](https://docs.microsoft.com/azure/kusto/query/dcountif-aggfunction): A kifejezés azon soraihoz tartozó különböző értékek becslését adja vissza, amelyek esetében a predikátum értéke TRUE (igaz).

A következő lekérdezés a különböző értékeit `Source` számítja `DamageProperty < 5000`ki.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSspVqhRKEnMTlUwNDDg5apRKC7NzU0syqxKVQjOLy1KTi1WsFVISc4vzSvJTNOACOkouCTmJqanBhTlF6QWlVQq2CiYGhgYaCokVSoElySWpAIAuk%2fTX14AAAA%3d) **\]**

```Kusto
StormEvents 
| take 100
| summarize Sources = dcountif(Source, DamageProperty < 5000) by State
```

### <a name="dcount_hll"></a>dcount_hll()

[**dcount_hll ()** ](https://docs.microsoft.com/azure/kusto/query/dcount-hllfunction): Kiszámítja a **DCount** a HyperLogLog eredményekből ( [**HLL**](https://docs.microsoft.com/azure/kusto/query/hll-aggfunction) vagy [**hll_merge**](https://docs.microsoft.com/azure/kusto/query/hll-merge-aggfunction)alapján létrehozva).

A következő lekérdezés a HLL algoritmus használatával állítja elő a darabszámot.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlXIyMkJSi1WsAUxNFwScxPTUwOK8gtSi0oqNRWSKhWSMvM0gksSi0pCMnNTdQwNcjUx9PumFqWnpkCMiM8FcTQgpoKVFhTlZ6UmlyikJOeX5pXEg6yB69EEAKm9wyCXAAAA) **\]**

```Kusto
StormEvents
| summarize hllRes = hll(DamageProperty) by bin(StartTime,10m)
| summarize hllMerged = hll_merge(hllRes)
| project dcount_hll(hllMerged)
```

### <a name="arg_max"></a>arg_max()

[**arg_max ()** ](https://docs.microsoft.com/azure/kusto/query/arg-max-aggfunction): Megkeresi a csoport egy olyan sorát, amely maximalizálja a kifejezést, és egy másik kifejezés (vagy *) értékét adja vissza a teljes sor visszaküldéséhez.

A következő lekérdezés az utolsó özönvíz-jelentés időpontját adja vissza az egyes állapotokban.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSjPSC1KVQDzQyoLUhVsbRWU3HLy81OUQLLFpbm5iUWZVakKiUXp8bmJFRrBJYlFJSGZuak6ClqaCkmVCkCBklSQ2oKi%2fKzU5BKIgI4CkkLXvBQoA2YNAHO1S0OFAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize arg_max(StartTime, *) by State
| project State, StartTime, EndTime, EventType
```

### <a name="makeset"></a>makeset()

[**makeset ()** ](https://docs.microsoft.com/azure/kusto/query/makeset-aggfunction): Egy olyan dinamikus (JSON) tömböt ad vissza, amely a kifejezés által a csoportba foglalt különböző értékek halmazát adja meg.

A következő lekérdezés visszaadja az összes olyan időpontot, amikor az egyes állapotok elárasztják az árvizeket, és létrehoznak egy tömböt a különböző értékek készletében.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWLQQ6CQBAE7yb8ocNJE76wR3mA8IEFOxF1mM3siIHweAVPHqsq1bianCeOnovDiveNRuzczokIAWX9VL2WW80vkWjDQuzuwqTmGQESH8z0Y%2bPRvB2EJ3QzvuTcvmR6Z%2b8%2fUf3NH6ZkMFeAAAAA) **\]**

```Kusto
StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports
```

### <a name="mv-expand"></a>mv-expand

[**MV – Expand**](https://docs.microsoft.com/azure/kusto/query/mvexpandoperator): Kibontja a többértékű gyűjtemény (eke) t egy dinamikus típusú oszlopból, hogy a gyűjtemény minden értéke külön sort kap. A kibontott sorokban lévő összes többi oszlop duplikálva van. Ez a MakeList ellentéte.

A következő lekérdezés létrehoz egy készletet, majd a segítségével bemutatja az **MV-Expand** képességeket.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWOQQ6CQAxF9yTcoWGliTcws1MPIFygyk9EKTPpVBTj4Z2BjSz%2f738v7WF06r1vD2xcp%2bCoNq9yHDFYLIsvvW5Q0JybKYCco2omqnyNTxHW7oPFckbwajFZhB%2bIsE1trNZ0gi1dpuRmQ%2baC%2bjuuthS7Fbwvi%2f%2bP8lpGvAMP7Wr3A6BceSu7AAAA) **\]**

```Kusto
let FloodDataSet = StormEvents
| where EventType == "Flood"
| summarize FloodReports = makeset(StartTime) by State
| project State, FloodReports;
FloodDataSet
| mv-expand FloodReports
```

### <a name="percentiles"></a>percentilis ()

[**százalékos érték ()** ](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction): Egy kifejezés által meghatározott legközelebb [**rangsorolt százalékos**](https://docs.microsoft.com/azure/kusto/query/percentiles-aggfunction) értékre vonatkozó becslést ad vissza. A pontosság a percentilis régiójában lévő populáció sűrűségét határozza meg. Csak az [**összegzésen**](https://docs.microsoft.com/azure/kusto/query/summarizeoperator)belüli összesítés kontextusában használható.

A következő lekérdezés kiszámítja a Storm időtartamának százalékos értékeit.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUUitKEnNS1FIKS1KLMnMz1OwVXDNSwnJzE1V0FUILkksKgGxQQrLM1KLUhHq7BQMirEI2ygYZ4CEi0tzcxOLMqtSFQpSi5KBlmXmpBZrwJTpKJjqKBgZACkgtgBiS1NNAEC7XiaYAAAA) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize percentiles(duration, 5, 20, 50, 80, 95)
```

A következő lekérdezés az állapot szerint kiszámítja a Storm időtartamának százalékos értékeit, és öt perces raktárhely (`5m`) alapján normalizálja az adatmennyiséget.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1NSwrCMBTcC95hli1EKEpBQd31BHUvafOgAZNI8uIPD28SEBVcDDMM8%2bnZedNdyHKYz56gG5NVUNFL1s5ih86qgzaEBXqWnrPOwetEnj65PZrwx95iNWU7RGOk1w8C5avj6KLlNF64qjHcMWhbvXsCralFPmT6rZ%2fJj2lAnyh8pwWWTaKEdcKmLYul%2fgLODFs%2b4AAAAA%3d%3d) **\]**

```Kusto
StormEvents
| extend duration = EndTime - StartTime
| where duration > 0s
| where duration < 3h
| summarize event_count = count() by bin(duration, 5m), State
| summarize percentiles(duration, 5, 20, 50, 80, 95) by State
```

### <a name="cross-dataset"></a>Több adathalmaz

Ez a szakasz azokat az elemeket ismerteti, amelyekkel összetettebb lekérdezéseket hozhat létre, összekapcsolhatja az adatokat a táblák között, és lekérdezéseket végezhet az adatbázisok és a fürtök között.

### <a name="let"></a>lehetővé teszik

[**let**](https://docs.microsoft.com/azure/kusto/query/letstatement): Javítja a modularitást és a használatot. A **let** utasítás lehetővé teszi, hogy egy potenciálisan összetett kifejezést több részre lehessen bontani, amelyek mindegyike névvel van kötve, és ezeket a részeket együtt kell összeállítani. A **let** utasítással felhasználó által definiált függvények és nézetek hozhatók létre (kifejezések a táblákon, amelyek eredményei úgy néznek ki, mint egy új tábla). A **let** utasítás által kötött kifejezések skaláris típusúak, táblázatos típusúak vagy felhasználó által definiált függvények (lambda) lehetnek.

A következő példa egy táblázatos típusú változót hoz létre, és azt egy későbbi kifejezésben használja.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAMtJLVHwyUzPKMnLzEsPLskvyi1WsOXlArNcy1LzSop5uWoUyjNSi1IVwPyQyoJUBVtbBSW4LiVrXq4coDGOZYk5iXnJGakkGQPXBTIGzSUgPVn5mXkKGmhmayrk5ykElySWpIKUpGQWl2TmJZdARACul3kY0gAAAA%3d%3d) **\]**

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

[**Csatlakozás**](https://docs.microsoft.com/azure/kusto/query/joinoperator): Egyesítse a két tábla sorait úgy, hogy az egyes táblákból a megadott oszlop (ok) megfelelő értékeivel új táblát formáljon. A Kusto az illesztési típusok teljes skáláját támogatja: **fullouter**, **Inner**, **innerunique**, **leftanti**, **leftantisemi**, **leftouter**, **leftsemi**, **rightanti**, **rightantisemi**, **rightouter** , **rightsemi**.

A következő példa egy belső illesztéssel rendelkező két táblát illeszt be.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA8tJLVGIULBVSEksAcKknFQN79RKq+KSosy8dB2FsMSc0lRDq5z8vHRNXq5oXi4FIFBPVNcx1IGyk9R1jJDYxjB2srqOCS9XrDUvVw7Qhkj8Nhih2wA0ydAAySgjZI4xnJMCtMQAYkuEQo1CVn5mnkJ2Zl6KbWZeXmoR0Nb8PAWgZQAFPLdO5AAAAA==) **\]**

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
> A és a **Project** operátorok segítségével csökkentheti a sorok és oszlopok számát a bemeneti táblákban az illesztés előtt. Ha egy tábla mindig kisebb, mint a másik, használja az illesztés bal oldali (vezetékes) oldalán. Az illesztési egyeztetés oszlopainak azonos névvel kell rendelkeznie. Ha szükséges, használja a **projekt** operátort az egyik táblában lévő oszlop átnevezéséhez.

### <a name="serialize"></a>szerializálása

[**szerializálás**](https://docs.microsoft.com/azure/kusto/query/serializeoperator): Szerializálja a sort, így olyan függvényeket használhat, amelyekben szerializált adatok, például a **row_number ()** is szükségesek.

A következő lekérdezés sikeres, mert az adatszerializált.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcumFmUm5gBlQZzUipLUvBSFovzy%2bLzS3KTUIgVbJI6GJgB4pV4NWgAAAA%3d%3d) **\]**

```Kusto
StormEvents
| summarize count() by State
| serialize
| extend row_number = row_number()
```

A sor akkor is szerializált minősül, ha a következőkből áll: **Rendezés**, **felső**vagy **tartomány** operátorok, opcionálisan a **projekt**, a **projekt el**, a **kiterjesztés**, **hol**, **elemzés**, **MV – Expand** , vagy **vegyen** fel operátorokat.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAAsuyS%2fKdS1LzSsp5uWqUSguzc1NLMqsSlVIzi%2fNK9HQVEiqVAguSSxJBcvmF5XABRQSi5NBgqkVJal5KQpF%2beXxeaW5SalFCrZIHA1NAEGimf5iAAAA) **\]**

```Kusto
StormEvents
| summarize count() by State
| sort by State asc
| extend row_number = row_number()
```

### <a name="cross-database-and-cross-cluster-queries"></a>Adatbázisok közötti és fürtök közötti lekérdezések

[Adatbázisok közötti és fürtök közötti lekérdezések](https://docs.microsoft.com/azure/kusto/query/cross-cluster-or-database-queries): Ugyanazon a fürtön lévő adatbázis lekérdezéséhez hivatkozva `database("MyDatabase").MyTable`kell lennie a következőre:. Egy távoli fürtön lévő adatbázis lekérdezéséhez hivatkozni kell rá `cluster("MyCluster").database("MyDatabase").MyTable`a következőre:.

A következő lekérdezés az egyik fürtből van meghívva, és `MyCluster` a fürt adatait kérdezi le. A lekérdezés futtatásához használja a saját fürt nevét és az adatbázis nevét.

```Kusto
cluster("MyCluster").database("Wiki").PageViews
| where Views < 2000
| take 1000;
```

### <a name="user-analytics"></a>Felhasználói elemzés

Ez a szakasz olyan elemeket és lekérdezéseket tartalmaz, amelyek azt mutatják be, hogy milyen egyszerű a felhasználói viselkedések elemzése a Kusto-ben.

### <a name="activity_counts_metrics-plugin"></a>activity_counts_metrics beépülő modul

[**activity_counts_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-counts-metrics-plugin): Kiszámítja a hasznos tevékenység mérőszámait (a teljes darabszámot, a különböző értékek darabszámát, az új értékek különböző számát és az összesített eltérő darabszámot). A rendszer minden egyes időablakra kiszámítja a metrikákat, és összehasonlítja őket, és az összes korábbi időpontra összesíti a-t és a-t.

A következő lekérdezés a napi tevékenységek számának kiszámításával elemzi a felhasználók bevezetését.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAJXSPQvCMBAG4L3Q%2f5CtFlLoFyiVDn4M6mqdREpsggTaKs1VEfzxXm0LDiEimcJz3CW8VwogClgDKWcgQFZiEvrB1PNnnh%2b4c9sqsUDUXMPxyA9Z8%2bsjDfhwz0hKsBzPuRSTgxLNlicKGllfKMmwBw6sbsnY0bWto205C4cS3Rso2tpgO4MtDbbSWvixzGD6eb1ttBYZev42%2fbzI8L%2fe9n9b3NkJQ8xs60XEnZUt1hBWgLxLeObFta1B5ZXAKAs1BPuVKO03iXb7gp36tXDfExVB%2f2ICAAA%3d) **\]**

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

### <a name="activity_engagement-plugin"></a>activity_engagement beépülő modul

[**activity_engagement beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-engagement-plugin): Az ID oszlop alapján kiszámítja a tevékenységek bevonási arányát egy csúszó idősor-ablakban. a **activity_engagement beépülő modul** használható Dau, WAU és Mau (napi, heti és havi aktív felhasználók) kiszámításához.

A következő lekérdezés a teljes számú felhasználó arányát adja vissza egy alkalmazással napi rendszerességgel az alkalmazás hetente, egy mozgó hét napján.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1RQWrDMBC8G%2fyHvUVOHGy1lByKD6GBviDkUIoR1tpVsS0jr0MCeXxXiigpVAiBVjOzM6uigHcc0SlCcGrUdgCtSIFtYZnRgWrInA0ZnNOkR4J6JuUIKo9CMgOKp1LutqXknb1GDI76P8RzQHCXDqHW6gqt43ZRkeydNxNOIHWa3AAv5Ctei2xvx06IQNtGTlZInT0AHQN9BpFt5EO59kHmKvQVUUivX8q1y3L4c9%2fIks%2bt5LoMwsMZLxMrgtHVXcb7pOuEthWemEFvBkPARL%2fSpCjgTfXN0vuBHvbH4rQ%2fsikyNjg6q37xL3GsV47cqQ4HHEl8rIxefeZhNHmMmIehsB2dp8nunnZy9hsbiriDWuqTWqpfxdBsLb2ZGzhm8y%2f6b2i%2bWO8HLFcMGe8BAAA%3d) **\]**

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
> A DAU/MAU kiszámításakor módosítsa a befejezési és a áthelyezési időszakot (OuterActivityWindow).

### <a name="activity_metrics-plugin"></a>activity_metrics beépülő modul

[**activity_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin): Kiszámítja a hasznos tevékenység mérőszámait (eltérő számú értékeket, az új értékek különböző számát, a megőrzési arányt és a forgalom arányát) a jelenlegi időszak ablak és az előző időszak ablaka alapján.

A következő lekérdezés kiszámítja egy adott adatkészlet adatváltozási és adatmegőrzési arányát.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG2SwW7CMAyG70i8g2%2bk0KoNE%2bIwscsOe4hpqqLGQFjaVKkLVNrDLw7RxjRyqBTr%2fz%2f3t1OW8IYdekUIXnXataAVKXB7GAf0oBoyZ0MGh%2fnMIkE9kPIEO1YhmRbFupLbopJFtc6ekwY7%2fV%2bxKZ4kK0KXA0Kt1QR7H9olIrmbbyDsQer57AvwSlxhFjnruoMQ0VYkT1ZKnd0JfRByBpGt5F255iDDLvYVCaSXm2rpsxz%2b3FfrKnwLGeoygtszXvtABKN3Nwz%2fJ009ur1gYwbWtIZAVvGw53JEn%2fK9PJwSi3rvTthQlOWBPp%2bVJbwq24yWN3FB%2fLQTeAwByLgOeD8x0lnZkRVpL1PdInnTDOJ9YfTiI0%2fE24DyONIctvpB0x94zfBlSJBDcxz97509PgDCM%2bAMzTEgvwEO44wSMAIAAA%3d%3d) **\]**

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

### <a name="new_activity_metrics-plugin"></a>new_activity_metrics beépülő modul

[**new_activity_metrics beépülő modul**](https://docs.microsoft.com/azure/kusto/query/new-activity-metrics-plugin): Kiszámítja a hasznos tevékenység mérőszámait (eltérő számú értékek, az új értékek különböző száma, a megőrzési arány és a forgalom arányát) az új felhasználók kohorsza számára. A beépülő modul fogalma hasonló a [**activity_metrics beépülő modulhoz**](https://docs.microsoft.com/azure/kusto/query/activity-metrics-plugin), de az új felhasználókra koncentrál.

A következő lekérdezés kiszámítja a megőrzési és a adatváltozási arányt az új felhasználók kohorsz (az első héten érkezett felhasználók) egy hétre felhasználva.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAG1Ry27DIBC8W%2fI%2f7C04wbJJFeVQ5VapP9BbVVnIrGMaGyy8eVjqxxcwh1QqBx7LzCwzVBW8o0EnCcFJo%2bwISpIE28F1RgeyJX3TpHHOswEJmpmkIzgFFJIeke1rcSzrQ1mL4jVh0Kj%2fEC8R4bucEd7kAp3z3ZIg2ZU2E04gVJ79AD4oVIIU2cGaM2OBVSZKUQlVPOGcxwUHrNiJp3ITbMyn2JUlHbU91FtXcPhz3u1rP5fC10UUHm%2f4mLwiaHVaZcIzaZnQdiwQCxj0qAlEHUeeVRV8yAuCNcMC1CN02s0Ed8QLtLa33igbpK9M0skRCd3q4CaHa%2fgBg%2fcmJb40%2ft7pdmafG602XzxExpN3HsPicFQ8z1IcQWhy9htbisk2EU92XZ1vZkhb04Sv5tD2V7fufwFYtolnAgIAAA%3d%3d) **\]**

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

### <a name="session_count-plugin"></a>session_count beépülő modul

[**session_count beépülő modul**](https://docs.microsoft.com/azure/kusto/query/session-count-plugin): Az ID oszlop alapján számítja ki a munkamenetek számát az idősoron.

A következő lekérdezés a munkamenetek számát adja vissza. A munkamenet akkor minősül aktívnak, ha egy felhasználói azonosító 100 időkereten belül legalább egyszer megjelenik, míg a munkamenet-visszatekintő ablak a 41-Time bővítőhely.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAFWPQYvCQAyF74X%2bh3dZUCjYgfUgMkcP3r2XoZPqaM3INK4u7I%2ffzOwiNQRC8pKPl5EEnXfiYJEcHwmHcKUxMGFI8QoDidhoYBK6wdTVD%2bgpxB5dd6FvPSuzcwyMS2BvAzMlLP5gez%2fDrNt%2fCN4Z1iwRua5Kk2GPE6WZkY%2bMsRZt1m4pnqmXl9qouK2r1Qo75cUB5RlPQ%2bAgoWDzpPj%2bcuPdCWGiaVKp6%2bOdZbH3zYxmNFuNUhp8mmU%2bTWpWv8or%2fckl%2bQXutT48NwEAAA%3d%3d) **\]**

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

### <a name="funnel_sequence-plugin"></a>funnel_sequence beépülő modul

[**funnel_sequence beépülő modul**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-plugin): Kiszámítja azoknak a felhasználóknak a különböző számát, akik az állapotok sorát vették igénybe; Megjeleníti a korábbi és a következő állapotok eloszlását, amelyek a sorozatot követték vagy követték.

A következő lekérdezés azt mutatja be, hogy milyen esemény történik az összes tornádó esemény előtt és után a 2007-ben.

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAEAGWOPYvCQBCG%2b0D%2bw3QmEEmieIqNVQrBRgxYiMhcdqKLyWzcnQiCP95V70DuYIrh5Xk%2f0hRWxpw1H8EwbMTYtrgSiwMnKNqJrtw8DNIU1vkcticUOGHXETv4ptpYgtJYRmWAnrbFGx39QbEWsv%2fIj7YwuHsZmx6FoO6ZqTk4uvTEFUVFp51RtFSJH4hWSt1SAsqj4r9olGXTYZb7i5Mw%2bJRnvzLkKhl%2fTXzAq668dc%2bAG2Orq2g3%2bBk22MfxA23MLGQQAQAA) **\]**

```Kusto
// Looking on StormEvents statistics:
// Q1: What happens before Tornado event?
// Q2: What happens after Tornado event?
StormEvents
| evaluate funnel_sequence(EpisodeId, StartTime, datetime(2007-01-01), datetime(2008-01-01), 1d,365d, EventType, dynamic(['Tornado']))
```

### <a name="funnel_sequence_completion-plugin"></a>funnel_sequence_completion beépülő modul

[**funnel_sequence_completion beépülő modul**](https://docs.microsoft.com/azure/kusto/query/funnel-sequence-completion-plugin): Kiszámítja a befejezett sorszámú lépések tölcsérét a különböző időszakokon belül.

A következő lekérdezés ellenőrzi a folyamat befejezési tölcsérét: `Hail -> Tornado -> Thunderstorm -> Wind` egy óra "általános" idején, négy órát és egy napot (`[1h, 4h, 1d]`).

**\[** [**Kattintson ide a lekérdezés futtatásához**](https://dataexplorer.azure.com/clusters/help/databases/Samples?query=H4sIAAAAAAAAA12QTYvCMBCG74L/YW6tkIV2XT9g8SjsnlvwICKhM9JAOqlJqrj4402CW0RIIB/PPLwzmjwcnZfWwwZQevKqo/yzKFYfRRnW7Hs60ZEhxjdi/UZcFaO5VuqPAjhfLvD/w9F5IG7iM95YdqrJ99mPVDoTkNXGskSTju3ASNZ5Y7t43wVhdhj9PVll0L1aylbAV9glJqyKldsLsXfTyR3oIvUQAsNpYCY95jg2puuDUhnOt71yBukXBVRxCnVoTjwnIlLX4rUzAUlf3/pEPYViDDd7AOyqowFQAQAA) **\]**

```Kusto
let _start = datetime(2007-01-01);
let _end = datetime(2008-01-01);
let _windowSize = 365d;
let _sequence = dynamic(['Hail', 'Tornado', 'Thunderstorm', 'Wind']);
let _periods = dynamic([1h, 4h, 1d]);
StormEvents
| evaluate funnel_sequence_completion(EpisodeId, StartTime, _start, _end, _windowSize, EventType, _sequence, _periods)
```

## <a name="functions"></a>Funkciók

Ez a szakasz [](https://docs.microsoft.com/azure/kusto/query/functions)a függvényeket tartalmazza: a kiszolgálón tárolt újrafelhasználható lekérdezések. A függvényeket lekérdezések és egyéb függvények is meghívhatják (a rekurzív függvények nem támogatottak).

> [!NOTE]
> A Súgó fürtön nem hozhatók létre függvények, amely csak olvasható. Ehhez a részhez használjon saját tesztelési fürtöt.

A következő példa egy olyan függvényt hoz létre, amely egy`MyState`állapot nevét () argumentumként veszi fel.

```Kusto
.create function with (folder="Demo")
MyFunction (MyState: string)
{
StormEvents
| where State =~ MyState
}
```

A következő példa egy függvényt hív meg, amely a Texas államnak szóló adatmennyiséget kéri le.

```Kusto
MyFunction ("Texas")
| summarize count()
```

A következő példa törli az első lépésben létrehozott függvényt.

```Kusto
.drop function MyFunction
```

## <a name="next-steps"></a>További lépések

[Kusto lekérdezési nyelvi referenciája](https://aka.ms/kustolangref)
