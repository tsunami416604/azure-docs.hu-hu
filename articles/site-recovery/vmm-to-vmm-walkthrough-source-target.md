---
title: "A forrás és cél Hyper-V replikáció egy másodlagos helyre, az Azure Site Recovery beállítása |} Microsoft Docs"
description: "Ismerteti, hogyan kell beállítani a forrás és cél Hyper-V virtuális gépek replikálása az Azure Site Recovery másodlagos VMM-hely."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: fa7809f1-7633-425f-b25d-d10d004e8d0b
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/30/2017
ms.author: raynew
ms.openlocfilehash: 07135e9b5e619971a59cc22ec68a0a4e8bcaabe1
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="step-6-set-up-the-replication-source-and-target"></a>6. lépés: A replikáció forrás- és a cél beállítása


Miután létrehozta a Recovery Services tároló VMM egy másodlagos helyre, amelynek a Hyper-V replikáció [Azure Site Recovery](site-recovery-overview.md), ez a cikk használatával a forrás és cél replikációs helyek beállítása. 

A cikk elolvasása után felmerülő megjegyzéseit alul vagy az [Azure Recovery Services fórumban](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr) teheti közzé.




## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Az Azure Site Recovery Provider telepítése a VMM-kiszolgálókon, és Fedezze fel, és regisztrálja a kiszolgálót a tárolóban.

1. Kattintson a **1. lépés: infrastruktúra előkészítése** > **forrás**.

    ![A forrás beállítása](./media/vmm-to-vmm-walkthrough-source-target/goals-source.png)
2. A **Forrás előkészítése** ablakban kattintson a **+ VMM** gombra a VMM-kiszolgálók felvételéhez.

    ![A forrás beállítása](./media/vmm-to-vmm-walkthrough-source-target/set-source1.png)
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **System Center VMM-kiszolgáló** megjelenik **kiszolgálótípus** , és hogy a VMM-kiszolgáló megfelel-e a [Előfeltételek](#prerequisites).
4. Töltse le az Azure Site Recovery Provider telepítőfájlját.
5. Töltse le a regisztrációs kulcsot. Erre a telepítő futtatása során lesz szükség. A kulcs a generálásától számított öt napig érvényes.

    ![A forrás beállítása](./media/vmm-to-vmm-walkthrough-source-target/set-source3.png)
6. Telepítse az Azure Site Recovery Providert a VMM-kiszolgálóra. Nem kell explicit módon a Hyper-V gazdakiszolgálókra telepít semmit.


## <a name="install-the-azure-site-recovery-provider"></a>Az Azure Site Recovery Provider telepítése

1. Futtassa a Providert telepítőfájl minden VMM-kiszolgálón. Ha a VMM fürtben lett telepítve, tegye a következőket telepíti először listájában:
    -  Telepítse a szolgáltató aktív csomópontra, és fejezze be a telepítést, a VMM-kiszolgáló regisztrálása a tárolóban lévő állapottal.
    - Ezután telepítse a szolgáltatót a többi csomóponton. Fürtcsomópontok összes futtassa a szolgáltató azonos verziója.
2. A telepítő néhány előfeltétel-ellenőrzéseket futtatja, és a VMM szolgáltatás engedélyt kér. A VMM szolgáltatás automatikusan újraindul a telepítő befejezése után. Ha telepíti a VMM-fürthöz, megkéri a fürtszerepkör leállítása.
3. A **Microsoft Update**, lapon kérheti a adja meg, hogy szolgáltató frissítések telepítve vannak-e a Microsoft Update-szabályzatnak megfelelően.
4. A **telepítési**, fogadja el vagy módosítsa az alapértelmezett telepítési hely, és kattintson a **telepítése**.

    ![Telepítés helye](./media/vmm-to-vmm-walkthrough-source-target/provider-location.png)
5. Kattintson a telepítés befejezése után **regisztrálása** regisztrálni a kiszolgálót a tárolóban lévő állapottal.

    ![Telepítés helye](./media/vmm-to-vmm-walkthrough-source-target/provider-register.png)
6. A **Tároló neve** résznél ellenőrizze a tároló nevét, amelyben a kiszolgálót regisztrálni fogja. Kattintson a *Tovább* gombra.

    ![Kiszolgáló regisztrációja](./media/vmm-to-vmm-walkthrough-source-target/vaultcred.png)
7. A **internetkapcsolat**, adja meg, hogy a VMM-kiszolgálón futó provider hogyan csatlakozzon az Azure-bA.

    ![Internetbeállítások](./media/vmm-to-vmm-walkthrough-source-target/proxydetails.png)

   - Megadhatja, hogy a szolgáltató közvetlenül az internethez, vagy egy proxyn keresztül kell-e csatlakozni.
   - Adja meg a proxybeállításokat, ha szükséges.
   - A proxyt használ, ha a VMM RunAs-fiókot (DRAProxyAccount) jön létre automatikusan a megadott proxy hitelesítő adatok használatával. Állítsa be úgy a proxykiszolgálót, hogy ez a fiók elvégezhesse a hitelesítést. A RunAs fiók beállításait módosíthatja a VMM-konzol > **beállítások** > **biztonsági** > **futtató fiókok**. Indítsa újra a VMM szolgáltatást módosításainak frissítésére.
8. A **Regisztrációs kulcs** résznél válassza ki az Azure Site Recoveryből letöltött, majd a VMM-kiszolgálóra másolt kulcsot.
9. A titkosítási beállítást csak akkor használja a rendszer, amikor a VMM-felhőkben található Hyper-V virtuális gépeket az Azure-ba replikálja. Másodlagos helyre történő replikáláskor a beállítást nem használja a rendszer.
10. A **Kiszolgáló neve** mezőben adjon meg egy, a tárolóban regisztrált VMM-kiszolgálót azonosító rövid nevet. Fürtkonfiguráció használata esetén adja meg a VMM-fürtszerepkör nevét.
11. A **Synchronize cloud metaadatok**, adja meg, hogy a VMM-kiszolgálón futó összes felhő metaadatait szinkronizálni a tárolóval szeretné. Ezt a műveletet kiszolgálónként csak egyszer szükséges elvégezni. Ha nem kívánja az összes felhő szinkronizálásához, hagyja bejelölve ezt a beállítást, és szinkronizálja egyenként a felhő tulajdonságai a VMM-konzolon.
12. A folyamat befejezéséhez kattintson a **Next** (Tovább) gombra. A regisztrációt követően az Azure Site Recovery lekéri a metaadatokat VMM-kiszolgálóról. A kiszolgáló ezt követően megjelenik a tároló **Kiszolgálók** lapjának **VMM-kiszolgálók** lapján.

    ![Kiszolgáló](./media/vmm-to-vmm-walkthrough-source-target/provider13.png)
13. Miután a kiszolgáló elérhető, a Site Recovery konzolján a **forrás** > **forrás előkészítése** válassza ki a VMM-kiszolgálót, és válassza ki a felhőt, amelyben a Hyper-V gazdagépen helyezkedik el. Ezután kattintson az **OK** gombra.

A provider a parancssorból is telepíthető:

[!INCLUDE [site-recovery-rw-provider-command-line](../../includes/site-recovery-rw-provider-command-line.md)]


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki a cél VMM-kiszolgáló és a felhő:

1. Kattintson a **infrastruktúra előkészítése** > **cél**, és válassza ki a használni kívánt cél VMM-kiszolgálóval.
2. Felhők a kiszolgálón, amely szinkronizálva legyenek a Site Recovery jelenik meg. Válassza ki a megcélzott felhőt.

   ![cél](./media/vmm-to-vmm-walkthrough-source-target/target-vmm.png)



## <a name="next-steps"></a>Következő lépések

Ugrás a [7. lépés: hálózatleképezés konfigurálása](vmm-to-vmm-walkthrough-network-mapping.md).
