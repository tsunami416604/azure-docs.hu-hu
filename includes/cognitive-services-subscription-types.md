---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 3b808f4eb853cf05eb08cd1acf08dedccabf71a6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274679"
---
## <a name="azure-cognitive-service-resource-types"></a>Az Azure kognitív szolgáltatás erőforrásainak típusai

<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Az Azure Cognitive Services-t két különböző erőforráson keresztül érheti el: Több szolgáltatásból álló erőforrás vagy egyetlen szolgáltatás. Ezek az előfizetések lehetővé teszik, hogy egyetlen kognitív szolgáltatáshoz vagy egyszerre több kognitív szolgáltatáshoz kapcsolódjon.

### <a name="multi-service-resource"></a>Több szolgáltatásból álló erőforrás

Előfizetés több szolgáltatás Cognitive Services erőforrásra:
* Lehetővé teszi, hogy egyetlen Azure-erőforrást használjon a legtöbb Azure-Cognitive Services.
* Egyetlen kulcsot szerezhet be, amely több Azure-Cognitive Services is használható.
* Konszolidálja a használatban lévő szolgáltatások számlázását. További információkért tekintse meg a [Cognitive Services díjszabását](https://azure.microsoft.com/pricing/details/cognitive-services/) .

>[!WARNING]
> Ezek a szolgáltatások jelenleg **nem** támogatják a több szolgáltatásból származó kulcsokat: QnA Maker, Speech Services, Custom Vision és anomália detektor.

### <a name="single-service-resource"></a>Egy szolgáltatásból származó erőforrás

Előfizetés egyetlen Service Cognitive Services erőforrásra:
* Lehetővé teszi egy adott kognitív szolgáltatás használatát, amellyel létrehozhatja az erőforrást (például Computer Vision vagy Speech Services).
* Egy olyan kulcsot kap, amely a kognitív szolgáltatásra vonatkozik, amelyhez erőforrást hoz létre.