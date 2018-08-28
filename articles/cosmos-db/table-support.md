---
title: Az Azure Table Storage támogatása az Azure Cosmos DB-ben | Microsoft Docs
description: Ebből a cikkből megtudhatja, hogyan használható együtt az Azure Cosmos DB Table API és az Azure Storage Tables.
services: cosmos-db
author: SnehaGunda
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-table
ms.devlang: na
ms.topic: overview
ms.date: 11/15/2017
ms.author: sngun
ms.openlocfilehash: a6ac954caa8341d6d949811f0bb9d7e68c0b5aac
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42022757"
---
# <a name="developing-with-azure-cosmos-db-table-api-and-azure-table-storage"></a>Fejlesztés az Azure Cosmos DB Table API és az Azure Table Storage használatával

Az Azure Cosmos DB Table API és az Azure Table Storage ugyanazt a táblaadatmodellt használja, és ugyanazokat a létrehozási, törlési, frissítési és lekérdezési műveleteket teszik elérhetővé az SDK-kon keresztül. 

[!INCLUDE [storage-table-cosmos-comparison](../../includes/storage-table-cosmos-comparison.md)]

## <a name="developing-with-the-azure-cosmos-db-table-api"></a>Fejlesztés az Azure Cosmos DB Table API-val

Az [Azure Cosmos DB Table API](table-introduction.md) jelenleg négy SDK-t biztosít a fejlesztéshez: 
- [Microsoft.Azure.CosmosDB.Table](https://aka.ms/tableapinuget) .NET SDK. Ez a kódtár ugyanazokkal az osztályokkal és metódus-aláírásokkal rendelkezik, mint a nyilvános [Microsoft Azure Storage SDK](https://www.nuget.org/packages/WindowsAzure.Storage), de képes Azure Cosmos DB-fiókokhoz is csatlakozni a Table API használatával. Vegye figyelembe, hogy a `Microsoft.Azure.CosmosDB.Table` kódtár jelenleg csak a .NET Standardhoz érhető el, a .NET Core-hoz nem.
- [Python SDK](table-sdk-python.md). Az új Azure Cosmos DB Python SDK az egyetlen SDK, amely támogatja az Azure Table Storage-ot a Pythonban. Ez az SDK az Azure Table Storage-hoz és az Azure Cosmos DB Table API-hoz is csatlakozik.
- [Java SDK](table-sdk-java.md). Ez az Azure Storage SDK képes Azure Cosmos DB-fiókokhoz csatlakozni a Table API használatával.
- [Node.js SDK](table-sdk-nodejs.md). Ez az Azure Storage SDK képes Azure Cosmos DB-fiókokhoz csatlakozni a Table API használatával.

A Table API használatával kapcsolatban a [Table API-val történő fejlesztéshez kapcsolódó gyakori kérdések](faq.md#develop-with-the-table-api) között tekinthet meg további információt.

## <a name="developing-with-azure-table-storage"></a>Fejlesztés az Azure Table Storage használatával

Az Azure Table Storage az alábbi SDK-kat biztosítja a fejlesztéshez:

- [WindowsAzure.Storage .NET SDK](https://www.nuget.org/packages/WindowsAzure.Storage/). Ez a kódtár lehetővé teszi a Storage Table Service használatát.
- [Python SDK](table-sdk-python.md). Az Azure Cosmos DB Table SDK Pythonhoz is támogatja a Storage Table Service-t.
- [A Java-hoz készült Azure Storage SDK](https://github.com/azure/azure-storage-java). Ez az Azure Storage SDK ügyféloldali kódtárat biztosít a Java-szolgáltatásban az Azure Table Storage használatához.
- [Node.js SDK](table-sdk-nodejs.md). Ez az SDK egy Node.js-csomagot és egy böngészőkompatibilis JavaScript ügyféloldali kódtárat biztosít a Storage Table Service használatához.
- [AzureRmStorageTable PowerShell-modul](https://www.powershellgallery.com/packages/AzureRmStorageTable/1.0.0.7). Ez a PowerShell-modul parancsmagokat biztosít a Storage Tables használatához.
- [Az Azure Storage ügyféloldali kódtára a C++ programnyelvhez](https://github.com/Azure/azure-storage-cpp/). Ez a kódtár lehetővé teszi alkalmazások létrehozását az Azure Storage-ban.
- [Az Azure Storage Table ügyféloldali kódtára a Rubyhoz](https://github.com/azure/azure-storage-ruby/tree/master/table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő Ruby-csomagot biztosít.
- [Azure Storage Table ügyféloldali PHP-kódtára](https://github.com/Azure/azure-storage-php/tree/master/azure-storage-table). Ez a projekt az Azure Storage Table Service elérést megkönnyítő ügyféloldali PHP-kódtárat biztosít.


   





