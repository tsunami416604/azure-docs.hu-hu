---
title: Az Azure Search API-verziók |} Microsoft Docs
description: Az Azure Search REST API-k és az ügyféloldali kódtára a .NET SDK házirendje.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 04/20/2018
ms.author: brjohnst
ms.openlocfilehash: 7754242aa79a2ba7931a6d80a7a12a0858c6f260
ms.sourcegitcommit: e221d1a2e0fb245610a6dd886e7e74c362f06467
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/07/2018
---
# <a name="api-versions-in-azure-search"></a>Az Azure Search API-verziók
Az Azure Search rendszeresen bevezeti szolgáltatás-frissítéseket. Néha, de nem minden esetben ezeket a frissítéseket, hogy az előző verziókkal való kompatibilitás miatt API felületen új verziójának közzétételéhez szükséges. Új verzió közzététele lehetővé teszi szabályozhatja, mikor és hogyan integrálja a keresési szolgáltatás frissítéseiről a kódban.

Szabály azt próbálja közzététele új verzió csak akkor, ha szükséges, mivel azt magába foglaló néhány annak érdekében, hogy frissítse a kódot egy új API-verziót használja. Új verzió csak azt tesznek közzé, ha néhány szempontja, hogy az API-t úgy, hogy megszakítja a visszamenőleges kompatibilitás érdekében módosítani kell. Ez akkor fordulhat elő, meglévő funkciók javításai, vagy módosítsa a meglévő API felületet biztosít új funkciókat.

Az SDK frissítések ugyanaz a szabály azt kövesse. Az Azure Search SDK követi a [szemantikai versioning](http://semver.org/) szabályok, ami azt jelenti, hogy ugyanolyan verziójú három részből áll: a fő alverziószám és buildszáma (például 1.1.0-ás). Törés az előző verziókkal való kompatibilitás változások esetén csak az SDK új főverzió azt ad ki. Nem törhető szolgáltatás-frissítéseket azt növeli a alverzió és hibajavítások a azt csak növeli a verzió buildszámával.

> [!NOTE]
> Az Azure Search szolgáltatáspéldány több REST API-verziók, többek között a legújabb egy támogatja. Továbbra is a verzióját használja, ha már nem a legújabb egyet, de azt javasoljuk, hogy az áttelepített a kódot a legújabb verzióját használja. A REST API használata esetén minden kérelemben keresztül az api-version paramétert meg kell adnia az API-verzió. A .NET SDK használatával, az SDK-t használ verziója határozza meg a megfelelő REST API-verzióra. Ha egy régebbi SDK használ, továbbra is futtassa ezt a kódot módosítások nélküli, még akkor is, ha a szolgáltatás frissítése újabb API-verzió támogatja.

## <a name="snapshot-of-current-versions"></a>Aktuális verzió pillanatképe
Alább van pillanatképet az összes aktuális verziója programozási felületek Azure search.

| Felületek | Legutóbbi főverzió | status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Általában akkor érhető el, 2018. áprilisi kiadás dátuma |
| [.NET SDK minta](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Megtekintés, előfordulhat, hogy 2017 kiadott |
| [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/) |2016-09-01 |Általánosan elérhető |
| [Szolgáltatás REST API 2016 09-01. dátumú előnézeti](search-api-2016-09-01-preview.md) |2016 09-01. dátumú előnézeti |Előzetes verzió |
| [Szolgáltatás REST API 2017 11-11 – előzetes verzió](search-api-2017-11-11-preview.md) |2017-11-11 – előzetes |Előzetes verzió |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Általánosan elérhető |
| [Kezelési REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Általánosan elérhető |

A REST API-k esetében, beleértve a `api-version` minden egyes hívásakor szükség. Ez megkönnyíti az egy adott verziójához, például egy előnézeti API. A következő példa bemutatja, hogyan a `api-version` paraméter meg van adva:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Bár egyes kérelem egy `api-version`, azt javasoljuk, hogy ugyanolyan verziójú API-kérelmek használjon. Ez akkor különösen igaz olyan esetben, ha új API-verziók bevezetni attribútumot, vagy nem ismeri fel a korábbi verziók műveletek. API-verziók keverése rendelkezhet nem várt következményekkel és el kell kerülni.
>
> A szolgáltatás REST API és a felügyeleti REST API-t is rendszerverzióval ellátott egymástól függetlenül. A verziószámokra bármilyen hasonlóság előfordulhatnak.

Általánosan elérhető (vagy GA) API-k éles környezetben használható, és az Azure szolgáltatásiszint-szerződések vonatkoznak. Az előzetes verziókban kísérleti funkciók, amelyek nem mindig áttelepítése az egy GA verziójával rendelkezik. **Kifejezetten ajánlott termelési alkalmazások API-k megtekintés ellen.**

## <a name="about-preview-and-generally-available-versions"></a>Előzetes és általánosan elérhető verziójának kapcsolatos
Az Azure Search mindig előzetes kiadását a REST API-n keresztül kísérleti funkciók először, majd előzetes verziói a .NET SDK használatával.

Előzetes verziójú funkciók nem garantált, hogy az áttelepítendő GA kiadási. GA verziójával szolgáltatások minősülnek stabil és kis visszamenőlegesen kompatibilis javítások és továbbfejlesztett kivételével módosítása nem valószínű, mivel a teszteléshez és a kísérletezés, azzal a céllal, a szolgáltatás tervezési és megvalósítási visszajelzéseket előzetes verziójú funkciók érhetők el.

Azonban mivel előzetes verziójú funkciók változhatnak, ajánlott írása, amely egy függőséget az előzetes verziókban az éles kódban ellen. Ha egy régebbi előzetes verzióját használja, ajánlott az áttelepítés általánosan elérhető (GA) verziójára.

A .NET SDK-ban: található kód áttelepítési útmutató a [frissítése a .NET SDK](search-dotnet-sdk-migration.md).

Általános rendelkezésre állási azt jelenti, hogy Azure Search most a szolgáltatásiszint-szerződéssel (SLA) alapján. A szolgáltatásiszint-szerződést található [Azure keresési szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
