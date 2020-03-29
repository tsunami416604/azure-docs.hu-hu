---
title: Cognitive Services-erőforrás létrehozása az Azure Portalon
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services első lépései az Azure Portalon létrehozott és egy erőforrásra való feliratkozással.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: conceptual
ms.date: 10/23/2019
ms.author: aahi
ms.openlocfilehash: dd4444bf42bcc8dda95f8fa37b42a365538efa85
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79219477"
---
# <a name="create-a-cognitive-services-resource-using-the-azure-portal"></a>Cognitive Services-erőforrás létrehozása az Azure Portal használatával

Ezzel a rövid útmutatóval elkezdheti használni az Azure Cognitive Servicest. Miután létrehozott egy Cognitive Service-erőforrást az Azure Portalon, egy végpontot és egy kulcsot kap az alkalmazások hitelesítéséhez.


[!INCLUDE [cognitive-services-subscription-types](../../includes/cognitive-services-subscription-types.md)]

## <a name="prerequisites"></a>Előfeltételek

* Érvényes Azure-előfizetés – [Hozzon létre egyet ingyen.](https://azure.microsoft.com/free/)

## <a name="create-a-new-azure-cognitive-services-resource"></a>Új Azure Cognitive Services-erőforrás létrehozása

1. Erőforrásokat hozhat létre.

    #### <a name="multi-service-resource"></a>[Többszolgáltatásos erőforrás](#tab/multiservice)
    
    A többszolgáltatásos erőforrás neve **Cognitive Services** a portálon. [Hozzon létre egy Cognitive Services-erőforrást.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAllInOne)
    
    Jelenleg a többszolgáltatásos erőforrás hozzáférést biztosít a következő Cognitive Services szolgáltatásokhoz:
    
    |                  |                                                      |                    |                               |                  |
    |------------------|------------------------------------------------------|--------------------|-------------------------------|------------------|
    | Számítógépes látástechnológia  | Tartalommoderátor                                    | Arcfelismerés               | Language Understanding (LUIS) | Szövegelemzés   |
    | Fordítói szöveg  | Bing Keresés v7 <br>(Web, Kép, Hírek, Videó, Vizuális) | Bing – Egyéni keresés | Bing – Entitáskeresés            | Bing – Automatikus kiegészítés |
    | Bing – Helyesírás-ellenőrzés |                                                      |                    |                               |                  |
    
    #### <a name="single-service-resource"></a>[Egyszolgáltatásos erőforrás](#tab/singleservice)

    Az alábbi hivatkozások segítségével hozzon létre egy erőforrást a rendelkezésre álló Cognitive Services számára:

    | Vizuális elemek                      | Beszéd                  | Nyelv                          | Döntés             | Keresés                 |
    |-----------------------------|-------------------------|-----------------------------------|----------------------|------------------------|
    | [Számítógépes látás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesComputerVision)         | [Speech Services](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices)     | [Magával ragadó olvasó](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesImmersiveReader)              | [Anomaly Detector](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesAnomalyDetector) | [Bing Keresési API V7](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSearch-v7) |
    | [Egyéni látási szolgáltatás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesCustomVision) | [Beszélőfelismerés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeakerRecognition) | [Language Understanding (LUIS)](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesLUISAllInOne) | [Tartalommoderátor](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesContentModerator) | [Bing – Egyéni keresés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingCustomSearch) |
    | [Arcfelismerés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFace)                    |                         | [QnA Maker](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker)                     | [Personalizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesPersonalizer)     | [Bing entitás keresése](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingEntitySearch) |
    | [Ink Recognizer](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesInkRecognizer)        |                         | [Szövegelemzés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextAnalytics)                |                      | [Bing helyesírás-ellenőrzés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingSpellCheck-v7)   |
    |           |                         | [Fordító szövege](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesTextTranslation)               |                      | [Bing – Automatikus kiegészítés](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesBingAutosuggest-v7)                       |
    ***

3. A **Létrehozás** lapon adja meg a következő információkat:

    #### <a name="multi-service-resource"></a>[Többszolgáltatásos erőforrás](#tab/multiservice)

    |    |    |
    |--|--|
    | **Név** | A kognitív szolgáltatások erőforrás leíró neve. Például *A MyCognitiveServicesResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetését. |
    | **Helyen** | A kognitív szolgáltatáspéldány helye. A különböző helyek késést okozhatnak, de nincsenek hatással az erőforrás futásidejű rendelkezésre állására. |
    | **Tarifacsomag** | A Cognitive Services-fiók költsége a választott beállításoktól és a használattól függ. További információt az API [díjszabásának részleteicímű témakörben talál.](https://azure.microsoft.com/pricing/details/cognitive-services/)
    | **Erőforráscsoport** | Az Azure erőforráscsoport, amely tartalmazza a Cognitive Services-erőforrást. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen-multi.png)

    Kattintson **a Létrehozás gombra.**

    #### <a name="single-service-resource"></a>[Egyszolgáltatásos erőforrás](#tab/singleservice)

    |    |    |
    |--|--|
    | **Név** | A kognitív szolgáltatások erőforrás leíró neve. Például *TextAnalyticsResource*. |
    | **Előfizetés** | Válassza ki az egyik elérhető Azure-előfizetését. |
    | **Helyen** | A kognitív szolgáltatáspéldány helye. A különböző helyek késést okozhatnak, de nincsenek hatással az erőforrás futásidejű rendelkezésre állására. |
    | **Tarifacsomag** | A Cognitive Services-fiók költsége a választott beállításoktól és a használattól függ. További információt az API [díjszabásának részleteicímű témakörben talál.](https://azure.microsoft.com/pricing/details/cognitive-services/)
    | **Erőforráscsoport** | Az Azure erőforráscsoport, amely tartalmazza a Cognitive Services-erőforrást. Létrehozhat egy új csoportot, vagy hozzáadhatja egy már meglévő csoporthoz. |

    ![Erőforrás-létrehozási képernyő](media/cognitive-services-apis-create-account/resource_create_screen.png)

    Kattintson **a Létrehozás gombra.**

    ***


## <a name="get-the-keys-for-your-resource"></a>Az erőforrás kulcsainak beszereznie

1. Az erőforrás sikeres telepítése után kattintson az **Ugrás az erőforráshoz** elemre a **Következő lépések területen.**

    ![Kognitív szolgáltatások keresése](media/cognitive-services-apis-create-account/resource-next-steps.png)

2. A megnyíló rövid útmutató ablaktáblából elérheti a kulcsot és a végpontot.

    ![Kulcs és végpont bekéselése](media/cognitive-services-apis-create-account/get-cog-serv-keys.png)

[!INCLUDE [cognitive-services-environment-variables](../../includes/cognitive-services-environment-variables.md)]

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha azt szeretné, hogy tisztítsák meg, és távolítsa el a Cognitive Services-előfizetés, törölheti az erőforrás vagy erőforráscsoport. Az erőforráscsoport törlése a csoportban található egyéb erőforrásokat is törli.

1. Az Azure Portalon bontsa ki a bal oldalon a szolgáltatásmenüt, és válassza az **Erőforráscsoportok** lehetőséget az erőforráscsoportok listájának megjelenítéséhez.
2. A törölendő erőforrást tartalmazó erőforráscsoport megkeresése
3. Kattintson a jobb gombbal az erőforráscsoport-listára. Válassza az **Erőforráscsoport törlése** elemet, és erősítse meg a választását.

## <a name="see-also"></a>Lásd még

* [Az Azure Cognitive Services kéréseinek hitelesítése](authentication.md)
* [Mi az Azure Cognitive Services?](Welcome.md)
* [Természetes nyelvi támogatás](language-support.md)
* [Docker-tároló támogatása](cognitive-services-container-support.md)
