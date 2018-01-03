---
title: "A Graph API Azure Cosmos DB globális telepítési útmutató |} Microsoft Docs"
description: "Útmutató: Azure Cosmos DB globális terjesztési a Graph API-jával beállítása."
services: cosmos-db
keywords: "globális terjesztési, graph, gremlin"
documentationcenter: 
author: luisbosquez
manager: jhubbard
editor: cgronlun
ms.assetid: 8b815047-2868-4b10-af1d-40a1af419a70
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 01/02/2018
ms.author: lbosq
ms.custom: mvc
ms.openlocfilehash: 1806bde383f04747f1f0fef46e5cf4d38de1e939
ms.sourcegitcommit: 9ea2edae5dbb4a104322135bef957ba6e9aeecde
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/03/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>How Azure Cosmos DB globális terjesztési a Graph API-jával beállítása

Ebben a cikkben megmutatjuk, hogyan használható az Azure-portálon Azure Cosmos DB globális terjesztési és a Graph API segítségével csatlakozzon.

Ez a cikk ismerteti a következő feladatokat: 

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Globális terjesztési használatával konfigurálja a [Graph API-k](graph-introduction.md)

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Csatlakozás előnyben részesített régióba a Graph API a .NET SDK használatával

A Graph API fel van fedve egy bővítmény tárként felett az SQL API-t.

Kihasználása érdekében [globális terjesztési](distribute-data-globally.md), ügyfélalkalmazások is adja meg a dokumentum műveletek végrehajtásához használandó régiók rendezett beállítások listáját. Ezt megteheti a kapcsolat házirend beállításával. Az Azure Cosmos DB-fiók konfigurációja, az aktuális területi rendelkezésre állás és a megadott beállításokat szabályozó lista alapján, a legoptimálisabb végpont választja ki az SDK írási és olvasási műveletek.

Ez a beállítás lista van megadva, az SDK-k használata a kapcsolat inicializálása közben. Az SDK-k elfogadása "PreferredLocations" nem kötelező paraméter, amely egy Azure-régiók rendezett listáját.

* **Írási műveletek**: az SDK automatikusan elküldi az összes írási műveletek az aktuális írási területen.
* **Beolvassa**: minden olvasási küldött a PreferredLocations lista első rendelkezésre álló terület. A kérés nem teljesíthető, ha az ügyfélnek nem sikerül lefelé a listában, a következő terület, és így tovább. Az SDK-k csak a megadott PreferredLocations régiók olvasás kísérlete. Igen például ha a Cosmos DB fiók három régiókban, de az ügyfél csak megadja két a nem írási PreferredLocations, majd nincs olvasási régiót kívül az írási régió, feladatátvétel esetén is.

Az alkalmazás a jelenlegi írási végpont ellenőrizheti és olvassa el a két tulajdonság, WriteEndpoint és ReadEndpoint, elérhető, a SDK 1.8-as verzióját és az újabb ellenőrzésével az SDK által választott végpont. A PreferredLocations tulajdonsága nincs beállítva, ha minden kérésnél szolgáltatott aktuális írási régióban.

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

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban ezt a következők:

> [!div class="checklist"]
> * Az Azure portál használatával globális terjesztési konfigurálása
> * Az SQL API-kkal globális terjesztési konfigurálása

Most már folytathatja a következő oktatóanyag megtudhatja, hogyan fejleszthet, helyileg emulátorral Azure Cosmos DB helyi.

> [!div class="nextstepaction"]
> [Helyileg emulátorral fejlesztése](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

