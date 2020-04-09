---
title: Az Azure Cosmos DB table API .NET SDK & erőforrások
description: Tudjon meg mindent az Azure Cosmos DB Table API-ról, beleértve a kiadási dátumokat, a megszüntetési dátumokat és az egyes verziók között végrehajtott módosításokat.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: 9356ab0aec375a046a8f200838df296b2cf74bce
ms.sourcegitcommit: 7d8158fcdcc25107dfda98a355bf4ee6343c0f5c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/09/2020
ms.locfileid: "80984980"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Azure Cosmos DB Table .NET API: Megjegyzések letöltése és kiadása

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK letöltés**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table)|
|**Első lépések**|[Azure Cosmos DB: Alkalmazás létrehozása a .NET és a Table API használatával](create-table-dotnet.md)|
|**Bemutató**|[Azure Cosmos DB: Fejlesztés a Table API-val .NET-keretrendszerben](tutorial-develop-table-dotnet.md)|
|**Jelenlegi támogatott keretrendszer**|[Microsoft .](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> A .NET Framework SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) karbantartási módban van, és hamarosan elavult. Frissítsen az új .NET Standard kódtárra, [a Microsoft.Azure.Cosmos.Table-re,](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) hogy továbbra is megkapja a Table API által támogatott legújabb funkciókat.

> Ha az előzetes verzióban hozta létre a Table API-fiókot, hozzon létre egy [új Table API-fiókot](create-table-dotnet.md#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="212"></a><a name="2.1.2"/>2.1.2

* Hibajavítások

### <a name="210"></a><a name="2.1.0"/>2.1.0

* Hibajavítások

### <a name="200"></a><a name="2.0.0"/>2.0.0

* Hozzáadott többrégiós írási támogatás
* Javítottuk a NuGet csomagfüggőségeit a Microsoft.Azure.DocumentDB, a Microsoft.OData.Core, a Microsoft.OData.Edm, a Microsoft.Spatial fájlban

### <a name="113"></a><a name="1.1.3"/>1.1.3

* Javítottuk a NuGet csomagfüggőségeit a Microsoft.Azure.Storage.Common és a Microsoft.Azure.DocumentDB webhelyen.
* Hibajavítások a tábla szerializálásán, ha a JsonConvert.DefaultSettings konfigurálva van.

### <a name="111"></a><a name="1.1.1"/>1.1.1

* A hibás anforinátlan EK-ok közvetlen módban való ellenőrzése hozzáadva.
* Javítottuk a LINQ lekérdezési hibát átjáró módban.
* A szinkron API-k mostantól a SynchronizationContext-környezettel rendelkező szálkészleten futnak.

### <a name="110"></a><a name="1.1.0"/>1.1.0

* TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism és TableQueryContinuationTokenLimitInKb hozzáadása a TableRequestOptions alkalmazáshoz
* Hibajavítások

### <a name="100"></a><a name="1.0.0"/>1.0.0

* Általános rendelkezésre állási kiadás

### <a name="090-preview"></a><a name="0.1.0-preview"/>0.9.0-előzetes

* Kezdeti előzetes kiadás

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok

A Microsoft legalább **12 hónappal** az SDK kicsomagolása előtt értesítést küld az újabb/támogatott verzióra való áttérés zökkenőmentessé.

A `Microsoft.Azure.CosmosDB.Table` könyvtár jelenleg csak a . Az új funkciók, funkciók és optimalizálások csak a [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table). [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table)

A [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) előzetes verziójú csomag elavult. 2018. november 15-én a WindowsAzure.Storage-PremiumTable SDK megszűnik, ekkor a kivett SDK-hoz érkező kérelmek nem engedélyezettek. 

Az Azure Cosmos DB-nek a kihajtott SDK használatával érkező kérelmeket a szolgáltatás elutasítja.
<br/>

| Verzió | Megjelenési dátum | Nyugdíjazás dátuma |
| --- | --- | --- |
| [2.1.2](#2.1.2) |2019. szeptember 16.September 16, 2019| |
| [2.1.0](#2.1.0) |2019. január 22.|2020. április 01. |
| [2.0.0](#2.0.0) |2018. szeptember 26.September 26, 2018|2020. március 01. |
| [1.1.3](#1.1.3) |2018. július 17.|2019. december 01., |
| [1.1.1](#1.1.1) |2018. március 26.March 26, 2018|2019. december 01., |
| [1.1.0](#1.1.0) |2018. február 21.February 21, 2018|2019. december 01., |
| [1.0.0](#1.0.0) |2017. november 15.|2019. november 15., 2019. |
| 0.9.0-előzetes |2017. november 11., 2017. |2019. november 11., 2019. |

## <a name="troubleshooting"></a>Hibaelhárítás

Ha a hibaüzenet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

a Microsoft.Azure.CosmosDB.Table NuGet csomag használatakor két lehetősége van a probléma megoldására:

* A Package Manage Console használatával telepítse a Microsoft.Azure.CosmosDB.Table csomagot és annak függőségeit. Ehhez írja be a következőt a csomagkezelő konzolba a megoldáshoz. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* A kívánt NuGet csomagkezelő eszközzel telepítse a Microsoft.Azure.Storage.Common NuGet csomagot a Microsoft.Azure.CosmosDB.Table telepítése előtt.

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még

Ha többet szeretne megtudni az Azure Cosmos DB Table API-ról, [olvassa el az Azure Cosmos DB Table API bemutatása című témakört.](table-introduction.md) 
