---
title: A VMware vész-helyreállítási replikációs házirendjének beállítása az Azure Site Recovery szolgáltatással| Microsoft dokumentumok
description: Bemutatja, hogyan konfigurálhatja a VMware vész-helyreállítási replikációs replikációs beállításait az Azure-ba az Azure Site Recovery segítségével.
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 04/08/2019
ms.author: sutalasi
ms.openlocfilehash: 45921bdf802a649b7b802f44d2842a543e44f02b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257120"
---
# <a name="configure-and-manage-replication-policies-for-vmware-disaster-recovery"></a>A VMware vész-helyreállítási replikációs házirendjeinek konfigurálása és kezelése

Ez a cikk azt ismerteti, hogyan konfigurálható a replikációs szabályzat, amikor vmware virtuális gépeket replikál az Azure-ba az [Azure Site Recovery](site-recovery-overview.md)használatával.

## <a name="create-a-policy"></a>Szabályzat létrehozása

1. Válassza a**Hely-helyreállítási infrastruktúra** **kezelése** > lehetőséget.
2. A **VMware és fizikai gépek esetén**válassza a **Replikációs házirendek**lehetőséget.
3. Kattintson **a +Replikációs házirend gombra,** és adja meg a házirend nevét.
4. Az **RPO küszöbértéke** beállításnál adja meg az RPO-korlátot. Riasztások akkor jönnek létre, ha a folyamatos replikáció meghaladja ezt a korlátot.
5. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama (a beállítás értéke órákban adható meg). A védelemmel ellátott gépeket az adatmegőrzési időtartamon belüli bármelyik pontra visszaállíthatja. A rendszer a prémium tárolóra replikált gépek esetében 24 órás megőrzést támogat. A normál tárolás akár 72 óra is elszámolható.
6. Az **alkalmazáskonzisztens pillanatkép-gyakoriságban**válasszon a legördülő helyből, hogy milyen gyakran (órában) létre kell hozni az alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat. Ha ki szeretné kapcsolni az alkalmazáskonzisztenciapontok generálását, válassza a legördülő menü "Ki" értékét.
7. Kattintson az **OK** gombra. A házirend létrehozása 30–60 másodpercet vesz igénybe.

Replikációs házirend létrehozásakor a rendszer automatikusan létrehoz egy megfelelő feladat-visszavételi replikációs házirendet a "feladat-visszavétel" utótaggal. A házirend létrehozása után módosíthatja azt, ha kijelöli > **Beállítások szerkesztése**.

## <a name="associate-a-configuration-server"></a>Konfigurációs kiszolgáló társítása

Társítsa a replikációs házirendet a helyszíni konfigurációs kiszolgálóhoz.

1. Kattintson **a Társítás**gombra, és válassza ki a konfigurációs kiszolgálót.

    ![Konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate1.png)
2. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának végrehajtása átlagosan 1–2 percet vesz igénybe.

    ![A konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate2.png)

## <a name="edit-a-policy"></a>Házirend szerkesztése

A replikációs házirendet létrehozás után módosíthatja.

- A házirend módosításai a házirendet használó összes gépre vonatkoznak.
- Ha a replikált gépeket egy másik replikációs házirendhez szeretné társítani, le kell tiltania és újra engedélyeznie kell a megfelelő gépek védelmét.

Házirend szerkesztése a következőképpen:
1. Válassza a Site**Recovery Infrastructure** > **replikációs házirendjeinek** **kezelése** > lehetőséget.
2. Válassza ki a módosítani kívánt replikációs házirendet.
3. Kattintson **a Beállítások szerkesztése**gombra, és szükség szerint frissítse az RPO-küszöbértéket/helyreállításipont-megőrzési órákat/alkalmazáskonzisztens pillanatkép-gyakorisági mezőket.
4. Ha ki szeretné kapcsolni az alkalmazáskonzisztenciapontok generálását, válassza a "Ki" értéket az **Alkalmazáskonzisztens pillanatkép gyakoriságának**legördülő menüjében.
5. Kattintson a **Mentés** gombra. A házirendet 30–60 másodpercen belül frissíteni kell.



## <a name="disassociate-or-delete-a-replication-policy"></a>Replikációs házirend társításának vagy törlésének leválasztani vagy törölni

1. Válassza ki a replikációs házirendet.
    a. A házirend nek a konfigurációs kiszolgálótól való elhatároltságához győződjön meg arról, hogy nem használnak replikált gépek a házirendet. Ezután kattintson **a Disszociált gombra.**
    b. A házirend törléséhez győződjön meg arról, hogy nincs társítva konfigurációs kiszolgálóhoz. Ezután kattintson a **Törlés gombra.** A törlés 30-60 másodpercet vesz igénybe.
2. Kattintson az **OK** gombra.
