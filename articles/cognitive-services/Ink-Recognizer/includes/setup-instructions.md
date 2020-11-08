---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.subservice: ink-recognizer
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: c68e5b7ab24e2d7e7f30ddc356ae3c4382137507
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2020
ms.locfileid: "94369089"
---
>[!NOTE]
> A 2019. július 1. után létrehozott erőforrásokhoz tartozó végpontok az alább látható egyéni altartomány-formátumot használják. További információk és a regionális végpontok teljes listája: [Cognitive Services egyéni altartománynevei nevei](../../cognitive-services-custom-subdomains.md). 

Az Azure Cognitive Services a-ra előfizetett Azure-erőforrások képviselik. Hozzon létre egy erőforrást a kézírás-felismerőhöz a [Azure Portal](../../cognitive-services-apis-create-account.md)használatával.

Miután létrehozta az erőforrást, a végpont és a kulcs beszerzéséhez nyissa meg az erőforrást a [Azure Portalon](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade), és kattintson a **gyors üzembe helyezés** elemre.

Hozzon létre két [környezeti változót](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource):

* `INK_RECOGNITION_SUBSCRIPTION_KEY` – A kérések hitelesítéséhez szükséges előfizetési kulcs. 

* `INK_RECOGNITION_ENDPOINT` – Az erőforrás végpontja. Ez így fog kinézni: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com`