---
title: Kiszolgálók és virtuális gépek áttelepítése az Azure-ba az Azure Site Recovery segítségével
description: Bemutatja, hogyan telepítheti át a helyszíni és az Azure IaaS virtuális gépeket az Azure-ba az Azure Site Recovery szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: a7107eae5c798deb78d4d35eccdf4adcf5273335
ms.sourcegitcommit: 0553a8b2f255184d544ab231b231f45caf7bbbb0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/30/2020
ms.locfileid: "80388899"
---
# <a name="about-migration"></a>A migrálás ismertetése

Ebből a cikkből gyors áttekintést kaphat arról, hogy az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás hogyan segíti a gépek áttelepítését. 

> [!TIP]
> Most már használja az Azure Áttelepítés i szolgáltatás virtuális gépek és kiszolgálók áttelepítése az Azure Site Recovery szolgáltatás helyett. [További információ](../migrate/migrate-services-overview.md).


A Site Recovery használatával az alábbiakat telepítheti át:

- **A helyszíni és az Azure-ba való áttelepítés:** A helyszíni Hyper-V virtuális gépek, vmware virtuális gépek és fizikai kiszolgálók áttelepítése az Azure-ba. Az áttelepítés után a helyszíni gépeken futó számítási feladatok az Azure-beli virtuális gépeken fognak futni. 
- **Áttelepítés az Azure-on belül**: Azure-beli virtuális gépek áttelepítése Azure-régiók között. 
- **AWS áttelepítése**: AWS Windows-példányok áttelepítése Azure IaaS virtuális gépekre. 

> [!NOTE]
> Most már áttelepítheti a helyszíni Azure-ba az Azure Áttelepítés szolgáltatás használatával. [További információ](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Mit értünk áttelepítés alatt?

A site recovery használata a helyszíni vész-helyreállítási helyszíni és az Azure-beli virtuális gépek, használhatja a Site Recovery szolgáltatás áttelepítésükhöz. Mi a különbség?

- Vész-helyreállítási gépek rendszeresen replikálja az Azure-ba. Kimaradás esetén az elsődleges helyről a másodlagos Azure-helyre átjutó gépek nem, és onnan érheti el őket. Ha az elsődleges hely ismét elérhető, az Azure-ból visszakell térnie.
- Áttelepítés esetén replikálja a helyszíni gépeket az Azure-ba, vagy az Azure-beli virtuális gépeket egy másodlagos régióba. Ezután nem sikerül a virtuális gép az elsődleges helyről a másodlagos, és az áttelepítési folyamat befejezése. Nincs szükség feladat-visszavételre.  


## <a name="migration-scenarios"></a> Áttelepítési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
**Áttelepítés a helyszíni környezetből az Azure-ba** | A helyszíni VMware virtuális gépek, a Hyper-V vm-ek és a fizikai kiszolgálók áttelepíthetők az Azure-ba. Ehhez szinte ugyanazokat a lépéseket kell végrehajtania, mint a teljes vészhelyreállításhoz. Egyszerűen nem sikerül gépek vissza az Azure-ból a helyszíni hely.
**Áttelepítés az Azure-régiók között** | Az Azure-beli virtuális gépek et áttelepítheti az egyik Azure-régióból a másikba. Az áttelepítés befejezése után konfigurálhatja a vész-helyreállítási az Azure-beli virtuális gépek most a másodlagos régióban, amelyre áttelepített.
**AWS áttelepítése az Azure-ba** | AWS-példányokat is áttelepíthet Azure virtuális gépekre. A Site Recovery az AWS-példányokat áttelepítési célú fizikai kiszolgálóként kezeli. 

## <a name="next-steps"></a>További lépések

- [Helyszíni gépek áttelepítése az Azure-ba](migrate-tutorial-on-premises-azure.md)
- [Virtuális gépek áttelepítése egyik Azure-régióból a másikba](azure-to-azure-tutorial-migrate.md)
- [AWS áttelepítése az Azure-ba](migrate-tutorial-aws-azure.md)
