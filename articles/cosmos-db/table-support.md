---
title: Azure Table Storage-támogatás az Azure Cosmos DB-ben
description: Ebből a cikkből megtudhatja, hogyan használható együtt az Azure Cosmos DB Table API és az Azure Storage Tables.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: overview
ms.date: 11/15/2017
author: wmengmsft
ms.author: wmeng
ms.reviewer: sngun
ms.openlocfilehash: c7c9caad6c454b90a6d50932450922a03bbc1944
ms.sourcegitcommit: 6ea7f0a6e9add35547c77eef26f34d2504796565
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/14/2019
ms.locfileid: "65604177"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Fejlesztés az Azure Cosmos DB Table API és az Azure Table Storage használatával

Az Azure Cosmos DB Table API és az Azure Table Storage ugyanazt a táblaadatmodellt használja, és ugyanazokat a létrehozási, törlési, frissítési és lekérdezési műveleteket teszik elérhetővé az SDK-kon keresztül. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Fejlesztés az Azure Cosmos DB Table API-val

Az [Azure Cosmos DB Table API](table-introduction.md) jelenleg négy SDK-t biztosít a fejlesztéshez: 

* [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table): .NET SDK. Ebben a könyvtárban .NET Standard célozza, és rendelkezik a függvénytárnak az osztályai és metódusainak aláírásai megegyeznek a nyilvános [Windows Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), de arra is van lehetősége a Table API használatával az Azure Cosmos DB-fiókokhoz való csatlakozással. .NET-keretrendszer könyvtár felhasználók [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table/) frissítése a javasolt [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) , karbantartási módban van, és hamarosan elavulttá válik.

* [Python SDK](table-sdk-python.md): Az új Azure Cosmos DB Python SDK az egyetlen SDK, amely támogatja az Azure Table Storage-ot a Pythonban. Ez az SDK az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz is csatlakozik.

* [Java SDK](table-sdk-java.md): Ez az Azure Storage SDK képes Azure Cosmos DB-fiókokhoz csatlakozni a Table API használatával.

* [Node.js SDK](table-sdk-nodejs.md): Ez az Azure Storage SDK képes Azure Cosmos DB-fiókokhoz csatlakozni a Table API használatával.


További információ a Table API használatának érhető el a [– gyakori kérdések: Fejlesztés a Table API-val](faq.md#table) cikk.

## <a name="developing-with-azure-table-storage"></a>Fejlesztés az Azure Table Storage használatával

Az Azure Table Storage az alábbi SDK-kat biztosítja a fejlesztéshez:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Ez a kódtár lehetővé teszi a Storage Table Service használatát.
- [Python SDK](table-sdk-python.md). Az Azure Cosmos DB Table SDK Pythonhoz is támogatja a Storage Table Service-t.
- [A Java-hoz készült Azure Storage SDK](https://github.com/azure/azure-storage-java). Ez az Azure Storage SDK ügyféloldali kódtárat biztosít a Java-szolgáltatásban az Azure Table Storage használatához.
- [Node.js SDK](table-sdk-nodejs.md). Ez az SDK egy Node.js-csomagot és egy böngészőkompatibilis JavaScript ügyféloldali kódtárat biztosít a Storage Table Service használatához.
- [AzureRmStorageTable PowerShell-modul](https://www.powershellgallery.com/packages/AzureRmStorageTable). Ez a PowerShell-modul parancsmagokat biztosít a Storage Tables használatához.
- [Az Azure Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp/). Ez a kódtár lehetővé teszi alkalmazások létrehozását az Azure Storage-ban.
- [Az Azure Storage Table ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő Ruby-csomagot biztosít.
- [Azure Storage Table ügyféloldali PHP-kódtára](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő ügyféloldali PHP-kódtárat biztosít.


   





