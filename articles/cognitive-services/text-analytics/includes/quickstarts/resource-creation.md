---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 10/28/2019
ms.author: aahi
ms.openlocfilehash: 2fe3104d61b5fe2fbf9624ed2fd4fdb2de5686a2
ms.sourcegitcommit: 827248fa609243839aac3ff01ff40200c8c46966
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2019
ms.locfileid: "73750203"
---
Szerezze be az alkalmazások hitelesítéséhez szükséges kulcsot és végpontot. Hozzon létre egy erőforrást Text Analytics a helyi gépen található [Azure Portal](../../../cognitive-services-apis-create-account.md) vagy az [Azure CLI](../../../cognitive-services-apis-create-account-cli.md) használatával. További lehetőségek:

* A [próbaverziós kulcs](https://azure.microsoft.com/try/cognitive-services/#decision) ingyenes 7 napig érvényes. A regisztráció után elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).  
* Az erőforrás megtekintése a [Azure Portal](https://portal.azure.com/)

A próbaverziós előfizetésből vagy erőforrásból származó kulcs és végpont létrehozása után hozzon létre két [környezeti változót](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-apis-create-account#configure-an-environment-variable-for-authentication). Egy elnevezett `TEXT_ANALYTICS_SUBSCRIPTION_KEY` a kulcshoz, és egy elnevezett `TEXT_ANALYTICS_ENDPOINT` a végponthoz.