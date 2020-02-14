---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/12/2020
ms.author: dacoulte
ms.openlocfilehash: 0b3547d5ddc989ac8bd72eb530215b3cf6385c0e
ms.sourcegitcommit: b07964632879a077b10f988aa33fa3907cbaaf0e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2020
ms.locfileid: "77192945"
---
|Name (Név) |Leírás |Hatás (ok) |Verzió |
|---|---|---|---|
|[Engedélyezett virtuális gépek SKU-i](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMSkusAllowed_Deny.json) |Ezzel a szabályzattal megadhatók a virtuális gépek azon termékváltozatai, amelyeket a szervezet üzembe helyezhet. |Megtagadás |1.0.0 |
|[Virtuális gépek naplózása vész-helyreállítás nélkül konfigurálva](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/RecoveryServices_DisasterRecovery_Audit.json) |Olyan virtuális gépek naplózása, amelyek nem rendelkeznek a vész-helyreállítási beállításokkal. Ha többet szeretne megtudni a vész-helyreállításról, látogasson el https://aka.ms/asr-docra. |auditIfNotExists |1.0.0 |
|[Felügyelt lemezeket nem használó virtuális gépek naplózása](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMRequireManagedDisk_Audit.json) |Ez a házirend a felügyelt lemezeket nem használó virtuális gépeket naplózza |ellenőrzési |1.0.0 |
|[A Windows Serverhez készült alapértelmezett Microsoft IaaSAntimalware-bővítmény telepítése](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMAntimalwareExtension_Deploy.json) |Ezzel a szabályzattal egy alapértelmezett konfigurációval telepítheti a Microsoft IaaSAntimalware bővítményt, ha a virtuális gép nem a kártevő szoftverrel együtt van konfigurálva. |deployIfNotExists |1.0.0 |
|[A Virtual Machine Scale Sets lévő diagnosztikai naplókat engedélyezni kell](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ServiceFabric_and_VMSS_AuditVMSSDiagnostics.json) |Javasoljuk, hogy engedélyezze a naplókat, hogy a tevékenység nyomvonala újra létre lehessen hozni, ha incidens vagy sérülés esetén szükséges a vizsgálatok. |AuditIfNotExists, letiltva |1.0.0 |
|[Az Azure-hoz készült Microsoft antimalware-t úgy kell konfigurálni, hogy automatikusan frissítse a védelmi aláírásokat](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_AntiMalwareAutoUpdate_AuditIfNotExists.json) |Ez a házirend minden olyan Windows rendszerű virtuális gépet naplóz, amely nincs konfigurálva a Microsoft antimalware Protection-aláírások automatikus frissítésével. |AuditIfNotExists, letiltva |1.0.0 |
|[A Microsoft IaaSAntimalware bővítményt Windows-kiszolgálókon kell telepíteni](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/WindowsServers_AntiMalware_AuditIfNotExists.json) |Ez a házirend a Microsoft IaaSAntimalware bővítmény telepítése nélkül naplózza a Windows Server rendszerű virtuális gépeket. |AuditIfNotExists, letiltva |1.0.0 |
|[Csak a jóváhagyott virtuálisgép-bővítményeket kell telepíteni](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VirtualMachines_ApprovedExtensions_Audit.json) |Ez a házirend a nem jóváhagyott virtuálisgép-bővítményeket szabályozza. |Naplózás, megtagadás, letiltva |1.0.0 |
|[Az operációsrendszer-rendszerkép automatikus javításának megkövetelése Virtual Machine Scale Sets](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/VMSSOSUpgradeHealthCheck_Deny.json) |Ez a szabályzat kényszeríti a Virtual Machine Scale Sets automatikus operációsrendszer-lemezképek javításának engedélyezését, hogy minden hónapban biztonságosan alkalmazza a Virtual Machines biztonságát a legújabb biztonsági javítások biztonságos alkalmazásával. |tagadja |1.0.0 |
|[A nem csatolt lemezeket titkosítani kell](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/UnattachedDisk_Encryption_Audit.json) |Ez a házirend minden nem csatlakoztatott lemezt naplóz a titkosítás engedélyezése nélkül. |Naplózás, letiltva |1.0.0 |
|[A virtuális gépeket át kell telepíteni az új Azure Resource Manager erőforrásokra](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Compute/ClassicCompute_Audit.json) |A virtuális gépek új Azure Resource Manager használatával olyan biztonsági fejlesztéseket biztosíthat, mint például a következők: erősebb hozzáférés-vezérlés (RBAC), jobb auditálás, ARM-alapú üzembe helyezés és irányítás, felügyelt identitásokhoz való hozzáférés, a Key Vault for Secrets, Azure Az AD-alapú hitelesítés és a címkék és erőforráscsoportok támogatása a könnyebb biztonság kezeléséhez |Naplózás, megtagadás, letiltva |1.0.0 |
