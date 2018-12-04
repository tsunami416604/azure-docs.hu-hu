---
title: A VMware-vészhelyreállításhoz az Azure-bA az Azure Site Recovery replikációs szabályzatok konfigurálására és felügyeletére |} A Microsoft Docs
description: Útmutatás a vész-helyreállítási VMware – Azure replikálás beállításainak konfigurálása az Azure Site Recoveryvel.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: e83b15b5e53a203a3583c02565ea9c316b7c481c
ms.sourcegitcommit: 11d8ce8cd720a1ec6ca130e118489c6459e04114
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/04/2018
ms.locfileid: "52832370"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Vész-helyreállítási VMware – Azure replikációs szabályzatok konfigurálására és felügyeletére
Ez a cikk ismerteti a replikációs szabályzat konfigurálása, Ön VMware virtuális gépek replikálása az Azure-ba, amikor használatával [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Szabályzat létrehozása

1. Válassza a **Kezelés** > **Site Recovery-infrastruktúra** lehetőséget.
1. A **a VMware és fizikai gépek**válassza **replikációs házirendek**. 
1. Kattintson a **+ replikációs házirend**, és adja meg a házirend nevét.
1. Az **RPO küszöbértéke** beállításnál adja meg az RPO-korlátot. Riasztások akkor jönnek létre, ha a folyamatos replikáció túllépi ezt a korlátot.
1. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama (a beállítás értéke órákban adható meg). A védelemmel ellátott gépeket az adatmegőrzési időtartamon belüli bármelyik pontra visszaállíthatja. A rendszer a prémium tárolóra replikált gépek esetében 24 órás megőrzést támogat. Akár 72 órát standard tárterület esetén támogatott.
1. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke percekben adható meg).
1. Kattintson az **OK** gombra. A házirend létrehozása 30–60 másodpercet vesz igénybe.

Amikor létrehoz egy replikációs házirendet, a megfelelő feladatátvételi replikációs házirend automatikusan létrejön, a "feladat-visszavétel" utótaggal. A szabályzat létrehozása után szerkesztheti ezt, ha kiválasztja > **beállításainak szerkesztése**.

## <a name="associate-a-configuration-server"></a>Konfigurációs kiszolgáló társítása 

A replikációs házirend társítása a helyszíni konfigurációs kiszolgálót.

1. Kattintson a **társítása**, és válassza ki a konfigurációs kiszolgálót.

    ![Konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate1.png)

1. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának végrehajtása átlagosan 1–2 percet vesz igénybe.

    ![A konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Szüntesse meg az vagy egy replikációs házirend törlése
1. Válassza ki a replikációs házirendet.
    a. A szabályzatot a konfigurációs kiszolgáló társításának megszüntetése, ellenőrizze, hogy nincsenek olyan replikált gépek használ-e a házirend. Kattintson a **szüntesse**.
    b. Törli a szabályzatot, ellenőrizze, hogy nem kapcsolódik a konfigurációs kiszolgáló. Kattintson a **törlése**. Érdemes igénybe törlése 30 – 60 másodpercre.
1. Kattintson az **OK** gombra.
