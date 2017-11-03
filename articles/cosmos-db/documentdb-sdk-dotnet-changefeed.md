---
title: "Az Azure DocumentDB .NET SDK adatcsatorna processzor & erőforrások módosítása |} Microsoft Docs"
description: "Tudnivalók a módosítás hírcsatorna processzor API és SDK kiadási dátum, használatból való kivonást dátumok és a DocumentDB .NET módosítás adatcsatorna-processzor SDK verziói között végrehajtott módosításokat."
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
ms.date: 10/31/2017
ms.author: maquaran
ms.openlocfilehash: 8c5a081105d37b8f1c78e90d9ccb8d945099b621
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="documentdb-net-change-feed-processor-sdk-download-and-release-notes"></a>A DocumentDB .NET módosítás adatcsatorna processzor SDK: Töltse le és a kibocsátási megjegyzések
> [!div class="op_single_selector"]
> * [.NET](documentdb-sdk-dotnet.md)
> * [.NET-módosítás adatcsatorna](documentdb-sdk-dotnet-changefeed.md)
> * [.NET Core](documentdb-sdk-dotnet-core.md)
> * [Node.js](documentdb-sdk-node.md)
> * [Java](documentdb-sdk-java.md)
> * [Python](documentdb-sdk-python.md)
> * [REST](https://docs.microsoft.com/rest/api/documentdb/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/documentdbresourceprovider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-JÁNAK dokumentációja**|[Adatcsatorna processzor könyvtár API-referenciadokumentáció módosítása](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Első lépések**|[A DocumentDB módosítás hírcsatorna processzor .NET SDK használatába](change-feed.md)|
|**Aktuális támogatott keretrendszer**| [Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [A Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="a-name120120"></a><a name="1.2.0"/>1.2.0
* Támogatást nyújt a .NET-szabvány 2.0. A csomag most már támogatja a `netstandard2.0` és `net451` keretrendszer monikerek.
* Kompatibilis [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.17.0 verzió vagy újabb verzió.
* Kompatibilis [DocumentDB .NET Core SDK](documentdb-sdk-dotnet-core.md) 1.5.1 verzió vagy újabb verzió.

### <a name="a-name111111"></a><a name="1.1.1"/>1.1.1
* Javítja a számítás a becsült hátralévő munka a változás-hírcsatorna üres volt, vagy nem volt függőben.
* Kompatibilis [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 verzió vagy újabb verzió.

### <a name="a-name110110"></a><a name="1.1.0"/>1.1.0
* A módosítás hírcsatorna feldolgozandó fennmaradó munka becsléséhez metódus hozzá.
* Kompatibilis [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) 1.13.2 verzió vagy újabb verzió.

### <a name="a-name100100"></a><a name="1.0.0"/>1.0.0
* GA SDK
* Kompatibilis [DocumentDB .NET SDK](documentdb-sdk-dotnet.md) verziók 1.14.1 vagy régebbi verzió.

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető. 

A Cosmos DB kivont SDK használatával fog kell elutasította a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.2.0](#1.2.0) |2017. október 31-ig. |--- |
| [1.1.1](#1.1.1) |2017. augusztus 29. |--- |
| [1.1.0](#1.1.0) |2017. augusztus 13. |--- |
| [1.0.0](#1.0.0) |2017. július 07. |--- |


## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még:
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

