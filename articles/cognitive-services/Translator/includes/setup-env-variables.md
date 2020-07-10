---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: 54ee19f3e278b424384ff55d7065713584235df1
ms.sourcegitcommit: 5cace04239f5efef4c1eed78144191a8b7d7fee8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86144209"
---
## <a name="set-up"></a>Beállítás

### <a name="create-a-translator-resource"></a>Fordítói erőforrás létrehozása

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a fordítóhoz a [Azure Portal](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) használatával a helyi gépen. További lehetőségek:

* Megtekintheti a [Azure Portal](https://portal.azure.com/)meglévő erőforrásait.

A próbaverziós előfizetésből vagy erőforrásból származó kulcs lekérése után hozzon létre két [környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication):

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`– A fordítói erőforráshoz tartozó előfizetési kulcs.
* `TRANSLATOR_TEXT_ENDPOINT`– A fordító globális végpontja. A `https://api.cognitive.microsofttranslator.com/` címet használja.
