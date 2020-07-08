---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84906716"
---


| Szolgáltatás |[Felhasználási terv](../articles/azure-functions/functions-scale.md#consumption-plan)|[Prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikált csomag](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅Yes|✅Yes|✅Yes|✅Yes|✅Yes|
|[Virtuális hálózat integrációja](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌No|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅Yes| ✅Yes|
|[Virtuális hálózati eseményindítók (nem HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌No| ✅Yes |✅Yes|✅Yes|✅Yes|
|[Hibrid kapcsolatok](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (csak Windows)|❌No|✅Yes|✅Yes|✅Yes|✅Yes|
|[Kimenő IP-korlátozások](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌No| ✅Yes|✅Yes|✅Yes|✅Yes|