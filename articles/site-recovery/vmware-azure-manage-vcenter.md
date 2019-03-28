---
title: A VMware virtuális gépek vészhelyreállítása az Azure-ban az Azure Site Recovery VMware vCenter-kiszolgálók kezelése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adja hozzá, és a VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery VMware vCenter kezelése.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 03/13/2019
ms.author: ramamill
ms.openlocfilehash: 9694c682f171ab715812b05fed2064c9bbcd36b3
ms.sourcegitcommit: 6da4959d3a1ffcd8a781b709578668471ec6bf1b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2019
ms.locfileid: "58518082"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter-kiszolgáló kezelése

Ez a cikk ismerteti a különböző a Site Recovery-műveletek egy VMware vcenter végrehajtható. Ellenőrizze a [Előfeltételek](vmware-physical-azure-support-matrix.md#replicated-machines) megkezdése előtt.


## <a name="set-up-an-account-for-automatic-discovery"></a>Az automatikus felderítési fiók beállítása

A folyamatkiszolgáló automatikusan a virtuális gépek felderítése és a feladatátvétel és feladat-visszavételt a virtuális gépek a Site Recovery VMware hozzá kell férnie. Következőképpen hozhat létre egy fiókot a hozzáféréshez:

1. Jelentkezzen be a konfigurációs kiszolgáló gép.
2. Nyissa meg a bevezetésre a cspsconfigtool.exe használatával az asztali parancsikonjára.
3. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Adja meg a fiók adatait, és kattintson a **OK** hozzáadáshoz.  A fióknak rendelkeznie kell a jogosultságokat a következő táblázat foglalja össze. 

Felfelé a Site Recovery szolgáltatással való szinkronizálása megtörténik az fiók információk nagyjából 15 percet vesz igénybe.

### <a name="account-permissions"></a>Fiók engedélyei

|**Tevékenység** | **Fiók** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**Automatikus felderítés/Migrálása (nélkül a feladat-visszavétel)** | Legalább egy olvasási jogosultsággal rendelkező felhasználó van szüksége | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum, a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).|
|**Replikáció és feladatátvétel** | Legalább egy olvasási jogosultsággal rendelkező felhasználó van szüksége| Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).<br/><br/> Az áttelepítés során, de nem a teljes replikáció, feladatátvétel, feladat-visszavétel hasznos.|
|**Replikációs és feladatátvételi és feladat-visszavétel** | Javasoljuk, hozzon létre egy szerepkört (AzureSiteRecoveryRole) a szükséges engedélyekkel, és rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy csoporthoz | Data Center object –> Propagate to Child Object, role=AzureSiteRecoveryRole<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum, a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).|


## <a name="add-vmware-server-to-the-vault"></a>VMware-kiszolgáló hozzáadása a tárolóhoz

1. Az Azure Portalon nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs adatbázisai**, és nyissa meg a konfigurációs kiszolgálón.
2. Az a **részletek** kattintson **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Hitelesítő adatok módosítása

A vCenter-kiszolgálón vagy ESXi-gazdagéphez való kapcsolódáshoz a következőképpen használt hitelesítő adatok módosítása:

1. Jelentkezzen be a konfigurációs kiszolgálót, és indítsa el a cspsconfigtool.exe az asztalról.
2. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

   ![add-account](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Adja meg az új fiók adatait, és kattintson a **OK** hozzáadáshoz. A fióknak rendelkeznie kell a felsorolt jogosultságok [fent](#account-permissions).
4. Az Azure Portalon nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs adatbázisai**, és nyissa meg a konfigurációs kiszolgálón.
5. Az a **részletek** kattintson **kiszolgáló frissítése**.
6. A kiszolgáló frissítése feladat befejezése után jelölje ki a vCenter-kiszolgálóra, nyissa meg a vcenter-kiszolgáló **összefoglalás** lapot.
7. Válassza ki az újonnan hozzáadott fiókot a **vCenter-kiszolgáló vagy vSphere-gazdagép fiókja** mezőben, majd kattintson a **mentése**.

   ![modify-account](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>VCenter-kiszolgáló törlése

1. Az Azure Portalon nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs adatbázisai**, és nyissa meg a konfigurációs kiszolgálón.
2. Az a **részletek** lapon, válassza ki a vCenter-kiszolgáló.
3. Kattintson a **törlése** gombra.

   ![delete-account](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

## <a name="modify-the-vcenter-ip-address-and-port"></a>Módosíthatja a vCenter IP-cím és port

1. Jelentkezzen be az Azure portálra.
2. Navigáljon a **Recovery Services-tároló** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
3. Kattintson a konfigurációs kiszolgálón a vcenter-kiszolgáló hozzá van rendelve.
4. Az a **vCenter-kiszolgálók** területén kattintson a módosítani kívánt vcenter.
5. Az összefoglalás lapon vCenter frissíteni az IP-cím és port, a vcenter-kiszolgáló a megfelelő mezőben, és mentse a módosításokat.

   ![add_ip_new_vcenter](media/vmware-azure-manage-vcenter/add-ip.png)

6. A módosítások érvénybe, várjon 15 percig vagy [frissítse a konfigurációs kiszolgáló](vmware-azure-manage-configuration-server.md#refresh-configuration-server).

## <a name="migrate-all-protected-virtual-machines-to-a-new-vcenter"></a>Minden védett virtuális gépek áttelepítése egy új vCenter

A vcenter-kiszolgáló összes virtuális gépet át, ne adjon hozzá egy másik vCenter-fióknak. Ismétlődő bejegyzéseket vezethet. Csak frissítse a vcenter-kiszolgáló IP-címe:

1. Jelentkezzen be az Azure portálra.
2. Navigáljon a **Recovery Services-tároló** > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálók**.
3. Kattintson a konfigurációs kiszolgálón a régi vcenter-kiszolgáló hozzá van rendelve.
4. Az a **vCenter-kiszolgálók** területén kattintson a vCenter-át kíván.
5. Az összefoglalás lapon vCenter a mező új vCenter IP-címének frissítése **vCenter kiszolgálóhoz/vSphere állomásnév vagy IP-cím**. Mentse a módosításokat.

Az IP-cím frissül, amint Site Recovery-összetevők indul el a virtuális gépek felderítési adatokat fogad a vcenter-kiszolgáló. A változás nincs hatással a folyamatban lévő replikáció tevékenységeket.

## <a name="migrate-few-protected-virtual-machines-to-a-new-vcenter"></a>Egy új vCenter néhány védett virtuális gépek áttelepítése

> [!NOTE]
> Ez a szakasz akkor alkalmazható, csak ha végzi az áttelepítést a védett virtuális gépek néhány új vCenter. Ha azt szeretné, hogy a virtuális gépek új készletét védelmét egy új vcenter [adhatja hozzá új vCenter adatait a konfigurációs kiszolgáló](#add-vmware-server-to-the-vault) és kezdődhet  **[engedélyezni a védelmet](vmware-azure-tutorial.md#enable-replication)**.

Néhány virtuális gépek áthelyezése új vCenter:

1. [Adja hozzá az új vCenter adatait és a konfigurációs kiszolgáló](#add-vmware-server-to-the-vault).
2. [Tiltsa le a replikációt a virtuális gépek](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure) tervez áttelepíteni.
3. Kijelölt virtuális gépek, a vcenter-kiszolgáló áttelepítésének befejezéséhez.
4. Most a által áttelepített virtuális gépek védelme [kiválasztása a vcenter-kiszolgáló, ha engedélyezte a védelmet](vmware-azure-tutorial.md#enable-replication).

> [!TIP]
> Ha az áttelepítés alatt álló virtuális gépek száma **magasabb** , hogy megőrzi a régi vcenter-kiszolgáló, a virtuális gépek száma frissítése a az itt megadott utasítások szerint új vcenter-kiszolgáló IP-címét. Néhány virtuális gépek, amelyek megmaradnak a régi vcenter [tiltsa le a replikációt](site-recovery-manage-registration-and-protection.md#disable-protection-for-a-vmware-vm-or-physical-server-vmware-to-azure); [adhatja hozzá új vCenter adatait a konfigurációs kiszolgáló](#add-vmware-server-to-the-vault), és kezdje el  **[engedélyezni a védelmet](vmware-azure-tutorial.md#enable-replication)**.

## <a name="frequently-asked-questions"></a>Gyakori kérdések

1. Ha a védett virtuális gépek kerülnek egy ESXi gazdagépről a másikra, ez negatív hatással lesz replikációs?

    Nem, a változás nincs hatással a folyamatban lévő replikáció. Azonban [a fő célkiszolgáló elegendő jogosultságokkal a központi telepítését](vmware-azure-reprotect.md#deploy-a-separate-master-target-server)

2. Mik azok a portszámokat közötti kommunikációhoz használt vCenter és más Site Recovery összetevők?

    Az alapértelmezett port: 443-as porton. Konfigurációs kiszolgáló hozzá fog férni a vCenter/vSphere gazdagépre információkat ezen a porton keresztül. Ha szeretné ezeket az információkat, kattintson a [Itt](#modify-the-vcenter-ip-address-and-port).
