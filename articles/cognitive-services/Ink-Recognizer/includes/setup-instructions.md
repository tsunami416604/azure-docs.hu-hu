---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9f330c3393a283faedaf9aa75b9fcfc06118b124
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/07/2019
ms.locfileid: "71996862"
---
>[!NOTE]
> A 2019. július 1. után létrehozott erőforrásokhoz tartozó végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a kézírás-felismerőhöz a [Azure Portal](../../cognitive-services-apis-create-account.md)használatával. 

* A [próbaverziót](https://azure.microsoft.com/try/cognitive-services/#decision) akár hét napig is elérheti ingyenesen. A regisztrációt követően a végpont elérhető lesz az [Azure webhelyén](https://azure.microsoft.com/try/cognitive-services/my-apis/).

Miután létrehozta az erőforrást, a végpont és a kulcs beszerzéséhez nyissa meg az erőforrást a [Azure Portalon](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), és kattintson a **gyors üzembe helyezés**elemre.

Hozzon létre két [környezeti változót](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – az erőforrás végpontja. A következőképpen fog kinézni: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT` – a kérések hitelesítéséhez szükséges előfizetési kulcs.   
