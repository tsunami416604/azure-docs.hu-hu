---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 674dd30ff3e493ec4c4036f032f82624a6ca5749
ms.sourcegitcommit: e9c866e9dad4588f3a361ca6e2888aeef208fc35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/19/2019
ms.locfileid: "68334248"
---
## <a name="azure-cognitive-service-resource-types"></a>Az Azure kognitív szolgáltatás erőforrásainak típusai

> [!NOTE]
> Az előfizetések tulajdonosai letilthatja az erőforráscsoportok és előfizetések Cognitive Services erőforrásainak létrehozását az [Azure Policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition)alkalmazásával, a "nem engedélyezett erőforrástípusok" házirend-definíció hozzárendelésével és a megadásával  **Microsoft. CognitiveServices/fiókok** célként megadott erőforrástípus.

Az Azure Cognitive Services-t két különböző erőforráson keresztül érheti el: Több szolgáltatásból álló erőforrás vagy egyetlen szolgáltatás. Ezek az előfizetések lehetővé teszik, hogy egyszerre egyetlen szolgáltatáshoz vagy több szolgáltatáshoz kapcsolódjon.

### <a name="multi-service-resource"></a>Több szolgáltatásból álló erőforrás

>[!WARNING]
> Ezek a szolgáltatások jelenleg **nem** támogatják a több szolgáltatásból származó kulcsokat: QnA Maker, Speech Services, Custom Vision és anomália detektor.

Előfizetés több szolgáltatás Cognitive Services erőforrásra:
* Lehetővé teszi, hogy egyetlen Azure-erőforrást használjon a legtöbb Azure-Cognitive Services.
* Konszolidálja a használatban lévő szolgáltatások számlázását. További információkért tekintse meg a [Cognitive Services díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/) .

### <a name="single-service-resource"></a>Egy szolgáltatásból származó erőforrás

Az egyszolgáltatásos erőforrások (például Computer Vision vagy Speech Services) csak a megadott szolgáltatásra korlátozódnak.