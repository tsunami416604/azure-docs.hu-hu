---
title: Az Azure Cosmos DB Table API .NET standard SDK & erőforrások
description: Ismerje meg az Azure Cosmos DB Table API-t és a .NET standard SDK-t, beleértve a kiadási dátumokat, a megszüntetési dátumokat és az egyes verziók között végrehajtott módosításokat.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: 48fd85e27feb3d0c7f7f722dbbd502be55684385
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76771597"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET Standard API: Megjegyzések letöltése és kiadása
> [!div class="op_single_selector"]
> 
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK letöltés**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)|
|**Minta**|[Cosmos DB table API .NET minta](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Quickstart**|[Quickstart](create-table-dotnet.md)|
|**Bemutató**|[Bemutató](tutorial-develop-table-dotnet.md)|
|**Jelenlegi támogatott keretrendszer**|[Microsoft .NET Standard 2.0](https://www.nuget.org/packages/NETStandard.Library)|
|**Jelentés kiadása**|[Jelentés kiadása](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>Kibocsátási megjegyzések a 2.0.0 sorozathoz
2.0.0 sorozat veszi a függőséget a [Microsoft.Azure.Cosmos,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)a teljesítmény javítása és a névtér konszolidáció Cosmos DB végpont.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0-előzetes
* a 2.0.0 Table SDK kezdeti előzetes verziója, amely a [Microsoft.Azure.Cosmos-tól](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)függ, teljesítményfejlesztéssel és névtér-konszolidációval a Cosmos DB-végpontra. A nyilvános API ugyanaz marad.

## <a name="release-notes-for-100-series"></a>Kibocsátási megjegyzések az 1.0.0 sorozathoz
Az 1.0.0 sorozat a [Microsoft.Azure.DocumentDB.Core függése.](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)

### <a name="105"></a><a name="1.0.5"/>1.0.5
* Új konfiguráció bevezetése a TableClientConfiguration csoportban a Rest Executor használatával kommunikálhat a Cosmos DB Table API-val

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5-preview
* Hibajavítások

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Hibajavítások
* Adja meg a HttpClientTimeout beállítást a RestExecutorConfiguration számára.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4-preview
* Hibajavítások
* Adja meg a HttpClientTimeout beállítást a RestExecutorConfiguration számára.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Hibajavítások

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Általános rendelkezésre állási kiadás

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0-előzetes
* Módosítások történtek a CloudTableClient konfigurálásának módjában. Most egy TableClientConfiguration objektumot vesz igénybe az építés során. A TableClientConfiguration különböző tulajdonságokat biztosít az ügyfél viselkedésének konfigurálásához attól függően, hogy a célvégpont a Cosmos DB Table API vagy az Azure Storage Table API.
* A TableQuery támogatása hozzáadva az egyéni oszlopok ban rendezett sorrendben adásához. Ez a szolgáltatás csak a Cosmos DB-táblázat végpontjai támogatott.
* Támogatás hozzáadva a requestcharges különböző eredménytípusokhoz való felfedéséhez. Ez a szolgáltatás csak a Cosmos DB-táblázat végpontjai támogatott.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1-előnézet
* Add támogatása SAS-jogkivonat, a TablePermissions, ServiceProperties és ServiceStats azure storage-végpontok on Operations. 
   > [!NOTE]
   > A korábbi Azure Storage Table SDK-k egyes funkciói még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0-előzetes
* Adja hozzá az alapvető CRUD-, kötegelt és lekérdezési műveletek támogatását az Azure Storage-tábla végpontjai ellen. 
   > [!NOTE]
   > A korábbi Azure Storage Table SDK-k egyes funkciói még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1-preview
* Az Azure Cosmos DB Table .NET Standard SDK egy platformfüggetlen .NET-kódtár, amely hatékony hozzáférést biztosít a Cosmos DB táblaadatmodelljéhez. Ez a kezdeti kiadás támogatja a Table és entity CRUD + Query funkciók teljes készletét, amelyek hasonló API-kkal rendelkeznek, mint a [Cosmos DB Table SDK for .](table-sdk-dotnet.md) 
   > [!NOTE]
   >  Az Azure Storage Table végpontok még nem támogatottak a 0.9.1-preview verzióban.

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** az SDK kicsomagolása előtt értesítést küld az újabb/támogatott verzióra való áttérés zökkenőmentessé.

Ez a platformfüggetlen .NET Standard könyvtár [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) a .NET Framework library [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)helyébe lép.

### <a name="200-series"></a>2.0.0 sorozat
| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [2.0.0-előzetes](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0 sorozat
| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [1.0.5](#1.0.5) |2019. szeptember 13.September 13, 2019 |--- |
| [1.0.5-preview](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4-preview](#1.0.4-preview) |2019. július 26.July 26, 2019 |--- |
| 1.0.2-preview |2019. május 2.May 2, 2019 |--- |
| [1.0.1](#1.0.1) |2019. április 19.April 19, 2019 |--- |
| [1.0.0](#1.0.0) |2019. március 13.March 13, 2019 |--- |
| [0.11.0-előzetes](#0.11.0-preview) |2019. március 5. |--- |
| [0.10.1-előnézet](#0.10.1-preview) |2019. január 22. |--- |
| [0.10.0-előzetes](#0.10.0-preview) |2018. december 18.December 18.December 18.December 18,8 |--- |
| [0.9.1-preview](#0.9.1-preview) |2018. október 18.October 18, 2018 |--- |


## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni az Azure Cosmos DB Table API-ról, [olvassa el az Azure Cosmos DB Table API bemutatása című témakört.](table-introduction.md)
