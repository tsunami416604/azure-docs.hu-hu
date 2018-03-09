---
title: "Az Azure Cosmos DB: .NET módosítás hírcsatorna processzor API, SDK & erőforrások |} Microsoft Docs"
description: "Tudnivalók a módosítás hírcsatorna processzor API és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és minden verziója a .NET módosítás hírcsatorna processzor SDK végzett módosítások."
services: cosmos-db
documentationcenter: .net
author: ealsur
manager: kirillg
editor: mimig1
ms.assetid: f2dd9438-8879-4f74-bb6c-e1efc2cd0157
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/07/2018
ms.author: maquaran
ms.openlocfilehash: 7e53cf67bc6e03a5f45de5ad4e23ff91624874f4
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="net-change-feed-processor-sdk-download-and-release-notes"></a>.NET módosítás adatcsatorna processzor SDK: Töltse le és a kibocsátási megjegyzések
> [!div class="op_single_selector"]
> * [.NET](sql-api-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](sql-api-sdk-dotnet-changefeed.md)
> * [.NET Core](sql-api-sdk-dotnet-core.md)
> * [Node.js](sql-api-sdk-node.md)
> * [Aszinkron Java](sql-api-sdk-async-java.md)
> * [Java](sql-api-sdk-java.md)
> * [Python](sql-api-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

[!INCLUDE [cosmos-db-sql-api](../../includes/cosmos-db-sql-api.md)]

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-JÁNAK dokumentációja**|[Adatcsatorna processzor könyvtár API-referenciadokumentáció módosítása](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Első lépések**|[Ismerkedés a módosítás hírcsatorna processzor .NET SDK-val](change-feed.md)|
|**Aktuális támogatott keretrendszer**| [Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [A Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name130130"></a><a name="1.3.0"/>1.3.0
* Jobb stabilitás.
* Manuális ellenőrzőpontok támogatása.
* Kompatibilis [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21 verzió vagy újabb verzió.

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Támogatást nyújt a .NET-szabvány 2.0. A csomag most már támogatja a `netstandard2.0` és `net451` keretrendszer monikerek.
* Kompatibilis [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.17.0 verzió vagy újabb verzió.
* Kompatibilis [SQL .NET Core SDK](sql-api-sdk-dotnet-core.md) 1.5.1 verzió vagy újabb verzió.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Javítja a számítás a becsült hátralévő munka a változás-hírcsatorna üres volt, vagy nem volt függőben.
* Kompatibilis [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 verzió vagy újabb verzió.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* A módosítás hírcsatorna feldolgozandó fennmaradó munka becsléséhez metódus hozzá.
* Kompatibilis [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.13.2 verzió vagy újabb verzió.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilis [SQL .NET SDK](sql-api-sdk-dotnet.md) verziók 1.14.1 vagy régebbi verzió.

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető. 

A Cosmos DB kivont SDK használatával fog kell elutasította a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.3.0](#1.3.0) |2018. március 07. |--- |
| [1.2.0](#1.2.0) |2017. október 31. |--- |
| [1.1.1](#1.1.1) |2017. augusztus 29. |--- |
| [1.1.0](#1.1.0) |2017. augusztus 13. |--- |
| [1.0.0](#1.0.0) |2017. július 07. |--- |


## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

