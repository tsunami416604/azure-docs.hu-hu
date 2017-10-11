---
title: "A forrás és cél a fizikai kiszolgáló replikáció az Azure szolgáltatásban az Azure Site Recovery beállítása |} Microsoft Docs"
description: "A forrás és cél beállítások megadása az Azure Site Recovery szolgáltatásban az Azure storage fizikai kiszolgálók replikálása ismertetett lépéseket foglalja"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: 2e3d03bc-4e53-4590-8a01-f702d3cc9500
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.openlocfilehash: e89bbf5a2c1d71852e49da43d3106a05ebfc28a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="step-7-set-up-the-source-and-target-for-physical-server-replication-to-azure"></a>7. lépés: A forrás- és a cél a fizikai kiszolgáló replikáció az Azure beállítása

A cikk ismerteti a forrás és cél beállítások konfigurálása, ha a helyszíni fizikai kiszolgálók replikálása Azure-ba, használja a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás az Azure portálon.

Ebben a cikkben, vagy az alsó megjegyzések és kérdéseket küldje a [Azure Recovery Services fórumon](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgáló, regisztrálja őket a tárolóban, és a gépek észlelése.

1. Kattintson a **helyreállítási hely** > **1. lépés: infrastruktúra előkészítése** > **forrás**.
2. Ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló**.
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a Site Recovery az egységes telepítő telepítőfájlját.
5. Töltse le a tárolóregisztrációs kulcsot. Ez szükséges az egységes telepítő futtatásakor. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/vmware-walkthrough-source-target/set-source2.png)


## <a name="register-the-configuration-server-in-the-vault"></a>Regisztrálja a konfigurációs kiszolgálót a tárolóban

Tegye a következőket előtt indítsa el, majd az egységes telepítőjének futtatásával telepítse a konfigurációs kiszolgáló, a folyamatkiszolgáló és a fő célkiszolgáló. A videó VMware virtuális gép Azure replikációs összetevők beállításának a módját ismerteti. Azonban ugyanazt a folyamatot nem érvényes Azure-replikációs szolgáltatására való fizikai kiszolgálóra.

- A konfigurációs kiszolgálón VM, győződjön meg arról, hogy a rendszer órája szinkronizálva van a [kiszolgálót](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Meg kell felelnie. Ha az előtérben 15 perc vagy mögött található, a telepítés meghiúsulhat.
- Futtassa a telepítőt a helyi rendszergazda, a konfigurációs kiszolgáló gépen.
- Győződjön meg arról, hogy a TLS 1.0 engedélyezve van a virtuális Gépen.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> A konfigurációs kiszolgáló is telepíthető [a parancssorból](http://aka.ms/installconfigsrv).




## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

A célkörnyezet beállítása előtt győződjön meg arról, hogy az Azure storage-fiók és a virtuális hálózat beállítása.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg-e a cél telepítési modell a Resource Manager-alapú, vagy a klasszikus.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![cél](./media/physical-walkthrough-source-target/gs-target.png)

4. Ha még nem hozott létre a storage-fiók vagy a hálózaton, kattintson a **+ tárfiók** vagy **+ hálózat**, az erőforrás-kezelő fiók létrehozása vagy szövegközi hálózati.

## <a name="next-steps"></a>Következő lépések

Ugrás a [8. lépés: a replikációs házirend beállítása](physical-walkthrough-replication.md)
