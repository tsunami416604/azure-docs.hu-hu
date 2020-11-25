---
ms.openlocfilehash: 82571d1a0e651f638dec29184f0ecdc88562b3ad
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "96020991"
---


| Funkció |[Használatalapú csomag](../articles/azure-functions/functions-scale.md#consumption-plan)|[Prémium szintű csomag](../articles/azure-functions/functions-scale.md#premium-plan)|[Dedikált csomag](../articles/azure-functions/functions-scale.md#app-service-plan)|[ASE](../articles/app-service/environment/intro.md)| [Kubernetes](../articles/azure-functions/functions-kubernetes-keda.md) |
|----------------|-----------|----------------|---------|-----------------------| ---|
|[Bejövő IP-korlátozások és privát webhely-hozzáférés](../articles/azure-functions/functions-networking-options.md#inbound-access-restrictions)|✅igen|✅igen|✅igen|✅igen|✅igen|
|[Virtuális hálózat integrációja](../articles/azure-functions/functions-networking-options.md#virtual-network-integration)|❌nem|✅Igen (regionális)|✅Igen (regionális és átjáró)|✅igen| ✅igen|
|[Virtuális hálózati eseményindítók (nem HTTP)](../articles/azure-functions/functions-networking-options.md#virtual-network-triggers-non-http)|❌nem| ✅igen |✅igen|✅igen|✅igen|
|[Hibrid kapcsolatok](../articles/azure-functions/functions-networking-options.md#hybrid-connections) (csak Windows)|❌nem|✅igen|✅igen|✅igen|✅igen|
|[Kimenő IP-korlátozások](../articles/azure-functions/functions-networking-options.md#outbound-ip-restrictions)|❌nem| ✅igen|✅igen|✅igen|✅igen|