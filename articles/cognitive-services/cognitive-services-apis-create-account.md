---
title: Cognitive Services erőforrás létrehozása a Azure Portal
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services megismeréséhez hozzon létre és iratkozzon fel a Azure Portal egyik erőforrására.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: 14157ed22ea40a2157f696e91c59af9b58110bf2
ms.sourcegitcommit: 8e271271cd8c1434b4254862ef96f52a5a9567fb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2019
ms.locfileid: "72819938"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Cognitive Services-erőforrás létrehozása a Azure Portal használatával

Ez a rövid útmutató az Azure Cognitive Services használatának megkezdésére használható. Miután létrehozta a kognitív szolgáltatás erőforrását a Azure Portalban, egy végpontot és egy kulcsot fog kapni az alkalmazások hitelesítéséhez.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [hozzon létre egyet ingyen](https://azure.microsoft.com/free/).

## <a name="create-a-new-azure-cognitive-services-resource"></a>Új Azure Cognitive Services-erőforrás létrehozása

1. Hozzon létre egy erőforrást.

    #### <a name="multi-service-resourcetabmultiservice"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)
    
    A Multi-Service erőforrás neve **Cognitive Services** a portálon. [Cognitive Services erőforrás létrehozása](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne).
    
    A több szolgáltatásból álló erőforrás jelenleg a következő Cognitive Serviceshoz biztosít hozzáférést:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Computer Vision  | Content Moderator                                    | Face               | Hangfelismerés (LUIS) | Text Analytics   |
    | Translator Text  | Bing Search v7 <br>(Web, kép, hírek, videó, vizualizáció) | Bing Custom Search | Bing Entity Search            | Bing Autosuggest |
    | Bing Spell Check |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resourcetabsingleservice"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

    Az alábbi hivatkozásokat követve hozhat létre erőforrást az elérhető Cognitive Serviceshoz:

    | Látás                      | Beszéd                  | Nyelv                          | Döntés             | Search                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Számítógépes látástechnológia](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Magával ragadó olvasó](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomália detektor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Search API v7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Egyéni jövőkép szolgáltatás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Speaker Recognition](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Content Moderator](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing Custom Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Arc](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Személyre szabás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing Entity Search](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Kézírás-felismerő](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Szövegelemzés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing helyesírás-ellenőrzés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Translator Text](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing Autosuggest](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. A **Létrehozás** lapon adja meg a következő információkat:

    #### <a name="multi-service-resourcetabmultiservice"></a>[Több szolgáltatásból álló erőforrás](#tab/multiservice)

    |    |    |
    |--|--|
    | **Name (Név)** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *MyCognitiveServicesResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. Vegye figyelembe az Azure-beli helyet, mert szüksége lehet rá az Azure-Cognitive Services meghívásakor. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
    | **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kattintson a  **Create** (Létrehozás) gombra.

    #### <a name="single-service-resourcetabsingleservice"></a>[Egy szolgáltatásból származó erőforrás](#tab/singleservice)

    |    |    |
    |--|--|
    | **Name (Név)** | A kognitív szolgáltatások erőforrásának leíró neve. Például: *TextAnalyticsResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetést. |
    | **Hely** | A kognitív szolgáltatás példányának helye. A különböző helyek késést okozhatnak, de nincs hatással az erőforrás futásidejű rendelkezésre állására. Vegye figyelembe az Azure-beli helyet, mert szüksége lehet rá az Azure-Cognitive Services meghívásakor. |
    | **Tarifacsomag** | A Cognitive Services fiókjának díja a választott beállításoktól és a használattól függ. További információt az API [díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/)ismertető témakörben talál.
    | **Erőforráscsoport** | Az Azure-erőforráscsoport, amely a Cognitive Services erőforrást fogja tartalmazni. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz is. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kattintson a  **Create** (Létrehozás) gombra.

    ***

## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beolvasása

1. Az erőforrás sikeres üzembe helyezése után kattintson az **Ugrás az erőforráshoz** lehetőségre a **következő lépések**alatt.

    ![Cognitive Services keresése](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. A megnyíló Gyorsindítás ablaktáblán elérheti a kulcsot és a végpontot.

    ![Kulcs és végpont beolvasása](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. Keresse meg a törölni kívánt erőforrást tartalmazó erőforráscsoportot.
3. Kattintson a jobb gombbal az erőforráscsoport listájára. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="see-also"></a>Lásd még:

* [Kérelmek hitelesítése az Azure Cognitive Services](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tárolók támogatása](cognitive-services-container-support.md)
