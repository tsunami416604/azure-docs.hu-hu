---
title: "Replikációs beállítások megadása az Azure Site Recoveryhez | Microsoft Docs"
description: "A cikkből megtudhatja, hogyan helyezze üzembe a Site Recovery-t a VMM-felhőben futó Hyper-V virtuális gépek Azure-ba történő replikálása, feladatátvétele és helyreállítása érdekében."
services: site-recovery
documentationcenter: 
author: sujayt
manager: rochakm
editor: rayne-wiselman
ms.assetid: 8e7d868e-00f3-4e8b-9a9e-f23365abf6ac
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: hero-article
ms.date: 01/19/2017
ms.author: sutalasi
translationtype: Human Translation
ms.sourcegitcommit: 28e905a20d878eab1428a4b88113544aa742124a
ms.openlocfilehash: 9c34ea792aa561b8155a915845ffb857dfef7a90


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>VMware–Azure replikációs házirend kezelése


## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Válassza a **Kezelés** > **Site Recovery-infrastruktúra** lehetőséget.
2. Válassza a **Replikációs házirendek** lehetőséget a **VMware- és fizikai gépek számára** területen.
3. Válassza a **+Replikációs házirend** lehetőséget.

      ![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Adja meg a házirend nevét.

5. Az **RPO küszöbértéke** beállításnál adja meg az RPO-korlátot. A rendszer riasztásokat küld, ha a folyamatos replikáció meghaladja ezt a korlátot.
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama (a beállítás értéke órákban adható meg). A védelemmel ellátott gépeket az adatmegőrzési időtartamon belüli bármelyik pontra visszaállíthatja.

    > [!NOTE]
    > A rendszer a prémium tárolóra replikált gépek esetében 24 órás megőrzést támogat. A rendszer a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.

    > [!NOTE]
    > A rendszer automatikusan hoz létre replikációs házirendet a feladat-visszavételhez.

7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke percekben adható meg).

8. Kattintson az **OK** gombra. A házirend létrehozása 30–60 másodpercet vesz igénybe.

![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-a-configuration-server-with-a-replication-policy"></a>Konfigurációs kiszolgáló társítása egy replikációs házirenddel
1. Válassza azt a replikációs házirendet, amelyikhez a konfigurációs kiszolgálót társítani szeretné.
2. Kattintson a **Társítás** gombra.
![Konfigurációs kiszolgáló társítása](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Válassza ki a konfigurációs kiszolgálót a kiszolgálók listájában.
4. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának végrehajtása átlagosan&1;–2 percet vesz igénybe.

![A konfigurációs kiszolgáló társítása](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-a-replication-policy"></a>Replikációs házirend szerkesztése
1. Válassza ki azt a replikációs házirendet, amelyiknek a replikációs beállításait módosítani szeretné.
![Replikációs házirend szerkesztése](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Kattintson a **Beállítások szerkesztése** lehetőségre.
![Replikációs házirend beállításainak szerkesztése](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Igény szerint módosítsa a beállításokat.
4. Kattintson a **Save** (Mentés) gombra. A replikációs házirend mentése&2;–5 percet vesz igénybe attól függően, hogy hány virtuális gép használja azt.

![Replikációs házirend mentése](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-a-configuration-server-from-a-replication-policy"></a>A konfigurációs kiszolgáló és a replikációs házirend társításának megszüntetése
1. Válassza azt a replikációs házirendet, amelyikhez a konfigurációs kiszolgálót társítani szeretné.
2. Kattintson a **Társítás megszüntetése** gombra.
3. Válassza ki a konfigurációs kiszolgálót a kiszolgálók listájában.
4. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának megszüntetése&1;–2 percet vesz igénybe.

    > [!NOTE]
    > Nem szüntetheti meg a konfigurációs kiszolgáló társítását, ha van legalább egy olyan replikált elem, amelyik az adott házirendet használja. A konfigurációs kiszolgáló társításának megszüntetése előtt győződjön meg arról, hogy nincsenek olyan replikált elemek, amelyek a házirendet használják.

## <a name="delete-a-replication-policy"></a>Replikációs házirend törlése

1. Válassza ki a törölni kívánt replikációs házirendet.
2. Kattintson a **Törlés** gombra. A házirend törlése 30–60 másodpercet vesz igénybe.

    > [!NOTE]
    > Nem törölheti a replikációs házirendet, ha legalább egy konfigurációs kiszolgáló társítva van hozzá. A házirend törlése előtt győződjön meg arról, hogy egy replikált elem sem használja azt, továbbá törölje az összes társított konfigurációs kiszolgálót is.



<!--HONumber=Feb17_HO3-->


