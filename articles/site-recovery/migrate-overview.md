---
title: "Az Azure Site Recovery áttelepítésével kapcsolatos |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan telepítheti át a helyszíni és az Azure virtuális gépek az Azure Site Recovery szolgáltatással."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: raynew
ms.openlocfilehash: edf6ffe1cd55884f1c18498213df290cb19bb246
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="about-migration"></a>A migrálás ismertetése

Ebben a cikkben egy gyors áttekintés bemutatja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás segít gépek áttelepítéséhez. 

Ez akkor lehet áttelepíteni a Site Recovery segítségével:

- **Az Azure-bA helyszíni át**: telepítse át a helyszíni Hyper-V virtuális gépek, a VMware virtuális gépek és fizikai kiszolgálók Azure-bA. Az áttelepítés után a helyszíni gépeken futó számítási feladatok az Azure-beli virtuális gépeken fognak futni. 
- **Áttelepítés az Azure-on belül**: Azure-beli virtuális gépek áttelepítése Azure-régiók között. 
- **AWS áttelepítése**: AWS Windows-példányok áttelepítése Azure IaaS virtuális gépekre. 


## <a name="what-do-we-mean-by-migration"></a>Mit értünk áttelepítés alatt?

Áttelepített mellett a Site Recovery a helyszíni és az Azure virtuális gépek vész-helyreállítási, használhatja a Site Recovery szolgáltatásban. Mi a különbség?

- Vész-helyreállítási rendszeresen gépek az Azure-bA replikálni. Nem tervezett kimaradás esetén átveheti a gépeket az elsődleges helyről másodlagos Azure helyen, és ott elérhet. Az elsődleges hely újból elérhető lesz, ha nem sikerül az Azure-ból.
- Az áttelepítéshez replikálja a helyszíni gépeket Azure vagy Azure virtuális gépek a másodlagos régióba. Ezután átveheti a virtuális gép az elsődleges helyről másodlagos, és az áttelepítési folyamat befejezéséig. Nincs szükség feladat-visszavételre.  


## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

**Scenario** | **Részletek**
--- | ---
**Az Azure-bA helyszíni áttelepítése** | Áttelepítheti a helyszíni VMware virtuális gépek Hyper-V virtuális gépek és fizikai kiszolgálók Azure-bA. Ennek befejezése majdnem ugyanazokat a lépéseket az teljes vész-helyreállítási. Egyszerűen nem utasíthat el gépek az Azure-ból a helyszíni hely számára.
**Áttelepítés Azure-régiók között** | Azure virtuális gépek áttelepíthetők egy Azure-régió, egy másikra. Az áttelepítés befejezése után, az Azure virtuális gépek most már a másodlagos régióba, amelyre az áttelepítést végezte a vész-helyreállítási konfigurálhatja.
**AWS áttelepítése az Azure-ba** | AWS-példányokat is áttelepíthet Azure virtuális gépekre. A Site Recovery AWS példányok kezeli az áttelepítéshez a fizikai kiszolgálóként. 

## <a name="next-steps"></a>További lépések

- [Helyszíni gépek áttelepítése az Azure-ba](migrate-tutorial-on-premises-azure.md)
- [Virtuális gépek áttelepítése egyik Azure-régióból a másikba](azure-to-azure-tutorial-migrate.md)
- [AWS áttelepítése az Azure-ba](migrate-tutorial-aws-azure.md)
