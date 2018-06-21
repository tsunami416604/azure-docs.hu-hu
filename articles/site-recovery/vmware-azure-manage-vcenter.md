---
title: " VMware vCenter Server az Azure Site Recovery kezelése |} Microsoft Docs"
description: Ez a cikk ismerteti, hogyan hozzáadása és kezelése az Azure Site Recovery a VMware vcenter programban.
author: AnoopVasudavan
ms.service: site-recovery
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: anoopkv
ms.openlocfilehash: 48b6cf9b90b429520df435aee00f57ea7b588748
ms.sourcegitcommit: d8ffb4a8cef3c6df8ab049a4540fc5e0fa7476ba
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/20/2018
ms.locfileid: "36284998"
---
# <a name="manage-vmware-vcenter-servers"></a>VMware vCenter-kiszolgálók kezelése 

A cikk ismerteti a különböző Site Recovery-műveleteket, amelyek a VMware vCenter végrehajtható. Ellenőrizze a [Előfeltételek](vmware-physical-azure-support-matrix.md#replicated-machines) megkezdése előtt.


## <a name="set-up-an-account-for-automatic-discovery"></a>Az automatikus felderítési fiókot

A Site Recovery VMware hozzáférésre van szüksége, a folyamatkiszolgálót a virtuális gépek automatikus észlelése és a feladatátvétel és a feladat-visszavételt a virtuális gépek. A hozzáférés-fiók létrehozása az alábbiak szerint:

1. Jelentkezzen be a konfigurációs kiszolgáló számítógépén.
2. Nyissa meg az indítási a cspsconfigtool.exe használatával az asztali parancsikonra.
3. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

  ![fiók hozzáadása](./media/vmware-azure-manage-vcenter/addaccount.png)
1. Adja meg a fiók adatait, majd kattintson a **OK** veheti fel.  A fióknak rendelkeznie kell a jogosultságokat az alábbi táblázat foglalja össze. 

A számítógépfiók adatai szinkronizálva a Site Recovery szolgáltatásban körülbelül 15 percet vesz igénybe.

### <a name="account-permissions"></a>Fiók engedélyeit

|**Tevékenység** | **Fiók** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**Automatikus felderítés/áttelepítése (nélkül a feladat-visszavétel)** | Legalább egy írásvédett felhasználó van szüksége | Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** objektum, a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok).|
|**Replikációs/feladatátvétel** | Legalább egy írásvédett felhasználó van szüksége| Adatközpont-objektum –> Gyermekobjektumba propagálás, szerepkör = csak olvasható | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok) objektum.<br/><br/> Akkor hasznos, ha az áttelepítési célokból, de nem a teljes replikáció, feladatátvétel, feladat-visszavétel.|
|**Replikációs és feladatátvételi/feladat-visszavételi** | Javasoljuk, hogy (AzureSiteRecoveryRole) szerepkör létrehozása a szükséges engedélyekkel, és hozzárendelheti a szerepkör egy VMware-felhasználó vagy csoport | Adatközpont objektum –> propagálása gyermekobjektum, a szerepkör = AzureSiteRecoveryRole<br/><br/> Adattároló -> Terület lefoglalása, adattároló böngészése, alacsony szintű fájlműveletek, fájl eltávolítása, virtuális gépek fájljainak frissítése<br/><br/> Hálózat -> Hálózat hozzárendelése<br/><br/> Erőforrás -> Virtuális gép hozzárendelése az erőforráskészlethez, kikapcsolt virtuális gép migrálása, bekapcsolt virtuális gép migrálása<br/><br/> Feladatok -> Feladat létrehozása, feladat frissítése<br/><br/> Virtuális gép -> Konfiguráció<br/><br/> Virtuális gép -> Használat -> Kérdés megválaszolása, eszközkapcsolat, CD-adathordozó konfigurálása, hajlékonylemezes adathordozó, kikapcsolás, bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuális gép -> Leltár -> Létrehozás, regisztrálás, regisztráció törlése<br/><br/> Virtuális gép -> Üzembe helyezés -> Virtuális gép letöltésének engedélyezése, virtuálisgépfájlok feltöltésének engedélyezése<br/><br/> Virtuális gép -> Pillanatképek -> Pillanatképek eltávolítása | A felhasználó az adatközpontszinten hozzárendelve, és hozzáféréssel rendelkezik az adatközpontban lévő összes objektumhoz.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** objektum, a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok).|


## <a name="add-vmware-server-to-the-vault"></a>VMware-kiszolgáló hozzáadása a tárolóhoz

1. Az Azure-portálon, nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálója**, és nyissa meg a konfigurációs kiszolgáló.
2. Az a **részletek** kattintson **+ vCenter**.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials"></a>Hitelesítő adatok módosítása

Módosítsa a hitelesítő adatokat, csatlakozhat a vCenter-kiszolgáló vagy ESXi-állomáson, az alábbiak szerint:

1. Jelentkezzen be a konfigurációs kiszolgáló, és indítsa el a cspsconfigtool.exe az asztalról.
2. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

  ![fiók hozzáadása](./media/vmware-azure-manage-vcenter/addaccount.png)
3. Adja meg az új fiók adatait, majd kattintson a **OK** veheti fel. A fióknak rendelkeznie kell a felsorolt jogosultságok [fent](#account-permissions).
4. Az Azure-portálon, nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálója**, és nyissa meg a konfigurációs kiszolgáló.
5. Az a **részletek** kattintson **kiszolgáló frissítését**.
6. A kiszolgáló frissítését feladat befejezése után válassza ki a vCenter-kiszolgálót, nyissa meg a vCenter **összegzés** lap.
7. Válassza ki az újonnan hozzáadott fiókot a **vCenter kiszolgáló vagy vSphere-gazdagép fiókja** mezőben, majd kattintson a **mentése**.

    ![-fiók módosítása](./media/vmware-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-server"></a>Törli a vCenter-kiszolgáló

1. Az Azure-portálon, nyissa meg a tároló > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálója**, és nyissa meg a konfigurációs kiszolgáló.
2. Az a **részletek** lapon, válassza ki a vCenter-kiszolgáló.
3. Kattintson a **törlése** gombra.

  ![törlés-fiók](./media/vmware-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Ha módosítania kell a vCenter IP-cím, az FQDN vagy a port, majd szeretné törölni a vCenter-kiszolgálót, és adja hozzá újra a portálon.
