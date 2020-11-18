---
title: Fogalmak – az Azure VMware-megoldás privát felhők monitorozása és javítása
description: Ismerje meg, hogyan figyeli és javítja az Azure VMware megoldás a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás privát felhőben.
ms.topic: conceptual
ms.date: 11/18/2020
ms.openlocfilehash: 11a3c53bff7ce7b67b677977eddb9829f336672d
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684695"
---
# <a name="monitor-and-repair-azure-vmware-solution-private-clouds"></a>Azure VMware-megoldás privát felhők monitorozása és javítása

Az Azure VMware megoldás folyamatosan figyeli a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás saját felhőben. 

## <a name="what-azure-vmware-solution-monitors"></a>Az Azure VMware-megoldás figyelők

Az Azure VMware-megoldás a következő esetekben figyeli a meghibásodási feltételeket a gazdagépen:  

- Processzor állapota 
- Memória állapota 
- A kapcsolatok és a tápellátás állapota 
- Hardver ventilátorának állapota 
- Hálózati kapcsolat elvesztése 
- A hardver rendszertáblájának állapota 
- Hibák történtek egy vSAN-állomás lemezén (i) 
- Hardveres feszültség 
- Hardver hőmérsékleti állapota 
- Hardver energiaellátási állapota 
- Tárolási állapot 
- Kapcsolódási hiba 

> [!NOTE]
> Az Azure VMware megoldás bérlői rendszergazdái nem módosíthatják és nem törölhetik a fent meghatározott VMware vCenter riasztásokat, mivel ezeket az Azure VMware megoldás-vezérlési sík felügyeli a vCenter-on. Ezeket a riasztásokat az Azure VMware-megoldás figyelése használja az Azure VMware Solution Host szervizelési folyamatának elindításához.

## <a name="azure-vmware-solution-host-remediation"></a>Azure VMware megoldás gazdagép szervizelése  

Ha az Azure VMware-megoldás romlást vagy hibát észlel egy bérlő privát felhőben lévő Azure VMware megoldás-csomóponton, elindítja a gazdagép szervizelési folyamatát. A gazdagép szervizelése azt jelenti, hogy a hibás csomópontot egy új kifogástalan állapotú csomópontra cseréli.  

A gazdagép szervizelési folyamata egy új, kifogástalan állapotú csomópont hozzáadásával indul el a fürtben. Ezután, ha lehetséges, a hibás gazdagép VMware vSphere karbantartási módba kerül. A VMware vMotion arra szolgál, hogy a virtuális gépeket a fürtön kívül más elérhető kiszolgálókra helyezze át, ami potenciálisan lehetővé teszi a számítási feladatok élő áttelepítését. Olyan esetekben, amikor a hibás gazdagép nem helyezhető karbantartási módba, a rendszer eltávolítja a gazdagépet a fürtből.

## <a name="next-steps"></a>Következő lépések

Az alábbi témakörökből megtudhatja, hogyan érdemes többet megtudni:

- [Azure VMware-megoldás saját Felhőbeli frissítései](concepts-upgrades.md)
- [Azure VMware-megoldás virtuális gépek életciklus-kezelése](lifecycle-management-of-azure-vmware-solution-vms.md)
- [Az Azure VMware-megoldás virtuális gépei a Azure Security Center integrációval védhetők](azure-security-integration.md)
- [Azure VMware-megoldás virtuális gépek biztonsági mentése Azure Backup Server](backup-azure-vmware-solution-virtual-machines.md)
- [A virtuális gépek vész-helyreállításának befejezése Azure VMware-megoldás használatával](disaster-recovery-for-virtual-machines.md)
