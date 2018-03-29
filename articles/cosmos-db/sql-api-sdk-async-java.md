---
title: 'Azure Cosmos DB: SQL aszinkron Java API-t, az SDK és erőforrások |} Microsoft Docs'
description: Tudnivalók az SQL aszinkron Java API-t és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és az Azure Cosmos DB SQL aszinkron Java SDK verziói között végrehajtott módosításokat.
services: cosmos-db
documentationcenter: java
author: mimig1
manager: jhubbard
editor: cgronlun
ms.assetid: a452ffa2-c15d-4b0a-a8c1-ec9b750ce52b
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: article
ms.date: 02/28/2018
ms.author: mimig
ms.openlocfilehash: d117f0571bef8f8a2ec48a067d7796f03c0e535a
ms.sourcegitcommit: d74657d1926467210454f58970c45b2fd3ca088d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2018
---
# <a name="azure-cosmos-db-async-java-sdk-for-sql-api-release-notes-and-resources"></a>Azure Cosmos DB aszinkron Java SDK API-SQL: kibocsátási megjegyzések és erőforrások
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> 
> 

A SQL API aszinkron Java SDK abban tér el az SQL API Java SDK-támogatása az aszinkron műveletek megadásával a [Netty könyvtár](http://netty.io/). A már meglévő [SQL API Java SDK](sql-api-sdk-java.md) nem támogatja az aszinkron műveletek. 

<table>

<tr><td>**SDK letöltése**</td><td>[Maven 3](https://mvnrepository.com/artifact/com.microsoft.azure/azure-cosmosdb)</td></tr>

<tr><td>**API-JÁNAK dokumentációja**</td><td>[Java API-referenciadokumentáció](https://azure.github.io/azure-cosmosdb-java/)</td></tr>

<tr><td>**Hozzájárul az SDK**</td><td>[GitHubon](https://github.com/Azure/azure-cosmosdb-java)</td></tr>

<tr><td>**Első lépések**</td><td>[Ismerkedés az aszinkron Java SDK-val](https://github.com/Azure-Samples/azure-cosmos-db-sql-api-async-java-getting-started)</td></tr>

<tr><td>**kódminta**</td><td>[Github](https://github.com/Azure/azure-cosmosdb-java#usage-code-sample)</td></tr>

<tr><td>**Teljesítménnyel kapcsolatos tippek**</td><td>[Github-fontos](https://github.com/Azure/azure-cosmosdb-java#guide-for-prod)</td></tr>

<tr><td>**Minimális támogatott futásidejű**</td><td>[JDK 8](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html)</td></tr>
</table></br>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK-val nem blokkoló IO használatával végpont támogatása a [Netty könyvtár](http://netty.io/) átjáró módban. 

## <a name="release-and-retirement-dates"></a>Kiadás és a használatból való kivonást dátuma
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető.

A Cosmos DB kivont SDK használatával fog kell elutasította a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.0.0](#1.0.0) |2018. február 27.|--- |

## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján.

