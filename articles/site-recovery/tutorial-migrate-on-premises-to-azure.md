---
title: "A helyszíni gépeket áttelepíteni az Azure szolgáltatásban az Azure Site Recovery |} Microsoft Docs"
description: "A cikkből megtudhatja, hogyan telepítheti át a helyszíni gépeket az Azure-bA az Azure Site Recovery."
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: ee9397406cbca21d8bd53019d9daac5a037f508c
ms.sourcegitcommit: 828cd4b47fbd7d7d620fbb93a592559256f9d234
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/18/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba

Használata mellett a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás kezeléséhez és vészhelyreállítás Azure virtuális gépek és a helyszíni gépeket levezényelni üzleti folytonossági és vészhelyreállítási (BCDR) céljából is használhatja a helyhez Visszaállítás a helyszíni gépeket az Azure-bA áttelepítésének kezelése.


Az oktatóanyag bemutatja, hogyan telepítheti át a helyszíni virtuális gépek és fizikai kiszolgálók Azure-bA. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Replikációs cél
> * A forrás és cél környezet beállítása
> * A replikációs házirend beállítása
> * A replikáció engedélyezése
> * Teszt migrálással azonban győződjön meg arról, hogy minden a várt módon működik
> * Egyszeri feladatátvételt végez az Azure

Ez az egy sorozat harmadik oktatóanyaga. Ez az oktatóanyag feltételezi, hogy már végrehajtotta a feladatokat a az előző oktatóanyagok:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. Készítse elő a helyi [VMware](tutorial-prepare-on-premises-vmware.md) vagy a Hyper-V kiszolgálók.

Mielőtt elkezdené, érdemes tekintse át a [VMware](concepts-vmware-to-azure-architecture.md) vagy [Hyper-V](concepts-hyper-v-to-azure-architecture.md) vész-helyreállítási architektúrák.


## <a name="prerequisites"></a>Előfeltételek

Paravirtualized illesztőprogramok által exportált eszközökön nem támogatottak.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Új** > **Felügyelet és kezelés** > **Backup és Site Recovery** lehetőségre.
3. A **neve**, adja meg a rövid név **ContosoVMVault**. Ha egynél több előfizetéssel rendelkezik, jelölje ki a megfelelő.
4. Hozzon létre egy erőforráscsoportot **ContosoRG**.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Gyors hozzáférést a tárolóhoz az irányítópultról, kattintson a **rögzítés az irányítópulton** majd **létrehozása**.

   ![Új tároló](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

Az új tároló hozzáadódik a **irányítópult** alatt **összes erőforrás**, majd a fő **Recovery Services-tárolók** lap.



## <a name="select-a-replication-goal"></a>Replikációs cél

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Recovery Services-tárolók** > tárolóban.
2. Az erőforrás menüjében kattintson **Site Recovery** > **infrastruktúra előkészítése** > **védelmi cél**.
3. A **védelmi cél**, válassza ki az áttelepíteni kívánt.
    - **VMware**: válasszon **az Azure-bA** > **Igen, amelyen a VMWare vSphere Hipervizorra**.
    - **Fizikai gép**: válasszon **az Azure-bA** > **nem virtualizált vagy egyéb**.
    - **A Hyper-V**: válasszon **Azure** > **Igen, a Hyper-V-vel**. Ha a Hyper-V virtuális gépek a VMM által felügyelt, válassza ki a **Igen**.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

- [Állítson be](tutorial-vmware-to-azure.md#set-up-the-source-environment) VMware virtuális gépek esetén a forrás környezetében.
- [Állítson be](tutorial-physical-to-azure.md#set-up-the-source-environment) fizikai kiszolgálók a forrás környezetében.
- [Állítson be](tutorial-hyper-v-to-azure.md#set-up-the-source-environment) Hyper-V virtuális gépek esetén a forrás környezetében.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki, és ellenőrizze a tároló erőforrásait.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager üzembe helyezési modellben.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="set-up-a-replication-policy"></a>A replikációs házirend beállítása

- [A replikációs házirend beállításához](tutorial-vmware-to-azure.md#create-a-replication-policy) VMware virtuális gépek esetén.
- [A replikációs házirend beállításához](tutorial-physical-to-azure.md#create-a-replication-policy) fizikai kiszolgálók számára.
- [A replikációs házirend beállításához](tutorial-hyper-v-to-azure.md#set-up-a-replication-policy) Hyper-V virtuális gépek esetén.


## <a name="enable-replication"></a>A replikáció engedélyezése

- [Engedélyezze a replikálást](tutorial-vmware-to-azure.md#enable-replication) VMware virtuális gépek esetén.
- [Engedélyezze a replikálást](tutorial-physical-to-azure.md#enable-replication) fizikai kiszolgálók számára.
- [Engedélyezze a replikálást](tutorial-hyper-v-to-azure.md#enable-replication) Hyper-V virtuális gépek esetén.


## <a name="run-a-test-migration"></a>Egy teszt áttelepítés futtatása

Futtassa a [feladatátvételi teszt](tutorial-dr-drill-azure.md) az Azure-ba, győződjön meg arról, hogy minden a várt módon működik.


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Az áttelepíteni kívánt gépek a feladatátvételt.

1. A **beállítások** > **replikált elemek** kattintson a gépre > **feladatátvételi**.
2. A **feladatátvételi** válassza ki a **helyreállítási pont** feladatokat. Válassza ki a legutóbbi helyreállítási pontot.
3. A titkosítási kulcs beállítás nem releváns ebben a forgatókönyvben.
4. Válassza ki **gép leállítása a feladatátvétel megkezdése előtt**. A Site Recovery megkísérli a forrás virtuális gépek leállítása tegye ahhoz, hogy kiváltsa a feladatátvételt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállítása sikertelen. A feladatátvételi folyamat előrehaladásának követheti a **feladatok** lap.
5. Ellenőrizze, hogy az Azure virtuális gép megjelenik az Azure-ban várt módon.
6. A **replikált elemek**, kattintson a jobb gombbal a virtuális gép > **az áttelepítés végrehajtásához**. Ez az áttelepítési folyamat befejeződik, leállítja a virtuális gép replikációs és a Site Recovery számlázási a virtuális gép leáll.

    ![Az áttelepítés végrehajtásához](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételi**: le van állítva a Virtuálisgép-replikációt, feladatátvétel megkezdése előtt. Ha megszakítja a folyamatban lévő feladatátvételi leáll, a feladatátvétel, de a virtuális gép nem fog újra replikálni.

Bizonyos esetekben feladatátvételi igényel, amely körülbelül nyolc-tíz perc végezze el a további feldolgozást. Bizonyára észrevette, hogy már a feladatátvételi idő a fizikai kiszolgálók, a VMware Linux gépek, a VMware virtuális gépek, amelyek nem rendelkeznek a DHCP szolgáltatás lehetővé teszi, hogy és a VMware virtuális gépek, amelyek nem rendelkeznek a következő rendszerindító illesztőprogramok tesztelése: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban a helyszíni virtuális gépek Azure virtuális gépek áttelepítése. Vész-helyreállítási most már konfigurálhatja az Azure virtuális gépekhez.

> [!div class="nextstepaction"]
> [Vész-helyreállítási](site-recovery-azure-to-azure-after-migration.md) Azure virtuális gépek egy helyszíni hely az áttelepítés után.
