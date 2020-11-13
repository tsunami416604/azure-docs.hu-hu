---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: 1d6ec4b6f60b7d9759269ce55b00c5ac5fb57d32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2020
ms.locfileid: "94578828"
---


| Jellemző |[Használatalapú csomag](../articles/azure-functions/functions-scale.md#consumption-plan)|[Prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikált csomag](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅Igen|✅Igen|✅Igen|✅Igen|✅Igen|
|[Virtuális hálózat integrációja](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌Nem|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅Igen| ✅Igen|
|[Virtuális hálózati eseményindítók (nem HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌Nem| ✅Igen |✅Igen|✅Igen|✅Igen|
|[Hibrid kapcsolatok](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (csak Windows)|❌Nem|✅Igen|✅Igen|✅Igen|✅Igen|
|[Kimenő IP-korlátozások](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌Nem| ✅Igen|✅Igen|✅Igen|✅Igen|