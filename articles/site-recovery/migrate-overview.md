---
title: A helyszíni gépek és Azure virtuális gépek Azure Site Recovery az áttelepítéssel kapcsolatos |} A Microsoft Docs
description: Ez a cikk ismerteti, hogyan telepítheti át a helyszíni és az Azure IaaS virtuális gépeket az Azure-bA az Azure Site Recovery szolgáltatással.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/18/2019
ms.author: raynew
ms.openlocfilehash: 856d03b1ecc1c7a3bd527eb265061f9a305d8f50
ms.sourcegitcommit: 90dcc3d427af1264d6ac2b9bde6cdad364ceefcc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/21/2019
ms.locfileid: "58315633"
---
# <a name="about-migration"></a>A migrálás ismertetése

Ez a cikk röviden bemutatja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás segít gépek áttelepítéséhez. 

Íme a következőket telepítheti át a Site Recovery használatával:

- **Migrálása a helyszínről az Azure-bA**: A helyszíni Hyper-V virtuális gépek, VMware virtuális gépek és fizikai kiszolgálók áttelepítése az Azure-bA. Az áttelepítés után a helyszíni gépeken futó számítási feladatok az Azure-beli virtuális gépeken fognak futni. 
- **Azure Migrate**: Azure virtuális gépek áttelepítése az Azure-régiók között. 
- **AWS áttelepítése**: AWS Windows-példányok áttelepítése az Azure IaaS virtuális gépekre. 


## <a name="what-do-we-mean-by-migration"></a>Mit értünk áttelepítés alatt?

Mellett a helyszíni és Azure virtuális gépek vészhelyreállítása a Site Recovery segítségével áttelepítheti a Site Recovery szolgáltatás használhatja. Mi a különbség?

- Vész-helyreállítási replikálhat gépeket rendszeres időközönként az Azure-bA. Egy kimaradás esetén a átadja a gépek feladatait az elsődleges helyről az Azure másodlagos helyre, és ahonnan érheti el őket. Ha az elsődleges hely megint elérhetővé válik, átadja az Azure-ból.
- Az áttelepítéshez, a helyszíni gépek replikálása az Azure-ban vagy Azure virtuális gépek egy másodlagos régióba. Ezután feladatátvételt a virtuális Gépet az elsődleges helyről másodlagos, és az áttelepítési folyamat befejezése. Nincs szükség feladat-visszavételre.  


## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
**Migrálása a helyszínről az Azure-bA** | Áttelepítheti a helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók Azure-bA. Ehhez végzett szinte ugyanazokat a lépéseket teljes vészhelyreállítás esetén ugyanúgy. Egyszerűen nem átadja a gépek az Azure-ból a helyszíni helyre.
**Áttelepítés Azure-régiók között** | Azure virtuális gépek egy Azure-régióból a másikba áttelepítheti. Az áttelepítés befejezése után vész-helyreállítási konfigurálható az Azure virtuális gépek mostantól a másodlagos régióban, amelyre az áttelepítést végezte.
**AWS áttelepítése az Azure-ba** | AWS-példányokat is áttelepíthet Azure virtuális gépekre. A Site Recovery AWS-példányokat kezeli, mint a fizikai kiszolgálókat az áttelepítés során. 

## <a name="next-steps"></a>További lépések

- [Helyszíni gépek áttelepítése az Azure-ba](migrate-tutorial-on-premises-azure.md)
- [Virtuális gépek áttelepítése egyik Azure-régióból a másikba](azure-to-azure-tutorial-migrate.md)
- [AWS áttelepítése az Azure-ba](migrate-tutorial-aws-azure.md)
