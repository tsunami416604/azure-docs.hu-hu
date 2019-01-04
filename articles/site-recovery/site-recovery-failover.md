---
title: Vészhelyreállítás az Azure Site Recovery feladatátvételi |} A Microsoft Docs
description: Ismerje meg, feladat-visszavétel során a virtuális gépek és fizikai kiszolgálók az Azure Site Recovery szolgáltatással vészhelyreállítás során.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 12/27/2018
ms.author: raynew
ms.openlocfilehash: 3d07b7156800b50daa75978add3ad3922108f142
ms.sourcegitcommit: 803e66de6de4a094c6ae9cde7b76f5f4b622a7bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2019
ms.locfileid: "53974012"
---
# <a name="fail-over-vms-and-physical-servers"></a>Virtuális gépek és fizikai kiszolgálók feladatátvételét 

Ez a cikk bemutatja, hogyan a feladatátvételi virtuális gépekhez és fizikai kiszolgálók Site Recovery által védett.

## <a name="prerequisites"></a>Előfeltételek
1. Mielőtt egy feladatátvételt hajt végre, hajtsa végre egy [feladatátvételi teszt](site-recovery-test-failover-to-azure.md) annak érdekében, hogy minden a várt módon működik.
1. [Előkészíti a hálózatot](site-recovery-network-design.md) célhelyen a feladatátvétel előtt.  

Az alábbi táblázat segítségével az Azure Site Recovery által biztosított feladatátvételi beállításokat ismertek. Ezek a beállítások a különböző feladatátvételi funkciók is megtalálhatók.

| Forgatókönyv | Alkalmazás recovery követelményeinek | A Hyper-V munkafolyamata | Munkafolyamat: VMware-ről
|---|--|--|--|
|Tervezett feladatátvétel miatt egy közelgő adatközpont állásidő| Az alkalmazás, amikor egy tervezett tevékenység történik adatvesztés| A Hyper-V az ASR replikálja az adatokat a felhasználó által megadott másolási gyakorisággal. Tervezett feladatátvétel a gyakoriság felülbírálása, és replikálja a végleges módosítások előtt a feladatátvétel indításáig szolgál. <br/> <br/> 1.    Tervezze meg a karbantartási időszak alapján az üzleti változáskezelési folyamatot. <br/><br/> 2. értesítse a felhasználókat a közelgő állásidőt. <br/><br/> 3. A felhasználó által használt alkalmazás a hálózatról.<br/><br/>4 az ASR portálról tervezett feladatátvételt kezdeményezzen. A helyszíni virtuális gép automatikus leállítási.<br/><br/>A hatékony adatvesztés = 0 <br/><br/>A napló a helyreállítási pontokat is tartalmaz a megőrzési időtartamon egy felhasználóhoz, aki szeretné használni egy régebbi helyreállítási pontra. (24 órás megőrzést Hyper-V).| VMware-ről az ASR replikálja az adatokat folyamatosan a CDP használatával. Feladatátvételi teszi a felhasználói feladatátvételi a legfrissebb adatok (beleértve a post kérelem leállított)<br/><br/> 1. A változáskezelési folyamatot megfelelően karbantartási időszak tervezése <br/><br/>2. értesítse a felhasználókat várható állásidő <br/><br/>3.    A felhasználó által használt alkalmazás a hálózatról. <br/><br/>4.  Kezdeményezzen egy tervezett feladatátvétel, a legutóbbi időpontra történő ASR-portál használatával, miután az alkalmazás offline állapotban. Használja az "A nem tervezett feladatátvétel" lehetőséget a portálon, és válassza ki a legutóbbi időpontra történő feladatátvételt. A helyszíni virtuális gép automatikus leállítási.<br/><br/>A hatékony adatvesztés = 0 <br/><br/>A helyreállítási pontok megőrzési időtartamon a napló egy régebbi helyreállítási pontra használni kívánó ügyfél biztosítunk. (72 órás megőrzést VMware-ről).
|Feladatátvétel miatt egy nem tervezett adatközpont állásidő (természetes vagy informatikai katasztrófa) | Az alkalmazás minimális adatvesztéssel | 1. a szervezet BCP terv kezdeményezése <br/><br/>2. A megőrzési időtartamon (napló) a legújabb vagy egy pontot az ASR-portál használatával nem tervezett feladatátvételt kezdeményezzen.| 1. Indítsa el a szervezet BCP tervet. <br/><br/>2.  A megőrzési időtartamon (napló) a legújabb vagy egy pontot az ASR-portál használatával nem tervezett feladatátvételt kezdeményezzen.


## <a name="run-a-failover"></a>Feladatátvétel futtatása
Az eljárás ismerteti, hogy futtasson egy feladatátvételt a egy [helyreállítási terv](site-recovery-create-recovery-plans.md). Alternatív megoldásként futtathatja a feladatátvételt egyetlen virtuális gép vagy fizikai kiszolgálót a **replikált elemek** oldalon leírt módon [Itt](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure).


![Feladatátvétel](./media/site-recovery-failover/Failover.png)

1. Válassza ki **helyreállítási tervek** > *recoveryplan_name*. Kattintson a **feladatátvétel**
2. Az a **feladatátvételi** képernyőn válassza ki a **helyreállítási pont** irányuló feladatátvételt. Az alábbi lehetőségek egyikét használhatja:
    1.  **Legújabb**: Ez a beállítás szerint a Site Recovery szolgáltatásba küldött összes adat feldolgozása elindítja a feladatot. Az adatok feldolgozását az egyes virtuális gépek helyreállítási pont létrehozása. A helyreállítási pontot használják a virtuális gép feladatátvétel során. Ez a beállítás a feladatátvételi minden adat, amelyet követően létrehozott virtuális gép, amely a feladatátvétel elindításakor a Site Recovery szolgáltatásba replikálása a legkisebb helyreállítási Időkorlát (Helyreállításipont-célkitűzés) biztosít.
    1.  **Legutóbb feldolgozott**: Ez a beállítás átadja a feladatokat a helyreállítási terv, amely a Site Recovery szolgáltatás által feldolgozott legutóbbi helyreállítási pontot az összes virtuális gépet. A virtuális gép feladatátvételi teszt során, a legutóbbi feldolgozott helyreállítási pontot időbélyegzőjét is látható. Ha a helyreállítási terv feladatátvétele, nyissa meg az egyes virtuális géphez, és nézze **legutóbbi helyreállítási pontok** csempére, hogy ezt az információt. Rendszer nem tölt időt a feldolgozatlan adatok feldolgozásához, ezt a lehetőséget egy alacsony RTO (helyreállítási időre vonatkozó célkitűzés) feladatátvételi lehetőséget biztosít.
    1.  **Legutóbbi alkalmazáskonzisztens**: Ez a beállítás az összes virtuális gép a legutóbbi alkalmazáskonzisztens helyreállítási pont, amely a Site Recovery szolgáltatás által feldolgozott, a helyreállítási terv feladatait. A virtuális gép feladatátvételi teszt során, a legutóbbi alkalmazáskonzisztens helyreállítási pont időbélyeg is látható. Ha a helyreállítási terv feladatátvétele, nyissa meg az egyes virtuális géphez, és nézze **legutóbbi helyreállítási pontok** csempére, hogy ezt az információt.
    1.  **Legújabb több virtuális gépre kiterjedő feldolgozott**: Ezt a beállítást csak a helyreállítási terveket, amelyek rendelkeznek legalább egy virtuális gép több virtuális gépre kiterjedő konzisztencia ON érhető el. Virtuális gépek, amelyek részei egy replikációs csoport feladatátvételt a legutóbbi közös virtuális gépre kiterjedő konzisztens helyreállítási pont. Más virtuális gépek feladatátvételt a legutóbbi feldolgozott helyreállítási pontot.  
    1.  **Legújabb több virtuális gépre kiterjedően alkalmazáskonzisztens**: Ezt a beállítást csak a helyreállítási terveket, amelyek rendelkeznek legalább egy virtuális gép több virtuális gépre kiterjedő konzisztencia ON érhető el. Virtuális gépek, amelyek részei egy replikációs csoport feladatátvételt a legutóbbi közös virtuális gépre kiterjedően alkalmazáskonzisztens helyreállítási pont. Más virtuális gépek feladatátvételt a legutóbbi alkalmazáskonzisztens helyreállítási pontot.
    1.  **Egyéni**: Ha a virtuális gép feladatátvételi tesztet, majd használhatja ezt a beállítást egy adott helyreállítási pontra történő feladatátvételt.

    > [!NOTE]
    > Egy helyreállítási pontot választhatja ki csak érhető el, ha az Azure-bA feladatátvétele.
    >
    >


1. Ha a helyreállítási tervben szereplő virtuális gépek némelyike feladatátvételt is korábbi futtatása és a virtuális gépek aktívak a forrás- és cél helye határozza meg, most is használhat **irányának módosítása** dönthet arról, hogy az irány, amelyben a beállítás a feladatátvétel történjen.
1. Ha Ön feladatátvétele már az Azure-ba, és a felhőben (csak akkor, ha a védett Hyper-v virtuális gépek VMM-kiszolgálóról vonatkozik) engedélyezett az adattitkosítás a **titkosítási kulcs** válassza ki a tanúsítványt, ha engedélyezte a korábban kiadott Adattitkosítás során a VMM-kiszolgáló a telepítést.
1. Válassza ki **leállított gép feladatátvétel megkezdése előtt** Ha azt szeretné, hogy a Site Recovery megkísérelje leállítani a forrás virtuális gépeket a feladatátvétel indítása előtt. Feladatátvételi továbbra is fennáll, akkor is, ha a leállított sikertelen lesz.  

    > [!NOTE]
    > Védett Hyper-v virtuális gépek, ha megpróbálja leállított is lehetősége van még nem lettek elküldve a szolgáltatásnak a feladatátvétel indítása előtt a helyszíni adatok szinkronizálása.
    >
    >

1. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. Akkor is, ha nem tervezett feladatátvétel során fordult elő hiba, a helyreállítási terv fut, mindaddig, amíg nem fejeződik be.
1. A feladatátvétel után ellenőrizze a naplózás be azt a virtuális gépet. Ha szeretne a virtuális gép egy másik helyreállítási pontra váltani, akkor használhatja **helyreállítási pont módosítása** lehetőséget.
1. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt. **Véglegesítés törli a szolgáltatással elérhető összes helyreállítási pont** és **helyreállítási pont módosítása** beállítás már nem érhető el.

## <a name="planned-failover"></a>Tervezett feladatátvétel
Virtuális gépek/fizikai kiszolgálók is támogatja a Site Recovery használatával védett **tervezett feladatátvételt**. Tervezett feladatátvétel beállítás nulla adatok elvesztése feladatátvétel. Egy tervezett feladatátvételt akkor aktiválódik, amikor először a forrás virtuális gépek leállítására, a legfrissebb adatok szinkronizálása és majd akkor aktiválódik, a feladatátvételt.

> [!NOTE]
> A Hyper-v virtuális gépek egy a helyszíni helyről egy másik helyszíni helyre feladatátvételkor térjen vissza a helyszíni elsődleges helyhez kell első **fordított-replikálás** a virtuális gép biztonsági elsődleges helyhez, majd a feladatátvétel indítása. Ha az elsődleges virtuális gép nem áll rendelkezésre, majd elindítása előtt, hogy **fordított-replikálás** kell a virtuális gép visszaállítása biztonsági másolatból.   
>
>
## <a name="failover-job"></a>Feladatátvételi feladat

![Feladatátvétel](./media/site-recovery-failover/FailoverJob.png)

A feladatátvétel elindításakor, magában foglalja a következő lépéseket:

1. Előfeltételek ellenőrzése: Ez a lépés biztosítja, hogy a feladatátvételhez szükséges feltételek közül mind teljesül-e
1. Feladatátvétel: Ebben a lépésben feldolgozza az adatokat, így készen áll, hogy kihozni az Azure virtuális gépek hozhatók létre. Ha úgy döntött, **legújabb** a helyreállítási ponttal, ebben a lépésben létrehoz egy helyreállítási pontot a szolgáltatásnak küldött adatokból.
1. Kezdés: Ez a lépés létrehoz egy Azure virtuális gépen az előző lépésben feldolgozott adatok használatával.

> [!WARNING]
> **Ne szakítsa egy folyamatban lévő feladatátvételi**: Feladatátvétel indítása előtt a virtuális gép replikációja le van állítva. Ha Ön **Mégse** egy feladat a folyamatban, a feladatátvételt, az leáll, de a virtuális gép nem indul el replikálni. Nem lehet újból elindítani a replikációt.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Az Azure-bA a feladatátvételhez szükséges idő

Bizonyos esetekben a virtuális gépek feladatátvétele egy extra közbenső lépés, amely általában eltart befejezéséhez körülbelül 8 – 10 percet igényel. A következő esetekben a szokásosnál magasabb lesz a feladatátvételi idő:

* VMware virtuális gépek használata a mobilitási szolgáltatás régebbi, mint 9.8 verzió
* Fizikai kiszolgálók
* A VMware Linux rendszerű virtuális gépek
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek
* Ahol következő illesztőprogramok nem találhatók, a VMware virtuális gépek rendszerindítási illesztőprogram
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* VMware virtuális gépek, amelyek nem rendelkeznek a DHCP-szolgáltatás engedélyezve van, függetlenül attól, hogy használják a DHCP vagy statikus IP-címek

Minden más esetben ez a köztes lépés nem kötelező, és a feladatátvétel végrehajtásához szükséges idő kisebb.

## <a name="using-scripts-in-failover"></a>A feladatátvevő szkriptek használatával
Előfordulhat, hogy automatizálni szeretné bizonyos műveleteket feladatátvétel végrehajtása közben. Parancsprogramokkal vagy [az Azure automation-runbookok](site-recovery-runbook-automation.md) a [helyreállítási tervek](site-recovery-create-recovery-plans.md) valósítható meg.

## <a name="post-failover-considerations"></a>Feladatátvételi szempontokat részletező cikkben közzététele
Vegye figyelembe a következőket érdemes feladatátvétel után:
### <a name="retaining-drive-letter-after-failover"></a>Meghajtóbetűjel megőrzése feladatátvétel után
A meghajtó betűjelét, a virtuális gépek a feladatátvételt követően megőrzéséhez beállíthatja a **TÁROLÓHÁLÓZATI szabályzata** a virtuális gép **OnlineAll**. [További információ](https://support.microsoft.com/help/3031135/how-to-preserve-the-drive-letter-for-protected-virtual-machines-that-are-failed-over-or-migrated-to-azure).

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha a feladatátvételt követően RDP vagy SSH segítségével szeretne kapcsolódni az Azure-beli virtuális gépekhez, kövesse a táblázatban összefoglalt követelményeket, [itt](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.


## <a name="next-steps"></a>További lépések

> [!WARNING]
> Miután a virtuális gépek feladatátadása, és a helyszíni adatközpont érhető el, akkor [ **ismételt védelme** ](vmware-azure-reprotect.md) VMware virtuális gépek biztonsági másolatot a helyszíni adatközponthoz.

Használat [ **tervezett feladatátvételt** ](hyper-v-azure-failback.md) beállítást **feladat-visszavétel** Hyper-v virtuális gépek vissza a helyszíni az Azure-ból.

Ha nem sikerült egy Hyper-v feletti virtuális gép egy másik helyszíni adatközpontot egy a VMM-kiszolgáló által kezelt és az elsődleges adatközpont érhető el, majd **a visszirányú replikálás** elindíthatja a replikációval vissza az elsődleges adatok központ.
