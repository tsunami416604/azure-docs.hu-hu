---
title: Az Azure Cosmos DB globális terjesztési oktatóanyaga a Graph API-hoz | Microsoft Docs
description: Ismerje meg, hogyan állíthatja be az Azure Cosmos DB globális terjesztését a Graph API használatával.
services: cosmos-db
keywords: globális terjesztés, gráf, gremlin
documentationcenter: ''
author: luisbosquez
manager: kfile
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
ms.openlocfilehash: 273b5aeafbf67016259da787f4dfef078ec0a669
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="how-to-setup-azure-cosmos-db-global-distribution-using-the-graph-api"></a>Az Azure Cosmos DB globális terjesztésének beállítása a Graph API használatával

Ebben a cikkben bemutatjuk, hogyan állíthatja be az Azure Cosmos DB globális terjesztését az Azure Portallal, és hogyan csatlakozhat a Graph API használatával.

Ez a cikk a következő feladatokat mutatja be: 

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása a [Graph API-k](graph-introduction.md) használatával

[!INCLUDE [cosmos-db-tutorial-global-distribution-portal](../../includes/cosmos-db-tutorial-global-distribution-portal.md)]


## <a name="connecting-to-a-preferred-region-using-the-graph-api-using-the-net-sdk"></a>Csatlakozás egy kívánt régióhoz a .NET SDK-t használó Graph API használatával

A Graph API egy kódtárbővítményként érhető el az SQL API-n felül.

A [globális terjesztés](distribute-data-globally.md) kihasználása érdekében az ügyfélalkalmazások megadhatják a preferált régiók sorrendbe rendezett listáját a dokumentumokkal kapcsolatos műveletek elvégzéséhez. Ezt a kapcsolódási szabályzat beállításával lehet megtenni. Az SDK az Azure Cosmos DB-fiók konfigurációja, az aktuális régiónkénti rendelkezésre állás és a megadott preferencialista alapján fogja kiválasztani az optimális végpontot az írási és olvasási műveletek végrehajtásához.

A preferencialista meghatározására akkor kerül sor, amikor kapcsolatot inicializál az SDK-k használatával. Az SDK-k egy „PreferredLocations” nevű választható paramétert is elfogadnak, amely az Azure-régiók rendezett listája.

* **Írások**: Az SDK az összes írást automatikusan az aktuális írási régióba küldi.
* **Olvasások**: Az olvasások a PreferredLocations lista első elérhető régiójába lesznek küldve. Ha a kérelem meghiúsul, az ügyfél továbbadja a listát a következő régiónak, és így tovább. Az SDK-k csak a PreferredLocations listában szereplő régiókból próbálnak meg olvasni. Így ha például a Cosmos DB-fiók három régióban érhető el, de az ügyfél csak kettőt ad meg a nem írási régiók közül a PreferredLocations listában, akkor az írási régió nem szolgál ki olvasásokat, még feladatátvétel esetén sem.

Az alkalmazás az SDK 1.8-as vagy újabb verzióiban elérhető WriteEndpoint és a ReadEndpoint tulajdonságok megtekintésével ellenőrizni tudja az SDK által kiválasztott aktuális írási és olvasási végpontot. Ha a PreferredLocations tulajdonság nincs beállítva, a kérelmek az aktuális írási régióból lesznek teljesítve.

### <a name="using-the-sdk"></a>Az SDK használata

Például a .NET SDK-ban a `DocumentClient` konstruktor `ConnectionPolicy` paramétere rendelkezik egy `PreferredLocations` nevű tulajdonsággal. Ez a tulajdonság megadható régiónevek egy listájaként. Az [Azure-régiók][regions] megjelenített nevei megadhatók a(z) `PreferredLocations` részeként.

> [!NOTE]
> A végpontok URL-címei nem tekinthetők hosszú élettartamú állandóknak. A szolgáltatás bármikor frissítheti őket. Az SDK automatikusan kezeli ezt a módosítást.
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

Ezzel el is végezte az oktatóanyagot. Ha meg szeretné ismerni, hogyan kezelheti a globálisan replikált fiók konzisztenciáját, olvassa el a [Konzisztenciaszintek az Azure Cosmos DB-ben](consistency-levels.md) című cikket. További információ a globális adatbázis-replikáció működéséről az Azure Cosmos DB szolgáltatásban: [Globális adatterjesztés az Azure Cosmos DB-vel](distribute-data-globally.md).

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a következőket hajtotta végre:

> [!div class="checklist"]
> * Globális terjesztés konfigurálása az Azure Portallal
> * Globális terjesztés konfigurálása az SQL API-k használatával

Továbbléphet a következő oktatóanyagra, amelyből megtudhatja, hogyan fejleszthet helyileg az Azure Cosmos DB helyi emulátorával.

> [!div class="nextstepaction"]
> [Helyi fejlesztés az emulátorral](local-emulator.md)

[regions]: https://azure.microsoft.com/regions/

