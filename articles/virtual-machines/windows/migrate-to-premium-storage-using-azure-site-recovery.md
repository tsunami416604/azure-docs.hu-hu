---
title: Windows-virtuális gépek áttelepítése az Azure Premium Storage szolgáltatásba az Azure Site Recovery segítségével
description: Telepítse át meglévő virtuális gépeit az Azure Premium Storage-ba a Site Recovery használatával. A Prémium szintű storage nagy teljesítményű, alacsony késleltetésű lemeztámogatást nyújt az Azure virtuális gépeken futó I/O-igényű számítási feladatokhoz.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: bd5f9fc787a6299e8d7c14f4b99f6f4d59cf78af
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74819062"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Áttérhet a prémium szintű tárhelyre az Azure Site Recovery használatával

[Az Azure prémium szintű SSD-k](disks-types.md) nagy teljesítményű, alacsony késleltetésű lemeztámogatást nyújtanak az I/O-igényes számítási feladatokat futtató virtuális gépekhez. Ez az útmutató segít áttelepíteni a virtuális gép lemezeit egy normál szintű tárfiókból egy prémium szintű tárfiókba az [Azure Site Recovery](../../site-recovery/site-recovery-overview.md)használatával.

A Site Recovery egy Azure-szolgáltatás, amely hozzájárul az üzletmenet-folytonossági és vész-helyreállítási stratégiához azáltal, hogy a helyszíni fizikai kiszolgálók és virtuális gépek replikációját a felhőbe (Azure) vagy egy másodlagos adatközpontba vezényli. Ha kimaradások fordulnak elő az elsődleges helyen, a feladatátvételt a másodlagos helyre, hogy az alkalmazások és a számítási feladatok elérhetők maradjanak. Ha visszaadja a szokásos működést, visszaadja a feladatát az elsődleges helyre. 

A Site Recovery tesztfeladat-átvételeket biztosít a vész-helyreállítási gyakorlatok támogatásához az éles környezetek befolyásolása nélkül. A feladatátvételeket minimális adatvesztéssel futtathatja (a replikáció gyakoriságától függően) váratlan katasztrófák esetén. A forgatókönyv a prémium szintű storage-ba való áttelepítés, használhatja a [feladatátvétel a Site Recovery](../../site-recovery/site-recovery-failover.md) a céllemezek áttelepítése egy prémium szintű tárfiókba.

Javasoljuk, hogy a Site Recovery használatával migráljon prémium szintű tárhelyre, mert ez a beállítás minimális állásidőt biztosít. Ez a beállítás a lemezek másolásának és az új virtuális gépek létrehozásának manuális végrehajtását is elkerüli. A Site Recovery rendszeresen másolja a lemezeket, és hozzon létre új virtuális gépeket a feladatátvétel során. 

A Site Recovery számos típusú feladatátvételt támogat minimális vagy nincs állásidő nélkül. Az állásidő megtervezéséhez és az adatvesztés becsléséhez tekintse meg a [Feladatátvétel típusait](../../site-recovery/site-recovery-failover.md)a Site Recovery alkalmazásban. Ha [előkészíti az Azure virtuális gépekhez való csatlakozást a feladatátvétel után,](../../site-recovery/vmware-walkthrough-overview.md)képesnek kell lennie arra, hogy az RDP használatával csatlakozzon az Azure virtuális géphez a feladatátvétel után.

![Vész-helyreállítási diagram][1]

## <a name="azure-site-recovery-components"></a>Az Azure site recovery összetevői

Ezek a Site Recovery összetevők relevánsak ebben az áttelepítési forgatókönyvben:

* **A konfigurációs kiszolgáló** egy Azure virtuális gép, amely koordinálja a kommunikációt, és kezeli az adatreplikációs és helyreállítási folyamatokat. Ezen a virtuális gépen egyetlen telepítőfájlt futtat a konfigurációs kiszolgáló és egy további összetevő, az úgynevezett folyamatkiszolgáló replikációs átjáróként való telepítéséhez. További információ a [konfigurációs kiszolgáló előfeltételeiről.](../../site-recovery/vmware-walkthrough-overview.md) A konfigurációs kiszolgálót csak egyszer állíthatja be, és az ugyanabba a régióba történő összes áttelepítéshez használhatja.

* **A folyamatkiszolgáló** olyan replikációs átjáró, amely: 

  1. Replikációs adatok fogadása a forrásvirtuális gépektől.
  2. Gyorsítótárazásával, tömörítéssel és titkosítással optimalizálja az adatokat.
  3. Elküldi az adatokat egy tárfiókba. 

  Emellett kezeli a leküldéses telepítés a mobilitási szolgáltatás a virtuális gépek forrás, és elvégzi a forrás virtuális gépek automatikus felderítése. Az alapértelmezett folyamatkiszolgáló telepítve van a konfigurációs kiszolgálón. További önálló folyamatkiszolgálókat telepíthet a központi telepítés méretezéséhez. Olvassa el [a folyamatkiszolgálók telepítésével](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) és [további folyamatkiszolgálók telepítésével](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers)kapcsolatos gyakorlati tanácsokat. A folyamatkiszolgálót csak egyszer állíthatja be, és az ugyanabba a régióba történő összes áttelepítéshez használhatja.

* **A mobilitási szolgáltatás** olyan összetevő, amely minden replikálni kívánt szabványos virtuális gépen telepítve van. Rögzíti az adatokat a szabványos virtuális gépre, és továbbítja azokat a folyamatkiszolgálóra. Olvassa el a [replikált gép előfeltételeit.](../../site-recovery/vmware-walkthrough-overview.md)

Ez az ábra bemutatja, hogyan hatnak ezek az összetevők:

![A hely-helyreállítási összetevők kölcsönhatása][15]

> [!NOTE]
> A Site Recovery nem támogatja a tárolóhelyek lemezeinek áttelepítését.

További összetevők más forgatókönyvekhez lásd: [Forgatókönyv-architektúra.](../../site-recovery/vmware-walkthrough-overview.md)

## <a name="azure-essentials"></a>Az Azure alapvető fontosságú

Ezek az Azure-követelmények ebben az áttelepítési forgatókönyvben:

* Azure-előfizetés.
* Azure prémium szintű tárfiók replikált adatok tárolására.
* Egy Azure virtuális hálózat, amelyhez a virtuális gépek csatlakoznak, amikor feladatátvételkor jönnek létre. Az Azure virtuális hálózatnak ugyanabban a régióban kell lennie, amelyben a Site Recovery fut.
* Egy Azure standard szintű tárfiók replikációs naplók tárolására. Ez lehet az áttelepítendő virtuálisgép-lemezek azonos tárfiókja.

## <a name="prerequisites"></a>Előfeltételek

* Ismerje meg az előző szakasz megfelelő áttelepítési forgatókönyv-összetevőit.
* Tervezze meg az állásidőt a [Site Recovery feladatátvételének megismerésével.](../../site-recovery/site-recovery-failover.md)

## <a name="setup-and-migration-steps"></a>Telepítési és áttelepítési lépések

A Site Recovery segítségével áttelepítheti az Azure IaaS virtuális gépeket régiók között vagy ugyanazon a régión belül. Az alábbi utasítások ehhez az áttelepítési forgatókönyvhöz vannak szabva a [VMware virtuális gépek vagy fizikai kiszolgálók replikálása az Azure-ba](../../site-recovery/vmware-walkthrough-overview.md)című cikkből. Kérjük, kövesse a linkeket a részletes lépéseket mellett az utasításokat ebben a cikkben.

### <a name="step-1-create-a-recovery-services-vault"></a>1. lépés: Helyreállítási szolgáltatások tárolójának létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza az > **Erőforrás-kezelés** > **biztonsági másolatának létrehozása és a webhely-helyreállítás (OMS) lehetőséget.** **Create a resource** Másik lehetőségként választhatja a**Recovery Services Vault** > **Add** **gombot.** > 
   >[!NOTE]
   >Backup és site recovery korábban része volt az [OMS suite](/azure/azure-monitor/terminology#april-2018---retirement-of-operations-management-suite-brand).
1. Adja meg azt a régiót, amelybe a virtuális gépek replikálni fognak. Az ugyanabban a régióban történő áttelepítés céljából válassza ki azt a régiót, ahol a forrás virtuális gépek és a forrástárfiókok találhatók. 

### <a name="step-2-choose-your-protection-goals"></a>2. lépés: Válaszd ki a védelmi célokat 

1. A virtuális gép, ahol telepíteni szeretné a konfigurációs kiszolgálót, nyissa meg az [Azure Portalon.](https://portal.azure.com)
2. Nyissa meg a Helyreállítási **szolgáltatások tárolóinak** > **beállítások** > **– Helyszíni helyreállítási** > **lépés 1.** > **Protection goal**

   ![Böngészés a Védelem célablaktáblára][2]

3. A **Védelmi cél csoportban**az első legördülő listában válassza az **Azure-ba**lehetőséget. A második legördülő listában válassza a **Nem virtualizált / egyéb**lehetőséget, majd az **OK**lehetőséget.

   ![Védelmi célablak tábla kitöltött dobozokkal][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>3. lépés: A forráskörnyezet beállítása (konfigurációs kiszolgáló)

1. Töltse le **az Azure Site Recovery egyesített telepítőjét** és a tároló regisztrációs kulcsát az Infrastruktúra >  **előkészítése****a kiszolgáló** > **hozzáadása** ablaktáblákon. 
 
   Az egységes beállítás futtatásához szüksége lesz a tároló regisztrációs kulcsára. A kulcs a generálásától számított öt napig érvényes.

   ![Böngészés a Kiszolgáló hozzáadása ablaktáblán][4]

2. A **Kiszolgáló hozzáadása** ablaktáblán adjon hozzá egy konfigurációs kiszolgálót.

   ![Kiszolgáló hozzáadása ablaktábla kijelölt konfigurációs kiszolgálóval][5]

3. A konfigurációs kiszolgálóként használt virtuális gépen futtassa az egyesített telepítőt a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítéséhez. A telepítés befejezéséhez [végigsétálhat a képernyőképeken.](../../site-recovery/vmware-walkthrough-overview.md) Az áttelepítési forgatókönyvhöz megadott lépésekről a következő képernyőképeken tájékolhatja meg.

   1. A **Kezdés előtt**jelölje be a **Konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**lehetőséget.

      ![Mielőtt elkezdené oldal][6]

   2. A **Regisztráció alkalmazásban**tallózással keresse meg és válassza ki a tárolóból letöltött regisztrációs kulcsot.

      ![Regisztrációs lap][7]

   3. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Ebben az áttelepítési forgatókönyvben válassza a **Nem**lehetőséget.

      ![Környezet részletei lap][8]

4. A telepítés befejezése után tegye a következőket a **Microsoft Azure Site Recovery Configuration Server** ablakban:
 
   1. A **Fiókok kezelése** lapon hozza létre azt a fiókot, amelyet a Site Recovery az automatikus felderítéshez használhat. (A fizikai gépek védelmével kapcsolatos forgatókönyvben a fiók beállítása nem releváns, de legalább egy fiókra van szükség az alábbi lépések egyikének engedélyezéséhez. Ebben az esetben a fiókot és a jelszót bármelyikként is elnevezheti.) 
   2. A **Vault regisztráció lapján** töltse fel a tároló hitelesítő adatait.

      ![A Tároló regisztráció lapja][9]

### <a name="step-4-set-up-the-target-environment"></a>4. lépés: A célkörnyezet beállítása

Válassza **az Infrastruktúra-cél** > **Target**előkészítése lehetőséget, és adja meg a virtuális gépekhez a feladatátvétel után használni kívánt telepítési modellt. A forgatókönyvtől függően választhatja a **Klasszikus** vagy **az Erőforrás-kezelő**lehetőséget.

![Cél ablaktábla][10]

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. 

> [!NOTE]
> Ha prémium szintű tárfiókot használ a replikált adatokhoz, be kell állítania egy további szabványos tárfiókot a replikációs naplók tárolásához.

### <a name="step-5-set-up-replication-settings"></a>5. lépés: Replikációs beállítások beállítása

Annak ellenőrzéséhez, hogy a konfigurációs kiszolgáló sikeresen társítva van-e a létrehozott replikációs házirenddel, kövesse a [Replikációs beállítások beállítása című részt.](../../site-recovery/vmware-walkthrough-overview.md)

### <a name="step-6-plan-capacity"></a>6. lépés: Kapacitás megtervezése

1. A [kapacitástervező](../../site-recovery/site-recovery-capacity-planner.md) segítségével pontosan megbecsülheti a hálózati sávszélességet, a tárhelyet és egyéb követelményeket a replikációs igények nek megfelelően. 
2. Ha elkészült, válassza **az Igen, én csináltam a** Van befejezte **kapacitástervezés?**.

   ![Doboz a kapacitástervezés befejezésének megerősítéséhez][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>7. lépés: Telepítse a mobilitási szolgáltatást, és engedélyezze a replikációt

1. Dönthet úgy, hogy [leküldéses telepítés](../../site-recovery/vmware-walkthrough-overview.md) a forrás virtuális gépek, vagy [manuálisan telepítse a mobilitási szolgáltatás](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) a forrás virtuális gépek. A telepítés megnyomásának követelményét és a kézi telepítő elérési útját a megadott linken találja. Ha manuális telepítést végez, előfordulhat, hogy belső IP-címet kell használnia a konfigurációs kiszolgáló megkereséséhez.

   ![A konfigurációs kiszolgáló részletei lap][12]

   A feladatátvevő virtuális gép két ideiglenes lemezt fog rendelkezni: az egyik az elsődleges virtuális gép, a másik a virtuális gép kiépítése során a helyreállítási régióban. Ha a replikáció előtt ki szeretné zárni az ideiglenes lemezt, telepítse a mobilitási szolgáltatást, mielőtt engedélyezné a replikációt. Az ideiglenes lemez kizárásáról a [Lemezek kizárása](../../site-recovery/vmware-walkthrough-overview.md)a replikációból című témakörben olvashat bővebben.

2. A replikációt a következők szerint engedélyezheti:
   1. Válassza az**Alkalmazásforrás** >  **replikálása**lehetőséget. Miután első alkalommal engedélyezte a replikációt, válassza a **+Replikálás** lehetőséget a tárolóban a további gépek replikációjának engedélyezéséhez.
   2. Az 1. **Source**
   3. A 2.
   4. 3. lépésben adja hozzá a védett virtuális gépeket IP-cím szerint. (A kereséshez belső IP-címre lehet szükség.)
   5. 4. lépésben konfigurálja a tulajdonságokat a folyamatkiszolgálón korábban beállított fiókok kiválasztásával.
   6. Az 5.
   7. Válassza **az OK gombot.**

   > [!NOTE]
   > Ha egy Azure virtuális gép felvan oldva, és újraindul, nincs garancia arra, hogy ugyanazt az IP-címet kapja. Ha a konfigurációs kiszolgáló/folyamatkiszolgáló vagy a védett Azure-virtuális gépek IP-címe megváltozik, ebben a forgatókönyvben előfordulhat, hogy a replikáció nem működik megfelelően.

   ![Replikáció engedélyezése a kijelölt Forrás mezővel][13]

Az Azure Storage-környezet tervezésekor azt javasoljuk, hogy egy rendelkezésre állási csoportban minden virtuális géphez külön tárfiókokat használjon. Azt javasoljuk, hogy kövesse az ajánlott gyakorlatot a tárolási [rétegben, hogy több tárfiókot használjon az egyes rendelkezésre állási csoportokhoz.](../linux/manage-availability.md) Virtuálisgép-lemezek több tárfiókok terjesztése segít javítani a tár rendelkezésre állását, és elosztja az I/O az Azure tárolási infrastruktúra között.

Ha a virtuális gépek egy rendelkezésre állási csoportban vannak, ahelyett, hogy az összes virtuális gép lemezeit replikálnák egy tárfiókba, azt javasoljuk, hogy több virtuális gépet többször is áttelepítsünk. Így az azonos rendelkezésre állási csoportban lévő virtuális gépek nem osztoznak egyetlen tárfiókon. A **Replikáció engedélyezése** ablaktábla segítségével hozzon létre egy céltárfiókot minden virtuális gép, egyenként.
 
Igény szerint választhat egy feladatátvétel utáni telepítési modellt. Ha az Azure Resource Manager-t választja a feladatátvétel utáni üzembe helyezési modellként, átveheti a virtuális gép (Erőforrás-kezelő) feladatát egy virtuális gép (Erőforrás-kezelő), vagy feladatátvételi egy virtuális gép (klasszikus) egy virtuális gép (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>8. lépés: Tesztfeladat-átvétel futtatása

Annak ellenőrzéséhez, hogy a replikáció befejeződött-e, jelölje ki a Webhely-helyreállítási példányt, majd válassza a**Replikált elemek** **beállításai** > lehetőséget. Látni fogja a replikációs folyamat állapotát és százalékos arányát. 

A kezdeti replikáció befejezése után futtasson egy tesztfeladat-átvételt a replikációs stratégia érvényesítéséhez. A tesztfeladat-átvétel részletes lépéseit a [Site Recovery tesztfeladat-átvétel futtatása című témakörben található.](../../site-recovery/vmware-walkthrough-overview.md) 

> [!NOTE]
> Mielőtt bármilyen feladatátvételt futtatna, győződjön meg arról, hogy a virtuális gépek és a replikációs stratégia megfelelnek a követelményeknek. A tesztfeladat-átvétel ről további információt a [Feladatátvétel tesztelése az Azure-ban a Site Recovery című témakörben talál.](../../site-recovery/site-recovery-test-failover-to-azure.md)

A tesztfeladat-átvétel állapotát a **Beállítások** > **feladatok** > *YOUR_FAILOVER_PLAN_NAME*. Az ablaktáblán láthatja a lépések és a sikeres/sikertelen eredmények bontását. Ha a teszt feladatátvétel sikertelen bármely lépésnél, válassza ki a lépést, hogy ellenőrizze a hibaüzenetet. 

### <a name="step-9-run-a-failover"></a>9. lépés: Feladatátvétel futtatása

A teszt feladatátvétel befejezése után futtasson egy feladatátvételt a lemezek prémium szintű storage-ba való áttelepítéséhez, és replikálja a virtuálisgép-példányokat. Kövesse a [feladatátvétel futtatása](../../site-recovery/site-recovery-failover.md#run-a-failover)című részletes lépést. 

Ügyeljen arra, hogy válassza **a Virtuális gépek leállítása lehetőséget, és szinkronizálja a legújabb adatokat.** Ez a beállítás azt határozza meg, hogy a Site Recovery megpróbálja leállítani a védett virtuális gépeket, és szinkronizálni az adatokat, hogy az adatok legújabb verziója feladatátvételt vegyen fel. Ha nem választja ezt a beállítást, vagy a kísérlet nem sikerül, a feladatátvétel a virtuális gép legújabb elérhető helyreállítási pontjából lesz. 

A Site Recovery létrehoz egy virtuálisgép-példányt, amelynek típusa megegyezik a prémium szintű storage-képes virtuális géptípussal, vagy ahhoz hasonló. Ellenőrizheti a teljesítményt és az árat a különböző virtuálisgép-példányok megy [a Windows virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy a Linux virtuális gépek [díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>A migrálást követő lépések

1. **Konfigurálja a replikált virtuális gépeket a rendelkezésre állási csoportra, ha van ilyen.** A Site Recovery nem támogatja a virtuális gépek áttelepítését a rendelkezésre állási készlettel együtt. A replikált virtuális gép üzembe helyezésétől függően tegye a következők egyikét:
   * A klasszikus üzembe helyezési modellen keresztül létrehozott virtuális gép: Adja hozzá a virtuális gép az Azure Portalon rendelkezésre állási készlet. A részletes lépésekért nyissa meg [a Meglévő virtuális gép hozzáadása egy rendelkezésre állási csoporthoz című](../linux/classic/configure-availability-classic.md)lehetőséget.
   * Az Erőforrás-kezelő telepítési modelljén keresztül létrehozott virtuális gép esetén: Mentse a virtuális gép konfigurációját, majd törölje és hozza létre újra a virtuális gépeket a rendelkezésre állási csoportban. Ehhez használja a parancsfájlt az [Azure Resource Manager virtuálisgép rendelkezésre állási készletének beállítása kor.](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4) A parancsfájl futtatása előtt ellenőrizze a korlátait, és tervezze meg az állásidőt.

2. **Régi virtuális gépek és lemezek törlése**. Győződjön meg arról, hogy a prémium szintű lemezek konzisztensek a forráslemezekkel, és hogy az új virtuális gépek ugyanazt a funkciót, mint a forrás virtuális gépek. Törölje a virtuális gép, és törölje a lemezeket a forrástárfiókok az Azure Portalon. Ha olyan probléma merül fel, amely miatt a lemez nem törlődik, annak ellenére, hogy törölte a virtuális gép, olvassa el [a Tárolási erőforrás törlési hibáinak elhárítása című témakört.](storage-resource-deletion-errors.md)

3. **Tisztítsa meg az Azure Site Recovery infrastruktúráját.** Ha a Site Recovery-re már nincs szükség, megtisztíthatja az infrastruktúrát. Törölje a replikált elemeket, a konfigurációs kiszolgálót és a helyreállítási szabályzatot, majd törölje az Azure Site Recovery-tárolót.

## <a name="troubleshooting"></a>Hibaelhárítás

* [Virtuális gépek és fizikai kiszolgálók védelmének figyelése és hibaelhárítása](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [Microsoft Azure webhely-helyreállítási fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>További lépések

A virtuális gépek áttelepítésének konkrét forgatókönyveit az alábbi forrásokban tetszhet:

* [Az Azure virtuális gépek áttelepítése a tárfiókok között](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Windows Server virtuális merevlemez létrehozása és feltöltése az Azure alkalmazásba](upload-generalized-managed.md)
* [Virtuális gépek áttelepítése az Amazon AWS-ről a Microsoft Azure-ba](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Az Azure Storage és az Azure virtuális gépekről az alábbi forrásokból is megtudhatja:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure virtuális gépek](https://azure.microsoft.com/documentation/services/virtual-machines/)

[1]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-1.png
[2]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-2.png
[3]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-3.png
[4]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-4.png
[5]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-5.png
[6]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-6.PNG
[7]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-7.PNG
[8]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-8.PNG
[9]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-9.PNG
[10]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-10.png
[11]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-11.PNG
[12]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-12.PNG
[13]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-13.png
[14]:../site-recovery/media/site-recovery-vmware-to-azure/v2a-architecture-henry.png
[15]:./media/migrate-to-premium-storage-using-azure-site-recovery/migrate-to-premium-storage-using-azure-site-recovery-14.png
