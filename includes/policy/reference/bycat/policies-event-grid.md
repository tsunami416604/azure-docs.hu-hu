---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 01/08/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f03688012100c31865fb25087f7b4049c087b43e
ms.sourcegitcommit: 8dd8d2caeb38236f79fe5bfc6909cb1a8b609f4a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2021
ms.locfileid: "98047289"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[Azure Event Grid tartományoknak privát hivatkozást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9830b652-8523-49cc-b1b3-e17dce1127ca) |Az Azure Private link használatával a virtuális hálózatot az Azure-szolgáltatásokhoz nyilvános IP-cím nélkül csatlakoztathatja a forráson vagy a célhelyen. A privát kapcsolati platform kezeli a fogyasztó és a szolgáltatások közötti kapcsolatot az Azure gerinc hálózaton. Ha privát végpontokat rendel a Event Grid-tartományokhoz a teljes szolgáltatás helyett, akkor az adatszivárgás kockázatait is védeni fogja. További információ: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridDomains_EnablePrivateEndpoint_Audit.json) |
|[Azure Event Grid témaköröknek privát hivatkozást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F4b90e17e-8448-49db-875e-bd83fb6f804f) |Az Azure Private link használatával a virtuális hálózatot az Azure-szolgáltatásokhoz nyilvános IP-cím nélkül csatlakoztathatja a forráson vagy a célhelyen. A privát kapcsolati platform kezeli a fogyasztó és a szolgáltatások közötti kapcsolatot az Azure gerinc hálózaton. Ha privát végpontokat rendel a témakörökhöz a teljes szolgáltatás helyett, akkor az adatszivárgás kockázatait is védeni fogja. További információ: [https://aka.ms/privateendpoints](https://aka.ms/privateendpoints) . |Naplózás, letiltva |[1.0.1](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Event%20Grid/EventGridTopics_EnablePrivateEndpoint_Audit.json) |
