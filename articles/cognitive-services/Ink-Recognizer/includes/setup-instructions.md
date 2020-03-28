---
author: aahill
ms.author: aahi
ms.service: cognitive-services
ms.topic: include
ms.date: 06/20/2019
ms.openlocfilehash: 942bcc6b150f990f9a9acab0d4ef68bfb6125c1b
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "71996862"
---
>[!NOTE]
> 2019. július 1-je után létrehozott erőforrások végpontjai az alábbi egyéni altartomány-formátumot használják. További információt és a regionális végpontok teljes listáját a [Cognitive Services egyéni altartománynevei című témakörben talál.](https://docs.microsoft.com/azure/cognitive-services/cognitive-services-custom-subdomains) 

Az Azure Cognitive Servicest olyan Azure-erőforrások képviselik, amelyekre előfizet. Hozzon létre egy erőforrást a tintafelismerő számára az [Azure Portalon](../../cognitive-services-apis-create-account.md)keresztül. 

* Ön is kap egy [próbakulcs](https://azure.microsoft.com/try/cognitive-services/#decision) érvényes hét napig ingyen. A regisztráció után, és egy végpont lesz elérhető az [Azure-ban honlapján.](https://azure.microsoft.com/try/cognitive-services/my-apis/)

Egy erőforrás létrehozása után az [Azure Portalon](https://ms.portal.azure.com#blade/HubsExtension/BrowseResourceGroupBlade)nyissa meg az erőforrást, és kattintson a **Gyorsindítás**gombra.

Hozzon létre két [környezeti változót:](../../cognitive-services-apis-create-account.md#get-the-keys-for-your-resource)

* `INK_RECOGNITION_SUBSCRIPTION_KEY`- Az erőforrás végpontja. Így fog kinézni: <br> `https://<your-custom-subdomain>.api.cognitive.microsoft.com` 

* `INK_RECOGNITION_ENDPOINT`- Az előfizetési kulcs a kérések hitelesítéséhez.   
