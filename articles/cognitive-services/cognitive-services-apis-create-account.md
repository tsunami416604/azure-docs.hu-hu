---
title: Cognitive Services-fiók létrehozása az Azure Portalon
titlesuffix: Azure Cognitive Services
description: Hogyan lehet egy Microsoft Cognitive Services API-k létrehozása az Azure Portalon.
services: cognitive-services
author: garyericson
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 02/01/2018
ms.author: garye
ms.openlocfilehash: c69e8b7360774971316b5a060aff9e7057d3e9aa
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55873972"
---
# <a name="quickstart-create-a-cognitive-services-account-in-the-azure-portal"></a>Gyors útmutató: Cognitive Services-fiók létrehozása az Azure Portalon

Ebben a rövid útmutatóban fog elsajátíthatja az Azure Cognitive Services regisztráljon, és hozzon létre egy egyetlen-szolgáltatást, vagy több szolgáltatásos előfizetést. Ezek a szolgáltatások szerepelnek az Azure által [erőforrások](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-portal), amely lehetővé teszi egy vagy több, az Azure Cognitive Services API-k csatlakozni.

## <a name="prerequisites"></a>Előfeltételek

* Egy érvényes Azure-előfizetés. [Hozzon létre egy fiókot](https://azure.microsoft.com/free/) ingyenes.

## <a name="create-and-subscribe-to-an-azure-cognitive-services-resource"></a>Hozzon létre, és az Azure Cognitive Services-erőforrás előfizetése

Mielőtt hozzáfogna, fontos tudni, hogy nincsenek-e kétféle Azure Cognitive Services-előfizetéseket. Az első az egyetlen szolgáltatás, például a Computer Vision, vagy a Speech Services-előfizetés. Előfizetés – olyan egyetlen szolgáltatást ennek az erőforrásnak korlátozódik. A második pedig az Azure Cognitive Services több szolgáltatásos előfizetést. Ez az előfizetés lehetővé teszi, hogy egyetlen előfizetéshez az Azure Cognitive Services többsége. Ez a beállítás is összesíti a számlázás. Lásd: [Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/) további információt.

>[!WARNING]
> Jelenleg ezek a szolgáltatások **nem** támogatja több szolgáltatásos kulcsokat: A QnA Maker, beszédszolgáltatások és egyéni vizuális.

A következő szakaszok végigvezetik egy egyetlen vagy több szolgáltatásos előfizetést.

### <a name="single-service-subscription"></a>Előfizetés – olyan egyetlen szolgáltatást

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), és kattintson a **+ erőforrás létrehozása**.

    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azurePortalScreen.png)

2. Azure Marketplace-en, válassza a **mesterséges Intelligencia és Machine Learning**. Ha érdekli a szolgáltatás nem jelenik meg, kattintson a **összes** megtekintéséhez a Cognitive Services API-k teljes katalógusát.

    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azureMarketplace.png)

3. Az a **létrehozás** lap, adja meg a következő információkat:

    |    |    |
    |--|--|
    | **Name (Név)** | Egy leíró nevet a cognitive services-erőforrás. Azt javasoljuk egy leíró nevet, például *MyNameFaceAPIAccount*. |
    | **Előfizetés** | Válasszon ki egy rendelkezésre álló Azure-előfizetéseit. |
    | **Hely** | A cognitive Services-példány helye. Különböző helyeken a késés bevezetni, de semmilyen hatást nem futásidejű rendelkezésre állását az erőforrást. |
    | **Tarifacsomag** | A Cognitive Services-fiók költsége a választott beállításokat, és a használat függ. További információkért tekintse meg az API-t [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Erőforráscsoport** | A [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) , amely tartalmazza a Cognitive Services-erőforrás. Hozzon létre egy új csoportot, vagy adja hozzá egy meglévő csoportot. |

    ![Erőforrás-létrehozás képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)

### <a name="multi-service-subscription"></a>Több szolgáltatásos előfizetés

1. Jelentkezzen be a [az Azure portal](http://portal.azure.com), és kattintson a **+ erőforrás létrehozása**.

    ![Válassza ki a Cognitive Services API-k](media/cognitive-services-apis-create-account/azurePortalScreenMulti.png)

2. Keresse meg a keresősávba, és adja meg: **A cognitive Services**.

    ![Keresse meg a Cognitive Services](media/cognitive-services-apis-create-account/azureCogServSearchMulti.png)

3. Válassza ki **a Cognitive Services**.

    ![Válassza ki a Cognitive Services](media/cognitive-services-apis-create-account/azureMarketplaceMulti.png)

3. Az a **létrehozás** lap, adja meg a következő információkat:

    |    |    |
    |--|--|
    | **Name (Név)** | Egy leíró nevet a cognitive services-erőforrás. Azt javasoljuk egy leíró nevet, például *MyCognitiveServicesAccount*. |
    | **Előfizetés** | Válasszon ki egy rendelkezésre álló Azure-előfizetéseit. |
    | **Hely** | A cognitive Services-példány helye. Különböző helyeken a késés bevezetni, de semmilyen hatást nem futásidejű rendelkezésre állását az erőforrást. |
    | **Tarifacsomag** | A Cognitive Services-fiók költsége a választott beállításokat, és a használat függ. További információkért tekintse meg az API-t [díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/).
    | **Erőforráscsoport** | A [Azure-erőforráscsoport](https://docs.microsoft.com/azure/architecture/cloud-adoption/getting-started/azure-resource-access#what-is-an-azure-resource-group) , amely tartalmazza a Cognitive Services-erőforrás. Hozzon létre egy új csoportot, vagy adja hozzá egy meglévő csoportot. |

    ![Erőforrás-létrehozás képernyő](media/cognitive-services-apis-create-account/resource_create_screen_multi.png)

## <a name="access-your-resource"></a>Az erőforrás eléréséhez

> [!NOTE]
> Az előfizetés-tulajdonosokat letilthatja a Cognitive Services-fiókok az erőforráscsoportok és előfizetések létrehozása alkalmazásával [az Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), a "nem engedélyezett erőforrástípusok" szabályzatdefiníció hozzárendelése és megadása**Microsoft.CognitiveServices/accounts** a cél erőforrás típusaként.

Miután létrehozta az erőforrást, hozzá tud férni az Azure irányítópultján Ha rögzítette azt. Ellenkező esetben megtalálja a **erőforráscsoportok**.

A végponti URL-cím és a kulcsot is használhat a Cognitive Services-erőforrás belül a **áttekintése** szakasz API készítésének megkezdéséhez hívja az alkalmazásokban.

![Erőforrások képernyő](media/cognitive-services-apis-create-account/resourceScreen.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Az Azure Cognitive Services-kérések hitelesítéséhez](authentication.md)

## <a name="see-also"></a>Lásd még

* [Rövid útmutató: Kézzel írt szöveg kinyerése egy rendszerképből](https://docs.microsoft.com/azure/cognitive-services/computer-vision/quickstarts/csharp-hand-text)
* [Oktatóanyag: Hozzon létre egy alkalmazást, és alkalmas keretet biztosítanak az arcok a képen](https://docs.microsoft.com/azure/cognitive-services/Face/Tutorials/FaceAPIinCSharpTutorial)
* [Egy egyéni keresési weblap létrehozása](https://docs.microsoft.com/azure/cognitive-services/bing-custom-search/tutorials/custom-search-web-page)
* [Language Understanding (LUIS) integrálása a robot a Bot keretrendszer használatával](https://docs.microsoft.com/azure/cognitive-services/luis/luis-nodejs-tutorial-build-bot-framework-sample)
