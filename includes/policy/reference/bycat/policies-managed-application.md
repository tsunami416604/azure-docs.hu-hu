---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 11/20/2020
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: a4f06313b577ffba8a10b4ecfff17e0117550d04
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95998795"
---
|Név<br /><sub>(Azure Portal)</sub> |Leírás |Hatás (ok) |Verzió<br /><sub>GitHub</sub> |
|---|---|---|---|
|[A felügyelt alkalmazás alkalmazás-definíciójának az ügyfél által biztosított Storage-fiókot kell használnia](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F9db7917b-1607-4e7d-a689-bca978dd0633) |A saját Storage-fiók használatával szabályozhatja az alkalmazás-definíció adatait, ha ez egy szabályozási vagy megfelelőségi követelmény. Megadhatja, hogy a felügyelt alkalmazás definícióját a létrehozás során megadott Storage-fiókon belül tárolja, így a hely és a hozzáférés teljes mértékben felügyelhető a szabályozási megfelelőségi követelmények teljesítése érdekében. |naplózás, megtagadás, letiltva |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/ApplicationDefinition_Missing_StorageAccount_Deny.json) |
|[Társítások telepítése felügyelt alkalmazásokhoz](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicyDefinitions%2F17763ad9-70c0-4794-9397-53d765932634) |Üzembe helyez egy olyan társítási erőforrást, amely a kiválasztott erőforrás-típusokat társítja a megadott felügyelt alkalmazáshoz.  Ez a házirend-telepítés nem támogatja a beágyazott erőforrástípusok használatát. |deployIfNotExists |[1.0.0](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Managed%20Application/AssociationForManagedApplication_Deploy.json) |
