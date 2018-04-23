---
title: Helyszíni gépek migrálása az Azure-ba az Azure Site Recoveryvel | Microsoft Docs
description: Ez a cikk bemutatja, hogyan migrálhatók a helyszíni gépek az Azure-ba az Azure Site Recovery használatával.
services: site-recovery
author: rayne-wiselman
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/18/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 406f0890da1ef4123b16082e7371d67f6328ea2c
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="migrate-on-premises-machines-to-azure"></a>Helyszíni gépek áttelepítése az Azure-ba

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás helyszíni gépek és Azure-beli virtuális gépek vészhelyreállításának az üzleti folytonosság biztosítása és vészhelyreállítás (BCDR) céljából való kezelésére és vezénylésére használható, valamint a helyi gépek az Azure-ba történő áttelepítésének kezelésére.


Az oktatóanyag bemutatja, hogyan migrálhatja a helyszíni virtuális gépeket és a fizikai kiszolgálókat az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Replikációs cél kiválasztása
> * Forrás- és célkörnyezet beállítása
> * Replikációs szabályzat beállítása
> * A replikáció engedélyezése
> * A várnak megfelelő működés ellenőrzése egy áttelepítési teszt futtatásával
> * Azure-ba irányuló egyszeri feladatátvételi teszt futtatása

Ez az oktatóanyag egy sorozat harmadik része. Ez az oktatóanyag feltételezi, hogy elvégezte az előző oktatóanyagok feladatait:

1. [Az Azure előkészítése](tutorial-prepare-azure.md)
2. Helyszíni [VMware](tutorial-prepare-on-premises-vmware.md) vagy Hyper-V kiszolgálók előkészítése.

Mielőtt elkezdené, érdemes áttekinteni a [VMware](concepts-vmware-to-azure-architecture.md) vagy a [Hyper-V](concepts-hyper-v-to-azure-architecture.md) architektúrát az esetleges vészhelyreállítás megkönnyítése érdekében.


## <a name="prerequisites"></a>Előfeltételek

A paravirtualizált illesztőprogramok által exportált eszközök nem támogatottak.


## <a name="create-a-recovery-services-vault"></a>Recovery Services-tároló létrehozása

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson az **Erőforrás létrehozása** > **Figyelés + felügyelet** > **Backup és Site Recovery** lehetőségre.
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több előfizetéssel rendelkezik, válassza ki ezek közül a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Ha gyors hozzáférést szeretne a tárolóhoz az irányítópultról, kattintson a **Rögzítés az irányítópulton**, majd a **Létrehozás** gombra.

   ![Új tároló](./media/tutorial-migrate-on-premises-to-azure/onprem-to-azure-vault.png)

Az új tároló megjelenik az **Irányítópult** **Minden erőforrás** részében, illetve a központi **Recovery Services-tárolók** oldalon.



## <a name="select-a-replication-goal"></a>Replikációs cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és hová.
1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Site Recovery** > **Az infrastruktúra előkészítése** > **Védelmi cél** lehetőségre.
3. A **Védelmi cél** ablakban válassza ki, hogy mit szeretne migrálni.
    - **VMware**: Válassza az **Azure-ba** > **Igen, a következővel: VMWare vSphere hipervizorral** lehetőséget.
    - **Fizikai gép**: Válassza az **Azure-ba** > **Nem virtualizált/egyéb** lehetőséget.
    - **Hyper-V**: Válassz az **Azure-ba** > **Igen, a következővel: Hyper-V** lehetőséget. Ha a Hyper-V virtuális gépeket egy VMM kezeli, válassza az **Igen** lehetőséget.


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

- [Állítsa be](tutorial-vmware-to-azure.md#set-up-the-source-environment) a forráskörnyezetet a VMware virtuális gépekhez.
- [Állítsa be](tutorial-physical-to-azure.md#set-up-the-source-environment) a forráskörnyezetet a fizikai kiszolgálókhoz.
- [Állítsa be](hyper-v-azure-tutorial.md#set-up-the-source-environment) a forráskörnyezetet a Hyper-V virtuális gépekhez.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a Resource Manager-alapú üzemi modell beállítást.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

## <a name="set-up-a-replication-policy"></a>Replikációs szabályzat beállítása

- [Állítsa be a replikációs szabályzatot](tutorial-vmware-to-azure.md#create-a-replication-policy) a VMware virtuális gépek számára.
- [Állítsa be a replikációs szabályzatot](tutorial-physical-to-azure.md#create-a-replication-policy) a fizikai kiszolgálók számára.
- [Állítsa be a replikációs szabályzatot](hyper-v-azure-tutorial.md#set-up-a-replication-policy) a Hyper-V virtuális gépek számára.


## <a name="enable-replication"></a>A replikáció engedélyezése

- [Engedélyezze a replikációt](tutorial-vmware-to-azure.md#enable-replication) a VMware virtuális gépek számára.
- [Engedélyezze a replikációt](tutorial-physical-to-azure.md#enable-replication) a fizikai kiszolgálók számára.
- [Engedélyezze a replikációt](hyper-v-azure-tutorial.md#enable-replication) a Hyper-V virtuális gépek számára.


## <a name="run-a-test-migration"></a>Migrálási teszt futtatása

A [rest failover](tutorial-dr-drill-azure.md) parancs Azure-ban történő futtatásával győződjön meg arról, hogy minden a vártnak megfelelően működik-e.


## <a name="migrate-to-azure"></a>Áttelepítés az Azure-ba

Futtasson egy feladatátvételt a migrálni kívánt gépen.

1. A **Beállítások** > **Replikált elemek** területen kattintson a gépre > **Feladatátvétel** ikonra.
2. A **Feladatátvétel** területen válassza ki a **Helyreállítási pontot** a feladatok átvételéhez. Válassza a legutóbbi helyreállítási pontot.
3. Ehhez a forgatókönyvhöz nem kell figyelembe venni a titkosítási kulcs beállítását.
4. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery a feladatátvitel indítása előtt megkísérli leállítani a forrás virtuális gépeket. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon.
5. Ellenőrizze, hogy az Azure-beli virtuális gép a várt módon jelenik-e meg az Azure-ban.
6. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Migrálás befejezése** parancsra. Ez befejezi a migrálási folyamatot, valamint leállítja a virtuális gép replikálását és a virtuális gép Site Recovery-számlázását.

    ![Az áttelepítés befejezése](./media/tutorial-migrate-on-premises-to-azure/complete-migration.png)


> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel indítása előtt a virtuális gép replikációja leáll. Ha megszakítja a folyamatban lévő feladatátvételt, az leáll, a virtuális gép replikációja azonban nem folytatódik.

Egyes forgatókönyvekben a feladatátvételhez további feldolgozás szükséges, ami körülbelül nyolc-tíz percet vesz igénybe. Bizonyára észrevette, hogy hosszabb a feladatátvételi teszt ideje a fizikai kiszolgálókon, a VMware Linux gépeken, a nem DHCP-vel kiosztott IP-című VMware virtuális gépeken, valamint a következő rendszerindító illesztőprogramokkal nem rendelkező VMware virtuális gépeken: storvsc, vmbus, storflt, intelide, atapi.


## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban helyszíni virtuális gépeket migrált Azure-beli virtuális gépekbe. Most konfigurálhatja az Azure-beli virtuális gépek vészhelyreállítását.

> [!div class="nextstepaction"]
> Az Azure-beli virtuális gépek [vészhelyreállítását](site-recovery-azure-to-azure-after-migration.md) a helyszíni helyről történő áttelepítést követően állítsa be.
