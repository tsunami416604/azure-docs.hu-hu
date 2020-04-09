---
title: Azure Kubernetes-szolgáltatás futtatása – Szövegelemzés
titleSuffix: Azure Cognitive Services
description: Telepítse a Text Analytics-tároló rendszerképet az Azure Kubernetes-szolgáltatásba, és tesztelje azt egy webböngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: 155f32ee76f69fe0f16e7698123381fdc12efd0e
ms.sourcegitcommit: 2d7910337e66bbf4bd8ad47390c625f13551510b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2020
ms.locfileid: "80877828"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Text Analytics-tároló üzembe helyezése az Azure Kubernetes szolgáltatásban

Ismerje meg, hogyan telepítheti az Azure Cognitive Services [text analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) tárolórendszerképet az Azure Kubernetes-szolgáltatás (AKS) számára. Ez az eljárás bemutatja, hogyan hozhat létre egy Text Analytics-erőforrást, hogyan hozhat létre egy kapcsolódó véleményelemzési képet, és hogyan gyakorolhatja a kettő vezénylését egy böngészőből. A tárolók használatával elterelheti a figyelmet az infrastruktúra kezeléséről az alkalmazásfejlesztésre összpontosítva.

## <a name="prerequisites"></a>Előfeltételek

Ez az eljárás több olyan eszközt igényel, amelyeket helyileg kell telepíteni és futtatni. Ne használja az Azure Cloud Shellt. A következőkre van szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/) mielőtt elkezdené.
* Szövegszerkesztő, például [Visual Studio-kód](https://code.visualstudio.com/download).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve van.
* A [Kubernetes CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) telepítve van.
* Egy Azure-erőforrás a megfelelő tarifacsomaggal. Nem minden tarifacsomag működik ezzel a tárolóval:
    * **Az Azure Text Analytics-erőforrás** csak F0 vagy standard tarifacsomagokkal rendelkezik.
    * **Azure Cognitive Services-erőforrás** az S0 tarifacsomaggal.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detection"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysis"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>További lépések

* További [Cognitive Services-tárolók használata](../../cognitive-services-container-support.md)
* A [Text Analytics csatlakoztatott szolgáltatás használata](../vs-text-connected-service.md)
