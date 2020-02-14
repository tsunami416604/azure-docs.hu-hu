---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 7b2179c0a924f7fc29aa70ff748d435e664980ae
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77193062"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |
|---|---|---|---|
|[Key Vault diagnosztikai beállításainak üzembe helyezése az Event hub-ban](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_DiagnosticLog_Deploy.json) |Központilag telepíti a Key Vault diagnosztikai beállításait egy regionális Event hubhoz, ha bármely olyan Key Vault, amelyből hiányzik ez a diagnosztikai beállítás, létrejön vagy frissül. |deployIfNotExists |1.0.0 |
|[A Key Vault lévő diagnosztikai naplókat engedélyezni kell](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_AuditDiagnosticLog_Audit.json) |Diagnosztikai naplók engedélyezésének naplózása. Ez lehetővé teszi, hogy újból létrehozza a vizsgálati célokra használandó tevékenység-nyomvonalat; biztonsági incidens bekövetkezésekor vagy a hálózat biztonsága esetén |AuditIfNotExists, letiltva |1.0.0 |
|[Key Vault objektumoknak helyreállítható kell lenniük](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Key%20Vault/KeyVault_Recoverable_Audit.json) |Ez a házirend azt naplózza, hogy a Key Vault-objektumok nem állnak-e helyreállítva. A Soft delete funkció segítségével még a TÖRLÉSi művelet után is hatékonyan megtarthatja az adott megőrzési időtartam (90 nap) erőforrásait, miközben megadhatja az objektum törlésének megjelenését. Ha a kiürítési védelem be van kapcsolva, a tároló vagy a törölt állapotban lévő objektum nem törölhető, amíg a 90 napos megőrzési idő el nem telt. Ezek a tárolók és objektumok továbbra is helyreállíthatók, így biztosítva az ügyfelek számára, hogy az adatmegőrzési szabályzatot követni fogjuk. |Naplózás, letiltva |1.0.0 |
