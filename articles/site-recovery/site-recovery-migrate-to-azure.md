---
title: "Helyszíni virtuális gépek és fizikai kiszolgálók áttelepítése az Azure-ba a Site Recoveryvel | Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan telepítheti át a virtuális gépeket és fizikai kiszolgálókat az Azure-ba az Azure Site Recoveryvel"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c413efcd-d750-4b22-b34b-15bcaa03934a
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 10/30/2017
ms.author: raynew
ms.openlocfilehash: 423a1727efb0e1fd54eb0f8d5971ace3f8efc6cb
ms.sourcegitcommit: 43c3d0d61c008195a0177ec56bf0795dc103b8fa
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-to-azure-with-site-recovery"></a>Áttelepítés Azure-ra a Site Recoveryvel

Ebből a cikkből megtudhatja, hogyan használható az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni virtuális gépek és a fizikai kiszolgálók Azure-beli virtuális gépekre való áttelepítésére.

## <a name="before-you-start"></a>Előkészületek

Tekintse meg ezt a videót az Azure-ba való áttelepítéshez szükséges lépések rövid áttekintéséért.
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/ASRHowTo-Video2-Migrate-Virtual-Machines-to-Azure/player]


## <a name="what-do-we-mean-by-migration"></a>Mit értünk áttelepítés alatt?

A Site Recovery üzembe helyezésével replikálhatja és áttelepítheti a helyszíni virtuális gépeket és fizikai kiszolgálókat.

- A replikálás során konfigurálja a helyszíni gépeket, hogy rendszeresen replikálódjanak az Azure-ba. Így leállás esetén a rendszer átadja a gépek feladatait a helyszíni helyről az Azure-ba, ahonnan érheti el őket. Amint a helyszíni hely megint elérhetővé válik, a rendszer végrehajtja a feladat-visszavételt az Azure-ból.
- Ha a Site Recovery szolgáltatást használja az áttelepítéshez, az Azure-ba replikálja a helyszíni gépeket. Ezután a rendszer átadja a feladataikat a helyszíni helyről az Azure-ba, majd befejezi az áttelepítési folyamatot. Nincs szükség feladat-visszavételre.  

## <a name="what-can-site-recovery-migrate"></a>Melyen típusú áttelepítéseket lehet elvégezni a Site Recoveryvel?

A következőket teheti:

- **Áttelepítés a helyszínről**: Helyszíni Hyper-V virtuális gépek, VMware virtuális gépek és fizikai kiszolgálók áttelepítése az Azure-ba. Az áttelepítés után a helyszíni gépeken futó számítási feladatok az Azure-beli virtuális gépeken fognak futni. 
- **Áttelepítés az Azure-on belül**: Azure-beli virtuális gépek áttelepítése Azure-régiók között. 
- **AWS áttelepítése**: AWS Windows-példányok áttelepítése Azure IaaS virtuális gépekre. 

## <a name="migrate-from-on-premises-to-azure"></a>Áttelepítés a helyszínről az Azure-ba

Helyszíni VMware virtuális gépek, Hyper-V virtuális gépek, illetve fizikai kiszolgálók áttelepítéséhez szinte ugyanazokat a lépéseket kell követnie, amelyeket a teljes replikációnál használ. 


## <a name="migrate-between-azure-regions"></a>Áttelepítés Azure-régiók között

Az Azure-beli virtuális gépek régiók közötti áttelepítéséhez szinte ugyanazokat a lépéseket kell követnie, amelyeket a teljes áttelepítésnél használ.

1. [Engedélyezze a replikációt](azure-to-azure-tutorial-enable-replication.md) az áttelepíteni kívánt gépeknél.
2. Egy [gyors feladatátvételi teszt futtatásával](azure-to-azure-tutorial-dr-drill.md) ellenőrizze, hogy minden jól működik-e.
3. Ezután az **Áttelepítés befejezése** lehetőséget használva futtasson egy [nem tervezett feladatátvételt](azure-to-azure-tutorial-failover-failback.md).
4. Az áttelepítés befejezése után [vészhelyreállításhoz is beállíthat replikációt](site-recovery-azure-to-azure-after-migration.md) abból az Azure-régióból, amelybe az áttelepítést végezte, egy másodlagos régióba.



## <a name="migrate-aws-to-azure"></a>AWS áttelepítése Azure-ra

AWS-példányokat is áttelepíthet Azure virtuális gépekre.
- Ebben a forgatókönyvben jelenleg csak az áttelepítés támogatott. Ez azt jelenti, hogy replikálhat AWS-példányokat és átirányíthatja azokat az Azure-ba, de feladat-visszavételt nem hajthat végre.
- Az áttelepítés során az AWS-példányokat a szolgáltatás ugyanúgy kezeli, mint a fizikai kiszolgálókat. Állítsa be a Recovery Services-tárolót, a replikáció felügyeletéhez helyezzen üzembe egy helyszíni konfigurációs kiszolgálót, adja hozzá a tárolóhoz, majd adja meg a replikációs beállításokat.
- Engedélyezze a replikációt azoknál a gépeknél, amelyeket szeretné áttelepíteni, majd futtasson le egy gyors feladatátvételi tesztet. Ezután az **Áttelepítés befejezése** lehetőséget használva futtasson egy nem tervezett feladatátvételt.






## <a name="next-steps"></a>Következő lépések

- [Helyszíni gépek áttelepítése az Azure-ba](tutorial-migrate-on-premises-to-azure.md)
- [Virtuális gépek áttelepítése egyik Azure-régióból a másikba](site-recovery-migrate-azure-to-azure.md)
- [AWS áttelepítése az Azure-ba](tutorial-migrate-aws-to-azure.md)
