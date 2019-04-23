---
title: Azure IaaS virtuális gépek áthelyezése az Azure Government és az Azure Site Recovery szolgáltatással nyilvános régiók közötti |} A Microsoft Docs
description: Az Azure Site Recoveryvel Azure IaaS virtuális gépek áthelyezése az Azure Government- és nyilvános régió között.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: fe2620c7a07389b2a86d36420eadd2ef5883f5da
ms.sourcegitcommit: bf509e05e4b1dc5553b4483dfcc2221055fa80f2
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/22/2019
ms.locfileid: "60013178"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure virtuális gépek áthelyezése az Azure Government és a nyilvános régió között 

Váltás az IaaS-beli virtuális gépek, Azure Government- vagy nyilvános régió a meglévő virtuális gépek rendelkezésre állásának növeléséhez, a kezelhetőség javítása vagy leírtaknak megfelelően cégirányítási okokból érdemes [Itt](azure-to-azure-move-overview.md).

Használata mellett a [Azure Site Recovery](site-recovery-overview.md) szolgáltatás való kezelésére és vezénylésére vészhelyreállítása az Azure-beli és helyszíni gépek üzletmenet-folytonossági és vészhelyreállítási (BCDR) céljából is használhatja a hely Helyreállítási kezelése Azure virtuális gépek áthelyezése egy másodlagos régióba.       

Az oktatóanyag bemutatja, hogyan lehet Azure virtuális gépek áthelyezése az Azure Government és az Azure Site Recovery nyilvános régió között. Azonos virtuális gépek áthelyezése között, amelyek nem ugyanazon a földrajzi fürtön belül régiópárok is kiterjeszthető. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A forrás virtuális gépek előkészítése
> * Készítse elő a célrégió
> * Másolja az adatokat a célrégió
> * A konfiguráció tesztelése
> * Az áthelyezés végrehajtásához
> * Elveti a forrásrégióban található erőforrások

> [!IMPORTANT]
> Az oktatóanyag bemutatja, hogyan helyezheti át az Azure virtuális gépek, Azure Government- vagy nyilvános régióban vagy régiók párok, amely a rendszeres vészhelyreállítási megoldás nem támogatja az Azure virtuális gépek között. Abban az esetben, a forrás és cél régiókat pár [támogatott](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), tekintse meg ezt [dokumentum](azure-to-azure-tutorial-migrate.md) az áthelyezés. Ha a követelmény a rendelkezésre állási zónához rögzített virtuális gépeket egy másik régióban lévő virtuális gépek áthelyezése a rendelkezésre állás növelése érdekében, tekintse meg az oktatóanyag [Itt](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Nem tanácsos ezt a módszert használja a DR konfigurálni között nem támogatott régiópárok, mivel a párok definiált adatkésleltetést tartja szem előtt, ami kritikus fontosságú egy Vészhelyreállítási forgatókönyvhöz.

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

> [!NOTE]
> Győződjön meg arról, hogy megértette a [architektúra és összetevők](physical-azure-architecture.md) ehhez a forgatókönyvhöz. Ez az architektúra áthelyezése az Azure virtuális gépekhez használható **által a virtuális gépek kezelésére, fizikai kiszolgálóként**.

- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](vmware-physical-secondary-support-matrix.md).
- Győződjön meg arról, hogy megfelelnek-e a replikálni kívánt kiszolgálók a [Azure virtuális gépekre vonatkozó](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Fiók előkészítése automatikus telepítés a mobilitási szolgáltatást a replikálni kívánt minden egyes kiszolgálón.

- Vegye figyelembe, hogy miután a célrégióban az Azure-beli hibák, nem közvetlenül végezhet a forrásrégióban vissza a feladatátvétel. Állítsa be újra a replikációt a cél vissza a kell.

### <a name="verify-azure-account-permissions"></a>Azure-fiók szükséges engedélyek ellenőrzése

Ellenőrizze, hogy jogosult az Azure-fiókját az Azure virtuális gépek replikálását.

- Tekintse át a [engedélyek](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) kell gépek replikálása az Azure-bA.
- Ellenőrizze és módosítsa [szerepköralapú hozzáférés-](../role-based-access-control/role-assignments-portal.md) engedélyeket. 

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Állítsa be a cél [az Azure network](../virtual-network/quick-create-portal.md).

- A feladatátvételt követően felálló Azure virtuális gépek kerülnek ehhez a hálózathoz.
- A hálózatnak és a Recovery Services-tárolónak ugyanabban a régióban kell lennie.


### <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Állítsa be egy [Azure storage-fiók](../storage/common/storage-quickstart-create-account.md).

- A Site Recovery replikálja a helyszíni gépek az Azure-tárolóba. Az Azure virtuális gépek a tárolóból lesznek létrehozva, feladatátvételt követően.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.


## <a name="prepare-the-source-vms"></a>A forrás virtuális gépek előkészítése

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A mobilitási szolgáltatásnak telepítve kell lennie minden replikálni kívánt kiszolgálón. A Site Recovery automatikusan telepíti ezt a szolgáltatást, amikor engedélyezi a kiszolgáló replikációját. Automatikusan telepített kell készíteni egy fiókot, amely a Site Recovery a kiszolgálóhoz való hozzáféréshez fogja használni.

- Egy tartományi vagy helyi fiókot is használhat
- Windows virtuális gépek esetében, ha nem használ tartományi fiókot, tiltsa le a távoli felhasználói hozzáférés-vezérlést a helyi gépen. Ehhez a nyilvántartásba alatt **localaccounttokenfilterpolicy**, adja hozzá a DWORD bejegyzést **LocalAccountTokenFilterPolicy**, 1 értékkel.
- A parancssori felületen a beállítás letiltása a beállításjegyzék-bejegyzés hozzáadásához írja be:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- A Linux rendszerre a fióknak rendszergazdai fióknak kell lennie a forrás Linux-kiszolgálón.


## <a name="prepare-the-target-region"></a>Készítse elő a célrégió

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. a Site Recovery használatakor az adatok másolásához a cél választja, az azonos méretű vagy a cél virtuális gép a legközelebbi lehetséges méretét.

3. Győződjön meg arról, hogy minden összetevő, a forrás hálózati elrendezés azonosított célként megadott erőforrás létrehozása. Ez azért fontos, ügyeljen arra, hogy, szélesítheti közzététele a célrégióban, a virtuális gépek rendelkeznek az összes kívánt funkciókat és szolgáltatásokat, a forrás volt.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és létrehoz egy virtuális hálózatot, amikor engedélyezi a replikációt a forrás virtuális gép, vagy is előre a hálózat létrehozása és hozzárendelése a felhasználói folyamatot a replikáció engedélyezése a virtuális Géphez. Azonban más erőforrások, az alábbiakban említett kell manuálisan hozza létre a célrégióban.

     Tekintse meg a következő dokumentumokat a leggyakrabban használt hálózati erőforrások megfelelő létrehozásához, a forrás virtuális gép konfigurációja alapján.

    - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Nyilvános IP-cím](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Bármely más hálózati összetevők, tekintse meg a hálózati [dokumentációját.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Manuálisan [hozzon létre egy nem éles hálózati](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha a konfiguráció tesztelése, a végső kivágási keresztül a célrégióban a végrehajtása előtt. Ez az üzemi minimális megérintése hoz létre, és javasolt.

## <a name="copy-data-to-the-target-region"></a>Másolja az adatokat a célrégió
Az alábbi lépéseket végigvezeti Önt az Azure Site Recovery használata az adatok másolása a célrégióban.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **Recovery Services** szolgáltatásba.
2. Kattintson a **erőforrás létrehozása** > **kezelőeszközök** > **Backup és Site Recovery**.
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha egynél több olyan. előfizetés, válassza ki a megfelelő.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók megtekintéséhez olvassa el az [Azure Site Recovery – Díjszabás](https://azure.microsoft.com/pricing/details/site-recovery/) című cikknek a földrajzi elérhetőséggel foglalkozó részét.
6. Kattintson a Recovery Services-tárolókra **áttekintése** > **ConsotoVMVault** > **+ replikálás**
7. Válassza ki **az Azure-bA** > **nem virtualizált/egyéb**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>A konfigurációs kiszolgáló beállítása a virtuális gépek felderítéséhez.


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

### <a name="configure-target-settings-for-replication"></a>Replikációs cél beállításainak konfigurálása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson az **Infrastruktúra előkészítése** > **Cél** elemre, majd válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a cél üzembehelyezési modellje.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Új replikációs szabályzat létrehozásához kattintson a **Site Recovery-infrastruktúra** > **Replikációs szabályzatok** > **+Replikációs szabályzat** elemre.
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). Ez az érték határozza meg, milyen gyakran jönnek létre adat-helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. A **alkalmazáskonzisztens pillanatkép gyakorisága**, adja meg, milyen gyakran (perc), létrehozza alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontokat. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Például, ha a replikációs szabályzat a **rep-policy** majd a feladat-visszavételi szabályzat **rep-policy-failback** jön létre. Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

### <a name="enable-replication"></a>A replikáció engedélyezése

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

   > [!WARNING]
   > Szeretné áthelyezni, az Azure virtuális gép IP-címét meg kell adnia

10. A **tulajdonságok** > **tulajdonságainak konfigurálása**, válassza ki a folyamatkiszolgáló automatikusan telepíti a mobilitási szolgáltatást a gépen használandó fiókot.
11. A **Replikációs beállítások** > **Replikációs beállítások konfigurálása** területen ellenőrizze, hogy a megfelelő replikációs szabályzat van-e kiválasztva. 
12. Kattintson a **Replikáció engedélyezése** elemre. A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban követheti nyomon. A **Védelem véglegesítése** feladat befejeződését követően a gép készen áll a feladatátvételre.


Figyeléséhez ad hozzá kiszolgálókat, ellenőrizheti azok esetében a legutolsó felderítésének időpontját **konfigurációs kiszolgálók** > **utolsó forduljon,**. Gépek felvétele egy ütemezett felderítés idejének várakozás nélkül, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), és kattintson a **frissítése**.

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Ugrás a tárolóhoz, a **beállítások** > **replikált elemek**, kattintson a virtuális gépet szeretne áthelyezni a célrégióban, kattintson a **+ feladatátvételi teszt** ikon.
2. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás minden virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pontnak feladatait. Megjelenik az időbélyeg.
   - **Egyéni**: Válassza ki bármelyik helyreállítási pontot.

3. Válassza ki a cél Azure-beli virtuális hálózatot, amelyre át szeretné helyezni a az Azure virtuális gépek a konfiguráció tesztelése. 

   > [!IMPORTANT]
   > Azt javasoljuk, hogy használja-e egy különálló Azure-beli Virtuálisgép-hálózaton a feladatátvételi tesztet, és nem az éles hálózati környezetben, amelybe át szeretné helyezni a virtuális gépek idővel, amely be lett állítva, ha engedélyezte a replikációt.

4. Az áthelyezés tesztelése indításához kattintson **OK**. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelése során létrejön a virtuális gép, kattintson a **feladatátvételi teszt utáni karbantartás** a replikált elemen. A **megjegyzések**, jegyezze fel, és mentse a teszt megfigyeléseket.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Hajtsa végre az áttérés a célrégióban, és erősítse meg.

1. Ugrás a tárolóhoz, a **beállítások** > **replikált elemek**, kattintson a virtuális gépen, majd **feladatátvételi**.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. 
4. Ha a feladat befejeződött, ellenőrizze, hogy a virtuális gép megjelenik a cél Azure-régió, a várt módon.
5. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ez befejezi az áttelepítési folyamat a célként megadott régióban. Várjon, amíg a véglegesítés feladat befejeződik.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás a forrásrégióban elvetése 

- Keresse meg a virtuális Gépet.  Kattintson a **tiltsa le a replikációt**.  Ezzel leállítja a virtuális gép számára az adatok másolásának folyamatát.  

   > [!IMPORTANT]
   > Fontos számolják fel ASR-replikáció elkerülése érdekében ez a lépés végrehajtásához.

Abban az esetben nem szándékozik a forrás-erőforrásokat használhat újból folytassa a következő lépések készletét.

1. Lépjen tovább a törli az összes releváns hálózati erőforrást, amely a 4. lépés részeként jelenik meg a forrásrégióban [a forrás virtuális gépek előkészítése](#prepare-the-source-vms) 
2. Törölje a megfelelő tárfiók a forrásrégióban.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy Azure virtuális Gépen egy másik régióba való áthelyezése. Vész-helyreállítási mostantól konfigurálhatja az áthelyezett virtuális gép számára.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)
