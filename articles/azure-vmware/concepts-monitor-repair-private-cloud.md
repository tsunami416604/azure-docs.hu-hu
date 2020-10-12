---
title: Fogalmak – az Azure VMware-megoldás privát felhők monitorozása és javítása
description: Ismerje meg, hogyan figyeli és javítja az Azure VMware megoldás a VMware ESXi-kiszolgálókat egy Azure VMware-megoldás privát felhőben.
ms.topic: conceptual
ms.date: 09/16/2020
ms.openlocfilehash: bee08304cd14f2aeec6995203638b5a37d9a861c
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "91343904"
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

Ismerje meg az [Azure VMware megoldás saját Felhőbeli frissítéseit](concepts-upgrades.md).  
