---
title: Az Azure-beli virtuális gépek áthelyezése a kormányzati és a nyilvános régiók között az Azure Site Recovery segítségével
description: Az Azure Site Recovery használatával áthelyezi az Azure-beli virtuális gépeket az Azure government és a nyilvános régiók között.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: acaf16e7469b3ea4e5e391db91e37dc76be3b261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298530"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Azure-beli virtuális gépek áthelyezése Azure Government- és nyilvános régiók között 

Előfordulhat, hogy az IaaS-virtuális gépeket az Azure Government és a nyilvános régiók között szeretné áthelyezni a meglévő virtuális gépek rendelkezésre állásának növelése, a kezelhetőség javítása vagy cégirányítási okokból, az [itt](azure-to-azure-move-overview.md)részletezett.

Az [Azure Site Recovery](site-recovery-overview.md) szolgáltatás használatával kezelheti és koordinálhatja a helyszíni gépek és az Azure-beli virtuális gépek vészutáni helyreállítását az üzletmenet folytonossága és a vész-helyreállítás (BCDR) céljából, a Site Recovery használatával is kezelheti az Azure-beli virtuális gépek áthelyezését egy másodlagos régióba.       

Ez az oktatóanyag bemutatja, hogyan helyezheti át az Azure-beli virtuális gépeket az Azure Government és a nyilvános régiók között az Azure Site Recovery használatával. Ugyanez kiterjeszthető a virtuális gépek áthelyezése régiópárok között, amelyek nem ugyanazon a földrajzi fürtön belül. Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Előfeltételek ellenőrzése
> * A forrásvirtuális gépek előkészítése
> * A célterület előkészítése
> * Adatok másolása a célterületre
> * A konfiguráció tesztelése
> * Az áthelyezés végrehajtása
> * A forrásrégió erőforrásainak elvetése

> [!IMPORTANT]
> Ez az oktatóanyag bemutatja, hogyan helyezheti át az Azure-beli virtuális gépeket az Azure Government és a nyilvános régiók között, vagy olyan régiók között, amelyeket az Azure virtuális gépek rendszeres vész-helyreállítási megoldása nem támogat. Abban az esetben, ha a [supported](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)forrás- és célterületpárok támogatottak, kérjük, olvassa el ezt a [dokumentumot](azure-to-azure-tutorial-migrate.md) az áthelyezéshez. Ha a követelmény az, hogy javítsa a rendelkezésre állást azáltal, hogy a virtuális gépek áthelyezése egy rendelkezésre álló készlet zónába rögzített virtuális gépek egy másik régióban, olvassa el az oktatóanyag [itt.](move-azure-VMs-AVset-Azone.md)

> [!IMPORTANT]
> Nem tanácsos ezt a módszert használni a nem támogatott régiópárok között a DR konfigurálásához, mivel a párok meghatározása az adatkésés szem előtt tartása, ami kritikus fontosságú a VÉSZ-forgatókönyv számára.

## <a name="verify-prerequisites"></a>Előfeltételek ellenőrzése

> [!NOTE]
> Győződjön meg arról, hogy tisztában van az [architektúrával és az összetevők](physical-azure-architecture.md) ebben a forgatókönyvben. Ez az architektúra az Azure virtuális gépek áthelyezésére szolgál, **a virtuális gépek fizikai kiszolgálókként való kezelésével.**

- Minden összetevőre vonatkozóan tekintse át a [támogatási követelményeket](vmware-physical-secondary-support-matrix.md).
- Győződjön meg arról, hogy a replikálni kívánt kiszolgálók megfelelnek [az Azure virtuális gép követelményeinek.](vmware-physical-secondary-support-matrix.md#replicated-vm-support)
- Készítsen elő egy fiókot a Mobilitás szolgáltatás automatikus telepítéséhez minden replikálni kívánt kiszolgálón.

- Vegye figyelembe, hogy miután feladatátvételt az Azure-ban a célrégióban, nem hajthatja végre közvetlenül a feladat-visszavétel a forrásrégióba. Újra be kell állítania a replikációt a célértékre.

### <a name="verify-azure-account-permissions"></a>Az Azure-fiók engedélyeinek ellenőrzése

Győződjön meg arról, hogy az Azure-fiók rendelkezik a virtuális gépek Azure-ba való replikálására vonatkozó engedélyekkel.

- Tekintse át a gépek Azure-ba replikálásához szükséges [engedélyeket.](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines)
- Szerepköralapú [hozzáférési](../role-based-access-control/role-assignments-portal.md) engedélyek ellenőrzése és módosítása. 

### <a name="set-up-an-azure-network"></a>Azure-hálózat beállítása

Állítsa be a cél [Azure-hálózatot.](../virtual-network/quick-create-portal.md)

- Az Azure virtuális gépek kerülnek ezen a hálózaton, amikor feladatátvétel után jönnek létre.
- A hálózatnak ugyanabban a régióban kell lennie, mint a Recovery Services tárolójának


### <a name="set-up-an-azure-storage-account"></a>Azure-tárfiók beállítása

Állítson be egy [Azure-tárfiókot.](../storage/common/storage-account-create.md)

- A Site Recovery replikálja a helyszíni gépeket az Azure storage-ba. Az Azure virtuális gépek a feladatátvétel után jönnek létre a tárolóból.
- A tárfióknak és a Recovery Services-tárolónak ugyanabban a régióban kell elhelyezkednie.


## <a name="prepare-the-source-vms"></a>A forrásvirtuális gépek előkészítése

### <a name="prepare-an-account-for-mobility-service-installation"></a>Fiók előkészítése a mobilitási szolgáltatás telepítéséhez

A Mobilitás szolgáltatást minden replikálni kívánt kiszolgálóra telepíteni kell. A Site Recovery automatikusan telepíti ezt a szolgáltatást, amikor engedélyezi a replikációt a kiszolgálón. Az automatikus telepítéshez elő kell készítenie egy fiókot, amelyet a Site Recovery a kiszolgáló eléréséhez használ.

- Tartomány- vagy helyi fiókot is használhat
- Windows virtuális gépek esetén, ha nem tartományi fiókot használ, tiltsa le a távoli felhasználói hozzáférés vezérlését a helyi számítógépen. Ehhez a **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**csoportban adja hozzá a **LocalAccountTokenFilterPolicy**duplaszó bejegyzést, amelynek értéke 1.
- Ha hozzá szeretné adni a beállításjegyzék-bejegyzést a beállítás CLI-ből való letiltásához, írja be a következőt:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Linux esetén a fióknak root-nak kell lennie a forrás Linux szerveren.


## <a name="prepare-the-target-region"></a>A célterület előkészítése

1. Győződjön meg arról, hogy Azure-előfizetése lehetővé teszi-e virtuális gépek létrehozását a vészhelyreállításhoz használni kívánt célrégióban. A szükséges kvóta engedélyezéséhez vegye fel a kapcsolatot az ügyfélszolgálattal.

2. Ellenőrizze, hogy előfizetése rendelkezik-e a megfelelő erőforrásokkal a forrásként szolgáló virtuális gépeknek megfelelő méretű virtuális gépek támogatásához. ha a Site Recovery segítségével másolja az adatokat a cél, akkor kiválasztja az azonos méretű vagy a legközelebbi méretet a cél virtuális gép.

3. Győződjön meg arról, hogy a forráshálózati elrendezésben azonosított összes összetevőhöz létrehoz egy célerőforrást. Ez fontos annak biztosításához, hogy a célrégióra való átvágás után a virtuális gépek minden olyan funkcióval és funkcióval rendelkezzenek, amely a forrásban volt.

    > [!NOTE]
    > Az Azure Site Recovery automatikusan felderíti és létrehozza a virtuális hálózatot, ha engedélyezi a replikációt a forrás virtuális gép, vagy is előre létrehozhat egy hálózatot, és rendelje hozzá a virtuális gép a felhasználói folyamat a replikáció engedélyezéséhez. De minden más erőforrás esetében, ahogy az alább is említettük, manuálisan kell létrehoznia őket a célrégióban.

     Kérjük, olvassa el a következő dokumentumokat a leggyakrabban használt hálózati erőforrások létrehozásához az Ön számára, a forrás virtuális gép konfigurációja alapján.

    - [Hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Terheléselosztók](https://docs.microsoft.com/azure/load-balancer)
    - [Nyilvános IP-cím](../virtual-network/virtual-network-public-ip-address.md)
    
    A többi hálózati összetevőről a hálózati [dokumentációban](https://docs.microsoft.com/azure/?pivot=products&panel=network)tájékozódhat.

4. Manuálisan [hozzon létre egy nem éles hálózatot](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) a célrégióban, ha szeretné tesztelni a konfigurációt, mielőtt végrehajtana a végső vágást a célrégióban. Ez minimális interferenciát okoz a termelésben, és ajánlott.

## <a name="copy-data-to-the-target-region"></a>Adatok másolása a célterületre
Az alábbi lépések bemutatják, hogyan használhatja az Azure Site Recovery segítségével az adatok másolása a célrégióba.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>A forrásrégió kivételével bármelyik régióban létrehozhat tárolót.

1. Jelentkezzen be az [Azure Portal](https://portal.azure.com) > **helyreállítási szolgáltatásokba.**
2. Kattintson **az Erőforráskezelő** > **eszközök** > **biztonsági mentése és a Webhely-helyreállítás elemre.**
3. A **Név** mezőben adja meg a **ContosoVMVault** rövid nevet. Ha több mint egy a. előfizetéssel válassza ki a megfelelőt.
4. Hozzon létre egy **ContosoRG** nevű erőforráscsoportot.
5. Válassza ki a kívánt Azure-régiót. A támogatott régiók ellenőrzéséhez tekintse meg a földrajzi elérhetőséget az [Azure Site Recovery díjszabási részletei ben.](https://azure.microsoft.com/pricing/details/site-recovery/)
6. A Helyreállítási szolgáltatások tárolóiban kattintson **a** > **ConsotoVMVault** > **áttekintése +Replikálás parancsra**
7. Válassza **az Azure-ba** > **nem virtualizált/egyéb**lehetőséget.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Állítsa be a konfigurációs kiszolgálót a virtuális gépek felderítéséhez.


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

### <a name="configure-target-settings-for-replication"></a>A replikáció célbeállításainak konfigurálása

Válassza ki és ellenőrizze a célerőforrásokat.

1. Kattintson **az Infrastruktúra-cél** > **Target**előkészítése gombra, és válassza ki a használni kívánt Azure-előfizetést.
2. Adja meg a céltelepítési modellt.
3. A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal.

   ![Cél](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Replikációs házirend létrehozása

1. Új replikációs házirend létrehozásához kattintson **a Hely-helyreállítási infrastruktúra** > **replikációs házirendjei** > **+Replikációs házirend elemre.**
2. A **Replikációs szabályzat létrehozása** beállításnál adja meg a szabályzat nevét.
3. Az **RPO küszöbértéke** beállításnál adja meg a helyreállítási időkorlátot (RPO). Ez az érték határozza meg, hogy milyen gyakran jönnek létre adat-helyreállítási pontok. A rendszer riasztást ad, ha a folyamatos replikáció túllépi ezt a korlátot.
4. A **Helyreállítási pont megőrzése** beállításnál azt adhatja meg, hogy milyen hosszú (hány órás) legyen az egyes helyreállítási pontok adatmegőrzési időtartama. A replikált virtuális gépek ezen az időtartamon belül bármikor helyreállíthatók. A rendszer a prémium tárolóra replikált gépek esetében 24 órás, a standard tárolóra replikált gépek esetében 72 órás megőrzést támogat.
5. Az **alkalmazáskonzisztens pillanatkép-gyakoriságban**adja meg, hogy milyen gyakran (percben) alkalmazáskonzisztens pillanatképeket tartalmazó helyreállítási pontok jönnek létre. A szabályzat létrehozásához kattintson az **OK** gombra.

    ![Replikációs szabályzat](./media/physical-azure-disaster-recovery/replication-policy.png)


A szabályzat automatikusan társítva lesz a konfigurációs kiszolgálóval. Alapértelmezés szerint a rendszer automatikusan létrehoz egy megfelelő szabályzatot a feladat-visszavételre is. Ha például a replikációs házirend **rep-policy,** majd jön létre egy feladat-visszavételi **házirend-házirend-feladat-visszavétel.** Ezt a szabályzatot nem használja a rendszer, amíg nem indít el egy feladat-visszavételt az Azure-ból.

### <a name="enable-replication"></a>A replikáció engedélyezése

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

   > [!WARNING]
   > Meg kell adnia az áthelyezni kívánt Azure virtuális gép IP-címét

10. A **Tulajdonságok** > **konfigurálása párbeszédpanelen**válassza ki azt a fiókot, amelyet a folyamatkiszolgáló a Mobilrendszer-szolgáltatás automatikus telepítéséhez használni fog a számítógépre.
11. A **Replikációs beállítások** > **konfigurálása**párbeszédpanelen ellenőrizze, hogy a megfelelő replikációs házirend van-e kiválasztva. 
12. Kattintson **a Replikáció engedélyezése gombra.** A **Védelem engedélyezése** feladat előrehaladását a **Beállítások** > **feladatok** > **hely-helyreállítási feladatai között követheti**nyomon. A **Véglegesítési védelem** feladat futtatása után a gép készen áll a feladatátvételre.


A hozzáadott kiszolgálók figyeléséhez ellenőrizheti a **konfigurációs kiszolgálók** > utolsó kapcsolatfelvételi területén az utolsó felderített**időpontot.** Ha ütemezett felderítési időpontra való várakozás nélkül szeretne gépeket hozzáadni, jelölje ki a konfigurációs kiszolgálót (ne kattintson rá), majd kattintson a **Frissítés gombra.**

## <a name="test-the-configuration"></a>A konfiguráció tesztelése


1. Nyissa meg a tárolót, a **Beállítások** > **replikált elemek között**kattintson arra a virtuális gépre, amelyet a célterületre kíván áthelyezni, kattintson a **+Feladatátvétel** ikonra.
2. A **Feladatátvételi teszt** területen válasszon ki egy helyreállítási pontot a feladatátvétel végrehajtásához:

   - **Legutóbb feldolgozott**: A virtuális gép feladatait a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontnak adja át. Megjelenik az időbélyeg. Ezzel a beállítással a rendszer nem tölt időt az adatok feldolgozásával, így a helyreállítási időre vonatkozó célkitűzés (RTO) alacsony.
   - **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép feladatait a legutóbbi alkalmazáskonzisztens helyreállítási pontnak adja át. Megjelenik az időbélyeg.
   - **Egyéni**: Bármelyik helyreállítási pontot kiválaszthatja.

3. Válassza ki a cél Azure virtuális hálózat, amelyhez át szeretné helyezni az Azure virtuális gépek a konfiguráció teszteléséhez. 

   > [!IMPORTANT]
   > Azt javasoljuk, hogy egy külön Azure virtuálisgép-hálózat a teszt feladatátvétel, és nem az éles hálózat, amelybe szeretné mozgatni a virtuális gépek végül, hogy beállított, amikor engedélyezte a replikációt.

4. Az áthelyezés tesztelésének megkezdéséhez kattintson az **OK**gombra. A folyamat előrehaladásának megtekintéséhez a virtuális gépre kattintva megnyithatja a tulajdonságait. Vagy kattintson a **Feladatátvételi teszt** feladatra a tároló neve > **Beállítások** > **Feladatok** > **Site Recovery-feladatok** menüpontban.
5. A feladatátvétel befejezését követően az Azure-beli virtuális gép replikája megjelenik az Azure Portal > **Virtuális gépek** területen. Győződjön meg arról, hogy a virtuális gép fut, megfelelő a méretezése és a megfelelő hálózathoz csatlakozik.
6. Ha törölni szeretné az áthelyezés tesztelésének részeként létrehozott virtuális gép, kattintson a **Karbantartás teszt feladatátvétel** a replikált elem. A **Megjegyzések alkalmazásban**rögzítse és mentse a teszthez kapcsolódó megfigyeléseket.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Végezze el az áthelyezést a célterületre, és erősítse meg.

1. Nyissa meg a tárolót a**Replikált elemek** **beállításai** > között, kattintson a virtuális gépre, majd a **Feladatátvétel**parancsra.
2. A **Feladatátvétel** területen válassza a **Legújabb** elemet. 
3. Válassza a **Gép leállítása a feladatátvétel megkezdése előtt** lehetőséget. A Site Recovery megkísérli leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. A feladatátvételi akkor is folytatódik, ha a leállítás meghiúsul. A feladatátvételi folyamatot a Feladatok lapon **követheti.** 
4. Miután a feladat befejeződött, ellenőrizze, hogy a virtuális gép megjelenik-e a cél Azure-régióban a várt módon.
5. A **Replikált elemek** listában kattintson a jobb gombbal a virtuális gépre, majd kattintson a **Véglegesítés** parancsra. Ezzel befejezi az áthelyezési folyamatot a célterületre. Várjon, amíg a véglegesítési feladat befejeződik.

## <a name="discard-the-resource-in-the-source-region"></a>Az erőforrás elvetése a forrásrégióban 

- Keresse meg a virtuális gép.  Kattintson a **Replikáció letiltása gombra.**  Ez leállítja a virtuális gép adatainak másolását.  

   > [!IMPORTANT]
   > Fontos, hogy hajtsa végre ezt a lépést, hogy elkerülje az ASR-replikáció díjának felszámítását.

Abban az esetben, ha nem tervezi a forráserőforrások újbóli felhasználását, kérjük, folytassa a következő lépésekkel.

1. Folytassa az összes megfelelő hálózati erőforrás törlésével a forrásrégióban, amelyet a [forrásvirtuális gépek előkészítése](#prepare-the-source-vms) című 4. 
2. Törölje a megfelelő tárfiókot a forrásrégióban.



## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban áthelyezett egy Azure-virtuális gép egy másik Azure-régióba. Most konfigurálhatja a vész-helyreállítási az áthelyezett virtuális gép.

> [!div class="nextstepaction"]
> [Vészhelyreállítás beállítása migrálás után](azure-to-azure-quickstart.md)
