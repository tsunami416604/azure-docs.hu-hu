---
title: Azure Monitor for VMs Guest Health (előzetes verzió) – problémamegoldás
description: Ismerteti azokat a hibaelhárítási lépéseket, amelyek a Azure Monitor for VMs állapotával kapcsolatos problémák esetén elvégezhető.
ms.subservice: ''
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 09/08/2020
ms.openlocfilehash: 048230bb85d2ac599efc6bd3fb326f98038cce57
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94687022"
---
# <a name="troubleshoot-azure-monitor-for-vms-guest-health-preview"></a>Azure Monitor for VMs Guest Health (előzetes verzió) – problémamegoldás
Ez a cikk azokat a hibaelhárítási lépéseket ismerteti, amelyeket a Azure Monitor for VMs állapotával kapcsolatos problémák esetén is végrehajthat.

## <a name="error-message-that-no-data-is-available"></a>Hibaüzenet, amely nem érhető el. 

![Nincsenek adatkészletek](media/vminsights-health-troubleshoot/no-data.png)


### <a name="verify-that-the-virtual-machine-meets-configuration-requirements"></a>Annak ellenőrzése, hogy a virtuális gép megfelel-e a konfigurációs követelményeknek

1. Győződjön meg arról, hogy a virtuális gép egy Azure-beli virtuális gép. A kiszolgálókhoz készült Azure arc jelenleg nem támogatott.
2. Ellenőrizze, hogy a virtuális gép [támogatott operációs rendszert](vminsights-health-enable.md?current-limitations.md)futtat-e.
3. Ellenőrizze, hogy a virtuális gép egy [támogatott régióban](vminsights-health-enable.md?current-limitations.md)van-e telepítve.
4. Ellenőrizze, hogy a Log Analytics munkaterület egy [támogatott régióban](vminsights-health-enable.md?current-limitations.md)van-e telepítve.

### <a name="verify-that-the-vm-is-properly-onboarded"></a>Ellenőrizze, hogy a virtuális gép megfelelően be van-e állítva
Ellenőrizze, hogy a Azure Monitor ügynök bővítménye és a vendég virtuális gép állapota ügynök sikeresen kiépítve lett-e a virtuális gépen. Válassza a **bővítmények** lehetőséget a virtuális gép menüjében a Azure Portal. Ha a két ügynök szerepel a listáján, tekintse meg a következőt: []() .

![Virtuálisgép-bővítmények](media/vminsights-health-troubleshoot/extensions.png)

### <a name="verify-the-system-assigned-identity-is-enabled-on-the-virtual-machine"></a>Ellenőrizze, hogy engedélyezve van-e a rendszerhez rendelt identitás a virtuális gépen
Ellenőrizze, hogy a rendszerhez rendelt identitás engedélyezve van-e a virtuális gépen. Válassza az **identitás** lehetőséget a virtuális gép menüjében a Azure Portal. Ha a két ügynök szerepel a listáján, tekintse meg a következőt: []() .

![Rendszerhez rendelt identitás](media/vminsights-health-troubleshoot/system-identity.png)

### <a name="verify-data-collection-rule"></a>Adatgyűjtés szabályának ellenőrzése
Ellenőrizze, hogy a virtuális géphez társítva van-e az adatforrásként megadott adatgyűjtési szabály az állapot kiterjesztésével.

## <a name="error-message-for-bad-request-due-to-insufficient-permissions"></a>Nem megfelelő engedélyek miatti hibás kérelem hibaüzenete
Ez a hiba azt jelzi, hogy a **Microsoft. WorkloadMonitor** erőforrás-szolgáltató nincs regisztrálva az előfizetésben. Az erőforrás-szolgáltató regisztrálásával kapcsolatos részletekért tekintse meg az [Azure erőforrás-szolgáltatókat és-típusokat](/azure/azure-resource-manager/management/resource-providers-and-types#register-resource-provider) . 

![Hibás kérelem](media/vminsights-health-troubleshoot/bad-request.png)

## <a name="next-steps"></a>Következő lépések

- [A Azure Monitor for VMs vendég állapotának áttekintése](vminsights-health-overview.md)
