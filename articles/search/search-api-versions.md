---
title: A .NET SDK-t és a REST API-k – Azure Search API-verziók felügyeletét
description: Verzió szabályzat az Azure Search REST API-k és az ügyféloldali kódtára a .NET SDK-ban.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: brjohnst
ms.openlocfilehash: 21374e20a2ba2c9134ed12e4b1072ff5c320c75e
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65149783"
---
# <a name="api-versions-in-azure-search"></a>Az Azure Search API-verziók
Az Azure Search rendszeresen funkciófrissítések bevezetésekor. Előfordulhat, de nem mindig a frissítések megkövetelik az előző verziókkal való kompatibilitás megőrzése érdekében API új verziója. Közzététel az új verzió lehetővé teszi szabályozhatja, mikor és hogyan keresési szolgáltatásfrissítések integrálása a kódban.

Szabály az Azure Search-csapat új verzió csak akkor szükséges, közzéteszi, mivel szükség lehet az egyes annak érdekében, hogy a kód egy új API-verzió használatához frissítse. Új verzió csak akkor, ha bizonyos elemeit az API megváltozott úgy, hogy megszakítja a visszamenőleges kompatibilitás érdekében van szükség. Az ilyen változások javításai, a meglévő funkciók miatt, vagy módosítsa a meglévő API-felület funkciói miatt fordulhat elő.

Ugyanaz a szabály vonatkozik, az SDK-frissítések. Az Azure Search SDK követi a [Szemantikus verziószámozást](https://semver.org/) szabályt, ami azt jelenti, hogy a verzió három részből áll: nagyobb, kisebb és buildszámát (például az 1.1.0-s). Egy új fő SDK verziója csak a módosításokat, amelyek törés az előző verziókkal való kompatibilitás. A nem törhető funkciófrissítések növeli az alverzió, és hibajavítások csak növeli a build-verziószáma.

> [!NOTE]
> Az Azure Search-szolgáltatáspéldányhoz több REST API-verziók, többek között a legújabb buildszám támogatja. Továbbra is verzióját használja, amikor már nem a legújabb buildszám, de azt javasoljuk, hogy a legújabb verzió használatához kódok migrálása. A REST API használata esetén az api-version paraméter használatával minden kérésben meg kell adnia az API-verziót. A .NET SDK használatával, az SDK-t használ a verzióját határozza meg a megfelelő REST API-verzió. Ha egy régebbi SDK-t használja, továbbra is futtassa, hogy a kód módosítása nélkül, akkor is, ha a szolgáltatás frissítése egy újabb API-verzió támogatja.

## <a name="snapshot-of-current-versions"></a>Aktuális verziók pillanatképe
Alább van az aktuális verziók az összes pillanatképet alkalmazásprogramozási felület az Azure Search.


| Adapterek | Legutóbbi főverzió | status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Általánosan elérhető, nyilvánosan 2019. május |
| [.NET SDK előzetes verzió](https://aka.ms/search-sdk-preview) |8.0.0-preview |Április 2019 kiadott előzetes |
| [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Mindenki számára elérhető |
| [Szolgáltatási REST API-t a 2019-05-06-Preview](search-api-preview.md) |A 2019-05-06-előzetes verzió |Előzetes verzió |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Mindenki számára elérhető |
| [Kezelési REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Mindenki számára elérhető |

A REST API-k, többek között a `api-version` minden hívás szükség. Használatával `api-version` megkönnyíti az egy adott, például az API előzetes verziójához. Az alábbi példa bemutatja, hogyan a `api-version` paraméter meg van adva:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Bár az egyes kérések rendelkezik egy `api-version`, azt javasoljuk, hogy minden API-kérések ugyanazt a verziót használjon. Ez akkor különösen igaz olyankor, amikor új API-verziókban bevezetni, attribútumok vagy nem ismeri fel a korábbi verziók műveleteket. API-verziók keverése rendelkezhet nem kívánt következményekkel és el kell kerülni.
>
> A szolgáltatás REST API-t és a felügyeleti REST API-t is rendszerverzióval ellátott egymástól. A verziószámok bármilyen hasonlóság előfordulhatnak.

Általánosan elérhető (vagy a GA) API-k éles környezetben is használható, valamint az Azure szolgáltatásiszint-szerződések vonatkozik. Előzetes verziók esetében az általánosan elérhető verzióra nem mindig át kísérleti jellegű funkciókhoz. **Erősen ajánlott éles üzemi alkalmazások pedig az API-k előzetes verziójával elkerülése érdekében.**

## <a name="about-preview-and-generally-available-versions"></a>Előzetes és általánosan elérhető verziókról
Az Azure Search mindig előre felszabadítja a REST API-n keresztül kísérleti funkciók először majd keresztül a .NET SDK előzetes verzióit.

Előzetes verziójú funkciók érhetők el a teszteléshez és kísérletezés a cél az, hogy a funkció megtervezéséhez és implementációjához visszajelzéseket. Ezért az előzetes verziójú funkciók idővel valószínűleg az, hogy a visszamenőleges kompatibilitás felosztása módosíthatja. Ez a szakembereket egy GA verzióban funkciók, amelyek stabil és nem valószínű, hogy kisebb visszamenőlegesen kompatibilis javításokat és fejlesztések kivételével módosítása. Emellett előzetes verziójú funkciók nem mindig teszi azt megismerheti a GA kiadás be.

Ebből kifolyólag javasoljuk, hogy a függőséget vesz fel az előzetes verziókat éles kód írása ellen. Ha régebbi előzetes verzióját használja, azt javasoljuk az általánosan elérhető (GA) verzió-ba való migrálás.

A .NET SDK-ban: Kód áttelepítési útmutató található [frissítése a .NET SDK](search-dotnet-sdk-migration-version-9.md).

Általános rendelkezésre állás azt jelenti, hogy Azure Search mostantól a szolgáltatásiszint-szerződés (SLA) alapján. Az SLA található [Azure Search szolgáltatásiszint-szerződései](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
