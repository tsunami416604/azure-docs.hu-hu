---
title: Előfizetői azonosítók beszerzése a Computer Vision API |} A Microsoft Docs
description: 'Útmutató: a Computer Vision API-hívások az előfizetési kulcsok beolvasása a Cognitive Services.'
services: cognitive-services
author: KellyDF
manager: corncar
ms.service: cognitive-services
ms.component: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.openlocfilehash: 682266df9e1a1f8209904697358d10bbaa633cf1
ms.sourcegitcommit: e8f443ac09eaa6ef1d56a60cd6ac7d351d9271b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2018
ms.locfileid: "35760228"
---
# <a name="how-to-obtain-subscription-keys"></a>Előfizetői azonosítók beszerzése

Számítógép vizuális szolgáltatások igényelnek Speciális előfizetési kulcsok. A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot kell átadni a lekérdezési sztring paramétereként, vagy a kérelem fejlécében megadott.

Előfizetői azonosítók regisztrálni, lásd: [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Célszerű a regisztráció ingyenes. A szolgáltatások díjszabása változhat.

>[!NOTE]
Az előfizetési kulcsok jsou platné Pro csak egyet [Microsoft Azure-régiók](https://azure.microsoft.com/regions/). 

| Régió | Cím |
|---|---|
| USA nyugati régiója | westus.API.cognitive.microsoft.com |
| USA 2. keleti régiója | eastus2.API.cognitive.microsoft.com |
| USA nyugati középső régiója | westcentralus.API.cognitive.microsoft.com |
| Nyugat-Európa | westeurope.API.cognitive.microsoft.com |
| Délkelet-Ázsia | southeastasia.API.cognitive.microsoft.com |

Ha regisztrál a Computer Vision ingyenes próbaverziót, az előfizetési kulcsok esetében érvényesek a **westcentral** régió (`https://westcentralus.api.cognitive.microsoft.com/`). Ez a leggyakoribb eset. Azonban ha regisztrál a Microsoft Azure Computer Vision fiók keresztül a [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) webhelyén, a régiót a próbaverzióban régiók az előző listában adja meg.

Ha regisztrál a Microsoft Azure-fiókra, és a Computer Vision adja meg például `westus` a régióban kell használnia a `westus` régiót a REST API-hívások (`https://westus.api.cognitive.microsoft.com/`).

A régió esetében az előfizetési kulcs, Miután beszerezte a Próbakulcs elfelejti, megkeresni a régióban található [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Kapcsolódó hivatkozások:

* [A Microsoft Cognitive API-k – díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/)
