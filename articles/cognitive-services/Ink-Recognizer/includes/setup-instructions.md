---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 9d46b304d598b4830cf325909f77eea6b68af757
ms.sourcegitcommit: 58d3b3314df4ba3cabd4d4a6016b22fa5264f05a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/02/2020
ms.locfileid: "89303992"
---
>[!NOTE]
> A 2019. július 1. után létrehozott erőforrásokhoz tartozó végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a kézírás-felismerőhöz a [Azure Portal](../../cognitive-services-apis-create-account.md)használatával.

Miután létrehozta az erőforrást, a végpont és a kulcs beszerzéséhez nyissa meg az erőforrást a [Azure Portalon](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), és kattintson a **gyors üzembe helyezés**elemre.

Hozzon létre két [környezeti változót](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – A kérések hitelesítéséhez szükséges előfizetési kulcs. 

* `INK_RECOGNITION_ENDPOINT` – Az erőforrás végpontja. Ez így fog kinézni: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`   
