---
title: "A Linux virtuális gépek áttelepítése az Azure Site Recovery a prémium szintű Azure Storage |} Microsoft Docs"
description: "A meglévő virtuális gépek áttelepítését a prémium szintű Azure Storage a Site Recovery segítségével. Prémium szintű Storage nagy teljesítményű, alacsony késésű támogatása az Azure virtuális gépeken futó I/O-igényes munkaterhelések kínál."
services: virtual-machines-linux
cloud: Azure
documentationcenter: na
author: luywang
manager: jeconnoc
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.openlocfilehash: d3b5c3a80262adc71374fe30092006fa6cb6865c
ms.sourcegitcommit: be9a42d7b321304d9a33786ed8e2b9b972a5977e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/19/2018
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Prémium szintű Storage telepítse át az Azure Site Recovery segítségével

[Prémium szintű Storage](premium-storage.md) nagy teljesítményű, alacsony késésű lemez I/O-igényes munkaterhelések futó virtuális gépek (VM) támogatását biztosítja. Ez az útmutató segítséget nyújt a virtuális gépek lemezei ki egy standard szintű tárfiók a prémium szintű storage-fiók használatával [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Az Azure-szolgáltatások, az üzletmenet folytonossága és vészhelyreállítás stratégiája hozzájárul a helyszíni fizikai kiszolgálóknak és virtuális gépek a replikáció, az Azure-felhőbe vagy egy másodlagos adatközpontba történő replikálásával. Ha az elsődleges helyen valamilyen okból kimaradás lép fel, hogy átadja a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak. Ön nem az elsődleges helyre, amikor visszatér a normál működés. 

A Site Recovery vészhelyreállítások részletezésének támogatásához anélkül, hogy befolyásolná a termelési környezetben feladatátvételi teszteket biztosít. A feladatátvételeket a váratlan vészhelyzetek esetére (replikáció gyakoriságától függően) minimális adatvesztéssel is futtathatja. Prémium szintű Storage áttelepítésének esetben használhatja a [a Site Recovery feladatátvételi](../../site-recovery/site-recovery-failover.md) céllemezek át a prémium szintű storage-fiók.

Ajánlott, hogy az áttelepítés prémium szintű Storage, a Site Recovery használatával, mert ezt a lehetőséget biztosít a minimális állásidővel. Ez a beállítás megakadályozza, hogy lemásolná a lemezeket, és új virtuális gépek létrehozása manuális végrehajtása. A Site Recovery rendszeresen a lemezek másolása, és hozzon létre új virtuális gépek a feladatátvétel során. 

A Site Recovery támogatja a feladatátvételt a minimális típusú számos vagy állásidő nélkül. Tervezze meg az állásidőt, és adatvesztés becsléséhez, tekintse meg a [a Site Recovery feladatátvételi típusú](../../site-recovery/site-recovery-failover.md). Ha Ön [Felkészülés az Azure virtuális gépek a feladatátvételt követően csatlakozhatnak](../../site-recovery/vmware-walkthrough-overview.md), kell kapcsolódnia kell az Azure virtuális gép a feladatátvételt követően RDP segítségével.

![Vész-helyreállítási diagramja][1]

## <a name="azure-site-recovery-components"></a>Az Azure Site Recovery-összetevők

A Site Recovery összetevők szükségesek a áttelepítési forgatókönyv:

* **Konfigurációs kiszolgáló** van egy Azure virtuális Gépen, amely kommunikáció koordinálását, és az adatok replikációs és helyreállítási folyamatok kezeli. A virtuális gépen, akkor futtassa a konfigurációs kiszolgáló és egy további összetevők telepítése egyetlen telepítőfájlt neve folyamat kiszolgáló, egy replikációs átjáróként. További információ a [konfigurációs kiszolgáló Előfeltételek](../../site-recovery/vmware-walkthrough-overview.md). Úgy állítja be a konfigurációs kiszolgálót csak egyszer, és használhatja az ugyanabban a régióban összes áttelepítéshez.

* **Folyamatkiszolgáló** replikációs átjáró, amely: 

  1. A forrás virtuális gépek kap replikációs adatokat.
  2. Optimalizálja az adatokat a gyorsítótárazás, tömörítés és titkosítás.
  3. Elküldi az adatokat egy tárfiókot. 

  Kezeli a forrás virtuális gépeknek a mobilitási szolgáltatás leküldéses telepítéséhez és a forrás virtuális gépek automatikus felderítést hajt végre. Az alapértelmezett folyamatkiszolgáló a konfigurációs kiszolgálón van telepítve. A telepítés méretezésére további önálló folyamatot kiszolgálók telepítése. További információ a [ajánlott eljárások a folyamatkiszolgáló telepítésének](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) és [további folyamat kiszolgálók üzembe helyezésével](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Csak egyszer állítsa be a folyamatkiszolgáló, és használhatja az ugyanabban a régióban összes áttelepítéshez.

* **Mobilitásiszolgáltatás** minden replikálni kívánt szabványos virtuális Gépnek a telepített összetevője. A normál virtuális Gépen végbemenő adatírásokat, és továbbítja őket a folyamatkiszolgálónak. További információ a [replikált gép Előfeltételek](../../site-recovery/vmware-walkthrough-overview.md).

A kép bemutatja, hogyan működnek együtt ezeket az összetevőket:

![A Site Recovery összetevők együttműködése][15]

> [!NOTE]
> A Site Recovery nem támogatja a tárolóhelyek lemezek az áttelepítés.

Tekintse meg az egyéb forgatókönyvek további összetevők [kialakítandó architektúra](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Azure essentials

Az áttelepítési forgatókönyv szerint az Azure-követelmények a következők:

* Azure-előfizetés.
* Egy prémium szintű Azure storage-fiók replikált adatok tárolásához.
* Egy Azure virtuális hálózatra, amelyhez virtuális gépek csatlakozni fognak Ha feladatátvétel jönnek létre. Az Azure virtuális hálózat ugyanabban a régióban, amelyben a Site Recovery fut azonosnak kell lennie.
* Replikációs naplók tárolásához standard Azure storage-fiók. Ez lehet a virtuális gép lemezeivel áttelepítendő ugyanazt a tárfiókot.

## <a name="prerequisites"></a>Előfeltételek

* Az előző szakaszban leírt megfelelő áttelepítési forgatókönyv-összetevők megismerése.
* Tervezze meg a leállás megismerését által [a Site Recovery feladatátvételi](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Telepítés és áttelepítés lépései

A Site Recovery segítségével Azure IaaS virtuális gépeket áttelepíteni, régiók közötti vagy régión belül. Az alábbi utasításokat a áttelepítési forgatókönyv a cikkből is lefednek [replikálja a VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA](../../site-recovery/vmware-walkthrough-overview.md). Kérjük, kövesse a jelen cikkben lévő utasítások mellett részletes lépéseket a hivatkozásokat.

### <a name="step-1-create-a-recovery-services-vault"></a>1. lépés: A Recovery Services-tároló létrehozása

1. Nyissa meg az [Azure portált](https://portal.azure.com).
2. Válassza ki **új** > **felügyeleti** > **biztonsági mentés** és **(OMS) helyreállítási hely**. Választhatja azt is megteheti, **Tallózás** > **Recovery Services-tároló** > **Hozzáadás**. 
3. Adjon meg egy régiót, amelyben a virtuális gépeket a rendszer replikálja. Ugyanabban a régióban áttelepítéshez válassza ki a régiót, amelyben a forrás virtuális gépeket és a forrás storage-fiókok is. 

### <a name="step-2-choose-your-protection-goals"></a>2. lépés: Jelölje ki a védelmi célok megválasztása 

1. Nyissa meg a virtuális gép, ahol a konfigurációs kiszolgáló telepíteni szeretné, a [Azure-portálon](https://portal.azure.com).
2. Ugrás a **Recovery Services-tárolók** > **beállítások** > **Site Recovery** > **1. lépés: előkészítése Infrastruktúra** > **védelmi cél**.

   ![A védelmi cél ablaktábla tallózással][2]

3. A **védelmi cél**, az első legördülő listában válassza ki a **az Azure-bA**. A második legördülő listában válassza ki a **nem virtualizált vagy egyéb**, majd válassza ki **OK**.

   ![Kitöltött mezőkbe védelmi cél ablaktábla][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>3. lépés: A forráskörnyezet (konfigurációs kiszolgáló) beállítása

1. Töltse le **Azure Site Recovery az egységes telepítő** és a tárolóbeli regisztrációs kulcs nyissa meg a **infrastruktúra előkészítése** > **forrás előkészítése**  >  **Kiszolgáló hozzáadása** ablaktábla. 
 
   Szüksége lesz a tárolóbeli regisztrációs kulcs az egységes telepítő futtatásához. A kulcs a generálásától számított öt napig érvényes.

   ![A kiszolgáló hozzáadása panelen tallózással][4]

2. Az a **kiszolgáló hozzáadása** ablaktáblán, adjon hozzá egy konfigurációs kiszolgálót.

   ![Kiszolgáló ablaktáblán a kiválasztott konfigurációs kiszolgáló hozzáadása][5]

3. A virtuális Gépen, mint a konfigurációs kiszolgálót használ futtassa a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése az egységes telepítő. Is [végezze el a képernyőképek](../../site-recovery/vmware-walkthrough-overview.md) a telepítés befejezéséhez. A megadott áttelepítési forgatókönyvhöz lépéseket a következő képernyőképeket hivatkozhat.

   1. A **előkészületek**, jelölje be **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**.

      ![Mielőtt elkezdené lap][6]

   2. A **regisztrációs**, keresse meg és jelölje ki a regisztrációs kulcsot letöltött a tárolóból.

      ![Regisztrációs lapjához][7]

   3. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Válassza ki az áttelepítési forgatókönyv szerint **nem**.

      ![Környezet részleteit megjelenítő oldalra][8]

4. A telepítés befejezése után, tegye a következőket a **Microsoft Azure Site Recovery konfigurációs kiszolgáló** ablakban:
 
   1. Használja a **fiókok kezelése** lap használatával összeállíthatja a fiókot, hogy a Site Recovery automatikus felderítést is használhat. (A forgatókönyv védelme fizikai gépek, a fiók beállítása nem megfelelő, de az alábbi lépések egyikét ahhoz legalább egy fiók szükséges. Ebben az esetben az nevet adhat a fiók és jelszó, mint bármelyik.) 
   2. Használja a **tároló regisztrációs** lapján töltse fel a tárolói hitelesítő adatok fájlját.

      ![Tároló regisztrálási lapján][9]

### <a name="step-4-set-up-the-target-environment"></a>4. lépés: A célkörnyezet beállítása

Válassza ki **infrastruktúra előkészítése** > **cél**, és adja meg a virtuális gépek a feladatátvételt követően használni kívánt telepítési modellt. Választhat **klasszikus** vagy **erőforrás-kezelő**, attól függően, a forgatókönyv.

![Cél ablaktábla][10]

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. 

> [!NOTE]
> A replikált adatok használata a prémium szintű storage-fiók, hozzon létre egy további standard szintű tárfiók tárolja a replikálási naplókhoz szüksége.

### <a name="step-5-set-up-replication-settings"></a>5. lépés: Replikációs beállítások megadása

Ellenőrizze, hogy a konfigurációs kiszolgáló sikeresen társítva a replikációs házirend, az Ön által létrehozott, hajtsa végre a [replikációs beállítások megadása](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>6. lépés: Csomag kapacitás

1. Használja a [kapacitástervező](../../site-recovery/site-recovery-capacity-planner.md) pontosan a hálózati sávszélesség, tárolási és egyéb követelmények teljesítéséhez a replikáció felbecsülni-nak szüksége van. 
2. Amikor elkészült, válassza ki a **Igen, elvégeztem** a **elvégezte a kapacitástervezést?**.

   ![Erősítse meg, hogy végrehajtotta a kapacitástervezés be][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>7. lépés: Telepítse a mobilitási szolgáltatást, és a replikáció engedélyezése

1. Dönthet úgy, hogy [leküldéses telepítése](../../site-recovery/vmware-walkthrough-overview.md) a forrás virtuális gépeket vagy [manuálisan telepíteni a mobilitási szolgáltatás](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) a forrás virtuális gépeken. A követelmény a telepítési és a kézi telepítő elérési útját küldését a megadott hivatkozás található. Ha manuális telepítés végzett, szükség lehet egy belső IP-címet használja a konfigurációs kiszolgáló található.

   ![Konfigurációs részletek lap][12]

   Virtuális gép feladatátadása két ideiglenes lemezek fog rendelkezni: egyet az elsődleges virtuális gép, a másik a virtuális Gépet a helyreállítási régióban kiépítés során létrehozott. Az ideiglenes a(z) replikáció előtt, telepítse a mobilitási szolgáltatást a replikáció engedélyezése előtt. Az ideiglenes lemez kihagyása kapcsolatos további információkért lásd: [lemezek kizárása a replikációból](../../site-recovery/vmware-walkthrough-overview.md).

2. Replikáció engedélyezése az alábbiak szerint:
   1. Válassza ki **alkalmazás replikálása** > **forrás**. Replikáció első alkalommal történő engedélyezését, válassza ki a **+ replikálás** a további gépek replikációjának engedélyezése a tárolóban lévő állapottal.
   2. Az 1. lépésben beállítása **forrás** folyamat kiszolgálóként.
   3. A 2. lépésben adja meg a feladatátvételt követően üzembe helyezési modellel, a prémium szintű tárfiók át, egy standard szintű tárfiókot naplókat, és hogy áthelyezze a virtuális hálózat mentéséhez.
   4. A 3. lépésben adja hozzá a védett virtuális gépek IP-cím alapján. (Azok megkereséséről belső IP-címet szükség lehet.)
   5. A 4. lépésben az Ön által beállított korábban a folyamatkiszolgáló fiókok kiválasztásával tulajdonságainak konfigurálása.
   6. 5. lépésben, válassza ki a replikációs házirendet, a korábban létrehozott "5. lépés: replikációs beállítások megadása."
   7. Kattintson az **OK** gombra.

   > [!NOTE]
   > Ha egy Azure virtuális gép felszabadítása. lehetséges, és újra elindítani, akkor nem garantálja, hogy megkapja-e az azonos IP-cím. Ha megváltoztatja a konfigurációs kiszolgáló/folyamat kiszolgáló vagy a védett Azure virtuális gépek IP-címét, előfordulhat, hogy ebben a forgatókönyvben a replikálás nem működik megfelelően.

   ![A kijelölt replikációs ablaktábla engedélyezése][13]

Az Azure Storage környezet tervezésekor azt javasoljuk, hogy használja-e külön storage-fiókok az egyes virtuális gépek rendelkezésre állási csoportba. Azt javasoljuk, hogy kövesse az ajánlott eljárás a tárolási rétegben [több tárfiókot használja minden egyes rendelkezésre állási csoport](../linux/manage-availability.md). Virtuális gépek lemezei több tárfiókot fokozza lévő tárterület rendelkezésre állását és az i/o elosztja a Azure tároló-infrastruktúra.

Ha a virtuális gépek rendelkezésre állási csoportba, ahelyett, hogy azok a virtuális gépek lemezek be egy tárfiókot, erősen ajánlott az áttelepítés több virtuális gép többször. Így az azonos rendelkezésre állási csoport a virtuális gépek nem osztják meg egy tárfiókot. Használja a **replikáció engedélyezése** ablaktáblán minden VM, egyenként, a cél tárfiókkal beállítása.
 
Kiválaszthatja a feladatátvételt követően telepítési modell az igényeknek megfelelően. Ha úgy dönt, Azure Resource Manager, a feladatátvételt követően üzembe helyezési modellel, átveheti a virtuális gépek (erőforrás-kezelő) egy virtuális géphez (Resource Manager), vagy átveheti a virtuális gép (klasszikus) egy virtuális géphez (Resource Manager).

### <a name="step-8-run-a-test-failover"></a>8. lépés: Feladatátvételi teszt futtatása

Ellenőrizze, hogy a replikáció befejeződött, válassza ki a Site Recovery-példányt, és válassza ki **beállítások** > **replikált elemek**. Az állapot és a replikáció során százalékos jelenik meg. 

Kezdeti replikáció befejeződése után a replikáció stratégia érvényesítéséhez feladatátvételi teszt futtatása. Feladatátvételi teszt részletes lépéseiért lásd: [feladatátvételi teszt futtatása a Site Recovery](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> A feladatátvétel futtatása előtt győződjön meg arról, hogy a virtuális gépek és a replikációs stratégiájának megfelelnek-e a követelményeknek. Feladatátvételi teszt futtatásával kapcsolatos további információkért lásd: [tesztelése az Azure Site Recovery a](../../site-recovery/site-recovery-test-failover-to-azure.md).

Láthatja, hogy a teszt feladatátvétel állapotának **beállítások** > **feladatok** > *YOUR_FAILOVER_PLAN_NAME*. A panelen tekintheti meg a lépéseket és a sikeres és sikertelen eredmények részletes információkat. A feladatátvételi tesztet bármely lépése meghiúsul, ha válassza ki a lépés tekintse meg a hibaüzenetet. 

### <a name="step-9-run-a-failover"></a>9. lépés: A feladatátvétel futtatása

A vizsgálat után feladatátvétel befejeződött, a lemezek áttelepítése a prémium szintű Storage, és replikálja a Virtuálisgép-példányok feladatátvételt. Kövesse a részletes lépéseket [feladatátvételt](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Ügyeljen arra, hogy válasszon **virtuális gépek leállítása és a legfrissebb adatok szinkronizálása**. Ezzel a beállítással megadhatja, hogy a Site Recovery végezzen el a védett virtuális gépek leállítása és szinkronizálja az adatokat, hogy a legújabb adatok átvétele fog megtörténni. Ha nem ezt a beállítást, vagy a kísérlet nem jár sikerrel, a feladatátvétel nem a virtuális gép számára a legújabb elérhető helyreállítási pontból. 

A Site Recovery hoz létre egy Virtuálisgép-példány, amelynek típusa megegyezik, vagy hasonló egy prémium szintű Storage-kompatibilis virtuális géphez. Ellenőrizheti a teljesítmény és a különböző Virtuálisgép-példányok árát a [Windows Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy [Linux virtuális gépek díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Az áttelepítést követő lépések

1. **A rendelkezésre állási csoportot, ha van ilyen a replikált virtuális gépek**. A Site Recovery nem támogatja a rendelkezésre állási csoport együtt áttelepítése virtuális gépek. Attól függően, hogy a replikált virtuális gép telepítése esetén tegye a következők egyikét:
   * A klasszikus üzembe helyezési modell használatával létrehozza a virtuális gépek: a virtuális gép hozzáadása a rendelkezésre állási csoportot az Azure portálon. A részletes lépéseket lásd a [hozzáadása egy meglévő virtuális gép rendelkezésre állási csoportok](../linux/classic/configure-availability-classic.md).
   * A Resource Manager üzembe helyezési modellel keresztül létrehozza a virtuális gépek: a virtuális gép a konfiguráció mentéséhez, és törölje és hozza létre a virtuális gépek a rendelkezésre állási csoport. Ehhez használja a következő parancsfájl [beállítása Azure Resource Manager virtuális gép rendelkezésre állási csoport](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Ez a parancsfájl futtatása előtt ellenőrizze a korlátozások vonatkoznak, és tervezze meg a leállás.

2. **Törölje a régi virtuális gépek és lemezek**. Győződjön meg arról, hogy a Premium lemezek forráslemezekhez konzisztens és, hogy az új virtuális gépek a forrás virtuális gépeknek ugyanazon művelet végrehajtására szolgál(nak). Törölje a virtuális gép, és a lemezek törlése a forrás storage-fiókok az Azure portálon. Ha probléma, amely a lemez nem törölt, annak ellenére, hogy törli a virtuális gép, lásd: [hibák elhárítása a virtuális merevlemezek törlésekor](../../storage/common/storage-resource-manager-cannot-delete-storage-account-container-vhd.md).

3. **Az Azure Site Recovery-infrastruktúra tiszta**. Ha már nincs szükség a Site Recovery, törölheti is az infrastruktúra. Törölje a replikált elemek, a konfigurációs kiszolgáló és a helyreállítási irányelv, és törölje az az Azure Site Recovery-tárolóban.

## <a name="troubleshooting"></a>Hibaelhárítás

* [Figyelése és hibaelhárítása szempontjából a virtuális gépek és fizikai kiszolgálók védelme](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [A Microsoft Azure Site Recovery-fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>További lépések

Virtuális gépek meghatározott forgatókönyvek lásd a következőket:

* [Az Azure virtuális gépek közötti Storage-fiókok áttelepítése](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Hozzon létre, és töltse fel a Windows Server VHD az Azure-bA](../windows/classic/createupload-vhd.md)
* [Létrehozás és a Linux operációs rendszert tartalmazó virtuális merevlemez feltöltése](../linux/classic/create-upload-vhd-classic.md?toc=%2fazure%2fvirtual-machines%2flinux%2fclassic%2ftoc.json)
* [Virtuális gépek áttelepítési Amazon AWS a Microsoft Azure](http://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

A következő források további információt az Azure Storage és az Azure virtuális gépek lásd még:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)
* [Premium Storage: Nagy teljesítményű tárolási szolgáltatás Azure-beli virtuális gépek számítási feladataihoz](premium-storage.md)

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
