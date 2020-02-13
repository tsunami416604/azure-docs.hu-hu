---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/10/2020
ms.author: dacoulte
ms.openlocfilehash: 0177f1ffbc21cf5508b86a9c0f3c625fead34faf
ms.sourcegitcommit: bdf31d87bddd04382effbc36e0c465235d7a2947
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2020
ms.locfileid: "77172939"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |
|---|---|---|---|
|[Service Fabric-fürtökön a ClusterProtectionLevel tulajdonságot EncryptAndSign értékre kell beállítani](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditClusterProtectionLevel_Audit.json) |Service Fabric egy elsődleges fürttanúsítvány csomópontok közötti kommunikációt három szintje (nincs, bejelentkezési és EncryptAndSign) védelmet biztosít. Állítsa be a védelmi szintet annak biztosítására, hogy minden csomópont-csomópont típusú üzenet titkosítva legyen, és digitálisan alá legyen írva. |Naplózás, letiltva |1.0.0 |
|[Service Fabric-fürtök esetében csak Azure Active Directoryt kell használnia az ügyfél-hitelesítéshez](https://github.com/Azure/azure-policy/blob/masterbuilt-in-policies/policyDefinitions/Service%20Fabric/ServiceFabric_AuditADAuth_Audit.json) |Az ügyfél-hitelesítés használatának naplózása csak Azure Active Directoryon keresztül Service Fabric |Naplózás, letiltva |1.0.0 |
