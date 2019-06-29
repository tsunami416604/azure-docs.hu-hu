---
title: Cognitive Services-fiók létrehozása az Azure Portalon
titlesuffix: Azure Cognitive Services
description: Tudnivalók az Azure Cognitive Services API-k létrehozása az Azure Portalon.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: aahi
ms.openlocfilehash: b857ee0395c447c8699b8f6a812853528812a7bd
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67445859"
---
# <a name="create-a-cognitive-services-account-using-the-azure-portal"></a>Az Azure portal használatával a Cognitive Services-fiók létrehozása

Ebben a rövid útmutatóban megismerheti, hogyan iratkozzon fel az Azure Cognitive Services, és hozzon létre egy fiókot, amely rendelkezik – olyan egyetlen szolgáltatást, illetve több szolgáltatási előfizetés lesz. Ezek a szolgáltatások szerepelnek az Azure által [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), amely lehetővé teszi egy vagy több, az Azure Cognitive Services API-k csatlakozni.

## <a name="prerequisites"></a>Előfeltételek

* Egy érvényes Azure-előfizetés. [Hozzon létre egy fiókot](https://azure.microsoft.com/free/) ingyenes.

[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="create-a-new-azure-cognitive-services-resource"></a>Hozzon létre egy új Azure Cognitive Services-erőforrás

Erőforrás létrehozása előtt rendelkeznie kell egy Azure-erőforráscsoportot. Azure-erőforráscsoport összes Cognitive Services-fiók (és a kapcsolódó Azure-erőforrás) kell tartoznia. Amikor létrehoz egy fiókot, lehetősége van, vagy hozzon létre egy új erőforráscsoportot, vagy használjon egy meglévőt. Ez a cikk bemutatja, hogyan hozhat létre egy új erőforráscsoportot.

1. Jelentkezzen be a [az Azure portal](https://portal.azure.com), és kattintson a **+ erőforrás létrehozása**.

    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Az elérhető a Cognitive Services a következő módon találhatja meg:
    * A keresősáv használja, és adja meg a szolgáltatás kívánt előfizetés nevét.
        * Hozzon létre egy erőforrást, több szolgáltatásos-előfizetéssel, írja be a következőt **Cognitive Services** sávban, a Keresés a **Cognitive Services** erőforrás.

        ![Keresse meg a Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

    * Elérhető a cognitive services összes megtekintéséhez válasszon **mesterséges Intelligencia és Machine Learning**alatt **Azure Marketplace-en**. Ha érdekli a szolgáltatás nem jelenik meg, kattintson a **összes** görgessen a **Cognitive Services**. Kattintson a **további** megtekintéséhez a Cognitive Services API-k teljes katalógusát.
    
        ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Az a **létrehozás** lap, adja meg a következő információkat:

    > [!IMPORTANT]
    > Az Azure-beli hely, ne felejtse el, előfordulhat, hogy szükség szerinti kiosztását az Azure Cognitive Services hívása során.

    |    |    |
    |--|--|
    | **Name (Név)** | Egy leíró nevet a cognitive services-erőforrás. Azt javasoljuk egy leíró nevet, például *MyCognitiveServicesAccount*. |
    | **Előfizetés** | Válasszon ki egy rendelkezésre álló Azure-előfizetéseit. |
    | **Location** | A cognitive Services-példány helye. Különböző helyeken a késés bevezetni, de semmilyen hatást nem futásidejű rendelkezésre állását az erőforrást. |
    | **Tarifacsomag** | A Cognitive Services-fiók költsége a választott beállításokat, és a használat függ. További információkért tekintse meg az API-t [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Erőforráscsoport** | A [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/governance/resource-consistency/azure-resource-access#what-is-an-azure-resource-group) , amely tartalmazza a Cognitive Services-erőforrás. Hozzon létre egy új csoportot, vagy adja hozzá egy meglévő csoportot. |

    ![Erőforrás-létrehozás képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)


## <a name="get-the-keys-for-your-subscription"></a>Az előfizetés-kulcsok beolvasása

Miután létrehozta az erőforrást, hozzá tud férni az Azure irányítópultján Ha rögzítette azt. Ellenkező esetben megtalálja a **erőforráscsoportok**. Miután kijelölte az erőforrást, kérheti a kulcsok kiválasztásával **kulcsok** alatt **erőforrás-kezelés**.

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha szeretné törölni, és távolítsa el a Cognitive Services-előfizetést, törölheti az erőforrást vagy erőforráscsoportot. Az erőforráscsoport törlésekor a az erőforráscsoporthoz társított erőforrásokat is törli.

Erőforráscsoport eltávolítása az Azure Portallal:

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforráscsoportot, és kattintson a jobb gombbal a lista jobb oldalán a további gombra (…).
3. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="see-also"></a>Lásd még

* [Az Azure Cognitive Services-kérések hitelesítéséhez](authentication.md)
* [Mik az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tároló támogatása](cognitive-services-container-support.md)
