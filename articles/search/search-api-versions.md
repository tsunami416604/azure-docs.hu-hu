---
title: API-verziók
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search REST API-khoz és a .NET SDK-hoz készült ügyféloldali kódtár verziójának szabályzata.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 06/30/2020
ms.openlocfilehash: a7179f88f507f0deedc79e7ae49988c8b5a32f86
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85830094"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-verziók az Azure Cognitive Search

Az Azure Cognitive Search rendszeresen kibővíti a szolgáltatás frissítéseit. Néha, de nem mindig, ezeknek a frissítéseknek az API új verziójára van szükségük a visszamenőleges kompatibilitás megőrzése érdekében. Az új verzió közzétételével szabályozhatja, hogy mikor és hogyan integrálja a keresési szolgáltatás frissítéseit a kódban.

Szabályként az Azure Cognitive Search csapata csak szükség esetén tesz közzé új verziókat, mert némi erőfeszítést okozhat a kód új API-verzió használatára való frissítéséhez. Az új verzióra csak akkor van szükség, ha az API valamilyen aspektusa úgy módosult, hogy a visszafelé való kompatibilitást megszakítja. Ezek a változások a meglévő szolgáltatások javításai, vagy a meglévő API-felületet módosító új szolgáltatások miatt fordulnak elő.

Ugyanez a szabály vonatkozik az SDK frissítéseire is. Az Azure Cognitive Search SDK a [szemantikai verziószámozási](https://semver.org/) szabályokat követi, ami azt jelenti, hogy a verziója három részből áll: a fő, a másodlagos és a kiépítési számokból (például 1.1.0). Az SDK új főverziójának kiadása csak a visszamenőleges kompatibilitást megszakító változások esetében engedélyezett. A nem feltörhető szolgáltatások frissítései megnövelik a másodlagos verziót, és a hibajavítások csak a Build verzióját fogják növelni.

> [!NOTE]
> Az Azure Cognitive Search Service-példányok számos REST API verziót támogatnak, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára telepítse át a kódot. A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

## <a name="rest-apis"></a>REST API-k

Ez a táblázat a Search Service REST API aktuális és korábban kiadott verzióinak korábbi verzióit tartalmazza. A dokumentáció az aktuális stabil és előzetes verzióra van közzétéve.

| Verziója&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | Állapot | Visszamenőleges kompatibilitási probléma |
|-------------|--------|------------------------------|
| [Management 2020-03-13](https://docs.microsoft.com/rest/api/searchmanagement/) | Általánosan elérhető | A felügyeleti REST API-k legújabb stabil kiadása az Endpoint Protection szolgáltatással. Privát végpontot, privát kapcsolati támogatást és hálózati szabályokat helyez el az új szolgáltatásokhoz. |
| [Management 2019-10-01-Preview](https://docs.microsoft.com/rest/api/searchmanagement/index-2019-10-01-preview) | Előnézet  | Verziószáma ellenére ez továbbra is a felügyeleti REST API-k aktuális előzetes verziója. Jelenleg nincsenek előzetes verziójú funkciók. Az előzetes verzió összes funkciója nemrég áttért az általános rendelkezésre állásra. |
| Felügyelet 2015-08-19  | Stable| A felügyeleti REST API-k első általánosan elérhető verziója. Biztosítja a szolgáltatás üzembe helyezését, a vertikális felskálázást és az API-kulcsok kezelését. |
| Felügyelet 2015-08-19 – előzetes verzió | Előnézet| A felügyeleti REST API-k első előzetes verziója. |
| [Search 2020-06-30](https://docs.microsoft.com/rest/api/searchservice/index)| Stable | A keresési keresési REST API-k legújabb stabil kiadása, a relevancia pontozásával. |
| [Search 2020-06-30-Preview](https://docs.microsoft.com/rest/api/searchservice/index-preview)| Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Search 2019-05-06 | Stable | Összetett típusok hozzáadására szolgál. |
| Search 2019-05-06 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2017-11-11 | Stable  | Szakértelmével és AI-gazdagítás hozzáadására szolgál. |
| Keresés 2017-11-11 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2016-09-01 |Stable | Indexelő hozzáadásával|
| Keresés 2016-09-01 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió.|
| Keresés 2015-02-28 | Stable  | Az első általánosan elérhető kiadás.  |
| Keresés 2015-02-28 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2014-10-20 – előzetes verzió | Előnézet | Második nyilvános előzetes verzió. |
| Keresés 2014-07-31 – előzetes verzió | Előnézet | Első nyilvános előzetes verzió. |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

A csomag korábbi verziói a NuGet.org címen érhetők el. Ez a táblázat az egyes csomagok oldalára mutató hivatkozásokat tartalmaz.

| SDK verziója | Állapot | Leírás |
|-------------|--------|------------------------------|
| [**Azure.Search.Documents 1.0.0 – előzetes verzió. 4**](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Előnézet | Az Azure .NET SDK-ból származó új ügyféloldali kódtár, amely a 2020-es kiadásban is elérhető. A REST 2020-06-30 API verzióját célozza meg|
| [**Microsoft. Azure. Search 10,0**](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Általánosan elérhető, május 2019. A REST 2019-05-06 API verzióját célozza meg.|
| [**Microsoft. Azure. Search 8,0 – előzetes verzió**](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Előzetes verzió, kiadás: április 2019. A REST 2019-05-06 – előzetes verzió API-verzióját célozza meg.|
| [**Microsoft. Azure. Management. Search 3.0.0**](https://docs.microsoft.com/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |

## <a name="azure-sdk-for-java"></a>Javához készült Azure SDK

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [**Java SearchManagementClient 1.35.0**](https://docs.microsoft.com/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg.|

## <a name="azure-sdk-for-python"></a>Azure SDK for Python

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [**Python Azure-mgmt-Search 1,0**](https://docs.microsoft.com/python/api/overview/azure/search?view=azure-python) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |