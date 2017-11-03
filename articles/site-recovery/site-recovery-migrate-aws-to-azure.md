---
title: "Az Azure virtuális gépek áttelepítésére AWS |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan telepítheti át az Amazon Web Services (AWS) fut az Azure Site Recovery segítségével Azure virtuális gépek."
services: site-recovery
documentationcenter: 
author: bsiva
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/31/2017
ms.author: bsiva
ms.openlocfilehash: b3c0727a279649f4f7dae30d41027129ce5b04ee
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="migrate-virtual-machines-in-amazon-web-services-aws-to-azure-with-azure-site-recovery"></a>Az Azure Site Recovery Azure virtuális gépek Amazon Web Services (AWS) áttelepítése

Ez a cikk ismerteti, hogyan telepítheti át az AWS Windows-példányok Azure virtuális gépekre a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás.

Az áttelepítés akkor hatékonyan AWS a feladatátvétel az Azure-bA. Feladat-visszavételi gépek AWS nem lehet, és nincs folyamatban lévő replikáció van. Ez a cikk az Azure portálon áttelepítési lépéseit mutatja be, és a rendszer a használati [a fizikai gépek replikálása Azure-bA](site-recovery-vmware-to-azure.md).

Ez a cikk, vagy az alsó megjegyzések vagy kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

## <a name="supported-operating-systems"></a>Támogatott operációs rendszerek

A Site Recovery segítségével telepítse át a következő operációs rendszereket futtató EC2 példányok:

- Windows (csak a 64 bites)
    - Windows Server 2008 R2 SP1 + (Citrix PV illesztőprogramok vagy csak az AWS PV illesztőprogramok. **RedHat PV illesztőprogramok futó példányok nem támogatottak**) Windows Server 2012 Windows Server 2012 R2
- Linux (csak a 64 bites)
    - Red Hat Enterprise Linux 6.7 (csak HVM virtuális példány)

## <a name="prerequisites"></a>Előfeltételek

Ez mit kell ehhez a központi telepítéshez:

* **Konfigurációs kiszolgáló**: az Amazon EC2 virtuális gép Windows Server 2012 R2 rendszerű a konfigurációs kiszolgálón van telepítve. Alapértelmezés szerint az egyéb Azure Site Recovery (a folyamatkiszolgáló és fő célkiszolgáló) ezeket az összetevőket a konfigurációs kiszolgáló telepítésekor. Ez a cikk az Azure portálon áttelepítési lépéseit mutatja be, és a rendszer a használati [további](site-recovery-components.md)

* **EC2 példányok**: az Amazon EC2 áttelepíteni kívánt virtuális gépek példányokat.

## <a name="deployment-steps"></a>A központi telepítés lépései

1. Recovery Services-tároló létrehozása.
2. A biztonsági csoport a EC2 példányok konfigurálva ahhoz, hogy az áttelepíteni kívánt EC2-példány és a példány, amelyre a konfigurációs kiszolgáló telepítését tervezi közötti kommunikáció szabályokat kell.

3. A azonos Amazon virtuális Magánfelhő a EC2 példányként telepítse az automatikus rendszer-Helyreállítás konfigurációs kiszolgáló. Tekintse meg a VMware / fizikai a konfigurációs kiszolgáló telepítési követelményei Azure előfeltételeit.

    ![DeployCS](./media/site-recovery-migrate-aws-to-azure/migration_pic2.png)

4.  Amikor a konfigurációs kiszolgáló AWS üzembe helyezve, és a Recovery Services-tároló regisztrálva, megjelenik a konfigurációs kiszolgáló és a Site Recovery-infrastruktúra folyamatkiszolgáló alább látható módon:

    ![CSinVault](./media/site-recovery-migrate-aws-to-azure/migration_pic3.png)

5. A konfigurációs kiszolgálón (is telhet, mire megjeleníteni az legfeljebb 15 minustes) telepítése után ellenőrizze, képes kommunikálni az áttelepíteni kívánt virtuális gépek.

6. [Replikációs beállítások megadása](site-recovery-setup-replication-settings-vmware.md).

7. Engedélyezze a replikálást: engedélyezze a replikációját a virtuális gépeket szeretne áttelepíteni. A EC2 példányok a EC2 konzolról kaphat privát IP-címek segítségével felderíthetők.

    ![SelectVM](./media/site-recovery-migrate-aws-to-azure/migration_pic4.png)

8. Amennyiben a EC2 példányokat a védett, és az Azure-bA a replikáció befejeződött, [feladatátvételi teszt futtatása](site-recovery-test-failover-to-azure.md) ellenőrzése az alkalmazás teljesítmény az Azure-ban.

    ![TFI](./media/site-recovery-migrate-aws-to-azure/migration_pic5.png)

9. Futtassa a feladatátvétel AWS az Azure-bA az egyes virtuális gépek. Szükség esetén a helyreállítási terv létrehozása, és a feladatátvételt, több virtuális gép Azure AWS telepíthetők át. [További](site-recovery-create-recovery-plans.md) helyreállítási tervek.

10. Az áttelepítéséhez nem kell véglegesíteni a feladatátvételt. Ehelyett az egyes gépek szeretne áttelepíteni az áttelepítés végrehajtásához lehetőséget választotta. Az áttelepítés végrehajtásához művelet az áttelepítési folyamat befejezését, eltávolítja a gép replikálását, és leállítja a gépet a Site Recovery számlázási.

    ![Migrate (Áttelepítés)](./media/site-recovery-migrate-aws-to-azure/migration_pic6.png)

## <a name="next-steps"></a>Következő lépések

- [Migrált gépek felkészítése egy másik régióba való migrálás engedélyezéséhez](site-recovery-azure-to-azure-after-migration.md) vészhelyreállítás esetében.
- Gondoskodjon számítási feladatai védelméről az [Azure virtuális gépek replikálásával](site-recovery-azure-to-azure.md).
