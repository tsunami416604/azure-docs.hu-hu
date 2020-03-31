---
title: API-verziókezelés a .NET és a REST rendszerhez
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search REST API-k és a .NET SDK ügyfélkönyvtárának verzióházirendje.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 178f56354120bf7a65c51f1c9cf54e34bd011d97
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79137288"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-verziók az Azure Cognitive Search szolgáltatásban

Az Azure Cognitive Search rendszeresen bevezeti a funkciófrissítéseket. Néha, de nem mindig, ezek a frissítések az API új verziójára van szükség a visszamenőleges kompatibilitás megőrzése érdekében. Az új verzió közzétételével szabályozhatja, hogy mikor és hogyan integrálja a keresési szolgáltatás frissítéseit a kódba.

Az Azure Cognitive Search csapata általában csak szükség esetén teszi közzé az új verziókat, mivel némi erőfeszítést igényel a kód frissítése egy új API-verzió használatára. Új verzióra csak akkor van szükség, ha az API bizonyos aspektusa ily módon a visszamenőleges kompatibilitást szünetelteti. Ilyen módosítások a meglévő funkciók javításai miatt, vagy a meglévő API-felületet megváltoztató új funkciók miatt következhetnek be.

Ugyanez a szabály vonatkozik az SDK-frissítésekre is. Az Azure Cognitive Search SDK követi a [szemantikai verziószámozási](https://semver.org/) szabályokat, ami azt jelenti, hogy a verzió három részből áll: fő, kisebb és build száma (például 1.1.0). Az SDK új főverziója csak a visszamenőleges kompatibilitást megszakító módosításokra szolgál. A nem törik funkciófrissítések növelik az alverziót, és a hibajavítások csak növelik a buildverziót.

> [!NOTE]
> Az Azure Cognitive Search szolgáltatáspéldány a REST API számos verzióját támogatja, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de azt javasoljuk, hogy telepítse át a kódot a legújabb verzió használatára. A REST API használatakor meg kell adnia az API-verziót minden kérelemben az api-version paraméteren keresztül. A .NET SDK használatakor a használt SDK-verzió határozza meg a REST API megfelelő verzióját. Ha egy régebbi SDK-t használ, továbbra is futtathatja a kódot módosítások nélkül, még akkor is, ha a szolgáltatás egy újabb API-verzió támogatásához lett frissítve.

## <a name="snapshot-of-current-versions"></a>Az aktuális verziók pillanatképe
Az alábbiakban az Azure Cognitive Search összes programozási felületének aktuális verzióinak pillanatképe látható.


| Interfészek | Legújabb főverzió | status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Általánosan elérhető, 2019 májusa |
| [.NET SDK előzetes verzió](https://aka.ms/search-sdk-preview) |8.0-előzetes |Preview, megjelent április 2019 |
| [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Általánosan elérhető |
| [Szolgáltatás REST API 2019-05-06-Preview](search-api-preview.md) |2019-05-06-Előzetes |Előzetes verzió |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Általánosan elérhető |
| [Kezelési REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2020-03-13|Általánosan elérhető |

A REST API-k, `api-version` beleértve az egyes hívás szükséges. A `api-version` használata megkönnyíti egy adott verzió, például egy előzetes API célzását. A következő példa bemutatja, hogyan van megadva a `api-version` paraméter:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Bár minden kérelemrendelkezik egy, `api-version`azt javasoljuk, hogy ugyanazt a verziót használja az összes API-kérelemhez. Ez különösen akkor igaz, ha az új API-verziók olyan attribútumokat vagy műveleteket vezetnek be, amelyeket a korábbi verziók nem ismernek fel. Az API-verziók keverése nem kívánt következményekkel járhat, ezért el kell kerülni.
>
> A Service REST API és a Management REST API egymástól függetlenül verziószámozott. A verziószámok bármilyen hasonlósága véletlen.

Az általánosan elérhető (vagy ga) API-k éles környezetben használhatók, és az Azure szolgáltatásiszint-szerződéshatálya alá tartoznak. Az előzetes verziók kísérleti funkciókkal rendelkeznek, amelyek nem mindig kerülnek át a GA-verzióba. **Javasoljuk, hogy ne használja az előzetes API-kat éles alkalmazásokban.**

## <a name="update-to-the-latest-version-of-the-rest-api-by-october-15-2020"></a>Frissítés a REST API legújabb verziójára 2020.
Az Azure Cognitive Search REST API következő verziói **2014-07-31-Preview**2020. **2014-10-20-Preview** **2015-02-28-Preview** **2015-02-28** Emellett az Azure Cognitive Search .NET SDK **3.0.0-rc-nél** régebbi verziói is megszűnnek, mivel ezek a REST API-verziók egyikét célozzák meg. Ezen időpont után az elavult REST API- vagy SDK-verziókat használó alkalmazások nem fognak működni, és frissíteni kell őket. Mint minden ilyen típusú változásnál, 12 hónapos felmondási időt adunk, így önnek is van ideje az alkalmazkodásra.  Az Azure Cognitive Search további használatának folytatásához telepítse át a [REST API-t](search-api-migration.md) a [REST API 2019-05-06-os](https://docs.microsoft.com/rest/api/searchservice/) vagy újabb verziójára, vagy a .NET SDK-t a [3.0-s](search-dotnet-sdk-migration.md) vagy újabb verzióra 2020 október 15-ig.  Ha bármilyen kérdése van a legújabb verzióra való azuresearch_contact@microsoft.com frissítéssel kapcsolatban, kérjük, küldjön e-mailt 2020.

## <a name="about-preview-and-generally-available-versions"></a>Az előzetes verzió és az általánosan elérhető verziók –
Az Azure Cognitive Search mindig előzetes kiadást ad ki a REST API-n keresztül először, majd a .NET SDK előzetes verzióin keresztül.

Az előzetes verziójú funkciók tesztelésre és kísérletezésre is rendelkezésre állnak, azzal a céllal, hogy visszajelzéseket gyűjtsenek a funkciótervezésről és -megvalósításról. Ezért az előnézeti funkciók idővel változhatnak, esetleg olyan módon, amely megtöri a visszamenőleges kompatibilitást. Ez ellentétben áll a GA verzió funkcióival, amelyek stabilak és nem valószínű, hogy megváltoznak, kivéve a kis visszafelé kompatibilis javításokat és fejlesztéseket. Emellett az előnézeti funkciók nem mindig teszik ga-kiadássá.

Ezen okok miatt azt javasoljuk, hogy ne írjon olyan éles kódot, amely az előzetes verzióktól függ. Ha régebbi előzetes verziót használ, javasoljuk, hogy az általánosan elérhető (GA) verzióra való áttérést.

A .NET SDK: A kódáttelepítéshez vezető útmutató [a .NET SDK frissítése című](search-dotnet-sdk-migration-version-9.md)dokumentumban található.

Az általános elérhetőség azt jelenti, hogy az Azure Cognitive Search most már a szolgáltatásiszint-szerződés (SLA) alatt áll. Az SLA az [Azure Cognitive Search szolgáltatásiszint-szerződésekkel](https://azure.microsoft.com/support/legal/sla/search/v1_0/)érhető el.
