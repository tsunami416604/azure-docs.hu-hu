---
ms.openlocfilehash: 8d5c3fff2646536ffe5c593e13ccdab23397d44b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "84906716"
---


| Szolgáltatás |[Használatalapú csomag](../articles/azure-functions/functions-scale.md#consumption-plan)|[Prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikált csomag](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](../articles/azure-functions/functions-networking-options.md#inbound-ip-restrictions)|✅igen|✅igen|✅igen|✅igen|✅igen|
|[Virtuális hálózat integrációja](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nem|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅igen| ✅igen|
|[Virtuális hálózati eseményindítók (nem HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nem| ✅igen |✅igen|✅igen|✅igen|
|[Hibrid kapcsolatok](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (csak Windows)|❌Nem|✅igen|✅igen|✅igen|✅igen|
|[Kimenő IP-korlátozások](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nem| ✅igen|✅igen|✅igen|✅igen|