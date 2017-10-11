---
title: "A forrás és cél a VMware-replikáció az Azure szolgáltatásban az Azure Site Recovery beállítása |} Microsoft Docs"
description: "A forrás és cél beállítások megadása a VMware virtuális gépek replikálása az Azure storage az Azure Site Recovery ismertetett lépéseket foglalja"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: d99e422e-daf7-4fa8-af3c-af2340340136
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: 94b629a62c3a54eee69ee397b2f27e3f20b753d5
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="step-8-set-up-the-source-and-target-for-vmware-replication-to-azure"></a>8. lépés: A forrás- és a cél a VMware-replikáció az Azure beállítása

A cikk ismerteti a forrás és cél beállítások konfigurálása, ha a helyszíni VMware virtuális gépek replikálása Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgáló, és regisztrálja őket a tárolóban lévő virtuális gépek felderítése.

1. Kattintson a **helyreállítási hely** > **1. lépés: infrastruktúra előkészítése** > **forrás**.
2. Ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló**.
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a Site Recovery az egységes telepítő telepítőfájlját.
5. Töltse le a tárolóregisztrációs kulcsot. Ez szükséges az egységes telepítő futtatásakor. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Regisztrálja a konfigurációs kiszolgálót a tárolóban

Tegye a következőket előtt indítsa el, majd az egységes telepítőjének futtatásával telepítse a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló.
    - Gyors áttekintő videó beolvasása

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - A konfigurációs kiszolgálón VM, győződjön meg arról, hogy a rendszer órája szinkronizálva van a [kiszolgálót](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Meg kell felelnie. Ha az előtérben 15 perc vagy mögött található, a telepítés meghiúsulhat.
    - Futtassa a telepítőt a helyi rendszergazda, a virtuális gép konfigurációs kiszolgálón.
    - Győződjön meg arról, hogy a TLS 1.0 engedélyezve van a virtuális Gépen.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló is telepíthető [a parancssorból](http://aka.ms/installconfigsrv).



## <a name="connect-to-vmware-servers"></a>VMware-kiszolgálókkal

Ahhoz, hogy az Azure Site Recovery számára a helyszíni környezetben futó virtuális gépek felderítése, meg kell kapcsolni a VMware vCenter-kiszolgáló vagy vSphere ESXi-gazdagépek a Site Recovery. Mielőtt elkezdené, vegye figyelembe a következőket:

- Ha a vCenter-kiszolgáló vagy vSphere-gazdagép Site Recovery rendszergazdai jogosultságok nélküli fiókkal ad hozzá a kiszolgálón, a fiók ezeket a jogokat engedélyezett van szüksége:
    - Datacenter, Datastore, mappa, állomás, hálózati, erőforrás, a virtuális gép, vSphere elosztott kapcsoló.
    - A vCenter-kiszolgálót kell tárolási nézetek engedélyekkel.
- Amikor VMware-kiszolgálók ad hozzá a Site Recovery, 15 percbe is telhet, vagy hosszabb, hogy megjelenjenek a portálon.

### <a name="add-the-account-for-automatic-discovery"></a>Adja hozzá a fiókot a automatikus felderítése

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="set-up-a-connection"></a>Kapcsolat beállítása

Kapcsolódás a kiszolgálók az alábbiak szerint:

1. Válassza ki **+ vCenter** elindítani a VMware vCenter-kiszolgáló vagy egy VMware vSphere ESXi-állomáson.
2. A **vCenter hozzáadása** területen adjon meg egy rövid nevet a vSphere-gazdagép vagy a vCenter-kiszolgáló számára, majd adja meg a kiszolgáló IP-címét vagy teljes tartománynevét.
3. A 443-as portot csak akkor módosítsa, ha a VMware-kiszolgálók úgy vannak konfigurálva, hogy más porton figyeljék a kéréseket. Válassza ki a VMware vCenter- vagy vSphere ESXi-kiszolgálóhoz csatlakoztatni kívánt fiókot. Kattintson az **OK** gombra.
4. A Site Recovery VMware-kiszolgálók, a megadott beállítások csatlakozik, és felderíti a virtuális gépek.

> [!NOTE]
> Ha a kiszolgáló vagy a gazdagép egy olyan fiókkal, amely nem rendelkezik rendszergazdai jogosultságokkal a vCenter vagy a gazdagép-kiszolgálón, győződjön meg arról, hogy a fiók rendelkezik-e ezek a jogosultságok engedélyezve: Datacenter, Datastore, mappa, Host, hálózati, erőforrás, a virtuális gép, és a vSphere elosztott kapcsoló. Emellett a VMware vCenter-kiszolgálót kell a tárolási nézetek jogosultság engedélyezve van.


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

A célkörnyezet beállítása előtt győződjön meg arról, hogy az Azure storage-fiók és a virtuális hálózat beállítása.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg-e a cél telepítési modell a Resource Manager-alapú, vagy a klasszikus.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![cél](./media/vmware-walkthrough-source-target/gs-target.png)
4. Ha még nem hozott létre a storage-fiók vagy a hálózaton, kattintson a **+ tárfiók** vagy **+ hálózat**, az erőforrás-kezelő fiók létrehozása vagy szövegközi hálózati.

## <a name="next-steps"></a>Következő lépések

Ugrás a [9. lépés: a replikációs házirend beállítása](vmware-walkthrough-replication.md)
