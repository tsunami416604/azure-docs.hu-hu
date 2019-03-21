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
ms.openlocfilehash: 38b283ed666b39b4e090bd95051a4454a9b47e62
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57975661"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Az Azure Cosmos DB tábla .NET Standard API: Töltse le és kibocsátási megjegyzések
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Minta**|[A cosmos DB Table API .NET-minta](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Gyors útmutató**|[Gyors útmutató](create-table-dotnet.md)|
|**Oktatóanyag**|[Oktatóanyag](tutorial-develop-table-dotnet.md)|
|**Aktuális támogatott keretrendszer**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Általánosan elérhető kiadások

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0-preview
* Hogyan konfigurálható a CloudTableClient módosítások történtek. Most már vesz egy a konstrukció során TableClientConfiguration objektum. TableClientConfiguration konfigurálása az ügyfél viselkedése attól függően, hogy a céloldali végpont Cosmos DB Table API vagy az Azure Storage Table API különböző tulajdonságokat tartalmaz.
* Támogatás hozzáadva a TableQuery rendezetten eredményeinek visszaadása az egyéni oszlop. Ez a funkció csak a Cosmos DB Table-végpont támogatott.
* Támogatás hozzáadva RequestCharges elérhetővé a különféle eredményt. Ez a funkció csak a Cosmos DB Table-végpont támogatott.

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
| [1.0.0](#1.0.0) |2019. márciusi 13. |--- |
| [0.11.0-Preview](#0.11.0-preview) |2019. márciusi 5. |--- |
| [0.10.1-Preview](#0.10.1-preview) |2019. január 22. |--- |
| [0.10.0-Preview](#0.10.0-preview) |2018. december 18. |--- |
| [0.9.1-Preview](#0.9.1-preview) |2018. október 18. |--- |


## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Az Azure Cosmos DB Table API kapcsolatos további információkért lásd: [bemutatása az Azure Cosmos DB Table API](table-introduction.md). 
