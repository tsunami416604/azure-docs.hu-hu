---
title: "A helyszíni gépeket áttelepíteni az Azure szolgáltatásban az Azure Site Recovery |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan telepítheti át a helyszíni gépeket az Azure-bA az Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: ddb412fd-32a8-4afa-9e39-738b11b91118
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 11/01/2017
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: cfd44f7f06faa7d1d00efa9427dbf5d1d0a89ef1
ms.sourcegitcommit: e462e5cca2424ce36423f9eff3a0cf250ac146ad
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/01/2017
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba

A [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeli, és koordinálja a replikációt, a feladatátvételi és a feladat-visszavétel a helyszíni gépeket, és az Azure virtuális gépek (VM).

Az oktatóanyag bemutatja, hogyan kell áttelepíteni a helyszíni virtuális gépek és fizikai kiszolgálók Azure Site Recovery szolgáltatással. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be a telepítési előfeltételek
> * A Site Recovery Recovery Services-tároló létrehozása
> * A helyi felügyeleti kiszolgálók központi telepítése
> * Állítson be egy replikációs házirendet és a replikáció engedélyezése
> * Futtassa egy vész-helyreállítási részletezéshez való győződjön meg arról, hogy minden működik
> * Egyszeri feladatátvételt végez az Azure

## <a name="overview"></a>Áttekintés

Telepít át a gépek replikációját engedélyezésével, és az Azure-bA feladatátvétele.


## <a name="prerequisites"></a>Előfeltételek

Ez az oktatóanyag elvégzéséhez szükséges.

- [Készítse elő](tutorial-prepare-azure.md) Azure-erőforrások, például az Azure-előfizetéssel, Azure virtuális hálózat és a storage-fiók.
- [Készítse elő](tutorial-prepare-on-premises-vmware.md) VMware helyszíni VMware-kiszolgálók és virtuális gépek.
- Vegye figyelembe, hogy paravirtualized illesztőprogramok által exportált eszközökön nem támogatottak.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Válassza ki a védelmi cél

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Recovery Services-tárolók** > tárolóban.
2. Az erőforrás menüjében kattintson **Site Recovery** > **infrastruktúra előkészítése** > **védelmi cél**.
3. A **védelmi cél**, jelölje be:
    - **VMware**: válasszon **az Azure-bA** > **Igen, amelyen a VMWare vSphere Hipervizorra**.
    - **Fizikai gép**: válasszon **az Azure-bA** > **nem virtualizált vagy egyéb**.
    - **A Hyper-V**: válasszon **Azure** > **Igen, a Hyper-V-vel**.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

- [Állítson be](tutorial-vmware-to-azure.md#set-up-the-source-environment) VMware virtuális gépek esetén a forrás környezetében.
- [Állítson be](tutorial-physical-to-azure.md#set-up-the-source-environment) fizikai kiszolgálók a forrás környezetében.
- [Állítson be](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) Hyper-V virtuális gépek esetén a forrás környezetében.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki, és ellenőrizze a tároló erőforrásait.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a tároló üzembe helyezési modellben.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

- [A replikációs házirend beállításához](tutorial-vmware-to-azure.md#create-a-replication-policy) VMware virtuális gépek esetén.


## <a name="enable-replication"></a>A replikáció engedélyezése

- [Engedélyezze a replikálást](tutorial-vmware-to-azure.md#enable-replication) VMware virtuális gépek esetén.


## <a name="run-a-test-migration"></a>Egy teszt áttelepítés futtatása

Futtassa a [feladatátvételi teszt](tutorial-dr-drill-azure.md) az Azure-ba, győződjön meg arról, hogy minden a várt módon működik.


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Az áttelepíteni kívánt gépek a feladatátvételt.

1. A **beállítások** > **replikált elemek** kattintson a gépre > **feladatátvételi**.
2. A **feladatátvételi** válassza ki a **helyreállítási pont** feladatokat. Válassza ki a legutóbbi helyreállítási pontot.
3. A titkosítási kulcs beállítás nem releváns ebben a forgatókönyvben.
4. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a hely helyreállításával lehet engedélyezi ezt a forrás virtuális gépek leállítása a feladatátvétel elindítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
5. Ellenőrizze, hogy az Azure virtuális gép megjelenik az Azure-ban várt módon.
6. A **replikált elemek**, kattintson a jobb gombbal a virtuális gép > **az áttelepítés végrehajtásához**. Ez az áttelepítési folyamat befejeződik, leállítja a virtuális gép replikációs és a Site Recovery számlázási a virtuális gép leáll.

    ![Az áttelepítés végrehajtásához](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: elindítani a feladatátvételt, mielőtt a virtuális gép replikációs le van állítva. Ha megszakítja a folyamatban lévő feladatátvételi leáll, a feladatátvétel, de a virtuális gép nem fog újra replikálni.

Bizonyos esetekben feladatátvételi igényel, amely körülbelül nyolc-tíz perc végezze el a további feldolgozást. Bizonyára észrevette, hogy már a feladatátvételi idő a fizikai kiszolgálók, a VMware Linux gépek, a VMware virtuális gépek, amelyek nem rendelkeznek a DHCP szolgáltatás lehetővé teszi, hogy és a VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramok tesztelése: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Egy másik régióban Azure virtuális gépek replikálása Azure való áttelepítése után](site-recovery-azure-to-azure-after-migration.md)
