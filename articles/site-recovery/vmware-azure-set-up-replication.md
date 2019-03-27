---
title: A VMware-vészhelyreállításhoz az Azure-bA az Azure Site Recovery replikációs szabályzatok konfigurálására és felügyeletére |} A Microsoft Docs
description: Útmutatás a vész-helyreállítási VMware – Azure replikálás beállításainak konfigurálása az Azure Site Recoveryvel.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 11/27/2018
ms.author: sutalasi
ms.openlocfilehash: 4f558d8d1e5e4e743e6d44cca1a804b3e5898b30
ms.sourcegitcommit: f0f21b9b6f2b820bd3736f4ec5c04b65bdbf4236
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2019
ms.locfileid: "58443423"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery-to-azure"></a>Vész-helyreállítási VMware – Azure replikációs szabályzatok konfigurálására és felügyeletére
Ez a cikk ismerteti a replikációs szabályzat konfigurálása, Ön VMware virtuális gépek replikálása az Azure-ba, amikor használatával [Azure Site Recovery](site-recovery-overview.md).

## <a name="create-a-policy"></a>Szabályzat létrehozása

1. Válassza a **Kezelés** > **Site Recovery-infrastruktúra** lehetőséget.
2. A **a VMware és fizikai gépek**válassza **replikációs házirendek**. 
3. Kattintson a **+ replikációs házirend**, és adja meg a házirend nevét.
4. Az **RPO küszöbértéke** beállításnál adja meg az RPO-korlátot. Riasztások akkor jönnek létre, ha a folyamatos replikáció túllépi ezt a korlátot.
5. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama (a beállítás értéke órákban adható meg). A védelemmel ellátott gépeket az adatmegőrzési időtartamon belüli bármelyik pontra visszaállíthatja. A rendszer a prémium tárolóra replikált gépek esetében 24 órás megőrzést támogat. Akár 72 órát standard tárterület esetén támogatott.
6. A **alkalmazáskonzisztens pillanatkép gyakorisága**, válassza ki a legördülő listából, milyen gyakran (óra) az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat kell létrehozni. Ha szeretne kapcsolja ki az alkalmazás konzisztenciájának pontok generációja, válassza a "Off" értéket a legördülő listában.
7. Kattintson az **OK** gombra. A házirend létrehozása 30–60 másodpercet vesz igénybe.

Amikor létrehoz egy replikációs házirendet, a megfelelő feladatátvételi replikációs házirend automatikusan létrejön, a "feladat-visszavétel" utótaggal. A szabályzat létrehozása után szerkesztheti ezt, ha kiválasztja > **beállításainak szerkesztése**.

## <a name="associate-a-configuration-server"></a>Konfigurációs kiszolgáló társítása 

A replikációs házirend társítása a helyszíni konfigurációs kiszolgálót.

1. Kattintson a **társítása**, és válassza ki a konfigurációs kiszolgálót.

    ![Konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate1.png)
2. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának végrehajtása átlagosan 1–2 percet vesz igénybe.

    ![A konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Egy szabályzat szerkesztése

1. Válassza ki **kezelése** > **Site Recovery-infrastruktúra** > **replikációs házirendek**.
2. Válassza ki a módosítani kívánt replikációs házirendet.
3. Kattintson a **beállításainak szerkesztése**, és frissítse a RPO küszöbértéket/helyreállítási pont megőrzési óra/alkalmazáskonzisztens pillanatkép gyakorisága szükséges mezők.
4. Ha szeretne kapcsolja ki az alkalmazás konzisztenciájának pontok generációja, válassza a "Off" értéket a legördülő listában, az iktatott **alkalmazáskonzisztens pillanatkép gyakorisága**.
5. Kattintson a **Save** (Mentés) gombra. A szabályzat 30 – 60 másodpercet frissíteni kell.

## <a name="disassociate-or-delete-a-replication-policy"></a>Szüntesse meg az vagy egy replikációs házirend törlése

1. Válassza ki a replikációs házirendet.
    a. A szabályzatot a konfigurációs kiszolgáló társításának megszüntetése, ellenőrizze, hogy nincsenek olyan replikált gépek használ-e a házirend. Kattintson a **szüntesse**.
    b. Törli a szabályzatot, ellenőrizze, hogy nem kapcsolódik a konfigurációs kiszolgáló. Kattintson a **törlése**. Érdemes igénybe törlése 30 – 60 másodpercre.
2. Kattintson az **OK** gombra.
