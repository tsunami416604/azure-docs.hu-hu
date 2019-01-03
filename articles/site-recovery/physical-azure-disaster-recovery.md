---
title: Vészhelyreállítás beállítása az Azure-bA a helyszíni fizikai kiszolgálók az Azure Site Recoveryvel |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a vész-helyreállítása a helyszíni Windows és Linux-kiszolgálók, az Azure Site Recovery szolgáltatással az Azure-bA.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/31/2018
ms.author: raynew
ms.openlocfilehash: 3b06335019cf15cc296c1814ee1fcf5ce50c3949
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53971849"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Vészhelyreállítás az Azure-bA a helyszíni fizikai kiszolgálók beállítása

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Ez az oktatóanyag bemutatja, hogyan állítható be, valamint a helyszíni fizikai Windows és Linux-kiszolgálók Azure-bA. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure és helyszíni Előfeltételek beállítása
> * Helyreállítási tár létrehozása a Site Recovery számára 
> * Állítsa be a forrás és cél replikációs környezet
> * Replikációs házirend létrehozása
> * A kiszolgáló a replikáció engedélyezése

[az architektúra áttekintése](concepts-hyper-v-to-azure-architecture.md) e vészhelyreállítási forgatókönyv.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Győződjön meg arról, hogy megértette a [architektúra és összetevők](physical-azure-architecture.md) ehhez a forgatókönyvhöz.
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](vmware-physical-secondary-support-matrix.md).
- Győződjön meg arról, hogy megfelelnek-e a replikálni kívánt kiszolgálók a [Azure virtuális gépekre vonatkozó](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Az Azure előkészítése. Azure-előfizetés, egy Azure virtual network és storage-fiók van szüksége.
- Fiók előkészítése automatikus telepítés a mobilitási szolgáltatást a replikálni kívánt minden egyes kiszolgálón.

Mielőtt hozzálátna, vegye figyelembe, hogy:

- Az Azure-ba történő feladatátvétel után a fizikai kiszolgálók vissza a helyszíni fizikai gépek nem sikertelen. Vissza a VMware virtuális gépek csak sikertelen. 
- Ebben az oktatóanyagban beállítja a fizikai kiszolgáló vészhelyreállítása az Azure-bA a legegyszerűbb beállításokkal. Ha szeretné más lehetőségek ismertetése, olvassa el az útmutató útmutatók:
    - Állítsa be a [replikációs forrás](physical-azure-set-up-source.md), többek között a Site Recovery konfigurációs kiszolgálónak.
    - A [replikációs cél](physical-azure-set-up-target.md) beállítása.
    - [Replikációs szabályzat](vmware-azure-set-up-replication.md) konfigurálása és a [replikáció engedélyezése](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Az Azure-fiók beállítása

A Microsoft első [Azure-fiók](https://azure.microsoft.com/).

- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- Ismerje meg [Site Recovery díjszabásáról](site-recovery-faq.md#pricing), és [díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/).
- Ismerje meg, amely [régiók támogatottak](https://azure.microsoft.com/pricing/details/site-recovery/) Site Recovery.

### <a name="verify-azure-account-permissions"></a>Azure-fiók szükséges engedélyek ellenőrzése

Ellenőrizze, hogy jogosult az Azure-fiókját az Azure virtuális gépek replikálását.

- Tekintse át a [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kell gépek replikálása az Azure-bA.
- Ellenőrizze és módosítsa [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md) engedélyeket. 



### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Állítsa be egy [Azure-beli hálózati](../virtual-network/quick-create-portal.md).

- A feladatátvételt követően felálló Azure virtuális gépek kerülnek ehhez a hálózathoz.
- A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.


## <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Állítsa be egy [Azure storage-fiók](../storage/common/storage-quickstart-create-account.md).

- A Site Recovery replikálja a helyszíni gépek az Azure-tárolóba. Az Azure virtuális gépek a tárolóból lesznek létrehozva, feladatátvételt követően.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
- A tárfiók lehet standard vagy [prémium](../virtual-machines/windows/premium-storage.md).
- Prémium szintű fiókot állít be, ha még szüksége lesz egy további standard szintű fiók naplóadatokat.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie minden replikálni kívánt kiszolgálón. A Site Recovery automatikusan telepíti ezt a szolgáltatást, amikor engedélyezi a kiszolgáló replikációját. Automatikusan telepített kell készíteni egy fiókot, amely a Site Recovery a kiszolgálóhoz való hozzáféréshez fogja használni.

- Egy tartományi vagy helyi fiókot is használhat
- Windows virtuális gépek esetében, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen. Ehhez a nyilvántartásba alatt **localaccounttokenfilterpolicy**, adja hozzá a DWORD bejegyzést **LocalAccountTokenFilterPolicy**, 1 értékkel.
- A parancssori felületen a beállítás letiltása a beállításjegyzék-bejegyzés hozzáadásához írja be:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- A Linux rendszerre a fióknak rendszergazdai fióknak kell lennie a forrás Linux-kiszolgálón.


## <a name="create-a-vault"></a>Tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, milyen replikálni, és replikálja azt.

1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Site Recovery** > **Az infrastruktúra előkészítése** > **Védelmi cél** lehetőségre.
3. A **védelmi cél**válassza **az Azure-bA** > **nem virtualizált/egyéb**.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgálót, regisztrálja a tárolóban, és a virtuális gépek felderítéséhez.

1. Kattintson a **Site Recovery** > **az infrastruktúra előkészítése** > **forrás**.
2. Ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló**.
3. A **-kiszolgáló hozzáadása**, ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. A Site Recovery egyesített telepítőjének telepítőfájl letöltéséhez.
5. Töltse le a tároló regisztrációs kulcsát. Ez szükséges az egységes telepítő futtatása során. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>A konfigurációs kiszolgálót regisztrálja a tárolóban

Mielőtt elkezdené, tegye a következőket: 

#### <a name="verify-time-accuracy"></a>Időt, pontosságának ellenőrzéséhez.
Az a konfigurációs kiszolgálón, győződjön meg arról, hogy a rendszeróra szinkronizálva van egy [időkiszolgálóval](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Meg kell egyeznie. Ha 15 perc alatt az előtérben vagy mögött található, a telepítés meghiúsulhat.

#### <a name="verify-connectivity"></a>Ellenőrizze a kapcsolatot
Győződjön meg arról, hogy a gép úgy férhet hozzá az alábbi URL-címek az adott környezet alapján: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

IP-cím-alapú tűzfalszabályainak lehetővé kell tennie az Azure URL-címeket fent felsorolt összes kommunikáció HTTPS (443) porton keresztül. Egyszerűsítése és az IP-címtartományok korlátozza, javasoljuk, hogy URL-CÍMEK szűrése végezhető.

- **Kereskedelmi IP-címek** – lehetővé teszik a [Azure Datacenter IP-címtartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653), és a HTTPS (443) portot. Lehetővé teszi az Azure-régió támogatja az aad-ben, a biztonsági mentés, a replikációs és a tároló URL-címek az előfizetéséhez tartozó IP-címtartományát.  
- **IP-címek kormányzati** -lehetővé teszi a [Azure Government adatközpont IP-címtartományok](https://www.microsoft.com/en-us/download/details.aspx?id=57063), és a HTTPS (443) port minden USGov régió (Virginia, Texas, Arizona és Iowa) támogatja az aad-ben, a biztonsági mentés, a replikáció és a tároló URL-címek esetében.  

#### <a name="run-setup"></a>A telepítő futtatása
Futtassa az egyesített telepítő egy helyi rendszergazdaként, a konfigurációs kiszolgáló telepítése. A folyamatkiszolgáló és a fő célkiszolgáló is települnek alapértelmezés szerint a konfigurációs kiszolgálón.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Regisztráció befejezését követően a folyamatkiszolgáló megjelenik a **beállítások** > **kiszolgálók** lap a tárolóban.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a cél üzembehelyezési modellje.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Új replikációs szabályzat létrehozásához kattintson a **Site Recovery-infrastruktúra** > **Replikációs szabályzatok** > **+Replikációs szabályzat** elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). Ez az érték határozza meg, milyen gyakran jönnek létre adat-helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, milyen gyakran (perc), létrehozza alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Például, ha a replikációs szabályzat a **rep-policy** majd a feladat-visszavételi szabályzat **rep-policy-failback** jön létre. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikálást, minden olyan kiszolgáló esetén.

- A Site Recovery telepíti a mobilitási szolgáltatást, ha a replikáció engedélyezve van.
- Ha a kiszolgáló a replikáció engedélyezése 15 percet is igénybe vehet, vagy hosszabb módosítások érvénybe lépéséhez, és megjelenik a portálon.

1. Kattintson az **Alkalmazás replikálása** > **Forrás** elemre.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **gép típusa**válassza **fizikai gépek**.
4. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgáló). Ezután kattintson az **OK** gombra.
5. A **cél**, válassza ki az előfizetést és az erőforráscsoport, amelyben szeretné létrehozni az Azure virtuális gépek a feladatátvételt követően. Válassza ki az Azure-ban (klasszikus vagy erőforrás-kezelés) használni kívánt üzemi modellhez.
6. Válassza ki az adatok replikálásához használni kívánt Azure-tárfiókot. 
7. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak.
8. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre. 
9. A **fizikai gépek**, és kattintson a **+ fizikai gép**. Adja meg a nevét és IP-címet. Válassza ki a replikálni kívánt gépek operációs rendszerének. A kiszolgálók felderítése és felsorolt néhány percet vesz igénybe. 
10. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen használandó fiókot.
11. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva. 
12. Kattintson a **Replikáció engedélyezése** elemre. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.


Figyeléséhez ad hozzá kiszolgálókat, ellenőrizheti azok esetében a legutolsó felderítésének időpontját **konfigurációs kiszolgálók** > **utolsó forduljon,**. Gépek felvétele egy ütemezett felderítés idejének várakozás nélkül, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), és kattintson a **frissítése**.

## <a name="next-steps"></a>További lépések

[Vészhelyreállítási próba végrehajtása](tutorial-dr-drill-azure.md).
