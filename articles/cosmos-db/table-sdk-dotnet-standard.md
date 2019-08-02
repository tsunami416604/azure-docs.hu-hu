---
title: Azure Cosmos DB Table API .NET Standard SDK &-erőforrások
description: Ismerkedjen meg a Azure Cosmos DB Table API és a .NET Standard SDK-val, beleértve a kiadási dátumokat, a nyugdíjazási dátumokat és az egyes verziók közötti módosításokat.
author: wmengmsft
ms.author: wmeng
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: dotnet
ms.topic: reference
ms.date: 03/18/2019
ms.openlocfilehash: b3ebd8b6f65ec86d338932579f5d4ccec6a90be3
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68704736"
---
# <a name="azure-cosmos-db-table-net-standard-api-download-and-release-notes"></a>Azure Cosmos DB table .NET Standard API: Megjegyzések letöltése és kibocsátási megjegyzései
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
|**Gyors útmutató**|[Gyors útmutató](create-table-dotnet.md)|
|**Oktatóanyag**|[Oktatóanyag](tutorial-develop-table-dotnet.md)|
|**Aktuális támogatott keretrendszer**|[Microsoft .NET Standard 2,0](https://www.nuget.org/packages/NETStandard.Library)|

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name104-preview104-preview"></a><a name="1.0.4-preview"/>1.0.4 – előzetes verzió
* Hibajavítások
* Adja meg a HttpClientTimeout beállítást a RestExecutorConfiguration számára.

### <a name="a-name101101"></a><a name="1.0.1"/>1.0.1
* Hibajavítások

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* Általánosan elérhető kiadások

### <a name="a-name0110-preview0110-preview"></a><a name="0.11.0-preview"/>0.11.0 – előzetes verzió
* Módosítások történtek a CloudTableClient konfigurálásához. Most egy TableClientConfiguration objektumot vesz igénybe az építőiparban. A TableClientConfiguration különböző tulajdonságokat biztosít az ügyfél viselkedésének konfigurálásához attól függően, hogy a célként megadott végpont Cosmos DB-e Table API vagy az Azure Storage Table API.
* A TableQuery-támogatás hozzáadva az eredmények egy egyéni oszlopon rendezett sorrendbe való visszaküldéséhez. Ez a funkció csak Cosmos DB tábla-végpontokon támogatott.
* További támogatás a RequestCharges elérhetővé tétele különböző eredménycsoportok esetében. Ez a funkció csak Cosmos DB tábla-végpontokon támogatott.

### <a name="a-name0101-preview0101-preview"></a><a name="0.10.1-preview"/>0.10.1 – előzetes verzió
* A SAS-token, a TablePermissions, a ServiceProperties és a ServiceStats műveleteinek támogatása az Azure Storage Table-végpontokon. 
   > [!NOTE]
   > Az előző Azure Storage Table SDK-k bizonyos funkciói még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="a-name0100-preview0100-preview"></a><a name="0.10.0-preview"/>0.10.0 – előzetes verzió
* Támogatás hozzáadása az alapvető szifilisz-, batch-és lekérdezési műveletekhez az Azure Storage Table-végpontokon. 
   > [!NOTE]
   > Az előző Azure Storage Table SDK-k bizonyos funkciói még nem támogatottak, például az ügyféloldali titkosítás.

### <a name="a-name091-preview091-preview"></a><a name="0.9.1-preview"/>0.9.1 – előzetes verzió
* Azure Cosmos DB table .NET Standard SDK egy platformfüggetlen .NET-könyvtár, amely hatékony hozzáférést biztosít a Table adatmodellhez Cosmos DB. Ez a kezdeti kiadás a tábla és az entitások közötti szifilisz + lekérdezési funkciók teljes készletét támogatja hasonló API-kkal, mint a .NET-keretrendszerhez készült [Cosmos db Table SDK](table-sdk-dotnet.md)-t. 
   > [!NOTE]
   >  Az Azure Storage Table-végpontok még nem támogatottak a 0.9.1 előzetes verziójában.

## <a name="release-and-retirement-dates"></a>Kiadási és nyugdíjazási dátumok
A Microsoft legalább **12 hónappal** korábban értesítést küld az SDK kivonásáról, hogy zökkenőmentes legyen az áttérés egy újabb/támogatott verzióra.

Ez a platformfüggetlen .NET Standard könyvtár a [Microsoft. Azure. Cosmos. table](https://www.nuget.org/packages/Microsoft.Azure.Cosmos.Table) a [Microsoft. Azure. CosmosDB. table](https://www.nuget.org/packages/Microsoft.Azure.CosmosDB.Table).NET-keretrendszer könyvtárát fogja cserélni.

| Version | Kiadás dátuma | Visszavonás dátuma |
| --- | --- | --- |
| [1.0.4 – előzetes verzió](#1.0.4-preview) |2019. július 26. |--- |
| 1.0.2 – előzetes verzió |2019. május 2. |--- |
| [1.0.1](#1.0.1) |Április 19., 2019 |--- |
| [1.0.0](#1.0.0) |Március 13., 2019 |--- |
| [0.11.0 – előzetes verzió](#0.11.0-preview) |Március 5., 2019 |--- |
| [0.10.1 – előzetes verzió](#0.10.1-preview) |2019. január 22. |--- |
| [0.10.0 – előzetes verzió](#0.10.0-preview) |December 18., 2018 |--- |
| [0.9.1 – előzetes verzió](#0.9.1-preview) |Október 18., 2018 |--- |


## <a name="faq"></a>GYIK

[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
Ha többet szeretne megtudni a Azure Cosmos DB Table APIról, tekintse meg a [Azure Cosmos DB Table API bemutatása](table-introduction.md)című témakört. 
