---
title: " Az Azure Site Recovery VMware vCenter-kiszolgáló kezelése |} Microsoft Docs"
description: "Ez a cikk ismerteti, hogyan hozzáadása és kezelése az Azure Site Recovery a VMware vcenter programban."
services: site-recovery
documentationcenter: 
author: AnoopVasudavan
manager: gauravd
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: backup-recovery
ms.date: 06/29/2017
ms.author: anoopkv
ms.openlocfilehash: 091f0884417535427c52beee7bcdc5ed1dd83315
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="manage-vmware-vcenter-server-in-azure-site-recovery"></a>VMware vCenter Server az Azure Site Recovery kezelése
A cikk ismerteti a különböző Site Recovery-műveleteket, amelyek a VMware vCenter végrehajtható.

## <a name="prerequisites"></a>Előfeltételek

**Támogatja a VMware vCenter és az ESX-gazdagépen VMware vSphere** | **Részletek** |
|--- | --- |
|**Helyszíni VMware-kiszolgálók** | Egy vagy több VMware vSphere, futtató kiszolgálók 6.0, 5.5, 5.1 legújabb frissítéseit. Kiszolgálók ugyanazon a hálózaton, mint a konfigurációs kiszolgáló (vagy különálló folyamatkiszolgálót) kell elhelyezni.<br/><br/> Azt javasoljuk, hogy a vCenter-kiszolgálót a legújabb frissítésekkel rendelkező 6.0 vagy 5.5 rendszerű gazdagépek felügyeletéhez. Csak azok a szolgáltatások által biztosított 5.5 6.0-s verzió telepítésekor támogatottak.|

## <a name="prepare-an-account-for-automatic-discovery"></a>Az automatikus felderítési fiók előkészítése
A Site Recovery VMware hozzáférésre van szüksége, a folyamatkiszolgálót a virtuális gépek automatikus észlelése és a feladatátvétel és a feladat-visszavételt a virtuális gépek.

* **Telepítse át**: csak áttelepíteni kívánt VMware virtuális gépek Azure-ba, anélkül, hogy legalább egyszer sikertelen vissza őket, ha használható a VMware-fiók egy csak olvasható szerepkör. Ilyen szerepkör feladatátvételi futtatható, de nem lehet leállítani a védett forrásgépek. Erre nincs szükség áttelepítésre.
* **Replikálás/Recover**: a teljes replikáció (replikálja, feladatátvétel, feladat-visszavétel) telepíteni szeretné a fiókot képesnek kell lennie műveletek, például a létrehozásával és lemezek, a virtuális gép bekapcsolása eltávolításával futtatásához.
* **Automatikus felderítés**: legalább egy csak olvasható fiókot kell megadni.


|**Feladatok** | **Szükséges fiók/szerepkör** | **Engedélyek** | **Részletek**|
|--- | --- | --- | ---|
|**A folyamatkiszolgáló automatikusan felderíti a VMware virtuális gépek** | Legalább egy írásvédett felhasználó van szüksége | Adatközpont objektum –> propagálása gyermekobjektum, a szerepkör = csak olvasható | Felhasználói datacenter szinten hozzárendelt, és rendelkezik hozzáféréssel az objektumokhoz az adatközpontban.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** objektum, a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok).|
|**Feladatátvétel** | Legalább egy írásvédett felhasználó van szüksége | Adatközpont objektum –> propagálása gyermekobjektum, a szerepkör = csak olvasható | Felhasználói datacenter szinten hozzárendelt, és rendelkezik hozzáféréssel az objektumokhoz az adatközpontban.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok) objektum.<br/><br/> Akkor hasznos, ha az áttelepítési célokból, de nem a teljes replikáció, feladatátvétel, feladat-visszavétel.|
|**A feladatátvételi és a feladat-visszavétel** | Javasoljuk, hogy (AzureSiteRecoveryRole) szerepkör létrehozása a szükséges engedélyekkel, és hozzárendelheti a szerepkör egy VMware-felhasználó vagy csoport | Adatközpont objektum –> propagálása gyermekobjektum, a szerepkör = AzureSiteRecoveryRole<br/><br/> Datastore foglaljon le terület ->, keresse meg az adattároló, alacsony szintű fájlműveletek, távolítsa el a fájlt, frissítse a virtuális géphez tartozó fájlokat<br/><br/> Hálózat -> hálózat hozzárendelése<br/><br/> Erőforrás -> rendelje hozzá a virtuális gép erőforráskészlethez, energiaforrással rendelkező ki a virtuális gép áttelepítése, energiaforrással rendelkező, a virtuális gép áttelepítése<br/><br/> Feladatok -> hozzon létre feladat, a frissítési feladat<br/><br/> Virtuális gép konfigurációja -><br/><br/> Virtuálisgép -> kommunikáció -> válasz kérdést, az eszköz kapcsolat, a CD media konfigurálásához, a konfigurálásához a hajlékonylemezes adathordozót, kapcsolja ki, a bekapcsolás, VMware-eszközök telepítése<br/><br/> Virtuálisgép -> leltár -> létrehozási, regisztrálása, unregister<br/><br/> Virtuálisgép -> kiépítés engedélyezése virtuális gép letöltési ->, hogy a virtuálisgép-fájlok feltöltése<br/><br/> Virtuális gép pillanatkép ->-Remove pillanatképek > | Felhasználói datacenter szinten hozzárendelt, és rendelkezik hozzáféréssel az objektumokhoz az adatközpontban.<br/><br/> A hozzáférés korlátozása érdekében rendelje hozzá a **nem lehet hozzáférni** szerepkört a **gyermekre propagálása** objektum, a gyermekobjektumokra (vSphere-gazdagép, datastores, virtuális gépek és hálózatok).|

## <a name="create-an-account-to-connect-to-vmware-vcenter-server-vmware-vsphere-exsi-host"></a>Csatlakozni a VMware vCenter Server-fiók létrehozása / VMware vSphere EXSi állomás
1. Bejelentkezés a konfigurációs kiszolgáló és a cspsconfigtool.exe a parancsikont az asztalon elhelyezett használatával indítsa el a.
2. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

  ![fiók hozzáadása](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Adja meg a fiók adatait, és kattintson az OK gombra, adja hozzá a fiókot. A fióknak a felsorolt jogosultsággal kell rendelkeznie a [fiók előkészítése automatikus felderítés](#prepare-an-account-for-automatic-discovery) szakasz.

  >[!NOTE]
  A számítógépfiók adatai szinkronizálva a Site Recovery szolgáltatásban körülbelül 15 percet vesz igénybe.


## <a name="associate-a-vmware-vcenter-vmware-vsphere-esx-host-add-vcenter"></a>Társítsa a VMware vCenter / VMware vSphere ESX-gazdagép (Hozzáadás vCenter)
* Keresse meg az Azure-portálon a *YourRecoveryServicesVault* > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálója**  >  *ConfigurationServer*
* A konfigurációs kiszolgáló részleteit megjelenítő oldalon kattintson a + vCenter gombra.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

## <a name="modify-credentials-used-to-connect-to-the-vcenter-server-vsphere-esxi-host"></a>Módosítsa a vCenter-kiszolgálóhoz való csatlakozáshoz használt hitelesítő adatok / ESXi-állomáson vSphere

1. Bejelentkezés a konfigurációs kiszolgáló és a cspsconfigtool.exe indítsa el a
2. Kattintson a **fiók hozzáadása** a a **fiók kezelése** fülre.

  ![fiók hozzáadása](./media/site-recovery-vmware-to-azure-manage-vcenter/addaccount.png)
3. Adja meg az új fiók adatait, és kattintson az OK gombra, adja hozzá a fiókot. A fióknak a felsorolt jogosultsággal kell rendelkeznie a [fiók előkészítése automatikus felderítés](#prepare-an-account-for-automatic-discovery) szakasz.
4. Keresse meg az Azure-portálon a *YourRecoveryServicesVault* > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálója**  >  *ConfigurationServer*
5. A konfigurációs kiszolgáló részleteit megjelenítő oldalon kattintson a **kiszolgáló frissítését** gombra.
6. Miután befejeződött a kiszolgáló-frissítési feladathoz, válassza ki a vCenter-kiszolgáló a vCenter összefoglaló lap megnyitásához.
7. Válassza ki az újonnan hozzáadott fiókot a **vCenter kiszolgáló vagy vSphere-gazdagép fiókja** mezőben, majd kattintson a **mentése** gombra.

  ![-fiók módosítása](./media/site-recovery-vmware-to-azure-manage-vcenter/modify-vcente-creds.png)

## <a name="delete-a-vcenter-in-azure-site-recovery"></a>Az Azure Site Recovery vCenter törlése
1. Keresse meg az Azure-portálon a *YourRecoveryServicesVault* > **Site Recovery-infrastruktúra** > **konfigurációs kiszolgálója**  >  *ConfigurationServer*
2. A konfigurációs kiszolgáló részleteit megjelenítő oldalon válassza ki a vCenter-kiszolgáló a vCenter összefoglaló lap megnyitásához.
3. Kattintson a **törlése** törli a vCenter gomb

  ![törlés-fiók](./media/site-recovery-vmware-to-azure-manage-vcenter/delete-vcenter.png)

> [!NOTE]
Ha módosítania kell a Vcenter IP-cím vagy teljes tartománynév, a kapcsolóport részletei majd szeretné törölni a vCenter-kiszolgálóhoz, és vissza újra hozzáadni.
