---
title: API-verziók
titleSuffix: Azure Cognitive Search
description: Az Azure Cognitive Search REST API-khoz és a .NET SDK-hoz készült ügyféloldali kódtár verziójának szabályzata.
manager: nitinme
author: HeidiSteen
ms.author: heidist
ms.service: cognitive-search
ms.topic: conceptual
ms.date: 07/20/2020
ms.openlocfilehash: bf3e4262f4342788f343ab287fd3db53d12736c7
ms.sourcegitcommit: 62e1884457b64fd798da8ada59dbf623ef27fe97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/26/2020
ms.locfileid: "88918111"
---
# <a name="api-versions-in-azure-cognitive-search"></a>API-verziók az Azure Cognitive Search

Az Azure Cognitive Search rendszeresen kibővíti a szolgáltatás frissítéseit. Néha, de nem mindig, ezeknek a frissítéseknek az API új verziójára van szükségük a visszamenőleges kompatibilitás megőrzése érdekében. Az új verzió közzétételével szabályozhatja, hogy mikor és hogyan integrálja a keresési szolgáltatás frissítéseit a kódban.

Szabályként az Azure Cognitive Search csapata csak szükség esetén tesz közzé új verziókat, mert némi erőfeszítést okozhat a kód új API-verzió használatára való frissítéséhez. Az új verzióra csak akkor van szükség, ha az API valamilyen aspektusa úgy módosult, hogy a visszafelé való kompatibilitást megszakítja. Ezek a változások a meglévő szolgáltatások javításai, vagy a meglévő API-felületet módosító új szolgáltatások miatt fordulnak elő.

Ugyanez a szabály vonatkozik az SDK frissítéseire is. Az Azure Cognitive Search SDK a [szemantikai verziószámozási](https://semver.org/) szabályokat követi, ami azt jelenti, hogy a verziója három részből áll: a fő, a másodlagos és a kiépítési számokból (például 1.1.0). Az SDK új főverziójának kiadása csak a visszamenőleges kompatibilitást megszakító változások esetében engedélyezett. A nem feltörhető szolgáltatások frissítései megnövelik a másodlagos verziót, és a hibajavítások csak a Build verzióját fogják növelni.

> [!Important]
> A .NET, a Java, a Python és a JavaScript rendszerhez készült Azure SDK-k új ügyféloldali kódtárakat vezetnek be az Azure Cognitive Searchhoz. Jelenleg az Azure SDK-kódtárak egyike sem támogatja teljes mértékben a legújabb keresési REST API-kat (2020-06-30) vagy a felügyeleti REST API-kat (2020-03-13), de ez idővel változni fog. Rendszeresen megtekintheti ezt az oldalt vagy a funkciók [újdonságait](whats-new.md) a funkcionális fejlesztésekben. 

## <a name="rest-apis"></a>REST API-k

Az Azure Cognitive Search Service-példányok számos REST API verziót támogatnak, beleértve a legújabbat is. Továbbra is használhatja a verziót, ha már nem a legújabb, de javasoljuk, hogy a legújabb verzió használatára [telepítse át a kódot](search-api-migration.md) . A REST API használatakor az API-verziót minden kérelemben meg kell adnia az API-Version paraméter használatával. A .NET SDK használatakor a használt SDK verziója meghatározza a REST API megfelelő verzióját. Ha régebbi SDK-t használ, továbbra is futtathatja ezt a kódot, még akkor sem, ha a szolgáltatás frissítve van egy újabb API-verzió támogatására.

A következő táblázat a Search Service REST API aktuális és korábban kiadott verzióinak korábbi verzióit tartalmazza. A dokumentációt csak a legfrissebb stabil és előzetes verziókra teszi közzé.

### <a name="search-service-apis"></a>Search Service API-k

Tartalom létrehozása és kezelése keresési szolgáltatásban.

| Verziója&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | status | Visszamenőleges kompatibilitási probléma |
|-------------------------|--------|------------------------------|
| [Search 2020-06-30](/rest/api/searchservice/index)| Stable | A Search REST API-k legújabb stabil kiadása, a relevancia pontozásával és általánosan elérhető a Knowledge Store szolgáltatással.|
| [Search 2020-06-30-Preview](/rest/api/searchservice/index-preview)| Előnézet | A stabil verzióhoz társított előzetes verzió. Több [előzetes verziójú funkciót](search-api-preview.md)is tartalmaz. |
| Search 2019-05-06 | Stable | [Összetett típusok](search-howto-complex-data-types.md)hozzáadására szolgál. |
| Search 2019-05-06 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2017-11-11 | Stable  | Szakértelmével és [AI-gazdagítás](cognitive-search-concept-intro.md)hozzáadására szolgál. |
| Keresés 2017-11-11 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2016-09-01 |Stable | [Indexelő](search-indexer-overview.md)hozzáadására szolgál. |
| Keresés 2016-09-01 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió.|
| Keresés 2015-02-28 | Stable  | Az első általánosan elérhető kiadás.  |
| Keresés 2015-02-28 – előzetes verzió | Előnézet | A stabil verzióhoz társított előzetes verzió. |
| Keresés 2014-10-20 – előzetes verzió | Előnézet | Második nyilvános előzetes verzió. |
| Keresés 2014-07-31 – előzetes verzió | Előnézet | Első nyilvános előzetes verzió. |

### <a name="management-rest-apis"></a>Felügyeleti REST API-k

Keresési szolgáltatás létrehozása és konfigurálása, valamint az API-kulcsok kezelése.

| Verziója&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;   | status | Visszamenőleges kompatibilitási probléma |
|-------------------------|--------|------------------------------|
| [Management 2020-03-13](/rest/api/searchmanagement/) | Stable | A felügyeleti REST API-k legújabb stabil kiadása az Endpoint Protection szolgáltatással. Privát [végpontot](service-create-private-endpoint.md) biztosít a privát kapcsolaton keresztül, valamint az új szolgáltatásokra vonatkozó [hálózati IP-szabályokat](service-configure-firewall.md) . |
| [Management 2019-10-01-Preview](/rest/api/searchmanagement/index-2019-10-01-preview) | Előnézet  | Verziószáma ellenére ez továbbra is a felügyeleti REST API-k aktuális előzetes verziója. Jelenleg nincsenek előzetes verziójú funkciók. Az előzetes verzió összes funkciója nemrég áttért az általános rendelkezésre állásra. |
| Felügyelet 2015-08-19  | Stable | A felügyeleti REST API-k első általánosan elérhető verziója. Biztosítja a szolgáltatás üzembe helyezését, a vertikális felskálázást és az API-kulcsok kezelését. |
| Felügyelet 2015-08-19 – előzetes verzió | Előnézet | A felügyeleti REST API-k első előzetes verziója. |

## <a name="azure-sdk-for-net"></a>Azure SDK for .NET

A csomag korábbi verziói a NuGet.org címen érhetők el. Ez a táblázat az egyes csomagok oldalára mutató hivatkozásokat tartalmaz.


| SDK verziója | Állapot | Leírás |
|-------------|--------|------------------------------|
| [Azure.Search.Documents 11,0](https://www.nuget.org/packages/Azure.Search.Documents/1.0.0-preview.4) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2020-06-30 REST API célozza meg, de még nem támogatja, Geo-szűrők vagy [FieldBuilder](/dotnet/api/microsoft.azure.search.fieldbuilder?view=azure-dotnet). |
| [Microsoft. Azure. Search 10,0](https://www.nuget.org/packages/Microsoft.Azure.Search/) | Stable | Kiadás dátuma: 2019. A keresési REST API-Version = 2019-05-06 célokat célozza meg.|
| [Microsoft. Azure. Search 8,0 – előzetes verzió](https://www.nuget.org/packages/Microsoft.Azure.Search/8.0.0-preview) | Előnézet | megjelent április 2019. A keresési REST API-Version = 2019-05 -06-Preview célokat célozza meg.|
| [Microsoft. Azure. Management. Search 3.0.0](/dotnet/api/overview/azure/search/management?view=azure-dotnet) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg.  |

## <a name="azure-sdk-for-java"></a>Javához készült Azure SDK

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [Java Azure – keresés – 11. dokumentum](https://azuresdkdocs.blob.core.windows.net/$web/java/azure-search-documents/11.0.0/index.html) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2019-05-06 célokat célozza meg. |
| [Java Management-ügyfél 1.35.0](/java/api/overview/azure/search/management?view=azure-java-stable) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |

## <a name="azure-sdk-for-javascript"></a>Azure SDK for JavaScript

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [JavaScript Azure – keresés 11,0](https://azure.github.io/azure-sdk-for-node/azure-search/latest/) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2016-09-01-et célozza meg. |
| [JavaScript Azure-ARM-Search](https://azure.github.io/azure-sdk-for-node/azure-arm-search/latest/) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |

## <a name="azure-sdk-for-python"></a>Azure SDK for Python

| SDK verziója | Állapot | Leírás  |
|-------------|--------|------------------------------|
| [Python Azure – keresés – dokumentumok 11,0](https://azuresdkdocs.blob.core.windows.net/$web/python/azure-search-documents/11.0.0/index.html) | Stable | Új ügyféloldali kódtár az Azure .NET SDK-ból, 2020. július. A keresési REST API-Version = 2019-05-06 célokat célozza meg. |
| [Python Azure-mgmt-Search 1,0](/python/api/overview/azure/search?view=azure-python) | Stable | A felügyeleti REST API-Version = 2015-08-19-et célozza meg. |