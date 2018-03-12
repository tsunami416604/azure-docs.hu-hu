---
title: "Állítsa be a vész-helyreállítási az Azure-bA helyszíni fizikai kiszolgálók Azure Site Recovery szolgáltatással |} Microsoft Docs"
description: "Ismerje meg, hogyan állíthat be a helyi Windows és Linux kiszolgálók, az Azure Site Recovery szolgáltatásban az Azure-bA vész-helyreállítási."
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 03/08/2018
ms.author: raynew
ms.openlocfilehash: d460da197c6e9f0bface402d83d4788f8164cc9c
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/09/2018
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>A helyszíni fizikai kiszolgálók Azure-bA vész-helyreállítási beállítása

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Az oktatóanyag bemutatja, hogyan állíthat be a helyszíni fizikai Windows és Linux kiszolgálók Azure vész-helyreállítási. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Állítsa be az Azure és a helyszíni Előfeltételek
> * A Site Recovery Recovery Services-tároló létrehozása 
> * Állítsa be a forrás és cél replikációs környezetekben
> * Replikációs házirend létrehozása
> * A kiszolgáló replikáció engedélyezése

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Ismernie kell a [forgatókönyv-architektúrát és az összetevőket](physical-azure-architecture.md).
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](vmware-physical-secondary-support-matrix.md).
- Győződjön meg arról, hogy a replikálni kívánt kiszolgálók megfelelnek [Azure virtuális gép](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Azure előkészítése. Azure-előfizetéssel, Azure virtuális hálózat és a storage-fiók szükséges.
- Egy fiók előkészítése automatikus telepíteni a mobilitási szolgáltatást minden replikálni kívánt kiszolgálón.

> [!NOTE]
> Mielőtt elkezdené, vegye figyelembe, hogy feladatátvétel az Azure-ba, fizikai kiszolgálók nem lehet nem vissza a helyszíni fizikai gépek. A feladatvisszavétel csak vissza a VMware virtuális gépek. 


### <a name="set-up-an-azure-account"></a>Az Azure-fiók beállítása

Egy Microsoft beolvasása [Azure-fiók](http://azure.microsoft.com/).

- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- További tudnivalók [Site Recovery díjszabásáról](site-recovery-faq.md#pricing), és [díjszabása](https://azure.microsoft.com/pricing/details/site-recovery/).
- Annak megállapítása, amely [régiókban támogatott](https://azure.microsoft.com/pricing/details/site-recovery/) hely helyreállításához.

### <a name="verify-azure-account-permissions"></a>Azure-fiók szükséges engedélyek ellenőrzése

Ellenőrizze, hogy az Azure-fiók jogosult az Azure virtuális gépek replikálását.

- Tekintse át a [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kell replikálni a gépeket az Azure-bA.
- Ellenőrizze és módosítsa [szerepkörön alapuló hozzáférés](../active-directory/role-based-access-control-configure.md) engedélyek. 



### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Állítson be egy [Azure hálózati](../virtual-network/quick-create-portal.md).

- Azure virtuális gépek kerülnek ehhez a hálózathoz, ha feladatátvételt követően jönnek létre.
- A hálózati és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.


## <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Állítson be egy [Azure storage-fiók](../storage/common/storage-create-storage-account.md#create-a-storage-account).

- A Site Recovery a helyszíni gépeket replikál az Azure storage. Azure virtuális gépek jönnek létre a tárból, feladatátvételt követően.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.
- A tárfiók lehet standard vagy [prémium](../virtual-machines/windows/premium-storage.md).
- Ha a prémium szintű fiók beállítása is szüksége lesz egy további standard fiók naplóadatokat.



### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatás minden replikálni kívánt kiszolgálón telepítenie kell. A Site Recovery automatikusan telepíti ezt a szolgáltatást, ha engedélyezi a kiszolgáló replikációját. Automatikusan telepíti, meg kell készíteni egy fiókot, amely a Site Recovery a kiszolgálóhoz való hozzáféréshez fogja használni.

- A tartományi vagy helyi fiók használható
- Windows virtuális gépek, ha nem használ egy olyan tartományi fiók, tiltsa le a távoli felhasználói hozzáférés-vezérlés a helyi számítógépen. Ehhez a nyilvántartásában **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, adja hozzá a DWORD bejegyzést **LocalAccountTokenFilterPolicy**, 1 értékű.
- Letiltja a beállítást a parancssori felület a beállításjegyzék-bejegyzés hozzáadásához írja be:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- A Linux a fióknak kell lennie a forráskiszolgálón Linux legfelső szintű.


## <a name="create-a-vault"></a>Tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Válassza ki a védelmi cél

Válassza ki, mi replikálni, és úgy, hogy replikálásához.

1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Site Recovery** > **Az infrastruktúra előkészítése** > **Védelmi cél** lehetőségre.
3. A **védelmi cél**, jelölje be **az Azure-bA** > **nem virtualizált vagy egyéb**.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgáló, és regisztrálja őket a tárolóban lévő virtuális gépek felderítése.

1. Kattintson a **helyreállítási hely** > **infrastruktúra előkészítése** > **forrás**.
2. Ha nem rendelkezik a konfigurációs kiszolgáló, kattintson a **+ konfigurációs kiszolgáló**.
3. A **kiszolgáló hozzáadása**, ellenőrizze, hogy **konfigurációs kiszolgáló** megjelenik **kiszolgálótípus**.
4. Töltse le a Site Recovery az egységes telepítő telepítőfájlját.
5. Töltse le a tároló regisztrációs kulcsát. Ez szükséges az egységes telepítő futtatásakor. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Regisztrálja a konfigurációs kiszolgálót a tárolóban

Mielőtt elkezdené, tegye a következőket: 

- A konfigurációs kiszolgáló számítógépen győződjön meg arról, hogy a rendszer órája szinkronizálva van a [kiszolgálót](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Meg kell felelnie. Ha az előtérben 15 perc vagy mögött található, a telepítés meghiúsulhat.
- Győződjön meg arról, hogy a gép úgy férhet hozzá az alábbi URL-címek:       [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]

- IP-címeken alapuló tűzfalszabályok szabályokat engedélyezni kell az Azure-kommunikációt.
- Engedélyezze az [Azure-adatközpont IP-tartományait](https://www.microsoft.com/download/confirmation.aspx?id=41653) és a HTTPS-portot (443).
- Engedélyezze az előfizetéshez tartozó Azure-régió, illetve az USA nyugati régiójának IP-tartományait (a hozzáférés-vezérléshez és az identitáskezeléshez szükséges).

Futtassa az egységes telepítő egy helyi rendszergazdaként, telepítse a konfigurációs kiszolgálót. A folyamatkiszolgáló és a fő célkiszolgáló is telepítve alapértelmezés szerint a konfigurációs kiszolgálón.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Regisztráció befejezése után a kiszolgáló megjelenik a **beállítások** > **kiszolgálók** lap a tárolóban lévő állapottal.


## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a tároló üzembe helyezési modellben.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Hozzon létre egy új replikációs házirendet, kattintson a **Site Recovery-infrastruktúra** > **replikációs házirendek** > **+ replikációs házirend**.
2. A **replikációs házirend létrehozása**, megadhatja a házirend nevét.
3. A **helyreállítási Időkorlát küszöbértéke**, adja meg a helyreállítási pont időkorlát (RPO) vonatkozó korlátozás. Ez az érték határozza meg, milyen gyakran adatok helyreállítási pontot hoz létre. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **helyreállításipont-megőrzést**, adja meg, hogy mennyi ideig (órákban) a megőrzési időszak az egyes helyreállítási pontok. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. Akár 24 óra megőrzési replikálva prémium szintű Storage, és standard szintű tárolást 72 órát gépek esetén támogatott.
5. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, milyen gyakran (percben) alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat hoz létre. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Például, ha a replikációs házirend **rep-házirend** majd egy feladatátvételi házirendhez **rep-házirend-feladat-visszavétel** jön létre. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Az egyes kiszolgálók replikáció engedélyezése.

- A Site Recovery a mobilitási szolgáltatást telepíti, ha engedélyezve van a replikáció.
- Ha engedélyezi a kiszolgáló replikációs, 15 percbe is telhet, vagy hosszabb, a módosítások érvénybe lépéséhez, és megjelenik a portálon.

1. Kattintson az **Alkalmazás replikálása** > **Forrás** elemre.
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **számítógép típusú**, jelölje be **fizikai gépek**.
4. Válassza ki a folyamatkiszolgáló (a konfigurációs kiszolgáló). Ezután kattintson az **OK** gombra.
5. A **cél**, válassza ki az előfizetés és az erőforráscsoport, amelyben létrehozása az Azure virtuális gépek a feladatátvételt követően szeretné. Válassza ki az Azure (klasszikus vagy erőforrás-kezelés) használni kívánt telepítési modelljét.
6. Válassza ki az adatok replikálásához használni kívánt Azure-tárfiókot. 
7. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak.
8. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Ha az egyes gépeknél külön-külön szeretné beállítani az Azure-hálózatot, kattintson a **Beállítás később** elemre. 
9. A **fizikai gépek**, és kattintson a **+ a fizikai gép**. Adja meg a nevét és IP-címet. Válassza ki a replikálni kívánt gépek operációs rendszerének. A kiszolgálók felderítése és felsorolt néhány percet vesz igénybe. 
10. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a fiókot, amelyek használják a folyamatkiszolgáló automatikusan telepíteni a mobilitási szolgáltatás a számítógépen.
11. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva. 
12. Kattintson a **Replikáció engedélyezése** elemre. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.


Hozzáadott kiszolgálók figyelése, ellenőrizheti a számukra a felderített legutóbbi **konfigurációs kiszolgálók** > **utolsó lépjen kapcsolatba a**. Egy ütemezett felderítési idő várakozás nélkül hozzáadni, jelölje ki a konfigurációs kiszolgálót (ne kattintson azt), és kattintson a **frissítése**.

## <a name="next-steps"></a>További lépések

[Futtassa a vész-helyreállítási részletezési](tutorial-dr-drill-azure.md).
