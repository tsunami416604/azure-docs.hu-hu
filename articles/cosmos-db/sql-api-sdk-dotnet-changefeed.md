---
title: 'Az Azure Cosmos DB: .NET módosítás hírcsatorna processzor API, SDK & erőforrások |} Microsoft Docs'
description: Tudnivalók a módosítás hírcsatorna processzor API és SDK, beleértve a kiadási dátum, használatból való kivonást dátumok és minden verziója a .NET módosítás hírcsatorna processzor SDK végzett módosítások.
services: cosmos-db
author: ealsur
manager: kfile
ms.service: cosmos-db
ms.component: cosmosdb-sql
ms.devlang: dotnet
ms.topic: reference
ms.date: 05/21/2018
ms.author: maquaran
ms.openlocfilehash: a2770b9349dac8caa8e0611d77522ab56ca1bf07
ms.sourcegitcommit: 6116082991b98c8ee7a3ab0927cf588c3972eeaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34798886"
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
> * [REST](https://docs.microsoft.com/rest/api/cosmos-db/)
> * [REST erőforrás-szolgáltató](https://docs.microsoft.com/rest/api/cosmos-db-resource-provider/)
> * [SQL](https://msdn.microsoft.com/library/azure/dn782250.aspx)
> * [BulkExecutor - .NET](sql-api-sdk-bulk-executor-dot-net.md)
> * [BulkExecutor - Java](sql-api-sdk-bulk-executor-java.md)

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://www.nuget.org/packages/Microsoft.Azure.DocumentDB.ChangeFeedProcessor/)|
|**API-JÁNAK dokumentációja**|[Adatcsatorna processzor könyvtár API-referenciadokumentáció módosítása](/dotnet/api/microsoft.azure.documents.changefeedprocessor?view=azure-dotnet)|
|**Első lépések**|[Ismerkedés a módosítás hírcsatorna processzor .NET SDK-val](change-feed.md)|
|**Aktuális támogatott keretrendszer**| [Microsoft .NET-keretrendszer 4.5](https://www.microsoft.com/download/details.aspx?id=30653)</br> [A Microsoft .NET Core](https://www.microsoft.com/net/download/core) |

## <a name="release-notes"></a>Kibocsátási megjegyzések

### <a name="stable-builds"></a>Stabil buildek

### <a name="a-name133133"></a><a name="1.3.3"/>1.3.3
* További naplózás hozzá.
* Rögzített DocumentClient-memóriavesztés többször a függőben lévő munkahelyi becslés hívásakor.

### <a name="a-name132132"></a><a name="1.3.2"/>1.3.2
* A függőben lévő munkahelyi becslése a javításokat.

### <a name="a-name131131"></a><a name="1.3.1"/>1.3.1
* Jobb stabilitás.
  * Javítsa ki a megszakított feladatok probléma, amely az egyes partíciók leállított megfigyelők vezethet kezelésére.
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

### <a name="pre-release-builds"></a>Előzetes buildjeit

### <a name="a-name202-prerelease202-prerelease"></a><a name="2.0.2-prerelease"/>2.0.2-prerelease
* Kisebb API változásai:
  * Elavultként megjelölt ChangeFeedProcessorOptions.IsAutoCheckpointEnabled eltávolítva.

### <a name="a-name201-prerelease201-prerelease"></a><a name="2.0.1-prerelease"/>2.0.1-prerelease
* Jobb stabilitás:
  * Jobban kezeli a címbérlet tanúsítványtár inicializálásához. Ha bérleti tároló üres, csak a processzor több példánya is inicializálnia, a többi várakozik.
  * További stabil hatékony Címbérlet megújítási-kiadás. Megújításához, és a címbérlet egy partíciót ki független a többi megújítása. A v1, amely végezhető el egymás után minden partíció esetében.
* Új v2 API:
  * A processzor rugalmas konstrukció jelentéskészítő mintát: a ChangeFeedProcessorBuilder osztály.
    * Paraméterek bármilyen kombinációját is igénybe vehet.
    * Figyelés és/vagy bérleti gyűjteményt (nem érhető el a v1) DocumentClient-példány is igénybe vehet.
  * IChangeFeedObserver.ProcessChangesAsync most CancellationToken vesz igénybe.
  * IRemainingWorkEstimator – a fennmaradó munkahelyi négyzetgyökének külön-külön is használható a processzor.
  * Új bővítési pontok:
    * IParitionLoadBalancingStrategy - egyéni terheléselosztás partíciók a processzor példányai között.
    * ILease, ILeaseManager – az egyéni partícióbérlés-kezelést.
    * IPartitionProcessor - partíció egyéni feldolgozási módosításait.
* Naplózás - használ [LibLog](https://github.com/damianh/LibLog) könyvtárban.
* 100 %-os visszamenőlegesen kompatibilis V1-es API-t.
* Új kódbázis.
* Kompatibilis [SQL .NET SDK](sql-api-sdk-dotnet.md) 1.21.1 verzió vagy újabb verzió.

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető. 

A Cosmos DB kivont SDK használatával fog kell elutasította a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [1.3.3](#1.3.3) |2018. Előfordulhat, hogy 08. |--- |
| [1.3.2](#1.3.2) |2018. április 18. |--- |
| [1.3.1](#1.3.1) |2018. március 13. |--- |
| [1.2.0](#1.2.0) |2017. október 31. |--- |
| [1.1.1](#1.1.1) |2017. augusztus 29. |--- |
| [1.1.0](#1.1.0) |2017. augusztus 13. |--- |
| [1.0.0](#1.0.0) |2017. július 07. |--- |


## <a name="faq"></a>GYIK
[!INCLUDE [cosmos-db-sdk-faq](../../includes/cosmos-db-sdk-faq.md)]

## <a name="see-also"></a>Lásd még
A Cosmos DB kapcsolatos további információkért lásd: [Microsoft Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/) szolgáltatás lapján. 

