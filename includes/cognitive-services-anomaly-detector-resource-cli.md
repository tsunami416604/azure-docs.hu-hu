---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 1ea0b01997d3d5cecff73f951c51de5898c2f07a
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67503468"
---
A következő Azure CLI-parancsokat az ingyenes tarifacsomag egy Anomáliadetektálási detector használatával az erőforrás is üzembe helyezi. Kattintson a **Kipróbálom** gombra, illessze be a kódot, és az enter billentyű lenyomásával futtathatja az Azure cloud shellben. Az alkalmazás hitelesítéséhez a kulcsok nyomtatja ki. Miután ez befejeződik, [egy környezeti változó létrehozásához](../articles/cognitive-services/cognitive-services-apis-create-account.md#configure-an-environment-variable-for-authentication) a kulcsok egyikét, az nevű `ANOMALY_DETECTOR_KEY`.

> [!NOTE]
> * Igény szerint is:
>    * Hozzon létre egy erőforrást a [az Azure portal](../articles/cognitive-services/cognitive-services-apis-create-account.md), vagy [Azure CLI-vel](../articles/cognitive-services/cognitive-services-apis-create-account.md) a helyi gépen.
>    * első egy [Próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes 7 napig ingyenesen. Után az azt regisztráló is elérhető legyen a [Azure-webhelyen](https://azure.microsoft.com/try/cognitive-services/my-apis/).
>    * az ezen erőforrás megtekintéséhez a [az Azure portal](https://portal.azure.com/). 

A cognitive Services üzembe helyezi az Azure-erőforrások képviseli. Azure-erőforráscsoport összes Cognitive Services-fiók (és a kapcsolódó Azure-erőforrás) kell tartoznia.

1. Azure-erőforráscsoport létrehozása

    ```azurecli-interactive
    az group create \
        --name example-anomaly-detector-resource-group \
        --location westus2
    ```

2. Az ingyenes szinten Anomáliadetektálási detector használatával erőforrás létrehozása

    ```azurecli-interactive
    az cognitiveservices account create \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group \
        --kind AnomalyDetector \
        --sku F0 \
        --location westus2 \
        --yes
    ```

3. Az erőforrás kulcsainak listázása

    ```azurecli-interactive
    az cognitiveservices account keys list \
        --name example-anomaly-detector-resource \
        --resource-group example-anomaly-detector-resource-group
    ```