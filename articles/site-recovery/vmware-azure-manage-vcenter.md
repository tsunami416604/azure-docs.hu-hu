---
title: VMware vCenter-kiszolgálók kezelése a Azure Site Recoveryban
description: Ez a cikk bemutatja, hogyan veheti fel és kezelheti a VMWare virtuális gépek vész-helyreállítását az Azure-ba a Azure Site Recovery használatával.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 8f339103f67f37d10999ef43fa57a6eb27b60f37
ms.sourcegitcommit: a22cb7e641c6187315f0c6de9eb3734895d31b9d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2019
ms.locfileid: "74083977"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter-kiszolgáló kezelése

Ez a cikk a VMware-vCenter végrehajtható különböző Site Recovery műveleteket ismerteti. Mielőtt elkezdené, ellenőrizze az [előfeltételeket](vmware-physical-azure-support-matrix.md#replicated-machines) .


## <a name="set-up-an-account-for-automatic-discovery"></a>Fiók beállítása az automatikus felderítéshez

Site Recovery a virtuális gépek automatikus felderítéséhez, valamint a virtuális gépek feladatátvételéhez és feladat-visszavételéhez szükséges a VMware-hez való hozzáférés. Hozzon létre egy fiókot a hozzáféréshez az alábbiak szerint:

1. Jelentkezzen be a konfigurációs kiszolgáló számítógépére.
2. Nyissa meg a cspsconfigtool. exe elindítása az asztali parancsikon használatával.
3. Kattintson a **fiók hozzáadása** lehetőségre a **fiók kezelése** lapon.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Adja meg a fiók adatait, majd kattintson **az OK** gombra a hozzáadásához.  A fióknak a következő táblázatban foglalt jogosultságokkal kell rendelkeznie. 

Körülbelül 15 percet vesz igénybe, hogy a fiókadatok a Site Recovery szolgáltatással legyenek szinkronizálva.

### <a name="account-permissions"></a>Fiók engedélyei

|**Tevékenység** | **Fiók** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**Automatikus felderítés/áttelepítés (feladat-visszavétel nélkül)** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználónak kell lennie | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).|
|**Replikálás/feladatátvétel** | Legalább egy csak olvasási jogosultsággal rendelkező felhasználónak kell lennie| Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).<br/><br/> Áttelepítési célokra hasznos, de nem teljes replikáció, feladatátvétel, feladat-visszavétel.|
|**Replikáció/feladatátvétel/feladat-visszavétel** | Javasoljuk, hogy hozzon létre egy szerepkört (AzureSiteRecoveryRole) a szükséges engedélyekkel, majd rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy-csoporthoz. | Adatközpont-objektum – > propagálás a gyermek objektumba, szerepkör = AzureSiteRecoveryRole<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozásához rendelje hozzá a **nincs hozzáférési** szerepkört a **propagálás a gyermek** objektumhoz (vSphere gazdagépek, adattárolók, virtuális gépek és hálózatok).|


## <a name="add-vmware-server-to-the-vault"></a>VMware-kiszolgáló hozzáadása a tárolóhoz

1. A Azure Portal nyissa meg a tárolót > **site Recovery infrastruktúra** > **konfigurációs severs**, és nyissa meg a konfigurációs kiszolgálót.
2. A **részletek** lapon kattintson a **+ vCenter**elemre.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Hitelesítő adatok módosítása

Módosítsa a vCenter-kiszolgálóhoz vagy ESXi-gazdagéphez való kapcsolódáshoz használt hitelesítő adatokat a következőképpen:

1. Jelentkezzen be a konfigurációs kiszolgálóra, és indítsa el a cspsconfigtool. exe fájlt az asztalról.
2. Kattintson a **fiók hozzáadása** lehetőségre a **fiók kezelése** lapon.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Adja meg az új fiók adatait, majd kattintson **az OK** gombra a hozzáadásához. A fióknak a [fent](#account-permissions)felsorolt jogosultságokkal kell rendelkeznie.
4. A Azure Portal nyissa meg a tárolót > **site Recovery infrastruktúra** > **konfigurációs severok**csomópontot, és nyissa meg a konfigurációs kiszolgálót.
5. A **részletek** lapon kattintson a **kiszolgáló frissítése**elemre.
6. A kiszolgáló frissítése feladatot követően válassza ki a vCenter Server, hogy megnyissa a vCenter **összegzése** lapot.
7. Válassza ki az újonnan hozzáadott fiókot a **vCenter-kiszolgáló/vSphere-gazda fiók** mezőben, majd kattintson a **Mentés**gombra.

   ![fiók módosítása](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>VCenter-kiszolgáló törlése

1. A Azure Portal nyissa meg a tárolót > **site Recovery infrastruktúra** > **konfigurációs severs**, és nyissa meg a konfigurációs kiszolgálót.
2. A **részletek** lapon válassza ki a vCenter-kiszolgálót.
3. Kattintson a **Törlés** gombra.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>A vCenter IP-címének és portjának módosítása

1. Jelentkezzen be az Azure portálra.
2. Navigáljon **Recovery Services** tároló > **site Recovery infrastruktúra** > **konfigurációs kiszolgálók**elemre.
3. Kattintson arra a konfigurációs kiszolgálóra, amelyhez a vCenter hozzá van rendelve.
4. A **vCenter-kiszolgálók** szakaszban kattintson a módosítani kívánt vCenter.
5. A vCenter összegzése lapon frissítse a vCenter IP-címét és portját a megfelelő mezőkben, majd mentse a módosításokat.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. A módosítások érvénybe léptetéséhez várjon 15 percet, vagy [frissítse a konfigurációs kiszolgálót](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Az összes védett virtuális gép migrálása egy új vCenter

Ha az összes virtuális gépet át szeretné telepíteni az új vCenter, ne adjon hozzá újabb vCenter-fiókot. Ennek eredményeképpen ismétlődő bejegyzések hozhatók létre. Csak frissítse az új vCenter IP-címét:

1. Jelentkezzen be az Azure portálra.
2. Navigáljon **Recovery Services** tároló > **site Recovery infrastruktúra** > **konfigurációs kiszolgálók**elemre.
3. Kattintson arra a konfigurációs kiszolgálóra, amelyhez a régi vCenter hozzá van rendelve.
4. A **vCenter-kiszolgálók** szakaszban kattintson arra a vCenter, amelyet át szeretne telepíteni.
5. A vCenter összegzése lapon frissítse az új vCenter IP-címét a **vCenter-kiszolgáló/vSphere-állomásnév vagy az IP-cím**mezőben. Mentse a módosításokat.

Amint az IP-cím frissül, Site Recovery-összetevők elkezdik a virtuális gépek felderítési információinak fogadását az új vCenter. Ez a művelet nem érinti a folyamatban lévő replikációs tevékenységeket.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Néhány védett virtuális gép migrálása egy új vCenter

> [!NOTE]
> Ez a szakasz csak akkor alkalmazható, ha néhány védett virtuális gépet egy új vCenter telepít át. Ha új vCenter szeretné megóvni a virtuális gépek új készletét, [adja hozzá az új vCenter-adatokat a konfigurációs kiszolgálóhoz](#add-vmware-server-to-the-vault) , és kezdjen a **[védelem engedélyezése](vmware-azure-tutorial.md#enable-replication)** lehetőséggel.

Néhány virtuális gép áthelyezése egy új vCenter:

1. [Adja hozzá az új vCenter adatait a konfigurációs kiszolgálóhoz](#add-vmware-server-to-the-vault).
2. [Tiltsa le az áttelepíteni kívánt virtuális gépek replikációját](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) .
3. Fejezze be a kiválasztott virtuális gépek áttelepítését az új vCenter.
4. Most [a védelem engedélyezésekor az új vCenter kiválasztásával](vmware-azure-tutorial.md#enable-replication)gondoskodhat az áttelepített virtuális gépek védelméről.

> [!TIP]
> Ha az áttelepítendő virtuális gépek száma **nagyobb** , mint a régi vCenter megőrzött virtuális gépek száma, frissítse az új vCenter IP-címét az itt megadott utasítások alapján. A régi vCenter megőrzött néhány virtuális gép esetén [Tiltsa le a replikációt](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure). [adja hozzá az új vCenter adatait a konfigurációs kiszolgálóhoz](#add-vmware-server-to-the-vault), és indítsa el a **[védelem engedélyezése](vmware-azure-tutorial.md#enable-replication)** lehetőséget.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. Ha a védett virtuális gépeket egy ESXi-gazdagépről egy másikra helyezi át, az hatással lesz a replikációra?

    Nem, ez nem befolyásolja a folyamatban lévő replikálást. Azonban [Győződjön meg arról, hogy a fő célkiszolgáló megfelelő jogosultságokkal van telepítve](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Mik a vCenter és az egyéb Site Recovery-összetevők közötti kommunikációhoz használt portszámok?

    Az alapértelmezett port a 443. A konfigurációs kiszolgáló ezen a porton keresztül fér hozzá a vCenter/vSphere gazdagép adataihoz. Ha szeretné frissíteni ezeket az információkat, kattintson [ide](#modify-the-vcenter-ip-address-and-port).
