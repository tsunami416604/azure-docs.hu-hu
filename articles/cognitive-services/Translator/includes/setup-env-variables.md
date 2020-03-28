---
author: erhopf
ms.service: cognitive-services
ms.topic: include
ms.date: 08/06/2019
ms.author: erhopf
ms.openlocfilehash: c737447c3a3bd2d76d3ed620b7c61aaa81250130
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70393816"
---
## <a name="set-up"></a>Beállítás

### <a name="create-a-translator-text-resource"></a>Fordítószöveg-erőforrás létrehozása

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a translator text az [Azure Portalon](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account) vagy az [Azure CLI](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account-cli) a helyi gépen. További lehetőségek:

* Kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services) érvényes részére 7 napok részére szabad. A regisztráció után elérhető lesz az Azure-webhelyen.
* Meglévő erőforrás megtekintése az [Azure Portalon.](https://portal.azure.com/)

Miután beszerzett egy kulcsot a próba-előfizetésből vagy erőforrásból, hozzon létre két [környezeti változót:](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication)

* `TRANSLATOR_TEXT_SUBSCRIPTION_KEY`- A Fordító szöveg erőforrásának előfizetési kulcsa.
* `TRANSLATOR_TEXT_ENDPOINT`- A fordítószöveg globális végpontja. A `https://api.cognitive.microsofttranslator.com/` címet használja.
