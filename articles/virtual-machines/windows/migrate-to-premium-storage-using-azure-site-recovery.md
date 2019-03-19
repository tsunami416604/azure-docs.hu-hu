---
title: A Windows virtuális gépek áttelepítése az Azure Premium Storage az Azure Site Recoveryvel |} A Microsoft Docs
description: A virtuális gépek migrálása az Azure Premium Storage a Site Recovery használatával. A Premium Storage nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokhoz az Azure Virtual machines szolgáltatásban futó kínál.
services: virtual-machines-windows,storage
cloud: Azure
author: luywang
ms.service: virtual-machines-windows
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 08/15/2017
ms.author: luywang
ms.subservice: disks
ms.openlocfilehash: 5744ee76d36b1cea256cd2594bcbc07c954f38f7
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57993286"
---
# <a name="migrate-to-premium-storage-by-using-azure-site-recovery"></a>Migrálás prémium szintű tárolóba az Azure Site Recovery használatával

[Az Azure prémium szintű SSD-k](disks-types.md) biztosításához nagy teljesítményű, kis késleltetésű lemeztámogatás I/O-igényes számítási feladatokat futtató virtuális gépek (VM). Az útmutató segítségével telepítheti át a VM-lemezeit, egy standard szintű storage-fiókból premium storage-fiók használatával [Azure Site Recovery](../../site-recovery/site-recovery-overview.md).

Site Recovery szolgáltatás egy Azure-szolgáltatás, amely replikálja a helyszíni fizikai kiszolgálóknak és virtuális gépek az Azure-felhőbe vagy egy másodlagos adatközpontba a üzletmenet-folytonossági és vészhelyreállítási stratégia megvalósításában. Amikor a leállások esetén az elsődleges helyen, átadja a feladatokat a másodlagos helynek, így az alkalmazások és számítási feladatok nem állnak. Visszaadja a feladatokat az elsődleges helyen amikor visszatér a normál működést. 

A Site Recovery feladatátvételi teszteket a vészhelyreállítási próbák támogatására anélkül, hogy ez hatással lenne az éles környezetet biztosít. Feladatátvételeket a váratlan vészhelyzetek esetére (replikáció gyakoriságától függően) minimális adatvesztéssel járó futtathatja. A Premium Storage-ba való migrálás foglalkozó forgatókönyvben is használhatja a [feladatátvétel a Site Recoveryben](../../site-recovery/site-recovery-failover.md) lemezeit migrálása a premium storage-fiók.

Azt javasoljuk, hogy a Premium Storage-ba való migrálás Site Recovery használatával, mert ez a beállítás teszi minimális üzemen kívüli idővel. Ezt a lehetőséget is elkerülheti a lemezek másolásának és új virtuális gépek létrehozásának manuális végrehajtását. A Site Recovery rendszeresen másolja a lemezeket, és hozzon létre új virtuális gépeket a feladatátvétel során. 

A Site Recovery támogatja a különféle típusú feladatátvételi kódmódosítással vagy állásidő nélkül. Az állásidő és megbecsülheti az adatvesztést, tekintse meg a [feladatátvétel a Site Recoveryben típusú](../../site-recovery/site-recovery-failover.md). Ha Ön [előkészítése az Azure virtuális géphez való kapcsolódásra a feladatátvételt követően](../../site-recovery/vmware-walkthrough-overview.md), akkor tud csatlakozni az Azure virtuális gép a feladatátvételt követően RDP segítségével kell lennie.

![Vész-helyreállítási diagramja][1]

## <a name="azure-site-recovery-components"></a>Az Azure Site Recovery-összetevők

A Site Recovery-összetevők kapcsolódik az áttelepítési forgatókönyvben:

* **Konfigurációs kiszolgáló** van egy Azure virtuális Gépen, amely kommunikáció koordinálását, valamint az adatok replikálását és helyreállítását folyamatokat kezeli. A virtuális gépen, akkor futtassa a konfigurációs kiszolgáló és a egy további összetevő telepítéséhez egyetlen telepítőfájlját nevű a folyamatkiszolgáló replikációs átjáróként. További információ [konfigurációs kiszolgálóra vonatkozó Előfeltételek](../../site-recovery/vmware-walkthrough-overview.md). Csak egyszer állítsa be a konfigurációs kiszolgálót, és használhatja az összes áttelepítéshez ugyanabban a régióban.

* **A folyamatkiszolgáló** egy replikációs átjáró, amely: 

  1. Fogadja a replikált adatokat a forrás virtuális gépeket.
  2. Optimalizálja az adatokat a gyorsítótárazás, tömörítés és titkosítás.
  3. Az adatokat küld egy tárfiókba. 

  Kezeli a forrás virtuális gépeket a mobilitási szolgáltatás leküldéses telepítését és a forrás virtuális gépek automatikus felderítését végzi. Az alapértelmezett folyamatkiszolgáló telepítve van a konfigurációs kiszolgálón. Telepíthet további önálló folyamatkiszolgálók a telepítés méretezésére. További információ [ajánlott eljárásai folyamatkiszolgáló telepítésének](https://azure.microsoft.com/blog/best-practices-for-process-server-deployment-when-protecting-vmware-and-physical-workloads-with-azure-site-recovery/) és [további folyamatkiszolgálók üzembe helyezése](../../site-recovery/site-recovery-plan-capacity-vmware.md#deploy-additional-process-servers). Csak egyszer állítsa be a folyamatkiszolgáló, és használhatja az összes áttelepítéshez ugyanabban a régióban.

* **A mobilitási szolgáltatás** egy összetevő, amely telepítve van minden standard szintű virtuális gép, amelyet replikálni szeretne. Azt rögzíti az adatok írásáért, a standard szintű virtuális gépen, és továbbítja őket a folyamatkiszolgálónak. További információ [replikált gép Előfeltételek](../../site-recovery/vmware-walkthrough-overview.md).

Az ábrán látható, hogyan ezeket az összetevőket használják:

![A Site Recovery-összetevők együttműködése][15]

> [!NOTE]
> A Site Recovery nem támogatja az áttelepítést, a tárolóhelyek lemezek.

Más esetekben a további összetevők, lásd: [forgatókönyv-architektúrát](../../site-recovery/vmware-walkthrough-overview.md).

## <a name="azure-essentials"></a>Az Azure essentials

Az Azure-áttelepítési forgatókönyvhöz követelmények a következők:

* Azure-előfizetés.
* Az Azure premium storage-fiók replikált adatok tárolásához.
* Egy Azure virtuális hálózatra, amelyhez virtuális gépek csatlakoznak, amikor a feladatátvétel során jönnek létre. Az Azure virtuális hálózat ugyanabban a régióban, amelyben a Site Recovery fut azonosnak kell lennie.
* Az Azure standard szintű tárfiók replikációs naplóinak tárolásához. Ez lehet ugyanazt a tárfiókot az áttelepítendő virtuális gép lemezeivel kapcsolatban.

## <a name="prerequisites"></a>Előfeltételek

* Az előző szakaszban a megfelelő áttelepítési forgatókönyv-összetevők ismertetése.
* Az állásidő megtervezése tanulás kapcsolatos által [feladatátvétel a Site Recoveryben](../../site-recovery/site-recovery-failover.md).

## <a name="setup-and-migration-steps"></a>Telepítés és áttelepítés lépései

A Site Recovery segítségével Azure IaaS virtuális gépek migrálása,-régiók között, vagy ugyanazon régión belül. Ez a cikk az áttelepítési forgatókönyv az alábbi utasításokat is lefednek [replikálni a VMware virtuális gépek vagy fizikai kiszolgálók Azure-bA](../../site-recovery/vmware-walkthrough-overview.md). Kövesse a részletes lépésekről lásd a jelen cikkben lévő utasítások mellett a hivatkozásokat.

### <a name="step-1-create-a-recovery-services-vault"></a>1. lépés: Recovery Services-tároló létrehozása

1. Nyissa meg az [Azure Portalt](https://portal.azure.com).
2. Válassza ki **erőforrás létrehozása** > **felügyeleti** > **Backup és Site Recovery (OMS)**. Másik lehetőségként kiválaszthatja **Tallózás** > **Recovery Services-tároló** > **Hozzáadás**.
   >[!NOTE]
   >Backup és Site Recovery lett korábban része a ![OMS csomagot](https://github.com/MicrosoftDocs/azure-docs-pr/pull/azure-monitor/azure-monitor-rebrand.md#retirement-of-operations-management-suite-brand).
1. Adjon meg egy régióban, amely a virtuális gépeket replikálja. Áttelepítéshez ugyanabban a régióban válassza ki a régiót, ahol a forrás virtuális gépek és a forrás-tárfiókok vannak. 

### <a name="step-2-choose-your-protection-goals"></a>2. lépés: Védelmi célok megválasztása 

1. A virtuális gép, ahol szeretné telepíteni a konfigurációs kiszolgálót, nyissa meg a [az Azure portal](https://portal.azure.com).
2. Lépjen a **Recovery Services-tárolók** > **beállítások** > **Site Recovery** > **1. lépés: Az infrastruktúra előkészítése** > **védelmi cél**.

   ![Keresse meg a védelmi cél ablaktábla][2]

3. A **védelmi cél**, az első legördülő listában válassza ki a **az Azure-bA**. A második legördülő listában válassza ki a **nem virtualizált / egyéb**, majd válassza ki **OK**.

   ![Védelmi cél panelről mezők ki vannak töltve][3]

### <a name="step-3-set-up-the-source-environment-configuration-server"></a>3. lépés: A forráskörnyezet (konfigurációs kiszolgáló) beállítása

1. Töltse le **Azure Site Recovery egyesített telepítőjének** , és nyissa meg a tárregisztrációs kulcsot a **infrastruktúra előkészítése** > **forrás előkészítése**  >  **-Kiszolgáló hozzáadása** ablaktáblái. 
 
   Szüksége lesz a tárregisztrációs kulcsot az egységes telepítő futtatásához. A kulcs a generálásától számított öt napig érvényes.

   ![Keresse meg a kiszolgáló hozzáadása panel][4]

2. Az a **-kiszolgáló hozzáadása** ablaktáblán adja hozzá a konfigurációs kiszolgáló.

   ![Adja hozzá a kiszolgáló panelen a kiválasztott konfigurációs kiszolgáló][5]

3. A konfigurációs kiszolgálóként használt virtuális Gépen futtassa az egyesített telepítő a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése. Is [végig a képernyőképek](../../site-recovery/vmware-walkthrough-overview.md) a telepítés befejezéséhez. Az alábbi képernyőfelvételnek megfelelően megadva a áttelepítési forgatókönyv lépéseit is hivatkozunk.

   1. A **alapismeretek**válassza **a konfigurációs kiszolgáló és a folyamatkiszolgáló telepítése**.

      ![Lap megkezdése előtt][6]

   2. A **regisztrációs**, keresse meg és válassza ki a, a kulcstartóból letöltött regisztrációs kulcsot.

      ![A regisztrációs lapon][7]

   3. A **Környezet részletei** területen válassza ki, hogy kívánja-e replikálni a VMware virtuális gépeket. Válassza ki az áttelepítési forgatókönyvben **nem**.

      ![Környezet részletei lap][8]

4. A telepítés befejeződése után hajtsa végre a következőket a **a Microsoft Azure Site Recovery konfigurációs kiszolgálónak** ablakban:
 
   1. Használja a **fiókok kezelése** fülre, és hozza létre a fiókot, hogy a Site Recovery automatikus felderítéshez használhatja. (A fizikai gépek védelméről a forgatókönyvben a fiók beállítása nem megfelelő, de legalább egy fiókot a következő lépések egyikét ahhoz van szüksége. Ebben az esetben nevet adhat a fiókot és jelszót, mint bármelyik.) 
   2. Használja a **tár regisztrálása** fülre, és töltse fel a tároló hitelesítőadat-fájlja.

      ![Tár regisztrálási lapját][9]

### <a name="step-4-set-up-the-target-environment"></a>4. lépés: A célkörnyezet beállítása

Válassza ki **infrastruktúra előkészítése** > **cél**, és adja meg a feladatátvételt követően a virtuális gépekhez használni kívánt üzemi modellhez. Választhat **klasszikus** vagy **Resource Manager**, attól függően, a forgatókönyv.

![Cél panel][10]

A Site Recovery ellenőrzi, hogy rendelkezik-e legalább egy kompatibilis Azure-tárfiókkal és -hálózattal. 

> [!NOTE]
> Ha prémium szintű tárfiókot használ a replikált adatok, állítsa be egy további standard szintű tárfiók replikációs naplóinak tárolásához szeretné.

### <a name="step-5-set-up-replication-settings"></a>5. lépés: Replikációs beállítások megadása

Győződjön meg arról, hogy a konfigurációs kiszolgáló sikeresen társítva a replikációs házirendet, Ön által létrehozott, hajtsa végre a [replikációs beállítások megadása](../../site-recovery/vmware-walkthrough-overview.md).

### <a name="step-6-plan-capacity"></a>6. lépés: Kapacitás tervezése

1. Használja a [kapacitás planner](../../site-recovery/site-recovery-capacity-planner.md) sávszélességel a hálózati sávszélesség, tárolási és egyéb követelményeket, a replikációs kell rendelkeznie. 
2. Ha elkészült, válassza ki a **Igen, elvégeztem** a **rendelkeznie végrehajtotta kapacitástervezés?**.

   ![Be megerősíti, hogy elvégezte kapacitásának megtervezése][11]

### <a name="step-7-install-the-mobility-service-and-enable-replication"></a>7. lépés: A mobilitási szolgáltatás telepítése és a replikáció engedélyezése

1. Dönthet úgy [leküldéses telepítése](../../site-recovery/vmware-walkthrough-overview.md) a forrás virtuális gépeket vagy [manuálisan a mobilitási szolgáltatás telepítése](../../site-recovery/site-recovery-vmware-to-azure-install-mob-svc.md) a forrás virtuális gépeket. Megtalálhatja a követelménynek, telepítés és a manuális telepítő elérési útját leküldése a hivatkozásra. Ha a manuális telepítés, szüksége lehet a konfigurációs kiszolgáló belső IP-címet használ.

   ![Konfigurációs kiszolgáló részletei lapon][12]

   A feladatátvételi virtuális Gépen két ideiglenes lemezek fog rendelkezni: egyet az elsődleges virtuális gép, a másik a helyreállítási régióban található virtuális gép kiépítése során létrehozott. Ki szeretne zárni az ideiglenes lemez replikáció előtt, telepítse a mobilitási szolgáltatást a replikáció engedélyezése előtt. Az ideiglenes lemez kizárása kapcsolatos további információkért lásd: [lemezek kizárása a replikációból](../../site-recovery/vmware-walkthrough-overview.md).

2. A replikációt a következők szerint engedélyezheti:
   1. Válassza ki **alkalmazás replikálása** > **forrás**. Miután engedélyezte a replikálás első alkalommal, válassza ki a **+ replikálás** további gépek replikációjának engedélyezéséhez a tárolóban.
   2. Az 1. lépésben, állítsa be **forrás** , a folyamatkiszolgáló.
   3. A 2. lépésben adja meg a feladatátvételt követő üzembe helyezési modellt, migrálása a premium storage-fiók, egy standard szintű tárfiókot a naplók és sikertelen lesz, virtuális hálózat mentése.
   4. A 3. lépésben adja hozzá a védett virtuális gépek IP-cím alapján. (Belső IP-cím megtalálni, szüksége lehet.)
   5. A 4. lépésben a fiókok, akkor állítsa be a korábban, a folyamatkiszolgáló kiválasztásával tulajdonságainak konfigurálása.
   6. 5. lépésben pedig válassza ki a replikációs házirendet, a korábban létrehozott "5. lépés: Replikációs beállítások megadása."
   7. Kattintson az **OK** gombra.

   > [!NOTE]
   > Ha egy Azure virtuális gép nincs lefoglalva, és újra elindult, nincs garancia arra, hogy megjelenik-e az azonos IP-cím. Ha a konfigurációs kiszolgáló-/ folyamatkiszolgálón, vagy az Azure virtuális gép IP-címe megváltozik, előfordulhat, hogy ebben a forgatókönyvben a replikálás nem működik megfelelően.

   ![A kiválasztott replikációs panel engedélyezése][13]

Amikor Azure Storage-környezetében, azt javasoljuk, hogy használjon külön storage-fiókot minden virtuális gép egy rendelkezésre állási csoportban. Azt javasoljuk, hogy kövesse az ajánlott eljárás, a tárolási rétegben [több tárfiók használata az egyes rendelkezésre állási](../linux/manage-availability.md). Több tárfiók kezelése virtuális gépek lemezei fokozza a tárterület rendelkezésre állását és az i/o elosztja a az Azure tároló-infrastruktúra.

Ha a virtuális gépek rendelkezésre állási csoport, az összes virtuális gép lemezeket egy tárfiókba, ahelyett erősen ajánlott áttelepítése több virtuális gép több alkalommal. Ezzel a módszerrel a virtuális gépek ugyanazon rendelkezésre állási csoportban ne ossza meg az egy tárfiókban. Használja a **replikáció engedélyezése** panelen az egyes virtuális Gépekhez, egyenként a cél tárfiók beállításához.
 
Feladatátvétel utáni telepítési modell kiválaszthatja az igényeknek megfelelően. Ha úgy dönt, az Azure Resource Manager, a feladatátvétel utáni telepítési modell, átadhatja a virtuális géphez (Resource Manager) egy virtuális Géphez (Resource Manager), vagy átadhatja a virtuális géphez (Resource Manager) (klasszikus) virtuális gép.

### <a name="step-8-run-a-test-failover"></a>8. lépés: Feladatátvételi teszt futtatása

Annak ellenőrzéséhez, hogy a replikálás is befejeződik, válassza ki a Site Recovery-példány majd **beállítások** > **replikált elemek**. Látni fogja az állapot és a replikációs folyamat százalékos aránya. 

Kezdeti replikáció befejeződése után futtasson egy feladatátvételi tesztet, ellenőrizze a replikációs stratégiát. Feladatátvételi teszt részletes lépéseiért lásd: [feladatátvételi teszt futtatása a Site Recoveryben](../../site-recovery/vmware-walkthrough-overview.md). 

> [!NOTE]
> Bármely a feladatátvétel előtt győződjön meg arról, hogy a virtuális gépek és a replikációs stratégiát megfelelnek-e a követelményeknek. Feladatátvételi teszt futtatásával kapcsolatos további információkért lásd: [tesztelheti a feladatátvételeket, az Azure-bA a Site Recoveryben](../../site-recovery/site-recovery-test-failover-to-azure.md).

Láthatja, hogy a teszt feladatátvétel állapotának **beállítások** > **feladatok** > *YOUR_FAILOVER_PLAN_NAME*. A panelen megjelenik egy bontása a lépéseket és a sikeres/sikertelen eredményt. Ha a feladatátvételi tesztet bármely lépése meghiúsul, válassza ki a lépés a hibaüzenetben. 

### <a name="step-9-run-a-failover"></a>9. lépés: Feladatátvétel futtatása

A teszt feladatátvételi futása befejeződött, feladatátvételt végez a lemezek áttelepítése a Premium Storage és a Virtuálisgép-példányok replikálni. Kövesse a részletes lépéseket [feladatátvételt](../../site-recovery/site-recovery-failover.md#run-a-failover). 

Ügyeljen arra, hogy válasszon **leállíthatja a virtuális gépeket, és a legfrissebb adatok szinkronizálása**. Ezzel a beállítással megadhatja, hogy a Site Recovery próbálja állítsa le a védett virtuális gépeket, és szinkronizálja az adatokat, így az adatok a legújabb verzióra fog a feladatátvételt. Ha nem ezt a beállítást, vagy a kísérlet nem sikerül, akkor a feladatátvétel a a legújabb elérhető helyreállítási pontot a virtuális gép lesz. 

A Site Recovery létrehoz egy Virtuálisgép-példánnyal, amelynek típusa megegyezik vagy hasonló prémium szintű Storage-kompatibilis virtuális géphez. Ellenőrizheti a teljesítmény és a különféle Virtuálisgép-példányok árát a [Windows Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) vagy [Linux Virtual Machines díjszabása](https://azure.microsoft.com/pricing/details/virtual-machines/linux/).

## <a name="post-migration-steps"></a>Az áttelepítést követő lépések

1. **Konfigurálja a replikált virtuális gépek a rendelkezésre állási csoportot, ha van ilyen**. A Site Recovery nem támogatja a rendelkezésre állási csoport együtt át virtuális gépek. Attól függően, a replikált virtuális gép telepítése esetén tegye a következők egyikét:
   * A klasszikus üzemi modellel létrehozott virtuális gépek: Adja hozzá a virtuális Gépet a rendelkezésre állási csoportot az Azure Portalon. Lépjen a részletes lépéseket [hozzáadása egy meglévő virtuális gépet egy rendelkezésre állási csoporthoz](../linux/classic/configure-availability-classic.md).
   * A Resource Manager-alapú üzemi modellel létrehozott virtuális gépek: Mentse a konfigurációt a virtuális gép, és ezután törölje és hozza létre újból a virtuális gépek rendelkezésre állási csoportban. Ehhez használja a parancsfájlt, [állítsa be az Azure Resource Manager virtuális gép rendelkezésre állási csoport](https://gallery.technet.microsoft.com/Set-Azure-Resource-Manager-f7509ec4). Ez a szkript futtatása előtt ellenőrizze a korlátatait, és az állásidő megtervezése.

2. **Törölje a régi virtuális gépeket és lemezeket**. Győződjön meg arról, hogy a forráslemez összhangban-e a prémium szintű lemezeket és, hogy az új virtuális gépek hajtsa végre ugyanezt a funkciót, mint a forrás virtuális gépeket. Törölje a virtuális Gépet, és törölje a lemezeket a forrás tárfiókok az Azure Portalon. Ha probléma, amely a lemez nincs törölve, annak ellenére, hogy törli a virtuális gép, lásd: [tárolási erőforrás törlésével kapcsolatos hibák elhárítása](storage-resource-deletion-errors.md).

3. **Az Azure Site Recovery-infrastruktúra tiszta**. A Site Recovery már nincs rá szükség, ha az infrastruktúra elemeit. Replikált elemek, a konfigurációs kiszolgáló és a helyreállítási szabályzat törlése, és ezután törölje az Azure Site Recovery-tárból.

## <a name="troubleshooting"></a>Hibaelhárítás

* [Védelem a virtuális gépek és fizikai kiszolgálók figyelése és hibaelhárítása](../../site-recovery/site-recovery-monitoring-and-troubleshooting.md)
* [A Microsoft Azure Site Recovery fórum](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr)

## <a name="next-steps"></a>További lépések

Bizonyos forgatókönyvek esetén a virtuális gépek migrálásáról az alábbi forrásanyagokban talál:

* [Az Azure Virtual Machines, Storage-fiókok közötti migrálása](https://azure.microsoft.com/blog/2014/10/22/migrate-azure-virtual-machines-between-storage-accounts/)
* [Hozzon létre, és a Windows Server VHD feltöltése az Azure-bA](upload-generalized-managed.md)
* [Virtuális gépek áttelepítése az Amazon AWS, a Microsoft Azure](https://channel9.msdn.com/Series/Migrating-Virtual-Machines-from-Amazon-AWS-to-Microsoft-Azure)

Azt is ellenőrizze, tudjon meg többet az Azure Storage és az Azure Virtual Machines az alábbi forrásanyagokat:

* [Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
* [Azure Virtual Machines](https://azure.microsoft.com/documentation/services/virtual-machines/)

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
