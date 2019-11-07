---
title: Kiszolgálók és virtuális gépek áttelepítése az Azure-ba Azure Site Recovery
description: Ismerteti, hogyan telepítheti át a helyszíni és az Azure IaaS virtuális gépeket az Azure-ba a Azure Site Recovery szolgáltatás használatával.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 8e256aac16bb8c2d2f1eca494981458f71cc2e4d
ms.sourcegitcommit: 6c2c97445f5d44c5b5974a5beb51a8733b0c2be7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/05/2019
ms.locfileid: "73620620"
---
# <a name="about-migration"></a>A migrálás ismertetése

Ebből a cikkből megtudhatja, hogyan segíti a [Azure site Recovery](site-recovery-overview.md) szolgáltatás a gépek áttelepítését. 

A Site Recovery használatával áttelepíthetők:

- **Migrálás a helyszínről az Azure**-ba: helyszíni Hyper-V virtuális gépek, VMWare virtuális gépek és fizikai kiszolgálók migrálása az Azure-ba. Az áttelepítés után a helyszíni gépeken futó számítási feladatok az Azure-beli virtuális gépeken fognak futni. 
- **Áttelepítés az Azure-on belül**: Azure-beli virtuális gépek áttelepítése Azure-régiók között. 
- **AWS áttelepítése**: AWS Windows-példányok áttelepítése Azure IaaS virtuális gépekre. 

> [!NOTE]
> Mostantól áttelepítheti a helyszínről az Azure-ba a Azure Migrate szolgáltatás használatával. [Részletek](../migrate/migrate-overview.md).

## <a name="what-do-we-mean-by-migration"></a>Mit értünk áttelepítés alatt?

A helyszíni és az Azure-beli virtuális gépek vész-helyreállítási Site Recovery használata mellett a Site Recovery szolgáltatás használatával is áttelepítheti őket. Mi a különbség?

- A vész-helyreállítás érdekében rendszeresen replikálja a gépeket az Azure-ba. Leállás esetén a gépeket a rendszer az elsődleges helyről a másodlagos Azure-helyre irányítja át, és onnan érheti el azokat. Ha az elsődleges hely ismét elérhetővé válik, az Azure-ból fog visszatérni.
- Az áttelepítés során a helyszíni gépeket az Azure-ba, vagy egy másodlagos régióba replikálja az Azure-ba. Ezután a virtuális gép az elsődleges helyről a másodlagosra kerül, és befejezi az áttelepítési folyamatot. Nincs szükség feladat-visszavételre.  


## <a name="migration-scenarios"></a>Áttelepítési forgatókönyvek

**Forgatókönyv** | **Részletek**
--- | ---
**Migrálás a helyszínről az Azure-ba** | Helyszíni VMware virtuális gépeket, Hyper-V virtuális gépeket és fizikai kiszolgálókat telepíthet át az Azure-ba. Ehhez szinte ugyanazokat a lépéseket kell végrehajtania, mint a teljes vész-helyreállításhoz. Egyszerűen nem tud számítógépeket visszaadni az Azure-ból a helyszíni helyre.
**Áttelepítés Azure-régiók között** | Azure-beli virtuális gépeket áttelepíthet egyik Azure-régióból a másikba. Az áttelepítés befejezése után az Azure-beli virtuális gépek vész-helyreállítását már a másodlagos régióban is konfigurálhatja, amelyre áttelepítette.
**AWS áttelepítése az Azure-ba** | AWS-példányokat is áttelepíthet Azure virtuális gépekre. Site Recovery az AWS-példányokat fizikai kiszolgálóként kezeli áttelepítési célokra. 

## <a name="next-steps"></a>További lépések

- [Helyszíni gépek áttelepítése az Azure-ba](migrate-tutorial-on-premises-azure.md)
- [Virtuális gépek áttelepítése egyik Azure-régióból a másikba](azure-to-azure-tutorial-migrate.md)
- [AWS áttelepítése az Azure-ba](migrate-tutorial-aws-azure.md)
