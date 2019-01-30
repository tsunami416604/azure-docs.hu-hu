---
title: Az Azure Cosmos DB Table API .NET Standard SDK-t és az erőforrások
description: Mindent megtudhat az Azure Cosmos DB Table API és a .NET Standard SDK kiadási dátum, kivezetési dátum és minden egyes verziója végzett módosításokat.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 10/18/2018
ms.openlocfilehash: ce7cc489b107ce4bd95270b9a7f8cb560a2d2398
ms.sourcegitcommit: 898b2936e3d6d3a8366cfcccc0fccfdb0fc781b4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55249646"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Az Azure Cosmos DB tábla .NET Standard API: Töltse le és kibocsátási megjegyzések
> [!div class="op_single_selector"]

> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Aktuális támogatott keretrendszer**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1-Preview
* Adja hozzá a SAS-tokent, az Azure Storage-táblához végpontokon TablePermissions, ServiceProperties és ServiceStats műveletek támogatása. 
   > [!NOTE]
   > Néhány funkció, a korábbi Azure Storage Table SDK-kkal még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0-Preview
* Alapvető CRUD-MŰVELETEKKEL, batch és az Azure Storage-táblához végpontokon lekérdezési műveletek támogatása hozzáadva. 
   > [!NOTE]
   > Néhány funkció, a korábbi Azure Storage Table SDK-kkal még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1-Preview
* Az Azure Cosmos DB Table .NET Standard SDK egy platformfüggetlen .NET-kódtár, amely a Cosmos DB a tábla adatmodell hatékony hozzáférést biztosít. Ez a kezdeti kiadás támogatja a táblázat és az entitás CRUD + hasonló API-kat, mint a lekérdezési funkciók teljes készletét a [Cosmos DB Table SDK For .NET-keretrendszer](table-sdk-dotnet.md). 
   > [!NOTE]
   >  Azure Storage Table-beli még nem támogatottak a 0.9.1-preview verzióban.

## <a name="release-and-retirement-dates"></a>Kiadás és kivezetési dátuma
A Microsoft biztosít értesítési legalább **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

| Verzió | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [0.10.1-Preview](#0.10.1-preview) |2019. január 22. |--- |
| [0.10.0-Preview](#0.10.0-preview) |2018. december 18. |--- |
| [0.9.1-Preview](#0.9.1-preview) |2018. október 18. |--- |


## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Az Azure Cosmos DB Table API kapcsolatos további információkért lásd: [bemutatása az Azure Cosmos DB Table API](table-introduction.md). 
