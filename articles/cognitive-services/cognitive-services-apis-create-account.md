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
ms.openlocfilehash: af01c0c2586ce7df1902a0bcc502c6fd06a5215d
ms.sourcegitcommit: 800f961318021ce920ecd423ff427e69cbe43a54
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2019
ms.locfileid: "68697911"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Cognitive Services-erőforrás létrehozása a Azure Portal használatával

Ezzel a rövid útmutatóval megkezdheti az Azure Cognitive Services használatát a Azure Portal használatával. A Cognitive Services az Azure-előfizetésében létrehozott Azure- [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal) jelölik. Az erőforrás létrehozása után az Ön által létrehozott kulcsokkal és végponttal hitelesítheti alkalmazásait. 

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/)

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Új Azure Cognitive Services-erőforrás létrehozása

Cognitive Services erőforrás létrehozása előtt rendelkeznie kell egy Azure-erőforrással, amely tartalmazza az erőforrást. Új erőforrás létrehozásakor létrehozhat egy új erőforráscsoportot, vagy használhat egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

1. Jelentkezzen be a [Azure Portalba](https://portal.azure.com), és kattintson az **+ erőforrás létrehozása**elemre.

    ![Cognitive Services API-k kiválasztása](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. A rendelkezésre álló Cognitive Services a következő módokon érheti el:
    * Használja a keresési sávot, és adja meg annak a szolgáltatásnak a nevét, amelyre elő kíván fizetni.
        * Több szolgáltatásból álló erőforrás létrehozásához írja be **Cognitive Services** a keresősáv mezőbe, és válassza ki a **Cognitive Services** erőforrást.

        ![Cognitive Services keresése](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Az összes rendelkezésre álló kognitív szolgáltatás megjelenítéséhez válassza az **AI + Machine learning**lehetőséget az **Azure Marketplace**-en. Ha nem látja a kívánt szolgáltatást, kattintson az **összes** megjelenítése elemre, és görgessen **Cognitive Services**. A Cognitive Services API-k teljes katalógusának megtekintéséhez kattintson a továbbiak gombra.
    
        ![Cognitive Services API-k kiválasztása](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. A **Létrehozás** lapon adja meg a következő információkat:

    > [!IMPORTANT]
    > Vegye figyelembe az Azure-beli helyet, mert szüksége lehet rá az Azure-Cognitive Services meghívásakor.

    |    |    |
    |--|--|
    | **Name** | A kognitív szolgáltatások erőforrásának leíró neve. Például *MyCognitiveServicesAccount*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Location** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API díjszabását ismertető [](https://azure.microsoft.com/pricing/details/cognitive-services/)témakörben talál.
    | **Erőforráscsoport** | Az [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beolvasása

Az erőforrás létrehozása után az Azure-irányítópultról férhet hozzá, ha rögzítette. Ellenkező esetben megkeresheti azt az **erőforráscsoportok**között. Az erőforrás kiválasztása után a kulcsokat az **Erőforrás-kezelés**területen a **kulcsok** lehetőség kiválasztásával érheti el.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="pricing-tiers-and-billing"></a>Árképzési szintek és számlázás

A díjszabási szintek (és a felszámított összeg) a hitelesítési adatok használatával elküldött tranzakciók számától függenek. Az egyes díjszabási szintek a következőket határozzák meg:
* a másodpercenként engedélyezett tranzakciók maximális száma (TPS).
* a szolgáltatási funkciók a díjszabási szinten engedélyezve vannak.
* Az előre meghatározott mennyiségű tranzakció díja. Ezen összeg fölött a szolgáltatás díjszabása szerint külön díjat számítunk fel [](https://azure.microsoft.com/pricing/details/cognitive-services/custom-vision-service/) .

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
