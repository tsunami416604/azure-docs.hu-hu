---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c202ba1d7363af9791daa801f0c447c49a80859b
ms.sourcegitcommit: bf8c447dada2b4c8af017ba7ca8bfd80f943d508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/25/2020
ms.locfileid: "85378623"
---
>[!NOTE]
> A 2019. július 1. után létrehozott erőforrásokhoz tartozó végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains). 

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a kézírás-felismerőhöz a [Azure Portal](../../cognitive-services-apis-create-account.md)használatával.

Miután létrehozta az erőforrást, a végpont és a kulcs beszerzéséhez nyissa meg az erőforrást a [Azure Portalon](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), és kattintson a **gyors üzembe helyezés**elemre.

Hozzon létre két [környezeti változót](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY`– Az erőforrás végpontja. A következőképpen fog kinézni: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`– A kérések hitelesítéséhez szükséges előfizetési kulcs.   
