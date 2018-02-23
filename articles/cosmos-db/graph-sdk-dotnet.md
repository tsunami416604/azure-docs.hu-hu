---
title: "Az Azure CosmosDB Graph API a .NET SDK & erőforrások |} Microsoft Docs"
description: "Tudnivalók az Azure CosmosDB Graph API, beleértve a kiadási dátum, a használatból való kivonást dátumot és az egyes verziók között végrehajtott módosításokat."
services: cosmos-db
documentationcenter: .net
author: luisbosquez
manager: jhubbard
ms.assetid: 
ms.service: cosmos-db
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/17/2017
ms.author: lbosq
ms.openlocfilehash: 6bae97783b63b76d2de2dbcd5e618f75bc4d701d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="azure-cosmos-db-graph-net-api-download-and-release-notes"></a>Az Azure Cosmos DB Graph API-val .NET: Töltse le és kibocsátási megjegyzések

|   |   |
|---|---|
|**SDK letöltése**|[NuGet](https://aka.ms/acdbgraphnuget)|
|**API-JÁNAK dokumentációja**|[.NET API-referenciadokumentáció](https://aka.ms/acdbgraphapiref)|
|**Gyors útmutató**|[Az Azure Cosmos DB: .NET és a Graph API segítségével graph-alkalmazás létrehozása](create-graph-dotnet.md)|
|**Oktatóanyag**|[Az Azure CosmosDB: Létrehozni egy tárolót a Graph API-hoz](tutorial-develop-graph-dotnet.md)|
|**Aktuális támogatott keretrendszer**| [A Microsoft .NET-keretrendszer 4.6.1.](https://www.microsoft.com/en-us/download/details.aspx?id=49981)</br> [A Microsoft .NET Core](https://www.microsoft.com/net/download/core) |


## <a name="release-notes"></a>Kibocsátási megjegyzések

A .NET-hez, Gremlin.Net a nyílt forráskódú Gremlin illesztőprogram használatát javasoljuk. Első lépések útmutató [Azure Cosmos DB: hozzon létre egy grafikonon alkalmazást .NET és a Graph API segítségével](create-graph-dotnet.md).

### <a name="a-name031-preview031-preview"></a><a name="0.3.1-preview"/>0.3.1-preview

#### <a name="bug-fixes"></a>Hibajavítások
* Javítsa ki a nem kötelezően betöltendő `appsettings.json` (`netstandard1.6`)

#### <a name="whats-new"></a>Újdonságok
* Váltás Microsoft.Azure.Graphs célplatform AnyCPU.
* Távolítsa el a monó szerelvényt `net461` csomag jegyzékben.

### <a name="a-name030-preview030-preview"></a><a name="0.3.0-preview"/>0.3.0-preview

#### <a name="whats-new"></a>Újdonságok
* Támogatása `.netstandard 1.6`
  * Igényel `Microsoft.Azure.DocumentDB.Core >= 1.5.1`
* Egy új hozzáadott `gremlin-groovy` lecseréli a meglévő elemző elemző. Ebbe az elemzőbe támogatja Tinkerpop meg egy részét `gremlin-groovy` szintaxist, és tartalmazza:
  * Javítja a teljesítményt elemzési 2 x.
  * Megoldott problémák számos kapcsolódó karakterláncok, nem megfelelően kezelt szöveges értéknek és a régi elemző más szabálytalanságok escape karakter.
* A hozzáadott optimalizálás traversals a biztonsági predikátumok esetében.
  *  A szűrők könyvtármegkerülési ugrások kell megjelennie ennek a fejlesztésnek köszönhetően, például: `g.V('1').outE().has('name', 'marko').inV()`.
* A traversals az optimalizálás hozzáadott `limit()` lépés.

#### <a name="breaking-changes"></a>Módosítások megszakítása
* .NET-keretrendszer 4.5.1 eltávolított támogatása

* Az új elemző igazodik `gremlin-groovy` nyelvtan. Ennek eredményeképpen egyes kifejezések, de az előző nem egyértelműek az új elemző. Megjegyzés: az egyetlen esetet:
  * `in` és `as` vannak a foglalt kulcsszavak `gremlin-groovy`, így az alábbi lépéseket kell minősíteni `.in()` vagy `.as()` szintaktikai hibák elkerülése érdekében. Például: `g.V().repeat(in()).times(2)`  ->  _szintaktikai hibát jelez._  
 `g.V().repeat(__.in()).times(2)` -> _sikeres_

### <a name="a-name024-preview024-preview"></a><a name="0.2.4-preview"/>0.2.4-preview

### <a name="a-name022-preview022-preview"></a><a name="0.2.2-preview"/>0.2.2-Preview

### <a name="a-name021-preview021-preview"></a><a name="0.2.1-preview"/>0.2.1-preview

### <a name="a-name020-preview020-preview"></a><a name="0.2.0-preview"/>0.2.0-preview

### <a name="a-name010-preview010-preview"></a><a name="0.1.0-preview"/>0.1.0-preview
* Kezdeti előzetes verziójára.

## <a name="release--retirement-dates"></a>Kiadás & használatból való kivonást dátumok
Microsoft legalább értesítést küldenek **12 hónapon keresztül** SDK eltávolítása érdekében vagy újabb támogatott verzióra való áttérés előtt.

Új szolgáltatásait és funkcióit és optimalizálás csak hozzá az aktuális SDK, így javasoljuk, hogy mindig a legújabb SDK verzióra frissít legkorábban lehető. 

Bármely Azure Cosmos DB kivont SDK használatával kérelmet a program elutasítja a szolgáltatás.

<br/>

| Verzió | Kiadás dátuma | Kivezetési dátum |
| --- | --- | --- |
| [0.3.1-preview](#0.3.1-preview) |2017. október 17. |--- |
| [0.3.0-preview](#0.3.0-preview) |2017. október 2. |--- |
| [0.2.4-preview](#0.2.4-preview) |2017. augusztus 4. |--- |
| [0.2.2-preview](#0.2.2-preview) |2017. június 23. |--- |
| [0.2.1-preview](#0.2.2-preview) |2017. június 8. |--- |
| [0.2.0-preview](#0.2.2-preview) |2017. május 10. |--- |
| [0.1.0-preview](#0.1.0-preview) |2017. május 8. |--- |

## <a name="see-also"></a>Lásd még
Az Azure Cosmos DB Graph API-val kapcsolatos további információkért lásd: [Azure Cosmos DB bemutatása: Graph API](graph-introduction.md). 
