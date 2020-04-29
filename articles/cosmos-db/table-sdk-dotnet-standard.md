---
title: Azure Cosmos DB Table API .NET Standard SDK &-erőforrások
description: Ismerkedjen meg a Azure Cosmos DB Table API és a .NET Standard SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és az egyes verziók közötti módosításokat.
author: sakash279
ms.author: akshanka
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: f7c6829d858e36b55f079f8ee03e431f67995e66
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "81272576"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB table .NET Standard API: letöltési és kibocsátási megjegyzések
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
|**Minta**|[Cosmos DB Table API .NET-minta](https://github.com/Azure-Samples/azure-cosmos-table-dotnet-core-getting-started)|
|**Első lépések**|[Első lépések](create-table-dotnet.md)|
|**Oktatóanyag**|[Oktatóanyag](tutorial-develop-table-dotnet.md)|
|**Jelenleg támogatott keretrendszer**|[Microsoft .NET Standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|
|**Probléma jelentése**|[Probléma jelentése](https://github.com/Azure/azure-cosmos-table-dotnet/issues)|

## <a name="release-notes-for-200-series"></a>A 2.0.0 sorozat kibocsátási megjegyzései
a 2.0.0 sorozat a [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)függőségét, a teljesítmény javítását és a névtér összevonását Cosmos db végpontra.

### <a name="200-preview"></a><a name="2.0.0-preview"/>2.0.0 – előzetes verzió
* a 2.0.0 Table SDK kezdeti előzetes verziója, amely a [Microsoft. Azure. Cosmos](https://www.nuget.org/packages/Microsoft.Azure.Cosmos/)függőségét veszi igénybe, a teljesítmény javításával és a névtér-konszolidációval Cosmos db végpontra. A nyilvános API-k változatlanok maradnak.

## <a name="release-notes-for-100-series"></a>A 1.0.0 sorozat kibocsátási megjegyzései
a 1.0.0-sorozat a [Microsoft. Azure. DocumentDB. Core](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.Core/)fájltól függ.

### <a name="107"></a><a name="1.0.7"/>1.0.7
* A teljesítmény javítása a Table SDK alapértelmezett nyomkövetési szintjének a SourceLevels. Off értékre állításával, amely az app. config fájlon keresztül engedélyezhető.

### <a name="105"></a><a name="1.0.5"/>1.0.5
* A TableClientConfiguration alatt új konfigurációt kell bevezetni a REST-végrehajtó használatára Cosmos DB Table API való kommunikációhoz

### <a name="105-preview"></a><a name="1.0.5-preview"/>1.0.5 – előzetes verzió
* Hibajavítások

### <a name="104"></a><a name="1.0.4"/>1.0.4
* Hibajavítások
* Adja meg a HttpClientTimeout beállítást a RestExecutorConfiguration számára.

### <a name="104-preview"></a><a name="1.0.4-preview"/>1.0.4 – előzetes verzió
* Hibajavítások
* Adja meg a HttpClientTimeout beállítást a RestExecutorConfiguration számára.

### <a name="101"></a><a name="1.0.1"/>1.0.1
* Hibajavítások

### <a name="100"></a><a name="1.0.0"/>1.0.0
* Általánosan elérhető kiadás

### <a name="0110-preview"></a><a name="0.11.0-preview"/>0.11.0 – előzetes verzió
* Módosítások történtek a CloudTableClient konfigurálásához. Most egy TableClientConfiguration objektumot vesz igénybe az építőiparban. A TableClientConfiguration különböző tulajdonságokat biztosít az ügyfél viselkedésének konfigurálásához attól függően, hogy a célként megadott végpont Cosmos DB-e Table API vagy az Azure Storage Table API.
* A TableQuery-támogatás hozzáadva az eredmények egy egyéni oszlopon rendezett sorrendbe való visszaküldéséhez. Ez a funkció csak Cosmos DB tábla-végpontokon támogatott.
* További támogatás a RequestCharges elérhetővé tétele különböző eredménycsoportok esetében. Ez a funkció csak Cosmos DB tábla-végpontokon támogatott.

### <a name="0101-preview"></a><a name="0.10.1-preview"/>0.10.1 – előzetes verzió
* A SAS-token, a TablePermissions, a ServiceProperties és a ServiceStats műveleteinek támogatása az Azure Storage Table-végpontokon. 
   > [!NOTE]
   > Az előző Azure Storage Table SDK-k bizonyos funkciói még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="0100-preview"></a><a name="0.10.0-preview"/>0.10.0 – előzetes verzió
* Támogatás hozzáadása az alapvető szifilisz-, batch-és lekérdezési műveletekhez az Azure Storage Table-végpontokon. 
   > [!NOTE]
   > Az előző Azure Storage Table SDK-k bizonyos funkciói még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="091-preview"></a><a name="0.9.1-preview"/>0.9.1 – előzetes verzió
* Azure Cosmos DB table .NET Standard SDK egy platformfüggetlen .NET-könyvtár, amely hatékony hozzáférést biztosít a Table adatmodellhez Cosmos DB. Ez a kezdeti kiadás a tábla és az entitások közötti szifilisz + lekérdezési funkciók teljes készletét támogatja hasonló API-kkal, mint a [.net-keretrendszerhez készült Cosmos db Table SDK](table-sdk-dotnet.md)-t. 
   > [!NOTE]
   >  Az Azure Storage Table-végpontok még nem támogatottak a 0.9.1 előzetes verziójában.

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Ez a platformfüggetlen .NET Standard könyvtár a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) a [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).NET-keretrendszer könyvtárát fogja cserélni.

### <a name="200-series"></a>2.0.0 sorozat
| Verzió | Kiadás dátuma | Nyugdíjazás dátuma |
| --- | --- | --- |
| [2.0.0 – előzetes verzió](#2.0.0-preview) |Auguest 22, 2019 |--- |

### <a name="100-series"></a>1.0.0-sorozat
| Verzió | Kiadás dátuma | Nyugdíjazás dátuma |
| --- | --- | --- |
| [1.0.5](#1.0.5) |Szeptember 13., 2019 |--- |
| [1.0.5 – előzetes verzió](#1.0.5-preview) |Auguest 20, 2019 |--- |
| [1.0.4](#1.0.4) |Auguest 12, 2019 |--- |
| [1.0.4 – előzetes verzió](#1.0.4-preview) |2019. július 26. |--- |
| 1.0.2 – előzetes verzió |2019. május 2. |--- |
| [1.0.1](#1.0.1) |Április 19., 2019 |--- |
| [1.0.0](#1.0.0) |Március 13., 2019 |--- |
| [0.11.0 – előzetes verzió](#0.11.0-preview) |2019. március 5. |--- |
| [0.10.1 – előzetes verzió](#0.10.1-preview) |2019. január 22. |--- |
| [0.10.0 – előzetes verzió](#0.10.0-preview) |December 18., 2018 |--- |
| [0.9.1 – előzetes verzió](#0.9.1-preview) |Október 18., 2018 |--- |


## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni a Azure Cosmos DB Table APIról, tekintse meg a [Azure Cosmos DB Table API bemutatása](table-introduction.md)című témakört.
