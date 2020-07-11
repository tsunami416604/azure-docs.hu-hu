---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 07/10/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5aaf534e08b3184668c334c86b957dd872fd0dd9
ms.sourcegitcommit: 0b2367b4a9171cac4a706ae9f516e108e25db30c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2020
ms.locfileid: "86277312"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A virtuálisgép-rendszerkép-készítő sablonoknak privát hivatkozást kell használniuk](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F2154edb9-244f-4741-9970-660785bccdaa) |Olyan virtuálisgép-rendszerkép-készítő sablonok naplózása, amelyeken nincs konfigurálva a virtuális hálózat. Ha nincs konfigurálva egy virtuális hálózat, akkor a rendszer létrehoz és használ egy nyilvános IP-címet, amely közvetlenül az interneten teszi elérhetővé az erőforrásokat, és növelheti a lehetséges támadási felületet. |Naplózás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/VM%20Image%20Builder/PrivateLinkEnabled_Audit.json) |
