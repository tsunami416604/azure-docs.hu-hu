---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: d5ed36700e2aea623fb259816e5baddff5215361
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70381750"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Az Azure Cognitive Services-t két különböző erőforráson keresztül érheti el: Több szolgáltatásból álló erőforrás vagy egyetlen szolgáltatás.

* Több szolgáltatásból álló erőforrás:
    * Több Azure-Cognitive Services elérése egyetlen kulccsal és végponttal.
    * Konszolidálja a használatban lévő szolgáltatások számlázását.
* Egy szolgáltatásból származó erőforrás:
    * Egyetlen Azure-beli kognitív szolgáltatás elérése egyedi kulccsal és végponttal minden létrehozott szolgáltatáshoz. 
    * A szolgáltatás kipróbálásához használja az ingyenes szintet.   
