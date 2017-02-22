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
ms.sourcegitcommit: 74d51269cdd55e39eaa2963fec2b409decb47d0a
ms.openlocfilehash: a279fdc35c62c76e6f0858d52c64240032669f15


---
# <a name="manage-replication-policy-for-vmware-to-azure"></a>VMware–Azure replikációs házirend kezelése


## <a name="create-a-new-replication-policy"></a>Új replikációs házirend létrehozása

1. Kattintson a bal oldali menü Kezelés -> Site Recovery-infrastruktúra elemére. 
2. Válassza a Replikációs házirendek lehetőséget a VMware- és fizikai gépek számára területen.
3. Kattintson felül a +Replikációs házirend gombra.

    ![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/createpolicy.png)

4. Adja meg a házirend nevét.

5. Az RPO küszöbértéke beállításnál adja meg az RPO-korlátot. A rendszer riasztásokat küld, ha a folyamatos replikáció meghaladja ezt a korlátot.
6. A Helyreállítási pont megőrzése beállításnál azt adhatja meg, hogy hány órás legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A védelemmel ellátott gépeket az időtartamon belüli bármelyik pontra visszaállíthatja. 

    > [!NOTE] 
    > A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
    
    > [!NOTE] 
    > A rendszer automatikusan létrehoz egy feladat-visszavételi replikációs házirendet.

7. Az Alkalmazáskonzisztens pillanatkép gyakorisága beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke percekben adható meg).

8. Kattintson az OK gombra. A házirend létrehozása átlagosan 0,5–1 percet vesz igénybe.

![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Creating-Policy.png)

## <a name="associate-configuration-server-with-replication-policy"></a>Konfigurációs kiszolgáló társítása replikációs házirenddel
1. Kattintson arra a replikációs házirendre, amelyikhez a konfigurációs kiszolgálót társítani szeretné.
2. Kattintson felül a Társítás gombra.
![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-1.PNG)

3. Válassza ki a Konfigurációs kiszolgáló elemet a kiszolgálók listájában.
4. Kattintson az OK gombra. A konfigurációs kiszolgáló társítása átlagosan 1–2 percet vesz igénybe.

![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Associate-CS-2.png)

## <a name="edit-replication-policy"></a>Replikációs házirend szerkesztése
1. Kattintson arra a replikációs házirendre, amelyiknek a replikációs beállításait módosítani szeretné.
![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Select-Policy.png)

2. Kattintson felül a Beállítások szerkesztése gombra.
![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Edit-Policy.png)

3. Igény szerint módosítsa a beállításokat.
4. Kattintson felül a Mentés gombra. A replikációs házirend mentése átlagosan 2–5 percet vesz igénybe attól függően, hogy hány virtuális gép használja azt.

![Replikációs házirend létrehozása](./media/site-recovery-setup-replication-settings-vmware/Save-Policy.png)

## <a name="dissociate-configuration-server-from-replication-policy"></a>A konfigurációs kiszolgáló és a replikációs házirend társításának megszüntetése
1. Kattintson arra a replikációs házirendre, amelyikhez a konfigurációs kiszolgálót társítani szeretné.
2. Kattintson felül a Társítás megszüntetése gombra.
3. Válassza ki a Konfigurációs kiszolgáló elemet a kiszolgálók listájában.
4. Kattintson az OK gombra. A konfigurációs kiszolgáló társításának megszüntetése átlagosan 1–2 percet vesz igénybe.
    
    > [!NOTE] 
    > Nem szüntetheti meg a konfigurációs kiszolgáló társítását, ha van legalább egy olyan replikált elem, amelyik az adott házirendet használja. A konfigurációs kiszolgáló társításának megszüntetése előtt győződjön meg arról, hogy nincsenek olyan replikált elemek, amelyek a házirendet használják.

## <a name="delete-replication-policy"></a>Replikációs házirend törlése 

1. Kattintson a törölni kívánt replikációs házirendre.
2. Kattintson a Törlés gombra. A házirend törlése átlagosan 0,5–1 percet vesz igénybe.

    > [!NOTE] 
    > Nem törölheti a replikációs házirendet, ha legalább egy konfigurációs kiszolgáló társítva van hozzá. A házirend törlése előtt győződjön meg arról, hogy egy replikált elem sem használja azt, továbbá törölje az összes társított konfigurációs kiszolgálót is.


<!--HONumber=Jan17_HO4-->


