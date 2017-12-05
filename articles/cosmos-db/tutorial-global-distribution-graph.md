---
title: "A Graph API Azure Cosmos DB globális telepítési útmutató |} Microsoft Docs"
description: "Útmutató: Azure Cosmos DB globális terjesztési a Graph API-jával beállítása."
services: cosmos-db
keywords: "globális terjesztési, graph, gremlin"
documentationcenter: 
author: dennyglee
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 05/10/2017
ms.author: denlee
ms.custom: mvc
ms.openlocfilehash: eb55bdee60400b4b14f47a6a0b1d0682b267d26f
ms.sourcegitcommit: 7136d06474dd20bb8ef6a821c8d7e31edf3a2820
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/05/2017
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>How Azure Cosmos DB globális terjesztési a Graph API-jával beállítása

Ebben a cikkben megmutatjuk, hogyan használja az Azure-portált telepítő Azure Cosmos DB globális terjesztési, és csatlakozzon a Graph API-val (előzetes verzió).

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Globális terjesztési használatával konfigurálja a [Graph API-k](graph-introduction.md) (előzetes verzió)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Csatlakozás előnyben részesített régióba a Graph API a .NET SDK használatával

A Graph API fel van fedve egy bővítmény tárként fölött a DocumentDB SDK-t.

Kihasználása érdekében [globális terjesztési](distribute-data-globally.md), ügyfélalkalmazások is adja meg a dokumentum műveletek végrehajtásához használandó régiók rendezett beállítások listáját. Ezt megteheti a kapcsolat házirend beállításával. Az Azure Cosmos DB-fiók konfigurációja, az aktuális területi rendelkezésre állás és a megadott beállításokat szabályozó lista alapján, a legoptimálisabb végpont választja ki az SDK írási és olvasási műveletek.

Ez a beállítás lista van megadva, az SDK-k használata a kapcsolat inicializálása közben. Az SDK-k elfogadása "PreferredLocations" nem kötelező paraméter, amely egy Azure-régiók rendezett listáját.

* **Írási műveletek**: az SDK automatikusan elküld minden írási műveleteket ad ki az aktuális írási régió.
* **Beolvassa**: minden olvasási kapnak a PreferredLocations lista első rendelkezésre álló terület. A kérés nem teljesíthető, ha az ügyfél lefelé a listában, a következő régióban sikertelen, és így tovább. Az SDK-k csak megpróbálja beolvasni a régió van megadva a PreferredLocations. Igen például ha három régiók a Cosmos DB fiók áll rendelkezésre, de az ügyfél csak meghatározza a nem írási régiók két PreferredLocations, majd nincs olvasási szolgáltató kívül az írási régió, feladatátvétel esetén is.

Az alkalmazás a jelenlegi írási végpont ellenőrizheti és olvassa el a két tulajdonság, WriteEndpoint és ReadEndpoint, elérhető, a SDK 1.8-as verzióját és az újabb ellenőrzésével az SDK által választott végpont. A PreferredLocations tulajdonsága nincs beállítva, ha minden kérésnél szolgáltató aktuális írási régióban.

### <a name="using-the-sdk"></a>Az SDK használatával

Például a .NET SDK-ban a `ConnectionPolicy` paramétere a `DocumentClient` konstruktor hívása tulajdonsággal rendelkezik `PreferredLocations`. Ez a tulajdonság régió neveinek listáját állítható be. A megjelenített neveket a [Azure-régiókat] [ regions] részeként adható meg `PreferredLocations`.

> [!NOTE]
> Az URL-címeket a végpontok nem hosszú élettartamú állandók kell tekinteni. A szolgáltatás ezek bármikor előfordulhat, hogy frissíteni. Az SDK kezeli automatikusan ezt a módosítást.
>
>

```cs
// Getting endpoints from application settings or other configuration location
Uri accountEndPoint = new Uri(Properties.Settings.Default.GlobalDatabaseUri);
string accountKey = Properties.Settings.Default.GlobalDatabaseKey;

ConnectionPolicy connectionPolicy = new ConnectionPolicy();

//Setting read region selection preference
connectionPolicy.PreferredLocations.Add(LocationNames.WestUS); // first preference
connectionPolicy.PreferredLocations.Add(LocationNames.EastUS); // second preference
connectionPolicy.PreferredLocations.Add(LocationNames.NorthEurope); // third preference

// initialize connection
DocumentClient docClient = new DocumentClient(
    accountEndPoint,
    accountKey,
    connectionPolicy);

// connect to Azure Cosmos DB
await docClient.OpenAsync().ConfigureAwait(false);
```

Ez azt, hogy ez az oktatóanyag befejezése. Megismerheti a globális replikált fiókja konzisztencia kezeléséhez olvasásával [Azure Cosmos DB-ben konzisztenciaszintek](consistency-levels.md). És hogyan globális adatbázis-replikációval kapcsolatos további információk az Azure Cosmos Adatbázisba működik, a következő témakörben: [adatok globálisan Azure Cosmos DB terjesztése](distribute-data-globally.md).

## <a name="next-steps"></a>Következő lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * A DocumentDB API-k használatával globális terjesztési konfigurálása

Most már folytathatja a következő oktatóanyag megtudhatja, hogyan fejleszthet, helyileg emulátorral Azure Cosmos DB helyi.

> [!div class="nextstepaction"]
> [Helyileg emulátorral fejlesztése](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

