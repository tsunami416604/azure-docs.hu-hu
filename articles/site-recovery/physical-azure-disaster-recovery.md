---
title: Fizikai helyszíni kiszolgálók vészhelyreállítása az Azure Site Recovery szolgáltatással
description: Ismerje meg, hogyan állíthatja be a vészhelyreállítást az Azure-ban a helyszíni Windows és Linux kiszolgálókhoz az Azure Site Recovery szolgáltatással.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 11/12/2019
ms.author: raynew
ms.openlocfilehash: 2f92c2b800c6d30cc5f365e6d24925a70d3db55a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79257926"
---
# <a name="set-up-disaster-recovery-to-azure-for-on-premises-physical-servers"></a>Vészhelyreállítás beállítása az Azure-ba helyszíni fizikai kiszolgálókhoz

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás a helyszíni számítógépek és az Azure-beli virtuális gépek replikálásának, feladatátvételének és feladat-visszavételének kezelésével és irányításával járul hozzá a vészhelyreállítási stratégia megvalósításához.

Ez az oktatóanyag bemutatja, hogyan állíthatja be a helyszíni fizikai Windows és Linux-kiszolgálók vészutáni helyreállítását az Azure-ba. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Az Azure és a helyszíni előfeltételek beállítása
> * Helyreállítási tár létrehozása a Site Recovery számára 
> * A forrás- és célreplikációs környezetek beállítása
> * Replikációs házirend létrehozása
> * Replikáció engedélyezése kiszolgálón

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez:

- Győződjön meg arról, hogy tisztában van az [architektúrával és az összetevők](physical-azure-architecture.md) ebben a forgatókönyvben.
- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](vmware-physical-secondary-support-matrix.md).
- Győződjön meg arról, hogy a replikálni kívánt kiszolgálók megfelelnek [az Azure virtuális gép követelményeinek.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
- Az Azure előkészítése. Szüksége van egy Azure-előfizetésre, egy Azure virtuális hálózatra és egy tárfiókra.
- Készítsen elő egy fiókot a Mobilitás szolgáltatás automatikus telepítéséhez minden replikálni kívánt kiszolgálón.

Mielőtt elkezdené, vegye figyelembe, hogy:

- Az Azure-ba való feladatátvétel után a fizikai kiszolgálók nem lehet visszaadni a helyszíni fizikai gépek. Csak a VMware virtuális gépek visszavétele. 
- Ez az oktatóanyag fizikai kiszolgálóvész-helyreállítást állít be az Azure-ba a legegyszerűbb beállításokkal. Ha szeretne többet megtudni a többi lehetőségről, olvassa el útmutatóinkat:
    - Állítsa be a [replikációs forrást](physical-azure-set-up-source.md), beleértve a Site Recovery konfigurációs kiszolgálót is.
    - A [replikációs cél](physical-azure-set-up-target.md) beállítása.
    - [Replikációs szabályzat](vmware-azure-set-up-replication.md) konfigurálása és a [replikáció engedélyezése](vmware-azure-enable-replication.md).


### <a name="set-up-an-azure-account"></a>Azure-fiók beállítása

Szerezzen be egy Microsoft [Azure-fiókot](https://azure.microsoft.com/).

- Kezdésként használhatja az [ingyenes próbaverziót](https://azure.microsoft.com/pricing/free-trial/) is.
- További információ a [Webhely-helyreállítási díjszabásról](site-recovery-faq.md#pricing), és [az árképzésrészleteinek beszerzése](https://azure.microsoft.com/pricing/details/site-recovery/).
- Megtudhatja, hogy mely [régiók támogatottak](https://azure.microsoft.com/pricing/details/site-recovery/) a Site Recovery számára.

### <a name="verify-azure-account-permissions"></a>Az Azure-fiók engedélyeinek ellenőrzése

Győződjön meg arról, hogy az Azure-fiók rendelkezik a virtuális gépek Azure-ba való replikálására vonatkozó engedélyekkel.

- Tekintse át a gépek Azure-ba replikálásához szükséges [engedélyeket.](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)
- Szerepköralapú [hozzáférési](../role-based-access-control/role-assignments-portal.md) engedélyek ellenőrzése és módosítása. 



### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Hozzon létre egy [Azure-hálózatot.](../virtual-network/quick-create-portal.md)

- Az Azure virtuális gépek kerülnek ezen a hálózaton, amikor feladatátvétel után jönnek létre.
- A hálózatnak ugyanabban a régióban kell lennie, mint a Recovery Services tárolójának


## <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Állítson be egy [Azure-tárfiókot.](../storage/common/storage-account-create.md)

- A Site Recovery replikálja a helyszíni gépeket az Azure storage-ba. Az Azure virtuális gépek a feladatátvétel után jönnek létre a tárolóból.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.


### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A Mobilitás szolgáltatást minden replikálni kívánt kiszolgálóra telepíteni kell. A Site Recovery automatikusan telepíti ezt a szolgáltatást, amikor engedélyezi a replikációt a kiszolgálón. Az automatikus telepítéshez elő kell készítenie egy fiókot, amelyet a Site Recovery a kiszolgáló eléréséhez használ.

- Tartomány- vagy helyi fiókot is használhat
- Windows virtuális gépek esetén, ha nem tartományi fiókot használ, tiltsa le a távoli felhasználói hozzáférés vezérlését a helyi számítógépen. Ehhez a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**csoportban adja hozzá a **LocalAccountTokenFilterPolicy**duplaszó bejegyzést, amelynek értéke 1.
- Ha hozzá szeretné adni a beállításjegyzék-bejegyzést a beállítás CLI-ből való letiltásához, írja be a következőt:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux esetén a fióknak root-nak kell lennie a forrás Linux szerveren.


## <a name="create-a-vault"></a>Tároló létrehozása

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-a-protection-goal"></a>Védelmi cél kiválasztása

Válassza ki, hogy mit szeretne replikálni, és mire szeretné replikálni.

1. Kattintson a **Helyreállítási tárak** > tár elemre.
2. Az Erőforrás menüben kattintson a **Helyhelyreállítása** >  > **infrastruktúra-védelem előkészítése parancsra.****Prepare Infrastructure**
3. A **Védelem cél ban**válassza az **Azure** > **Nem virtualizált/egyéb**lehetőséget.

## <a name="set-up-the-source-environment"></a>A forráskörnyezet beállítása

Állítsa be a konfigurációs kiszolgálót, regisztrálja azt a tárolóban, és fedezze fel a virtuális gépeket.

1. Kattintson **a Hely helyreállítása** > **infrastruktúra** > előkészítése**forrás parancsra.**
2. Ha nem rendelkezik konfigurációs kiszolgálóval, kattintson **a +Configuration server**gombra.
3. A **Kiszolgáló hozzáadása területen**ellenőrizze, hogy a **konfigurációs kiszolgáló** kiszolgáló **típusú-e.**
4. Töltse le a Site Recovery egyesített telepítési telepítőfájlját.
5. Töltse le a tároló regisztrációs kulcsát. Erre az Egyesített telepítő futtatásakor van szükség. A kulcs a generálásától számított öt napig érvényes.

   ![A forrás beállítása](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>A konfigurációs kiszolgáló regisztrálása a tárolóban

A kezdés előtt tegye a következőket: 

#### <a name="verify-time-accuracy"></a>Az idő pontosságának ellenőrzése
A konfigurációs kiszolgáló számítógépen ellenőrizze, hogy a rendszeróra szinkronizálva [van-e egy időkiszolgálóval.](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) Egyeznie kell. Ha 15 perc van előtte vagy mögöttünk, a beállítás sikertelen lehet.

#### <a name="verify-connectivity"></a>Kapcsolat ellenőrzése
Győződjön meg arról, hogy a készülék a környezetalapján hozzáfér ezekhez az URL-címekhez: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

AZ IP-címalapú tűzfalszabályoknak lehetővé kell tenniük a kommunikációt a https (443) porton keresztül fent felsorolt összes Azure-URL-címhez. Az IP-tartományok egyszerűsítése és korlátozása érdekében ajánlott az URL-szűrés.

- **Kereskedelmi IP-cím** – Az [Azure Datacenter IP-tartományok](https://www.microsoft.com/download/confirmation.aspx?id=41653)és a HTTPS (443) port engedélyezése. Az AAD, a biztonsági mentés, a replikáció és a tárolási URL-címek támogatásához engedélyezze az előfizetés Azure-régiójában az IP-címtartományokat.  
- **Kormányzati IP-címek** – Az AAD, a biztonsági mentés, a replikáció és a tárolási URL-ek támogatásához engedélyezze az [Azure Government Datacenter IP-tartományait](https://www.microsoft.com/en-us/download/details.aspx?id=57063)és a HTTPS (443) portot az összes USGov-régióban (Virginia, Texas, Arizona és Iowa).  

#### <a name="run-setup"></a>A telepítő futtatása
Futtassa az egyesített telepítést helyi rendszergazdaként a konfigurációs kiszolgáló telepítéséhez. A folyamatkiszolgáló és a fő célkiszolgáló alapértelmezés szerint a konfigurációs kiszolgálóra is telepítve van.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

A regisztráció befejezése után a konfigurációs kiszolgáló megjelenik a **tároló Beállítások** > **kiszolgálói** lapján.

## <a name="set-up-the-target-environment"></a>A célkörnyezet beállítása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson **az Infrastruktúra-cél** > **Target**előkészítése gombra, és válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a céltelepítési modellt.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/physical-azure-disaster-recovery/network-storage.png)


## <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Új replikációs házirend létrehozásához kattintson **a Hely-helyreállítási infrastruktúra** > **replikációs házirendjei** > **+Replikációs házirend elemre.**
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). Ez az érték határozza meg, hogy milyen gyakran jönnek létre adat-helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. Az **alkalmazáskonzisztens pillanatkép-gyakoriságban**adja meg, hogy milyen gyakran (percben) alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontok jönnek létre. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Ha például a replikációs házirend **rep-policy,** majd jön létre egy feladat-visszavételi **házirend-házirend-feladat-visszavétel.** Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

## <a name="enable-replication"></a>A replikáció engedélyezése

Engedélyezze a replikációt az egyes kiszolgálókon.

- A Site Recovery akkor telepíti a Mobilitás szolgáltatást, ha a replikáció engedélyezve van.
- Ha engedélyezi a replikációt egy kiszolgálón, a módosítások érvénybe léptetése akár 15 percet is igénybe vehet, és megjelenhet a portálon.

1. Kattintson az > **Alkalmazásforrás** **replikálása gombra.**
2. A **Forrás** mezőben válassza ki a konfigurációs kiszolgálót.
3. A **Géptípus**csoportban válassza **a Fizikai gépek**lehetőséget.
4. Válassza ki a folyamatkiszolgálót (a konfigurációs kiszolgálót). Ezt követően kattintson az **OK** gombra.
5. A **Target alkalmazásban**válassza ki az előfizetést és az erőforráscsoportot, amelyben létre szeretné hozni az Azure virtuális gépeket a feladatátvétel után. Válassza ki az Azure-ban használni kívánt telepítési modellt (klasszikus vagy erőforrás-kezelés).
6. Válassza ki az adatok replikálásához használni kívánt Azure-tárfiókot. 
7. Válassza ki azt az Azure-hálózatot, valamint alhálózatot, amelyhez a feladatátvételt követően felálló Azure virtuális gépek csatlakozni fognak.
8. Ha a megadott hálózati beállításokat az összes védelemre kijelölt gépre szeretné alkalmazni, válassza a **Beállítás most a kijelölt gépekhez** lehetőséget. Válassza **a Később konfigurálása** lehetőséget az Azure-hálózat számítógépenkéntkiválasztásához. 
9. A **Fizikai gépek csoportban**kattintson a **+Fizikai gép gombra.** Adja meg a nevet és az IP-címet. Válassza ki a replikálni kívánt gép operációs rendszerét. A kiszolgálók felderítése és listázása néhány percet vesz igénybe. 
10. A **Tulajdonságok** > **konfigurálása párbeszédpanelen**válassza ki azt a fiókot, amelyet a folyamatkiszolgáló a Mobilrendszer-szolgáltatás automatikus telepítéséhez használni fog a számítógépre.
11. A **Replikációs beállítások** > **konfigurálása**párbeszédpanelen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. 
12. Kattintson **a Replikáció engedélyezése gombra.** A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **feladatok** > **hely-helyreállítási feladatai között követheti**nyomon. A **Véglegesítési védelem** feladat futtatása után a gép készen áll a feladatátvételre.


A hozzáadott kiszolgálók figyeléséhez ellenőrizheti a **konfigurációs kiszolgálók** > utolsó kapcsolatfelvételi területén az utolsó felderített**időpontot.** Ha ütemezett felderítési időpontra való várakozás nélkül szeretne gépeket hozzáadni, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), majd kattintson a **Frissítés gombra.**

## <a name="next-steps"></a>További lépések

[Futtasson vész-helyreállítási gyakorlatot.](tutorial-dr-drill-azure.md)
