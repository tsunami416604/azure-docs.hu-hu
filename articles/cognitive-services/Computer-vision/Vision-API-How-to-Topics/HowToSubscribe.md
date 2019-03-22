---
title: Előfizetői azonosítók – Computer Vision beolvasása
titlesuffix: Azure Cognitive Services
description: További tudnivalók a Computer Vision API-hívások előfizetési kulcsainak beszerzése az Azure Cognitive Servicesben.
services: cognitive-services
author: KellyDF
manager: nitinme
ms.service: cognitive-services
ms.subservice: computer-vision
ms.topic: article
ms.date: 05/19/2017
ms.author: kefre
ms.custom: seodec18
ms.openlocfilehash: 03e519520d4a956a5c9690dc1327089505aafced
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58120856"
---
# <a name="how-to-obtain-subscription-keys"></a>Előfizetői azonosítók beszerzése

Számítógép vizuális szolgáltatások igényelnek Speciális előfizetési kulcsok. A Computer Vision API minden meghívásához előfizetési kulcs szükséges. Ezt a kulcsot egy lekérdezésisztring-paraméteren keresztül kell továbbítani, vagy a kérelemfejlécben kell megadni.

Előfizetői azonosítók regisztrálni, lásd: [előfizetések](https://azure.microsoft.com/try/cognitive-services/). Célszerű a regisztráció ingyenes. A szolgáltatások díjszabása változhat.

> [!NOTE]
> Az előfizetési kulcsok jsou platné Pro csak egyet [Microsoft Azure-régiók](https://azure.microsoft.com/regions/). 

| Régió | Cím |
|---|---|
| USA nyugati régiója | westus.API.cognitive.microsoft.com |
| USA 2. keleti régiója | eastus2.api.cognitive.microsoft.com |
| USA nyugati középső régiója | westcentralus.api.cognitive.microsoft.com |
| Nyugat-Európa | westeurope.api.cognitive.microsoft.com |
| Délkelet-Ázsia | southeastasia.api.cognitive.microsoft.com |

Ha regisztrál a Computer Vision ingyenes próbaverziót, az előfizetési kulcsok esetében érvényesek a **westcentral** régió (`https://westcentralus.api.cognitive.microsoft.com/`). Ez a leggyakoribb eset. Azonban ha regisztrál a Microsoft Azure Computer Vision fiók keresztül a [ https://azure.microsoft.com/ ](https://azure.microsoft.com/) webhelyén, a régiót a próbaverzióban régiók az előző listában adja meg.

Ha regisztrál a Microsoft Azure-fiókra, és a Computer Vision adja meg például `westus` a régióban kell használnia a `westus` régiót a REST API-hívások (`https://westus.api.cognitive.microsoft.com/`).

A régió esetében az előfizetési kulcs, Miután beszerezte a Próbakulcs elfelejti, megkeresni a régióban található [ https://azure.microsoft.com/try/cognitive-services/my-apis/ ](https://azure.microsoft.com/try/cognitive-services/my-apis/).

### <a name="related-links"></a>Kapcsolódó hivatkozások:

* [Az Azure Cognitive Services API-k – díjszabás](https://azure.microsoft.com/pricing/details/cognitive-services/)
