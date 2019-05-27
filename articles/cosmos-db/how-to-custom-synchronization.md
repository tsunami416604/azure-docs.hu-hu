---
title: Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB-szinkronizálás megvalósítása
description: Ismerje meg, hogyan optimalizálható a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB egyéni-szinkronizálás megvalósítása.
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 05/23/2019
ms.author: rimman
ms.openlocfilehash: cd89a145f5746696cc8fc163eb46896081d85a90
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "66240966"
---
# <a name="implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>A magasabb rendelkezésre állás és teljesítmény optimalizálása érdekében egyéni-szinkronizálás megvalósítása

Az Azure Cosmos DB-ajánlatok [öt jól definiált konzisztenciaszintet](consistency-levels.md) választhat a konzisztencia, a teljesítmény és a rendelkezésre állási közötti elosztása érdekében. Erős konzisztencia lehetővé teszi, hogy adatokat szinkronban replikálva lettek, és minden régióban, ahol érhető el az Azure Cosmos-fiók tartósan tárolja. Ez a konfiguráció biztosítja a legnagyobb szintű tartósságot biztosítja, de a megoldás a teljesítmény és rendelkezésre állás. Ha azt szeretné, hogy az alkalmazás szabályozhatja, vagy az adatok megőrzését enyhíteni az alkalmazás igényeinek megfelelően kell rendelkezésre állási veszélyeztetése nélkül, akkor használhatja *egyéni szinkronizálási* az alkalmazási rétegben a tartóssági szintjének eléréséhez, szeretné.

Az alábbi képen vizuálisan ábrázolja az egyéni szinkronizálási modell:

![Egyéni szinkronizálási](./media/how-to-custom-synchronization/custom-synchronization.png)

Ebben a forgatókönyvben egy Azure Cosmos-tárolóhoz replikál a rendszer globálisan számos régióban lévő több kontinensen. Erős konzisztencia használata ebben a forgatókönyvben minden régió esetében befolyásolja a teljesítményt. Nélkül veszélyeztetése írási késés magasabb szintű az adatok tartóssága érdekében az alkalmazás két olyan ügyfelek, amelyek azonos használható [tokenu relace](how-to-manage-consistency.md#utilize-session-tokens).

Az első ügyfél tudjon adatokat írni a helyi régió (például USA nyugati régiója). A második ügyfél (például az USA keleti régiója) olvasási ügyfél, amely a szinkronizálás biztosítja. Által a munkamenet-jogkivonat a következő olvasási, írási válaszból áramló, az olvasási biztosítja a szinkronizálást az írását az USA keleti régiójában. Az Azure Cosmos DB biztosítja, hogy az írási műveletek láthatók-e legalább egy régió szerint. Hogy garantáltan stabilitást biztosít egy regionális kimaradás, ha az eredeti írási régió leáll. Ebben a forgatókönyvben minden írási szinkronizálva van az USA keleti régiójához, így csökkentve az erős konzisztencia alkalmazó minden régióban. Több főkiszolgálós forgatókönyvekben, ahol írási minden régióban történik, ez a modell párhuzamosan több régióban való szinkronizálása bővítheti.

## <a name="configure-the-clients"></a>Az ügyfelek konfigurálása

Az alábbi mintában látható, amely két ügyfelek egyéni szinkronizálási példányosítja adatelérési réteg:

```csharp
class MyDataAccessLayer
{
    private DocumentClient writeClient;
    private DocumentClient readClient;

    public async Task InitializeAsync(Uri accountEndpoint, string key)
    {
        ConnectionPolicy writeConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp,
            UseMultipleWriteLocations = true
        };
        writeConnectionPolicy.SetCurrentLocation(LocationNames.WestUS);

        ConnectionPolicy readConnectionPolicy = new ConnectionPolicy
        {
            ConnectionMode = ConnectionMode.Direct,
            ConnectionProtocol = Protocol.Tcp
        };
        readConnectionPolicy.SetCurrentLocation(LocationNames.EastUS);

        writeClient = new DocumentClient(accountEndpoint, key, writeConnectionPolicy);
        readClient = new DocumentClient(accountEndpoint, key, readConnectionPolicy, ConsistencyLevel.Session);

        await Task.WhenAll(new Task[]
        {
            writeClient.OpenAsync(),
            readClient.OpenAsync()
        });
    }
}
```

## <a name="implement-custom-synchronization"></a>Egyéni szinkronizálás implementálása

Után az ügyfelek inicializálása, az alkalmazás elvégezhet a helyi régió (USA nyugati régiója) és a kényszerített-szinkronizálási írási műveletek a írását az USA keleti régiójában módon.

```csharp
class MyDataAccessLayer
{
    public async Task CreateItem(string containerLink, Document document)
    {
        ResourceResponse<Document> response = await writeClient.CreateDocumentAsync(
            containerLink, document);

        await readClient.ReadDocumentAsync(response.Resource.SelfLink,
            new RequestOptions { SessionToken = response.SessionToken });
    }
}
```

Szinkronizálni kívánt párhuzamosan több régióban a modell bővítheti.

## <a name="next-steps"></a>További lépések

Globális terjesztés és konzisztencia az Azure Cosmos DB kapcsolatos további információkért olvassa el ezeket a cikkeket:

* [Válassza ki a megfelelő konzisztenciaszint Azure Cosmos DB-ben](consistency-levels-choosing.md)
* [Konzisztencia, a rendelkezésre állás és a teljesítmény és kompromisszumot kínál a Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
* [Az Azure Cosmos DB konzisztencia kezelése](how-to-manage-consistency.md)
* [Particionálás és az adatok terjesztési Azure Cosmos DB-ben](partition-data.md)
