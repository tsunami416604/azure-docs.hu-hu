---
title: API-verziók kezelése a .NET-hez és a REST-hoz
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search REST API-khoz és a .NET SDK-hoz készült ügyféloldali kódtár verziójának szabályzata.
manager: nitinme
author: brjohnstmsft
ms.author: brjohnst
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 11/04/2019
ms.openlocfilehash: 03dbb679c25ea692d2c52f80b9493889e367823d
ms.sourcegitcommit: 598c5a280a002036b1a76aa6712f79d30110b98d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/15/2019
ms.locfileid: "74112149"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-verziók az Azure Cognitive Search

Az Azure Cognitive Search rendszeresen kibővíti a szolgáltatás frissítéseit. Néha, de nem mindig, ezeknek a frissítéseknek az API új verziójára van szükségük a visszamenőleges kompatibilitás megőrzése érdekében. Az új verzió közzétételével szabályozhatja, hogy mikor és hogyan integrálja a keresési szolgáltatás frissítéseit a kódban.

Szabályként az Azure Cognitive Search csapata csak szükség esetén tesz közzé új verziókat, mert némi erőfeszítést okozhat a kód új API-verzió használatára való frissítéséhez. Az új verzióra csak akkor van szükség, ha az API valamilyen aspektusa úgy módosult, hogy a visszafelé való kompatibilitást megszakítja. Ezek a változások a meglévő szolgáltatások javításai, vagy a meglévő API-felületet módosító új szolgáltatások miatt fordulnak elő.

Ugyanez a szabály vonatkozik az SDK frissítéseire is. Az Azure Cognitive Search SDK a [szemantikai verziószámozási](https://semver.org/) szabályokat követi, ami azt jelenti, hogy a verziója három részből áll: a fő, a másodlagos és a kiépítési számokból (például 1.1.0). Az SDK új főverziójának kiadása csak a visszamenőleges kompatibilitást megszakító változások esetében engedélyezett. A nem feltörhető szolgáltatások frissítései megnövelik a másodlagos verziót, és a hibajavítások csak a Build verzióját fogják növelni.

> [!NOTE]
> Az Azure Cognitive Search Service-példány számos REST API verziót támogat, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

## <a name="snapshot-of-current-versions"></a>Aktuális verziók pillanatképe
Az alábbiakban az Azure Cognitive Search összes programozási felületének aktuális verzióit tartalmazó pillanatkép látható.


| Adapterek | Legújabb főverzió | status |
| --- | --- | --- |
| [.NET SDK](https://aka.ms/search-sdk) |9.0 |Általánosan elérhető, megjelent május 2019 |
| [.NET SDK – előzetes verzió](https://aka.ms/search-sdk-preview) |8,0 – előzetes verzió |Előzetes verzió, kiadás dátuma április 2019 |
| [Szolgáltatás REST API-ja](https://docs.microsoft.com/rest/api/searchservice/) |2019-05-06 |Mindenki számára elérhető |
| [Service REST API 2019-05-06 – előzetes verzió](search-api-preview.md) |2019-05-06 – előzetes verzió |Előzetes verzió |
| [.NET Management SDK](https://aka.ms/search-mgmt-sdk) |3.0 |Mindenki számára elérhető |
| [Kezelési REST API](https://docs.microsoft.com/rest/api/searchmanagement/) |2015-08-19 |Mindenki számára elérhető |

A REST API-k esetében, beleértve az egyes hívások `api-version` is. A `api-version` használatával egyszerűen megcélozhat egy adott verziót, például egy előzetes verziójú API-t. Az alábbi példa azt szemlélteti, hogyan van megadva a `api-version` paraméter:

    GET https://my-demo-app.search.windows.net/indexes/hotels?api-version=2019-05-06

> [!NOTE]
> Bár minden kérelemhez `api-version`tartozik, javasoljuk, hogy ugyanazt a verziót használja az összes API-kéréshez. Ez különösen akkor igaz, ha az új API-verziók olyan attribútumokat vagy műveleteket vezetnek be, amelyeket a korábbi verziók nem ismernek fel. Az API-verziók keverése nem szándékolt következményekkel járhat, ezért kerülendő.
>
> A szolgáltatás REST API és a felügyeleti REST API egymástól függetlenül vannak verzióban. A verziószámok közötti hasonlóság a véletlen egybeesés.

Az általánosan elérhető (vagy GA) API-k éles környezetben is használhatók, és az Azure-szolgáltatói szerződések hatálya alá tartoznak. Az előzetes verziók olyan kísérleti funkciókkal rendelkeznek, amelyek nem mindig a GA verzióra vannak áttelepítve. **Határozottan javasoljuk, hogy ne használja az előnézeti API-kat éles alkalmazásokban.**

## <a name="about-preview-and-generally-available-versions"></a>Az előzetes verzió és az általánosan elérhető verziók
Az Azure Cognitive Search mindig előzetesen kibocsátja a kísérleti szolgáltatásokat a REST API a .NET SDK előzetes verzióival.

Az előzetes verziójú funkciók tesztelésre és kísérletezésre használhatók, és a szolgáltatás kialakításával és megvalósításával kapcsolatos visszajelzések összegyűjtésének célja. Ezért az előzetes verziójú funkciók idővel változhatnak, valószínűleg olyan módon, hogy a visszafelé való kompatibilitást is megszakítja. Ez ellentétben áll a GA verziójának szolgáltatásaival, amelyek stabilak, és nem valószínű, hogy a kis visszamenőlegesen kompatibilis javítások és fejlesztések kivételével megváltoznak. Emellett az előzetes verziójú funkciók nem mindig teszik azt elérhetővé a GA kiadásban.

Ezen okok miatt javasoljuk, hogy az előnézeti verzióktól függő termelési kód írásához. Ha régebbi előzetes verziót használ, javasoljuk, hogy az általánosan elérhető (GA) verzióra váltson át.

A .NET SDK esetén: a kód áttelepítésére vonatkozó útmutató [a .net SDK frissítése](search-dotnet-sdk-migration-version-9.md)című témakörben található.

Az általános rendelkezésre állás azt jelenti, hogy az Azure Cognitive Search jelenleg a szolgáltatói szerződés (SLA) alatt van. Az SLA az [Azure Cognitive Search szolgáltatói szerződésekkel](https://azure.microsoft.com/support/legal/sla/search/v1_0/)érhető el.
