---
title: Az Azure Kubernetes Service futtatása – Text Analytics
titleSuffix: Azure Cognitive Services
description: Telepítse az Text Analytics-tároló lemezképét az Azure Kubernetes szolgáltatásba, és tesztelje egy böngészőben.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 04/01/2020
ms.author: aahi
ms.openlocfilehash: b7a5953edd9aec96a7f75e747c39e8f07f7210bb
ms.sourcegitcommit: c293217e2d829b752771dab52b96529a5442a190
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/15/2020
ms.locfileid: "88243768"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Text Analytics tároló üzembe helyezése az Azure Kubernetes Service-ben

Megtudhatja, hogyan helyezheti üzembe az Azure Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) -tároló lemezképét az Azure Kubernetes Service-ben (ak). Ez az eljárás bemutatja, hogyan hozhat létre egy Text Analytics-erőforrást, hogyan hozhat létre egy kapcsolódó hangulat-elemzési képet, és hogyan gyakorolhatja a kettőt egy böngészőből. A tárolók használatával átirányíthatja a figyelmet az infrastruktúra kezelése helyett, hogy az alkalmazások fejlesztésére összpontosítsanak.

## <a name="prerequisites"></a>Előfeltételek

Ennek az eljárásnak számos olyan eszközre van szüksége, amelyet helyileg kell telepíteni és futtatni. Ne használja a Azure Cloud Shell. A következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/cognitive-services), mielőtt hozzákezd.
* Egy szövegszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/download).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve van.
* A [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) telepítve van.
* Egy megfelelő árképzési szintű Azure-erőforrás. Nem minden díjszabási csomag működik ezzel a tárolóval:
    * Az **Azure Text Analytics** erőforrás csak a F0 vagy a standard szintű árképzési szinttel rendelkezik.
    * **Azure Cognitive Services** erőforrás a S0 díjszabási szintjével.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extraction"></a>[Kulcsszókeresés](#tab/keyphrase)

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

* További [Cognitive Services tárolók](../../cognitive-services-container-support.md) használata
* A [text Analytics csatlakoztatott szolgáltatás](../vs-text-connected-service.md) használata
