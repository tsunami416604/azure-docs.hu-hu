---
title: 'Az Azure Cosmos DB: SQL API cikkek |} Microsoft Docs'
description: Összes dokumentum adatbázisok létrehozásához az SQL API-t az Azure Cosmos Adatbázisba vonatkozó cikkek listáját.
services: cosmos-db
author: mimig1
manager: jhubbard
documentationcenter: ''
ms.assetid: 82bec99a-ac2b-474e-b41f-d2fb296c8feb
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/25/2017
ms.author: mimig
ms.openlocfilehash: 0ad80ce15769cfe823925cf36bbd47f2ddadec21
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-sql-api-documentation"></a>Az Azure Cosmos DB: SQL API-JÁNAK dokumentációja

Ez a cikk az SQL API-t adott Azure Cosmos DB tartalomra mutató hivatkozásokat biztosít.

Ezek a cikkek nem vonatkoznak a Graph API-val, a tábla API vagy a MongoDB API. 

## <a name="introduction-and-concepts"></a>Bevezetés és fogalmak

Ezek a kell olvasni a témakör és forrás elindítani az SQL API-t az Azure Cosmos DB megismerését.

- [Az SQL API bemutatása](sql-api-introduction.md)
- [Adatbázis erőforrás-modellje](sql-api-resources.md)
- Webhely: [tesztlekérdezéseket](https://www.documentdb.com/sql/demo)
- Lap cheat: [SQL-szintaxis](query-cheat-sheet.md)

## <a name="quickstarts"></a>Gyors útmutatók

A gyors üzembe helyezés témakörök szolgálnak a leggyorsabban Azure Cosmos DB hozzon létre egy működő alkalmazást. Minden egyes gyors üzembe helyezés elsajátíthatja a Felhasználóifelület-alapú Azure-portál és a nyelvi kódolási kedvenc használata adatbázis megoldások létrehozásához Azure Cosmos DB számára. A Githubról klónozható web apps esetén minden egyes gyors üzembe helyezés érhetők el. 

- [.NET + azure-portál + webalkalmazások](create-sql-api-dotnet.md)
- [Java + Azure-portál + webalkalmazások](create-sql-api-java.md)
- [NODE.js + Azure-portál + webalkalmazások](create-sql-api-nodejs.md)
- [Python + Azure-portál + webalkalmazások](create-sql-api-python.md)
- [Xamarin + .NET + Azure-portál + webalkalmazások](create-sql-api-xamarin-dotnet.md)

## <a name="tutorials"></a>Oktatóanyagok

Ezek az oktatóanyagok egy szint a Quickstarts értékénél. Az oktatóanyagok teljesen új, és másolja az alkalmazások készítéséhez, és illessze be a kódot az alkalmazásba. Is megtudhatja, hogyan importálhatja adatokat, adatait, és az adatbázisok globálisan terjesztése.

### <a name="create-a-web-app"></a>Webalkalmazás létrehozása

- [.NET](sql-api-dotnet-application.md)
- [Node.js](sql-api-nodejs-application.md) 
- [Java](sql-api-java-application.md)
- [Python](sql-api-python-application.md)

### <a name="create-a-console-app"></a>Hozzon létre egy konzolalkalmazást

- [.NET](sql-api-get-started.md)
- [.NET Core](sql-api-dotnetcore-get-started.md) 
- [Java](sql-api-java-get-started.md) 
- [Node.js](sql-api-nodejs-get-started.md) 
- [C++](sql-api-cpp-get-started.md)

### <a name="create-a-mobile-app"></a>Mobilalkalmazás létrehozása

- [Xamarin](mobile-apps-with-xamarin.md)

### <a name="work-with-data"></a>Adatok használata

- [Adatok importálása](import-data.md)
- [Lekérdezés](tutorial-query-sql-api.md)
- [Globális adatok terjesztése](tutorial-global-distribution-sql-api.md)

### <a name="work-with-azure-functions"></a>Az Azure Functions használata

- [Hozzon létre egy Azure Cosmos DB eseményindító](../azure-functions/functions-create-cosmos-db-triggered-function.md)
- [Azure Cosmos-Adatbázist kíván használni a bemeneti és kimeneti kötések](../azure-functions/functions-integrate-store-unstructured-data-cosmosdb.md)

## <a name="developers-guide"></a>Fejlesztői útmutató

- [Az SQL-lekérdezések](sql-api-sql-query.md)
- [Particionálás](sql-api-partition-data.md)
- [Kiszolgálóoldali programozása: tárolt eljárások, eseményindítók adatbázis és a felhasználó által megadott függvények](programming.md)
- [Teljesítménytesztelés](performance-testing.md)
- [Teljesítménnyel kapcsolatos tippek](performance-tips.md)
- [Több főkiszolgálós telepítés](multi-region-writers.md)
- [Dátum/idő](working-with-dates.md)
- [A dokumentum adatok modellezését](modeling-data.md) 

## <a name="sdks"></a>SDK-k

Az Azure Cosmos DB SDK-k engedélyezése ügyféloldali alkalmazásfejlesztés számos.

- [Java](sql-api-sdk-java.md)
- [.NET](sql-api-sdk-dotnet.md)
- [.NET – Csatorna módosítása](sql-api-sdk-dotnet-changefeed.md)
- [.NET Core](sql-api-sdk-dotnet-core.md)
- [Node.js](sql-api-sdk-node.md)
- [Python](sql-api-sdk-python.md)

## <a name="reference"></a>Leírások

- [REST](/rest/api/documentdb/)
- [REST erőforrás-szolgáltató](/rest/api/documentdbresourceprovider/)
- [SQL-lekérdezés hivatkozása](sql-api-sql-query-reference.md)
- [Az Azure Functions hivatkozik](../azure-functions/functions-bindings-cosmosdb.md)

## <a name="samples"></a>Példák

Az adott minta lapok adja meg, mintakód és API hivatkozik a leggyakrabban használt SQL API-feladatok tartalmát.

- [.NET](sql-api-dotnet-samples.md)
- [Node.js](sql-api-nodejs-samples.md)
- [Python](sql-api-python-samples.md) 
