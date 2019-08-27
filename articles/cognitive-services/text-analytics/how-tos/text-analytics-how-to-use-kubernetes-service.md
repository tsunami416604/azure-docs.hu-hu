---
title: Az Azure Kubernetes Service futtatása – Text Analytics
titleSuffix: Azure Cognitive Services
description: Telepítse az Text Analytics-tároló lemezképét az Azure Kubernetes szolgáltatásba, és tesztelje egy böngészőben.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/21/2019
ms.author: dapine
ms.openlocfilehash: c0c3c3483b73564e136035f04f9e4139b34f463f
ms.sourcegitcommit: bba811bd615077dc0610c7435e4513b184fbed19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/27/2019
ms.locfileid: "70051897"
---
# <a name="deploy-a-text-analytics-container-to-azure-kubernetes-service"></a>Text Analytics tároló üzembe helyezése az Azure Kubernetes Service-ben

Megtudhatja, hogyan helyezheti üzembe az Azure Cognitive Services [text Analytics](https://docs.microsoft.com/azure/cognitive-services/text-analytics/how-tos/text-analytics-how-to-install-containers) -tároló lemezképét az Azure Kubernetes Service-ben (ak). Ez az eljárás bemutatja, hogyan hozhat létre egy Text Analytics-erőforrást, hogyan hozhat létre egy kapcsolódó hangulat-elemzési képet, és hogyan gyakorolhatja a kettőt egy böngészőből. A tárolók használatával átirányíthatja a figyelmet az infrastruktúra kezelése helyett, hogy az alkalmazások fejlesztésére összpontosítsanak.

## <a name="prerequisites"></a>Előfeltételek

Ennek az eljárásnak számos olyan eszközre van szüksége, amelyet helyileg kell telepíteni és futtatni. Ne használja a Azure Cloud Shell. A következőkre lesz szüksége:

* Azure-előfizetés. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.
* Egy szövegszerkesztő, például a [Visual Studio Code](https://code.visualstudio.com/download).
* Az [Azure CLI](https://docs.microsoft.com/cli/azure/install-azure-cli?view=azure-cli-latest) telepítve van.
* A [KUBERNETES CLI](https://kubernetes.io/docs/tasks/tools/install-kubectl/) telepítve van.
* Egy megfelelő árképzési szintű Azure-erőforrás. Nem minden díjszabási csomag működik ezzel a tárolóval:
    * Az **Azure Text Analytics** erőforrás csak a F0 vagy a standard szintű árképzési szinttel rendelkezik.
    * **Azure Cognitive Services** erőforrás a S0 díjszabási szintjével.

[!INCLUDE [Create a Cognitive Services Text Analytics resource](../includes/create-text-analytics-resource.md)]

[!INCLUDE [Create a Text Analytics container on Azure Kubernetes Service (AKS)](../../containers/includes/create-aks-resource.md)]

#### <a name="key-phrase-extractiontabkeyphrase"></a>[Kulcskifejezések kinyerése](#tab/keyphrase)

[!INCLUDE [Key Phrase Extraction Kubernetes config and deploy steps](../includes/key-phrase-extraction-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Key Phrase Extraction container instance](../includes/verify-key-phrase-extraction-container.md)]

#### <a name="language-detectiontablanguage"></a>[Nyelvfelismerés](#tab/language)

[!INCLUDE [Language Detection Kubernetes config and deploy steps](../includes/language-detection-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Language Detection container instance](../includes/verify-language-detection-container.md)]

#### <a name="sentiment-analysistabsentiment"></a>[Hangulatelemzés](#tab/sentiment)

[!INCLUDE [Sentiment Analysis Kubernetes config and deploy steps](../includes/sentiment-analysis-kubernetes-config-deploy.md)]

[!INCLUDE [Verify the Sentiment Analysis container instance](../includes/verify-sentiment-analysis-container.md)]

***

## <a name="next-steps"></a>További lépések

* További [Cognitive Services tárolók](../../cognitive-services-container-support.md) használata
* A [text Analytics csatlakoztatott szolgáltatás](../vs-text-connected-service.md) használata
