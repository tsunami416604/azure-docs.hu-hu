---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: d5ed36700e2aea623fb259816e5baddff5215361
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70381750"
---
<!-- > [!NOTE]
> Subscription owners can disable the creation of Cognitive Services resources for resource groups and subscriptions by applying [Azure policy](https://docs.microsoft.com/azure/governance/policy/overview#policy-definition), assigning a “Not allowed resource types” policy definition, and specifying **Microsoft.CognitiveServices/accounts** as the target resource type. -->
Az Azure Cognitive Services két különböző erőforráson keresztül érhető el: egy többszolgáltatásos erőforráson vagy egy szolgáltatáson keresztül.

* Többszolgáltatásos erőforrás:
    * Egyetlen kulccsal és egy végponttal több Azure Cognitive Services érhető el.
    * Konszolidálja a használt szolgáltatásokból származó számlázást.
* Egyszolgáltatásos erőforrás:
    * Egyetlen Azure Cognitive Service-t érhet el egy egyedi kulccsal és végponttal minden egyes létrehozott szolgáltatáshoz. 
    * Használja az ingyenes szint, hogy próbálja ki a szolgáltatást.   
