---
title: 'Az Azure Cosmos DB: DocumentDB API cikkek |} Microsoft Docs'
description: "Összes dokumentum adatbázisok létrehozásához a DocumentDB API-t az Azure Cosmos Adatbázisba vonatkozó cikkek listáját."
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: 
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: mimig
ms.openlocfilehash: 8d618e00ed274f5d5705346a4ffd8281769de4e3
ms.sourcegitcommit: 21a58a43ceceaefb4cd46c29180a629429bfcf76
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2017
---
# <a name="azure-cosmos-db-documentdb-api-documentation"></a>Az Azure Cosmos DB: DocumentDB API dokumentációja

Ez a cikk a DocumentDB API adott Azure Cosmos DB tartalomra mutató hivatkozásokat biztosít.

Ezek a cikkek nem vonatkoznak a Graph API-val, a tábla API vagy a MongoDB API. 

## <a name="introduction-and-concepts"></a>Bevezetés és fogalmak

Ezek a kell olvasni a témakör és forrás, a DocumentDB API-t az Azure Cosmos DB megismerését elindításához.

- [A DocumentDB API bemutatása](documentdb-introduction.md)
- [Adatbázis erőforrás-modellje](documentdb-resources.md)
- Webhely: [tesztlekérdezéseket](https://www.documentdb.com/sql/demo)
- Lap cheat: [SQL-szintaxis](documentdb-sql-query-cheat-sheet.md)

## <a name="quickstarts"></a>Gyors útmutatók

A gyors üzembe helyezés témakörök szolgálnak a leggyorsabban Azure Cosmos DB hozzon létre egy működő alkalmazást. Minden egyes gyors üzembe helyezés elsajátíthatja a Felhasználóifelület-alapú Azure-portál és a nyelvi kódolási kedvenc használata adatbázis megoldások létrehozásához Azure Cosmos DB számára. A Githubról klónozható web apps esetén minden egyes gyors üzembe helyezés érhetők el. 

- [.NET + azure-portál + webalkalmazások](create-documentdb-dotnet.md)
- [Java + Azure-portál + webalkalmazások](create-documentdb-java.md)
- [NODE.js + Azure-portál + webalkalmazások](create-documentdb-nodejs.md)
- [Python + Azure-portál + webalkalmazások](create-documentdb-python.md)
- [Xamarin + .NET + Azure-portál + webalkalmazások](create-documentdb-xamarin-dotnet.md)

## <a name="tutorials"></a>Oktatóanyagok

Ezek az oktatóanyagok egy szint a Quickstarts értékénél. Az oktatóanyagok teljesen új, és másolja az alkalmazások készítéséhez, és illessze be a kódot az alkalmazásba. Is megtudhatja, hogyan importálhatja adatokat, adatait, és az adatbázisok globálisan terjesztése.

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

- [.NET](documentdb-dotnet-application.md)
- [Node.js](documentdb-nodejs-application.md) 
- [Java](documentdb-java-application.md)
- [Python](documentdb-python-application.md)

### <a name="create-a-console-app"></a>Hozzon létre egy konzolalkalmazást

- [.NET](documentdb-get-started.md)
- [.NET Core](documentdb-dotnetcore-get-started.md) 
- [Java](documentdb-java-get-started.md) 
- [Node.js](documentdb-nodejs-get-started.md) 
- [C++](documentdb-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>Mobilalkalmazás létrehozása

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>Adatok használata

- [Adatok importálása](import-data.md)
- [Lekérdezés](tutorial-query-documentdb.md)
- [Globális adatok terjesztése](tutorial-global-distribution-documentdb.md)

### <a name="work-with-azure-functions"></a>Az Azure Functions használata

- [Hozzon létre egy Azure Cosmos DB eseményindító](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [Azure Cosmos-Adatbázist kíván használni a bemeneti és kimeneti kötések](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>Fejlesztői útmutató

- [Az SQL-lekérdezések](documentdb-sql-query.md)
- [Particionálás](documentdb-partition-data.md)
- [Kiszolgálóoldali programozása: tárolt eljárások, eseményindítók adatbázis és a felhasználó által megadott függvények](programming.md)
- [Teljesítménytesztelés](performance-testing.md)
- [Teljesítménnyel kapcsolatos tippek](performance-tips.md)
- [Több főkiszolgálós telepítés](multi-region-writers.md)
- [Dátum/idő](working-with-dates.md)
- [A dokumentum adatok modellezését](modeling-data.md) 

## <a name="sdks"></a>SDK-k

Az Azure Cosmos DB SDK-k engedélyezése ügyféloldali alkalmazásfejlesztés számos.

- [Java](documentdb-sdk-java.md)
- [.NET](documentdb-sdk-dotnet.md)
- [.NET – Csatorna módosítása](documentdb-sdk-dotnet-changefeed.md)
- [.NET Core](documentdb-sdk-dotnet-core.md)
- [Node.js](documentdb-sdk-node.md)
- [Python](documentdb-sdk-python.md)

## <a name="reference"></a>Referencia

- [REST](/rest/api/documentdb/)
- [REST erőforrás-szolgáltató](/rest/api/documentdbresourceprovider/)
- [SQL-lekérdezés hivatkozása](documentdb-sql-query-reference.md)
- [Az Azure Functions hivatkozik](../azure-functions/functions-bindings-documentdb.md)

## <a name="samples"></a>Példák

Az adott minta lapok adja meg, mintakód és API hivatkozhat DocumentDB API leggyakoribb feladatokat tartalmát.

- [.NET](documentdb-dotnet-samples.md)
- [Node.js](documentdb-nodejs-samples.md)
- [Python](documentdb-python-samples.md) 
