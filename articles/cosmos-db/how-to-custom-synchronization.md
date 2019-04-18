---
title: Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB-szinkronizálás megvalósítása
description: Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB-szinkronizálás megvalósítása
author: rimman
ms.service: cosmos-db
ms.topic: sample
ms.date: 04/15/2019
ms.author: rimman
ms.openlocfilehash: d948798f161eb36578cb679b6d96409917424fd4
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59678462"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény-szinkronizálás megvalósítása

Az Azure Cosmos DB-ajánlatok [öt jól definiált konzisztenciaszintet](consistency-levels.md) a közül választhat a konzisztencia, a teljesítmény és a rendelkezésre állási közötti elosztása érdekében. Erős konzisztencia biztosítja az adatok szinkronban replikálva lettek, és minden régióban, ahol érhető el az Azure Cosmos-fiók tartósan tárolja. Ebben a konfigurációban a legnagyobb szintű tartósságot biztosítja, miközben cserébe teljesítmény és rendelkezésre állás érhető el. Ha egy alkalmazás az adatok tartós megőrzését vezérlő/enyhítése szeretné az alkalmazás igényeinek megfelelően kell rendelkezésre állási veszélyeztetése nélkül, azt azonban *egyéni szinkronizálási* az alkalmazási rétegben a kívánt szint eléréséhez tartóssági.

Az alábbi ábrán vizuálisan ábrázolja az egyéni szinkronizálási modell.

![Egyéni szinkronizálási](./media/how-to-custom-synchronization/custom-synchronization.png)

Ebben a forgatókönyvben egy Azure Cosmos-tárolót a rendszer replikálja globálisan számos régióban több kontinens. Erős konzisztencia használata ebben a forgatókönyvben minden régió esetében hatással lenne a a teljesítményre. Az írási késése veszélyeztetése nélkül magasabb szintű az adatok tartóssága érdekében az alkalmazás két olyan ügyfelek, amelyek azonos használható [tokenu relace](how-to-manage-consistency.md#utilize-session-tokens).

Az első ügyfél tudjon adatokat írni a helyi régió (például USA nyugati régiója). A második ügyfél (például az USA keleti régiója), hogy a szinkronizálás használt olvasási ügyfél. A munkamenet-jogkivonat a következő olvasási, írási válaszból áramló, az olvasási szinkronizálásának írását az USA keleti régiója fog biztosítják. Az Azure Cosmos DB biztosítja az írási műveletek legalább egy régió szerint láthatók, és garantáltan stabilitást biztosít az olyan regionális szolgáltatáskimaradás, ha az eredeti írási régió leáll. Ebben a forgatókönyvben minden írási USA keleti régiójában, így csökkentve az erős konzisztencia alkalmazó minden régióban van szinkronizálva. Több főkiszolgálós forgatókönyvekben, ahol írási minden régióban is megjelenhetnek, ez a modell szinkronizálása párhuzamosan több régióban is kiterjeszthető.

## <a name="configure-the-clients"></a>Az ügyfelek konfigurálása

Az alábbi minta bemutatja az adatelérési réteg hárítható el két ügyfelek erre a célra.

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

Miután az ügyfél inicializálása, alkalmazás hajthat végre írási műveletek (USA nyugati régiója) a helyi régióban, és kényszerített szinkronizálhatja az USA keleti régiójában, az írások módon.

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

Ez a modell szinkronizálása párhuzamosan több régióban is kiterjeszthető.

## <a name="next-steps"></a>További lépések

Globális terjesztés és konzisztencia az Azure Cosmos DB kapcsolatos további információkért olvassa el a következő cikkeket:

* [Az Azure Cosmos DB-ben a megfelelő konzisztenciaszint kiválasztása](consistency-levels-choosing.md)
* [Konzisztencia, a rendelkezésre állás és a teljesítmény és kompromisszumot kínál a Azure Cosmos DB-ben](consistency-levels-tradeoffs.md)
* [Az Azure Cosmos DB konzisztencia kezelése](how-to-manage-consistency.md)
* [Particionálás és az adatok terjesztési Azure Cosmos DB-ben](partition-data.md)
