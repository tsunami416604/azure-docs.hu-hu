---
title: Cognitive Services erőforrás létrehozása a Azure Portal
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services megismeréséhez hozzon létre és iratkozzon fel a Azure Portal egyik erőforrására.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 07/16/2019
ms.author: aahi
ms.openlocfilehash: c7db2b4d49e3b9297c32d2e11ffe7c7702c17544
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274669"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Cognitive Services-erőforrás létrehozása a Azure Portal használatával

Ezzel a rövid útmutatóval létrehozhat egy Azure Cognitive Services-erőforrást a Azure Portal használatával. Miután sikeresen létrehozott egy Cognitive Services erőforrást, egy végpontot és egy kulcsot fog kapni, amely az alkalmazások hitelesítéséhez használható.

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Új Azure Cognitive Services-erőforrás létrehozása

Cognitive Services erőforrás létrehozása előtt rendelkeznie kell egy Azure-erőforrással, amely tartalmazza az erőforrást. Új erőforrás létrehozásakor létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az **+ erőforrás létrehozása**elemre.

    ![Cognitive Services API-k kiválasztása](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. A korábban leírtaknak megfelelően létrehozhat egy Cognitive Services-erőforrást két módon – több szolgáltatást használó erőforrás vagy egyetlen szolgáltatásból álló erőforrás használatával.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

    Több szolgáltatásból álló erőforrás létrehozásához írja be a **Cognitive Services** kifejezést a keresősávba.

    ![Cognitive Services keresése](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    A Cognitive Services lapon válassza a **Létrehozás**lehetőséget.

    ![Cognitive Services fiók létrehozása](media/cognitive-services-apis-create-account/azurecogservsearchmulti-2.png)

    #### <a name="single-service-resourcetabsingleservice"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

    Az összes rendelkezésre álló kognitív szolgáltatás megjelenítéséhez válassza az **AI + Machine learning**lehetőséget az **Azure Marketplace**-en. Ha nem látja a kívánt szolgáltatást, kattintson az **összes** megjelenítése elemre, és görgessen **Cognitive Services**. Kattintson a **továbbiak** gombra a Cognitive Services teljes katalógusának megtekintéséhez.

    Ha a szolgáltatás érdekli, kattintson a **Létrehozás**gombra.
    
    ![Cognitive Services API-k kiválasztása](media/cognitive-services-apis-create-account/azureMarketplace.png)

    ***
3. A **Létrehozás** lapon adja meg a következő információkat:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *MyCognitiveServicesResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Location** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. Vegye figyelembe az Azure-beli helyet, mert szüksége lehet rá az Azure-Cognitive Services meghívásakor. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
    | **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kattintson a **Create** (Létrehozás) gombra.

    #### <a name="single-service-resourcetabsingleservice"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *TextAnalyticsResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Location** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. Vegye figyelembe az Azure-beli helyet, mert szüksége lehet rá az Azure-Cognitive Services meghívásakor. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
    | **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kattintson a **Create** (Létrehozás) gombra.

    ***

## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beolvasása

Az erőforrás sikeres létrehozása után megjelenik egy előugró értesítés a képernyő jobb felső részén. Az értesítésben kattintson az **erőforrás keresése** lehetőségre a létrehozott kognitív szolgáltatási erőforrás megtekintéséhez. 

![Ugrás a kognitív szolgáltatás erőforrásaira](media/cognitive-services-apis-create-account/cog-serv-go-to-resource.png)

A megnyíló Gyorsindítás ablaktáblán elérheti a végpontot és a kulcsot.

![Kulcs és végpont beolvasása](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Árképzési szintek és számlázás

A díjszabási szintek (és a felszámított összeg) a hitelesítési adatok használatával elküldött tranzakciók számától függenek. Az egyes díjszabási szintek a következőket határozzák meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* a szolgáltatási funkciók a díjszabási szinten engedélyezve vannak.
* Az előre meghatározott számú tranzakció díja. Ezen összeg fölött a szolgáltatás [díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) szerint külön díjat számítunk fel.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán található további gombra (...).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="see-also"></a>Lásd még

* [Kérelmek hitelesítése az Azure Cognitive Services](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tárolók támogatása](cognitive-services-container-support.md)
