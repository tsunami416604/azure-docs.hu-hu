---
title: Az Azure Search API-verziók |} Microsoft Docs
description: Az Azure Search REST API-k és az ügyféloldali kódtára a .NET SDK házirendje.
author: brjohnstmsft
manager: jlembicz
services: search
ms.service: search
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 06/04/2018
ms.author: brjohnst
ms.openlocfilehash: 7bbc26402b30c7796ba11fef159d5cedefa5d58d
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2018
ms.locfileid: "34802469"
---
# <a name="api-versions-in-azure-search"></a>Az Azure Search API-verziók
Az Azure Search rendszeresen bevezeti szolgáltatás-frissítéseket. Néha, de nem minden esetben ezek a frissítések megkövetelése az előző verziókkal való kompatibilitás miatt API egy új verziója. Új verzió közzététele lehetővé teszi szabályozhatja, mikor és hogyan integrálja a keresési szolgáltatás frissítéseiről a kódban.

Szabály az Azure Search csapat új verzió csak akkor, ha szükséges, közzéteszi, azt is tartalmaz, amely néhány annak érdekében, hogy a kódot egy új API-verziót használja a frissítés óta. Új verzióra van szükség, csak akkor, ha néhány szempontja, hogy az API-t úgy, hogy megszakítja az előző verziókkal való kompatibilitás megváltozott. Ezek a változások akkor fordulhat elő, meglévő funkciók javításai, vagy módosítsa a meglévő API felületet biztosít új funkciókat.

Ugyanaz a szabály vonatkozik, az SDK-frissítések. Az Azure Search SDK követi a [szemantikai versioning](http://semver.org/) szabályok, ami azt jelenti, hogy ugyanolyan verziójú három részből áll: a fő alverziószám és buildszáma (például 1.1.0-ás). Törés az előző verziókkal való kompatibilitás módosítások csak az megjelenik egy új fő verziója az SDK-t. Szolgáltatás-frissítéseket nem törhető növeli a alverzió, és hibajavítások csak növeli a verzió buildszámával.

> [!NOTE]
> Az Azure Search szolgáltatáspéldány több REST API-verziók, többek között a legújabb egy támogatja. Továbbra is a verzióját használja, ha már nem a legújabb egyet, de azt javasoljuk, hogy az áttelepített a kódot a legújabb verzióját használja. A REST API használata esetén minden kérelemben keresztül az api-version paramétert meg kell adnia az API-verzió. A .NET SDK használatával, az SDK-t használ verziója határozza meg a megfelelő REST API-verzióra. Ha egy régebbi SDK használ, továbbra is futtassa ezt a kódot módosítások nélküli, még akkor is, ha a szolgáltatás frissítése újabb API-verzió támogatja.

## <a name="snapshot-of-current-versions"></a>Aktuális verzió pillanatképe
Alább van pillanatképet az összes aktuális verziója programozási felületek Azure search.

| Adapterek | Legutóbbi főverzió | status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |5.0 |Általában akkor érhető el, 2018. áprilisi kiadás dátuma |
| [.NET SDK minta](https://aka.ms/search-sdk-preview) |4.0.1-Preview |Megtekintés, előfordulhat, hogy 2017 kiadott |
| [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/) |2017-11-11 |Általánosan elérhető |
| [Szolgáltatás REST API 2017 11-11 – előzetes verzió](search-api-2017-11-11-preview.md) |2017. 11. 11. dátumú előzetes verzió |Előzetes verzió |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |2.0 |Általánosan elérhető |
| [Kezelési REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Általánosan elérhető |

A REST API-k esetében, beleértve a `api-version` minden egyes hívásakor szükség. Használatával `api-version` segítségével egyszerűen egy adott verziójához, például egy előnézeti API. A következő példa bemutatja, hogyan a `api-version` paraméter meg van adva:

    GET https://adventure-works.search.windows.net/indexes/bikes?api-version=2017-11-11

> [!NOTE]
> Bár egyes kérelem egy `api-version`, azt javasoljuk, hogy ugyanolyan verziójú API-kérelmek használjon. Ez akkor különösen igaz olyan esetben, ha új API-verziók bevezetni attribútumot, vagy nem ismeri fel a korábbi verziók műveletek. API-verziók keverése rendelkezhet nem várt következményekkel és el kell kerülni.
>
> A szolgáltatás REST API és a felügyeleti REST API-t is rendszerverzióval ellátott egymástól függetlenül. A verziószámokra bármilyen hasonlóság előfordulhatnak.

Általánosan elérhető (vagy GA) API-k éles környezetben használható, és az Azure szolgáltatásiszint-szerződések vonatkoznak. Az előzetes verziókban kísérleti funkciók, amelyek nem mindig áttelepítése az egy GA verziójával rendelkezik. **Erősen ajánlott kerülheti el a minta API-k éles alkalmazásokban.**

## <a name="about-preview-and-generally-available-versions"></a>Előzetes és általánosan elérhető verziójának kapcsolatos
Az Azure Search mindig előzetes kiadását a REST API-n keresztül kísérleti funkciók először, majd előzetes verziói a .NET SDK használatával.

Előzetes verziójú funkciók érhetők el a teszteléshez és a kísérletezés, azzal a céllal, a szolgáltatás tervezési és megvalósítási visszajelzéseket. Emiatt az előzetes verziójú funkciók változnak az idők, valószínűleg az, hogy az törés visszamenőleges kompatibilitás is. Ez a ellentétben stabil és valószínű, hogy módosítsa kivételével kis visszamenőlegesen kompatibilis javítások és továbbfejlesztett funkciók egy GA verziójával. Emellett előzetes verziójú funkciók nem mindig így GA kiadás be.

Ezen okok miatt javasoljuk írása, amely egy függőséget az előzetes verziókban az éles kódban ellen. Ha egy régebbi verziója előzetes verzió használata esetén ajánlott az áttelepítés általánosan elérhető (GA) verziójára.

A .NET SDK-ban: található kód áttelepítési útmutató a [frissítése a .NET SDK](search-dotnet-sdk-migration.md).

Általános rendelkezésre állási azt jelenti, hogy Azure Search most a szolgáltatásiszint-szerződéssel (SLA) alapján. A szolgáltatásiszint-szerződést található [Azure keresési szolgáltatói szerződések](https://azure.microsoft.com/support/legal/sla/search/v1_0/).
