---
title: VMware vCenter-kiszolgálók kezelése az Azure Site Recovery szolgáltatásban
description: Ez a cikk ismerteti, hogyan vehet hozzá és kezelhet VMware vCenter a VMware virtuális gépek vész-helyreállítási az Azure-ban az Azure Site Recovery.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 12/24/2019
ms.author: ramamill
ms.openlocfilehash: 01aef3aca4f6967b1681bff9598c7dd7a24739cd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257263"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter Server kezelése

Ez a cikk összefoglalja a Felügyeleti műveleteket egy VMware vCenter-kiszolgálón az [Azure Site Recovery szolgáltatásban.](site-recovery-overview.md)

## <a name="verify-prerequisites-for-vcenter-server"></a>A vCenter-kiszolgáló előfeltételeinek ellenőrzése

A vCenter-kiszolgálók és a virtuális gépek előfeltételei a VMware virtuális gépek Azure-ba irányuló vészhelyreállítása során a [támogatási mátrixban](vmware-physical-azure-support-matrix.md#replicated-machines)találhatók.

## <a name="set-up-an-account-for-automatic-discovery"></a>Fiók beállítása automatikus felderítéshez

Amikor beállítja a vész-helyreállítási helyszíni VMware virtuális gépek, Site Recovery hozzáférést kell biztosítania a vCenter Server/vSphere gazdagép. A Site Recovery folyamatkiszolgáló ezután automatikusan felderítheti a virtuális gépeket, és szükség szerint átveheti azokat. Alapértelmezés szerint a folyamatkiszolgáló a Site Recovery konfigurációs kiszolgálón fut. Adjon hozzá egy fiókot a konfigurációs kiszolgálóhoz a vCenter Server/vSphere állomáshoz való csatlakozáshoz az alábbiak szerint:

1. Jelentkezzen be a konfigurációs kiszolgálóra.
1. Nyissa meg a konfigurációs kiszolgáló eszközt (_cspsconfigtool.exe_) az Asztal parancsikonjával.
1. A **Fiók kezelése** lapon kattintson a **Fiók hozzáadása gombra.**

   ![kiegészítő fiók](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Adja meg a fiók adatait, majd kattintson az **OK** gombra a hozzáadáshoz. A fióknak a fiókengedélyek táblájában összesített jogosultságokkal kell rendelkeznie.

   > [!NOTE]
   > Körülbelül 15 percet vesz igénybe a fiókadatok szinkronizálása a Site Recovery-vel.

### <a name="account-permissions"></a>Fiókengedélyek

|**Tevékenység** | **Fiók** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**Virtuális gép felderítése/áttelepítése (feladat-visszavétel nélkül)** | Legalább egy írásvédett felhasználói fiók. | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **Nincs hozzáférési** szerepkört a **Propagálás gyermekobjektumhoz,** a gyermekobjektumokhoz (vSphere-állomásokhoz, adattárakhoz, virtuális gépekhez és hálózatokhoz).|
|**Replikáció/feladatátvétel** | Legalább egy írásvédett felhasználói fiók. | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **Nincs hozzáférési** szerepkört a **Propagálás gyermekobjektumokhoz** (vSphere-állomásokhoz, adattárakhoz, virtuális gépekhez és hálózatokhoz).<br/><br/> Áttelepítési célokra hasznos, de nem teljes replikációra, feladatátvételre, feladat-visszavételre.|
|**Replikáció/feladatátvétel/feladat-visszavétel** | Javasoljuk, hogy hozzon létre egy szerepkört (AzureSiteRecoveryRole) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy -csoporthoz. | Adatközpont-objektum – > propagálása gyermekobjektumba, role=AzureSiteRecoveryRole<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **Nincs hozzáférési** szerepkört a **Propagálás gyermekobjektumhoz,** a gyermekobjektumokhoz (vSphere-állomásokhoz, adattárakhoz, virtuális gépekhez és hálózatokhoz).|

## <a name="add-vmware-server-to-the-vault"></a>VMware-kiszolgáló hozzáadása a tárolóhoz

Amikor vészhelyreállítást állít be a helyszíni VMware virtuális gépekhez, hozzáadja azt a vCenter Server/vSphere-állomást, amelyen a virtuális gépeket észleli a Site Recovery-tárolóhoz, az alábbiak szerint:

1. A tárolóban > **Site Recovery Infrastructure** > **Configuration Severs**programban nyissa meg a konfigurációs kiszolgálót.
1. A **Részletek** lapon kattintson a **vCenter gombra.**
1. A **VCenter hozzáadása**csoportban adja meg a vSphere-gazdagép vagy a vCenter-kiszolgáló rövid nevét.
1. Adja meg a kiszolgáló IP-címét vagy teljes tartománynát.
1. A 443-as portot csak akkor módosítsa, ha a VMware-kiszolgálók úgy vannak konfigurálva, hogy más porton figyeljék a kéréseket.
1. Válassza ki a VMware vCenter vagy vSphere ESXi kiszolgálóhoz való csatlakozáshoz használt fiókot. Ezt követően kattintson az **OK** gombra.

## <a name="modify-credentials"></a>Hitelesítő adatok módosítása

Szükség esetén a vCenter Server/vSphere gazdagéphez való csatlakozáshoz használt hitelesítő adatokat az alábbiak szerint módosíthatja:

1. Jelentkezzen be a konfigurációs kiszolgálóra.
1. Nyissa meg a konfigurációs kiszolgáló eszközt (_cspsconfigtool.exe_) az Asztal parancsikonjával.
1. Kattintson a **Fiók** **hozzáadása** gombra a Fiók kezelése lapon.

   ![kiegészítő fiók](./media/vmware-azure-manage-vcenter/addaccount.png)

1. Adja meg az új fiók adatait, majd kattintson az **OK gombra.** A fióknak szüksége van a [fiókengedélyek](#account-permissions) táblájában felsorolt engedélyekre.
1. A tárolóban > **site recovery infrastruktúra** > **konfigurációs severs**, nyissa meg a konfigurációs kiszolgálót.
1. A **Részletek menüben**kattintson **a Kiszolgáló frissítése gombra.**
1. A Kiszolgáló frissítése feladat befejezése után válassza ki a vCenter-kiszolgálót.
1. Az **Összegzés csoportban**válassza ki az újonnan hozzáadott fiókot a **vCenter-kiszolgáló/vSphere gazdafiókban,** majd kattintson a **Mentés gombra.**

   ![módosítás-fiók](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>VCenter-kiszolgáló törlése

1. A tárolóban > **site recovery infrastruktúra** > **konfigurációs severs**, nyissa meg a konfigurációs kiszolgálót.
1. A **Részletek** lapon jelölje ki a vCenter-kiszolgálót.
1. Kattintson a **Törlés** gombra.

   ![törlés-fiók](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-ip-address-and-port"></a>Az IP-cím és a port módosítása

Módosíthatja a vCenter-kiszolgáló IP-címét, illetve a kiszolgáló és a site recovery közötti kommunikációhoz használt portokat. Alapértelmezés szerint a Site Recovery a vCenter Server/vSphere gazdagép adatait a 443-as porton keresztül éri el.

1. A tárolóban > **hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálói ,** kattintson a konfigurációs kiszolgáló, amelyhez a vCenter Server hozzá.
1. A **vCenter-kiszolgálókon**kattintson a módosítani kívánt vCenter-kiszolgálóra.
1. Az **Összegzés alkalmazásban**frissítse az IP-címet és a portot, és mentse a módosításokat.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

1. A módosítások hatálybalépéséhez várjon 15 percet, vagy [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-vms-to-a-new-server"></a>Az összes virtuális gép áttelepítése új kiszolgálóra

Ha azt szeretné, hogy az összes virtuális gépet egy új vCenter-kiszolgáló használatához, csak frissítenie kell a vCenter-kiszolgálóhoz rendelt IP-címet. Ne adjon hozzá újabb VMware-fiókot, mivel ez ismétlődő bejegyzésekhez vezethet. Frissítse a címet az alábbiak szerint:

1. A tárolóban > **hely-helyreállítási infrastruktúra** > **konfigurációs kiszolgálói ,** kattintson a konfigurációs kiszolgáló, amelyhez a vCenter Server hozzá.
1. A **vCenter-kiszolgálók** csoportban kattintson arra a vCenter-kiszolgálóra, amelyről át szeretne térni.
1. Az **összegzésben**frissítse az IP-címet az új vCenter-kiszolgáló ip-címére, és mentse a módosításokat.
1. Amint az IP-cím frissül, site recovery megkezdi a virtuális gép felderítési információk fogadása az új vCenter-kiszolgálóról. Ez nincs hatással a folyamatban lévő replikációs tevékenységekre.

## <a name="migrate-a-few-vms-to-a-new-server"></a>Néhány virtuális gép áttelepítése új kiszolgálóra

Ha csak néhány replikáló virtuális gépet szeretne áttelepíteni egy új virtuális központkiszolgálóra, tegye a következőket:

1. [Adja hozzá](#add-vmware-server-to-the-vault) az új vCenter-kiszolgálót a konfigurációs kiszolgálóhoz.
1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) az új kiszolgálóra átkerülő virtuális gépek replikációját.
1. A VMware-ben telepítse át a virtuális gépeket az új vCenter-kiszolgálóra.
1. [Engedélyezze](vmware-azure-tutorial.md#enable-replication) újra az áttelepített virtuális gépek replikációját, és jelölje ki az új vCenter-kiszolgálót.

## <a name="migrate-most-vms-to-a-new-server"></a>A legtöbb virtuális gép áttelepítése új kiszolgálóra

Ha az új vCenter-kiszolgálóra áttelepíteni kívánt virtuális gépek száma magasabb, mint az eredeti vCenter-kiszolgálón maradó virtuális gépek száma, tegye a következőket:

1. Frissítse a vCenter-kiszolgálóhoz rendelt [IP-címet](#modify-the-ip-address-and-port) a konfigurációs kiszolgáló beállításaiban az új vCenter-kiszolgáló címére.
1. [Tiltsa le](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) a replikációt a régi kiszolgálón maradó néhány virtuális gép replikációjával.
1. [Adja hozzá a régi vCenter-kiszolgálót](#add-vmware-server-to-the-vault) és annak IP-címét a konfigurációs kiszolgálóhoz.
1. [Engedélyezze újra](vmware-azure-tutorial.md#enable-replication) a replikációt a régi kiszolgálón maradó virtuális gépek számára.

## <a name="next-steps"></a>További lépések

Ha bármilyen problémája van, olvassa [el a vCenter Server felderítési hibáinak elhárítása című témakört.](vmware-azure-troubleshoot-vcenter-discovery-failures.md)
