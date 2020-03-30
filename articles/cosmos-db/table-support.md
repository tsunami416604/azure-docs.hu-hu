---
title: Az Azure Table Storage támogatása az Azure Cosmos DB-ben
description: Ismerje meg, hogyan működik együtt az Azure Cosmos DB Table API és az Azure Storage Tables, ha ugyanazt a táblaadat-modellt osztja meg egy művelettel
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 12/02/2019
author: sakash279
ms.author: akshanka
ms.reviewer: sngun
ms.openlocfilehash: 82397e49e473b8660dfada54a0e05cafe0da4120
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76770668"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Fejlesztés az Azure Cosmos DB Table API és az Azure Table Storage használatával

Az Azure Cosmos DB Table API és az Azure Table Storage ugyanazt a táblaadatmodellt használja, és ugyanazokat a létrehozási, törlési, frissítési és lekérdezési műveleteket teszik elérhetővé az SDK-kon keresztül.

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Fejlesztés az Azure Cosmos DB Table API-val

Az [Azure Cosmos DB Table API](table-introduction.md) jelenleg négy SDK-t biztosít a fejlesztéshez: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Ez a könyvtár a .NET Standardot célozza meg, és ugyanazokat az osztályokat és metódusaláírásokat rendelkezik, mint a nyilvános [Windows Azure Storage SDK,](https://www.nuget.org/packages/WindowsAzure.Storage)de képes az Azure Cosmos DB-fiókokhoz való csatlakozásra is a Table API használatával. A .NET Framework függvénytár [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) felhasználói számára ajánlott a [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) frissítése karbantartási módban, és hamarosan elavult.

* [Python SDK:](table-sdk-python.md)Az új Azure Cosmos DB Python SDK az egyetlen SDK, amely támogatja az Azure Table storage pythonban. Ez az SDK az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz is csatlakozik.

* [Java SDK:](table-sdk-java.md)Ez az Azure Storage SDK képes csatlakozni az Azure Cosmos DB-fiókok a table API használatával.

* [Node.js SDK:](table-sdk-nodejs.md)Ez az Azure Storage SDK képes csatlakozni az Azure Cosmos DB-fiókok a table API használatával.


A Table API használatával kapcsolatban a [Table API-val történő fejlesztéshez kapcsolódó gyakori kérdések](faq.md#table) között tekinthet meg további információt.

## <a name="developing-with-azure-table-storage"></a>Fejlesztés az Azure Table Storage használatával

Az Azure Table Storage az alábbi SDK-kat biztosítja a fejlesztéshez:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Ez a kódtár lehetővé teszi a Storage Table Service használatát.
- [Python SDK](https://github.com/Azure/azure-cosmos-table-python). Az Azure Cosmos DB Table SDK python számára támogatja a table storage-szolgáltatást (mivel az Azure Table Storage és a Cosmos DB Table API ugyanazokat a funkciókat és funkciókat használja, és annak érdekében, hogy az SDK fejlesztési erőfeszítéseinek faktorizálása érdekében, javasoljuk, hogy használja ezt az SDK-t).
- [A Java-hoz készült Azure Storage SDK](https://github.com/azure/azure-storage-java). Ez az Azure Storage SDK ügyféloldali kódtárat biztosít a Java-szolgáltatásban az Azure Table Storage használatához.
- [Node.js SDK](https://github.com/Azure/azure-storage-node). Ez az SDK egy Node.js-csomagot és egy böngészőkompatibilis JavaScript ügyféloldali kódtárat biztosít a Storage Table Service használatához.
- [AzureRmStorageTable PowerShell-modul](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ez a PowerShell-modul parancsmagokat biztosít a Storage Tables használatához.
- [Az Azure Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp/). Ez a kódtár lehetővé teszi alkalmazások létrehozását az Azure Storage-ban.
- [Az Azure Storage Table ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő Ruby-csomagot biztosít.
- [Azure Storage Table ügyféloldali PHP-kódtára](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő ügyféloldali PHP-kódtárat biztosít.


   





