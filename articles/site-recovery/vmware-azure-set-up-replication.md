---
title: Konfigurálhatja és kezelheti a VMware-replikáció az Azure Site Recovery replikációs házirendeket |} Microsoft Docs
description: Ismerteti, hogyan lehet VMware-replikáció Azure replikációs beállításainak konfigurálása az Azure Site Recovery szolgáltatással.
services: site-recovery
author: sujayt
manager: rochakm
ms.service: site-recovery
ms.topic: article
ms.date: 03/05/2018
ms.author: sutalasi
ms.openlocfilehash: 6a8e6e7c6fbdbcbf58557a0896e976a608164041
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
ms.locfileid: "29812669"
---
# <a name="configure-and-manage-replication-policies-for-vmware-replication"></a>Konfigurálhatja és kezelheti a replikációs házirendeket a VMware-replikáció
Ez a cikk ismerteti a replikációs házirend konfigurálása az Azure-ba, hogy a VMware virtuális gépek replikált használatával [Azure Site Recovery](site-recovery-overview.md).


## <a name="create-a-policy"></a>Szabályzat létrehozása

1. Válassza a **Kezelés** > **Site Recovery-infrastruktúra** lehetőséget.
2. A **a VMware és fizikai gépek**, jelölje be **replikációs házirendek**. 
3. Kattintson a **+ replikációs házirend**, és adja meg a házirend nevét.
5. Az **RPO küszöbértéke** beállításnál adja meg az RPO-korlátot. Riasztások jönnek létre, ha a folyamatos replikálásra túllépi ezt a korlátozást.
6. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú legyen az egyes helyreállítási pontok adatmegőrzési időtartama (a beállítás értéke órákban adható meg). A védelemmel ellátott gépeket az adatmegőrzési időtartamon belüli bármelyik pontra visszaállíthatja. A rendszer a prémium tárolóra replikált gépek esetében 24 órás megőrzést támogat. Standard szintű tárolást – 72 óra esetén támogatott.
7. Az **Alkalmazáskonzisztens pillanatkép gyakorisága** beállítás azt határozza meg, hogy milyen gyakran hozzon létre a rendszer alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat (a beállítás értéke percekben adható meg).
8. Kattintson az **OK** gombra. A házirend létrehozása 30–60 másodpercet vesz igénybe.

Amikor egy replikációs házirend létrehozásához a megfelelő feladatátvételi replikációs házirend automatikusan létrejön, "feladat-visszavétel" utótaggal rendelkező. A házirend létrehozása után szerkesztheti ezt lehetőséget > **beállításainak szerkesztése**.

## <a name="associate-a-configuration-server"></a>A konfigurációs kiszolgáló társítása 

A replikációs házirend társítása a helyszíni konfigurációs kiszolgáló.

1. Kattintson a **társítása**, és válassza ki a konfigurációs kiszolgálót.

    ![Társítsa a konfigurációs kiszolgálón](./media/vmware-azure-set-up-replication/associate1.png)

2. Kattintson az **OK** gombra. A konfigurációs kiszolgáló társításának végrehajtása átlagosan 1–2 percet vesz igénybe.

    ![A konfigurációs kiszolgáló társítása](./media/vmware-azure-set-up-replication/associate2.png)


## <a name="disassociate-or-delete-a-replication-policy"></a>Szüntesse meg vagy töröl egy replikációs házirendet
1. Válassza ki a replikációs házirendet.
    a. Leválasztja a házirendet, a konfigurációs kiszolgálóról, ellenőrizze, hogy nem replikált gépek vannak a házirendet. Kattintson a **szüntesse**.
    b. Törölheti a házirendet, ellenőrizze, hogy a konfigurációs kiszolgáló nem van társítva. Kattintson a **törlése**. 30 – 60 másodperc törölni kell vennie.
2. Kattintson az **OK** gombra.
