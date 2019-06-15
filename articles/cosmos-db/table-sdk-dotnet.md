---
title: Az Azure Cosmos DB Table API .NET SDK-t és az erőforrások
description: Mindent megtudhat az Azure Cosmos DB Table API többek között a kiadási dátum, használatból való kivonást egyaránt dátumok és minden verzió között végrehajtott módosítások.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 08/17/2018
ms.openlocfilehash: db7cc556525ab57f14984232bf1797764865fca3
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65606255"
---
# <a name="azure-cosmos-db-table-net-api-download-and-release-notes"></a>Az Azure Cosmos DB Table .NET API: Töltse le és kibocsátási megjegyzések

> [!div class="op_single_selector"]
> * [.NET](table-sdk-dotnet.md)
> * [.NET Standard](table-sdk-dotnet-standard.md)
> * [Java](table-sdk-java.md)
> * [Node.js](table-sdk-nodejs.md)
> * [Python](table-sdk-python.md)

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://aka.ms/acdbtablenuget)|
|**API-dokumentáció**|[.NET API dokumentációja](https://aka.ms/acdbtableapiref)|
|**Gyors útmutató**|[Azure Cosmos DB: .NET-tel és a Table API-alkalmazás létrehozása](create-table-dotnet.md)|
|**Oktatóanyag**|[Azure Cosmos DB: Fejlesztés a Table API a .NET használatával](tutorial-develop-table-dotnet.md)|
|**Aktuális támogatott keretrendszer**|[A Microsoft .NET-keretrendszer 4.5.1](https://www.microsoft.com/en-us/download/details.aspx?id=40779)|

> [!IMPORTANT]
> A .NET-keretrendszer SDK [Microsoft.Azure.CosmosDB.Table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table) van a karbantartás mód, és hamarosan el fognak évülni. Frissítsen az új .NET Standard kódtár [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) továbbra is a legújabb funkciók, a Table API által támogatott.

> Ha az előzetes verzióban hozta létre a Table API-fiókot, hozzon létre egy [új Table API-fiókot](create-table-dotnet.md#create-a-database-account), amely használható az általánosan elérhető Table API SDK-kkal.
>

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name210210"></a><a name="2.1.0"/>2.1.0

* Hibajavítások

### <a name="a-name200200"></a><a name="2.0.0"/>2.0.0

* A hozzáadott többrégiós írási támogatása
* Rögzített Microsoft.Azure.DocumentDB, Microsoft.OData.Core, Microsoft.OData.Edm, Microsoft.Spatial NuGet-csomagfüggőségeket

### <a name="a-name113113"></a><a name="1.1.3"/>1.1.3

* Rögzített NuGet-csomagfüggőségeket Microsoft.Azure.Storage.Common és Microsoft.Azure.DocumentDB.
* A tábla szerializálási JsonConvert.DefaultSettings konfigurálásakor hibajavításokat tartalmaz.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1

* A hozzáadott érvényesítése helytelen ETag közvetlen módban.
* LINQ lekérdezés hiba kijavítva átjáró módban.
* Szinkron API-k most futtassa a szálkészlet SynchronizationContext együtt.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0

* Add TableQueryMaxItemCount, TableQueryEnableScan, TableQueryMaxDegreeOfParallelism, and TableQueryContinuationTokenLimitInKb to TableRequestOptions
* Hibajavítások

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0

* Általánosan elérhető kiadások

### <a name="a-name010-preview090-preview"></a><a name="0.1.0-preview"/>0.9.0-Preview

* Kezdeti előzetes kiadás

## <a name="release-and-retirement-dates"></a>Kiadás és kivezetési dátuma

A Microsoft biztosít értesítési legalább **12 hónapig** kivonása egy SDK-t kiegyenlítse az a és újabb támogatott verzióra váltás előtt.

A `Microsoft.Azure.CosmosDB.Table` függvénytár jelenleg elérhető .NET-keretrendszer csak, és a karbantartási módban van, és hamarosan elavulttá válik. Új szolgáltatások és funkciók és optimalizálási lehetőségek csak hozzáadódnak a .NET Standard kódtár [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table), mert az ilyen frissítése a javasolt [Microsoft.Azure.Cosmos.Table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table).

A [WindowsAzure.Storage-PremiumTable](https://www.nuget.org/packages/WindowsAzure.Storage-PremiumTable/0.1.0-preview) előzetes csomag elavult. 2018\. November 15. a WindowsAzure.Storage-PremiumTable SDK-t kivezetjük, mely arra kéri, a kivont SDK nem fog tudni. 

Az Azure Cosmos DB egy kivont SDK használatával bármilyen kérelmeket a szolgáltatás által a rendszer elutasítja.
<br/>

| Version | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [2.1.0](#2.1.0) |2019\. január 22.|2020\. április 01. |
| [2.0.0](#2.0.0) |2018\. szeptember 26.|2020\. március 01. |
| [1.1.3](#1.1.3) |2018\. július 17.|2019\. december 01. |
| [1.1.1](#1.1.1) |2018\. március 26.|2019\. december 01. |
| [1.1.0](#1.1.0) |2018\. február 21.|2019\. december 01. |
| [1.0.0](#1.0.0) |2017\. november 15.|2019\. november 15. |
| 0.9.0-Preview |2017\. november 11. |2019\. november 11. |

## <a name="troubleshooting"></a>Hibaelhárítás

Ha megjelenik a hibaüzenet 

```
Unable to resolve dependency 'Microsoft.Azure.Storage.Common'. Source(s) used: 'nuget.org', 
'CliFallbackFolder', 'Microsoft Visual Studio Offline Packages', 'Microsoft Azure Service Fabric SDK'`
```

használja a Microsoft.Azure.CosmosDB.Table NuGet-csomagot próbál, ha a probléma megoldásához két lehetősége van:

* Csomag kezelése konzol segítségével telepítse a Microsoft.Azure.CosmosDB.Table csomagot és annak függőségeit. Ehhez írja be a következőt a Csomagkezelői konzol a megoldáshoz. 

    ```powershell
    Install-Package Microsoft.Azure.CosmosDB.Table -IncludePrerelease
    ```

    
* Az előnyben részesített NuGet Csomagkezelő eszközt használja, telepítse a Microsoft.Azure.Storage.Common NuGet-csomag Microsoft.Azure.CosmosDB.Table telepítése előtt.

## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még

Az Azure Cosmos DB Table API kapcsolatos további információkért lásd: [bemutatása az Azure Cosmos DB Table API](table-introduction.md). 