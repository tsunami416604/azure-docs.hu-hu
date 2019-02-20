---
title: Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB-szinkronizálás megvalósítása
description: Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény az Azure Cosmos DB-szinkronizálás megvalósítása
author: markjbrown
ms.service: cosmos-db
ms.topic: sample
ms.date: 2/12/2019
ms.author: mjbrown
ms.openlocfilehash: 43cb73784806358bccb9758be2923d3df5e9badd
ms.sourcegitcommit: 79038221c1d2172c0677e25a1e479e04f470c567
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2019
ms.locfileid: "56414881"
---
# <a name="how-to-implement-custom-synchronization-to-optimize-for-higher-availability-and-performance"></a>Egyéni optimalizálása a magasabb rendelkezésre állás és teljesítmény-szinkronizálás megvalósítása

Az Azure Cosmos DB öt jól meghatározott konzisztenciaszint közül választhat a konzisztencia, a teljesítmény és a rendelkezésre állási közötti elosztása érdekében a kínál. Erős konzisztencia biztosítja az adatok szinkronban replikálva lettek, és minden régióban, ahol érhető el az Azure Cosmos-fiók tartósan tárolja. Ebben a konfigurációban a legnagyobb szintű tartósságot biztosítja, miközben cserébe teljesítmény és rendelkezésre állás érhető el. Ha egy alkalmazás igényeinek megfelelően kell application rendelkezésre állási veszélyeztetése nélkül az adatok tartós megőrzését vezérlő/enyhítése fájlvédelemre, ez azonban egyéni szinkronizálási az alkalmazási rétegben a kívánt tartóssági szint elérése érdekében.

Az alábbi ábrán vizuálisan ábrázolja az egyéni szinkronizálási modell.

![Egyéni szinkronizálási](./media/how-to-custom-synchronization/custom-synchronization.png)

Ebben a forgatókönyvben egy Azure Cosmos-tárolót a rendszer replikálja globálisan számos régióban több kontinens. Erős konzisztencia használata ebben a forgatókönyvben minden régió esetében hatással lenne a teljesítményre. Magasabb szintű az adatok tartóssága érdekében, az írási késése veszélyeztetése nélkül, az alkalmazás két ügyfelek, amelyek az ugyanazon munkamenet jogkivonata használja.

Az első ügyfél tudjon adatokat írni a helyi régió (például USA nyugati régiója). A második ügyfél (például az USA keleti régiója), hogy a szinkronizálás használt olvasási ügyfél. Folyó munkamenet tokent az alábbi olvasási, írási válaszból az olvasási írását az USA keleti régiójában szinkronizálásának fogja biztosítja. Az Azure Cosmos DB biztosítja az írási műveletek legalább egy régió szerint láthatók, és garantáltan stabilitást biztosít az olyan regionális szolgáltatáskimaradás, ha az eredeti írási régió leáll. Ebben a forgatókönyvben minden írási USA keleti régiójában, így csökkentve az erős konzisztencia alkalmazó minden régióban van szinkronizálva. Több főkiszolgálós forgatókönyvekben, ahol írási minden régióban is megjelenhetnek, ez a modell szinkronizálása párhuzamosan több régióban is kiterjeszthető.

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
