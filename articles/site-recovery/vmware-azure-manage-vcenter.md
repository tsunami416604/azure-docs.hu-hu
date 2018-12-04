---
title: A VMware virtuális gépek vészhelyreállítása az Azure-ban az Azure Site Recovery VMware vCenter-kiszolgálók kezelése |} A Microsoft Docs
description: Ez a cikk azt ismerteti, hogyan adja hozzá, és a VMware virtuális gépek vészhelyreállítása az Azure-bA az Azure Site Recovery VMware vCenter kezelése.
author: Rajeswari-Mamilla
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: ramamill
ms.openlocfilehash: 6c00ed6f3bec1e16b2a3cb8588335c1741a92883
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52849037"
---
# <a name="manage-vmware-vcenter-server"></a>VMware vCenter-kiszolgáló kezelése

Ez a cikk ismerteti a különböző a Site Recovery-műveletek egy VMware vcenter végrehajtható. Ellenőrizze a [Előfeltételek](vmware-physical-azure-support-matrix.md#replicated-machines) megkezdése előtt.


## <a name="set-up-an-account-for-automatic-discovery"></a>Az automatikus felderítési fiók beállítása

A folyamatkiszolgáló automatikusan a virtuális gépek felderítése és a feladatátvétel és feladat-visszavételt a virtuális gépek a Site Recovery VMware hozzá kell férnie. Következőképpen hozhat létre egy fiókot a hozzáféréshez:

1. Jelentkezzen be a konfigurációs kiszolgáló gép.
2. Nyissa meg a bevezetésre a cspsconfigtool.exe használatával az asztali parancsikonjára.
3. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

  ![fiók hozzáadása](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Adja meg a fiók adatait, és kattintson a **OK** hozzáadáshoz.  A fióknak rendelkeznie kell a jogosultságokat a következő táblázat foglalja össze. 

Felfelé a Site Recovery szolgáltatással való szinkronizálása megtörténik az fiók információk nagyjából 15 percet vesz igénybe.

### <a name="account-permissions"></a>Fiók engedélyei

|**Tevékenység** | **Fiók** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**Automatikus felderítés/Migrálása (nélkül a feladat-visszavétel)** | Legalább egy olvasási jogosultsággal rendelkező felhasználó van szüksége | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum, a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).|
|**Replikáció és feladatátvétel** | Legalább egy olvasási jogosultsággal rendelkező felhasználó van szüksége| Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).<br/><br/> Az áttelepítés során, de nem a teljes replikáció, feladatátvétel, feladat-visszavétel hasznos.|
|**Replikációs és feladatátvételi és feladat-visszavétel** | Javasoljuk, hozzon létre egy szerepkört (AzureSiteRecoveryRole) a szükséges engedélyekkel, és rendelje hozzá a szerepkört egy VMware-felhasználóhoz vagy csoporthoz | Adatközpont-objektum gyermekobjektumba propagálás, szerepkör = AzureSiteRecoveryRole<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> Hozzáférés korlátozásához rendelje a **nincs hozzáférés** szerepkört a **Gyermekobjektumba** objektum, a gyermekobjektumokhoz (vSphere-gazdagépek, adattárolók, virtuális gépek és hálózatok).|


## <a name="add-vmware-server-to-the-vault"></a>VMware-kiszolgáló hozzáadása a tárolóhoz

1. Az Azure Portalon nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs adatbázisai**, és nyissa meg a konfigurációs kiszolgálón.
2. Az a **részletek** kattintson **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Hitelesítő adatok módosítása

A vCenter-kiszolgálón vagy ESXi-gazdagéphez való kapcsolódáshoz a következőképpen használt hitelesítő adatok módosítása:

1. Jelentkezzen be a konfigurációs kiszolgálót, és indítsa el a cspsconfigtool.exe az asztalról.
2. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

  ![fiók hozzáadása](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Adja meg az új fiók adatait, és kattintson a **OK** hozzáadáshoz. A fióknak rendelkeznie kell a felsorolt jogosultságok [fent](#account-permissions).
4. Az Azure Portalon nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs adatbázisai**, és nyissa meg a konfigurációs kiszolgálón.
5. Az a **részletek** kattintson **kiszolgáló frissítése**.
6. A kiszolgáló frissítése feladat befejezése után jelölje ki a vCenter-kiszolgálóra, nyissa meg a vcenter-kiszolgáló **összefoglalás** lapot.
7. Válassza ki az újonnan hozzáadott fiókot a **vCenter-kiszolgáló vagy vSphere-gazdagép fiókja** mezőben, majd kattintson a **mentése**.

    ![fiók módosítása](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>VCenter-kiszolgáló törlése

1. Az Azure Portalon nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs adatbázisai**, és nyissa meg a konfigurációs kiszolgálón.
2. Az a **részletek** lapon, válassza ki a vCenter-kiszolgáló.
3. Kattintson a **törlése** gombra.

  ![delete-fiók](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Ha szeretné módosítani a vCenter IP-címet, a teljes tartománynév vagy a port, majd szeretné törölni a vCenter-kiszolgáló, és adja hozzá újból a portálon.
