---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 8319ec2bf3965ee30db3e7d4ba1346bd7a3dd7d4
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77169915"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |
|---|---|---|---|
|[Engedélyezett helyek](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedLocations_Deny.json) |Ezzel a szabályzattal korlátozható azon helyek köre, amelyeket a szervezet megadhat az erőforrások üzembe helyezésekor. A földrajzi megfelelőségi követelmények betartására szolgál. Az erőforráscsoportok, a Microsoft. AzureActiveDirectory/b2cDirectories és a "globális" régiót használó erőforrások kizárása. |tagadja |1.0.0 |
|[Erőforráscsoportok engedélyezett helyei](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourceGroupAllowedLocations_Deny.json) |Ez a szabályzat lehetővé teszi, hogy korlátozza a szervezete által az erőforráscsoportok létrehozásához szükséges helyet. A földrajzi megfelelőségi követelmények betartására szolgál. |tagadja |1.0.0 |
|[Engedélyezett erőforrástípusok](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/AllowedResourceTypes_Deny.json) |Ez a szabályzat lehetővé teszi a szervezet által üzembe helyezhető erőforrástípusok megadását. Ez a szabályzat csak a "címkéket" és a "location" támogatást támogató erőforrásokat fogja érinteni. Az összes erőforrás korlátozásához ismételje meg ezt a házirendet, és módosítsa a "Mode" beállítást az "all" értékre. |tagadja |1.0.0 |
|[A naplózási erőforrás helye megegyezik az erőforráscsoport helyével](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/ResourcesInResourceGroupLocation_Audit.json) |Annak naplózása, hogy az erőforrás helye megegyezik az erőforráscsoport helyével |ellenőrzési |1.0.0 |
|[Egyéni RBAC-szabályok használatának naplózása](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/Subscription_AuditCustomRBACRoles_Audit.json) |Olyan beépített szerepköröket naplózhat, mint például a "tulajdonos, közreműködő, olvasó", az egyéni RBAC-szerepkörök helyett, amelyek hajlamosak a hibákra. Az egyéni szerepkörök használata Kivételként kezelendő, és szigorú felülvizsgálati és veszélyforrás-modellezést igényel |Naplózás, letiltva |1.0.0 |
|[Az egyéni előfizetés tulajdonosi szerepkörei nem létezhetnek](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/CustomSubscription_OwnerRole_Audit.json) |Ez a szabályzat biztosítja, hogy egyetlen egyéni előfizetés-tulajdonosi szerepkör sem létezik. |Naplózás, letiltva |1.0.0 |
|[Nem engedélyezett erőforrástípusok](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/General/InvalidResourceTypes_Deny.json) |Ez a szabályzat lehetővé teszi azon erőforrástípusok megadását, amelyeket a szervezet nem tud központilag telepíteni. |Megtagadás |1.0.0 |
