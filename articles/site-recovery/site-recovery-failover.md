---
title: Feladatátvétel a vész-helyreállítás során a Azure Site Recovery | Microsoft Docs
description: A virtuális gépek és a fizikai kiszolgálók feladatátvételének megismerése a Azure Site Recovery szolgáltatással való vész-helyreállítás során.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: article
ms.date: 06/30/2019
ms.author: raynew
ms.openlocfilehash: da55d83665792f6ea2f4c78aa2a6c3ca26c39233
ms.sourcegitcommit: 49c4b9c797c09c92632d7cedfec0ac1cf783631b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/05/2019
ms.locfileid: "70383182"
---
# <a name="fail-over-vms-and-physical-servers"></a>Virtuális gépek és fizikai kiszolgálók feladatátvétele 

Ez a cikk a Site Recovery által védett virtuális gépek és fizikai kiszolgálók feladatátvételét ismerteti.

## <a name="prerequisites"></a>Előfeltételek
1. A feladatátvétel előtt végezzen [feladatátvételi tesztet](site-recovery-test-failover-to-azure.md) , és győződjön meg róla, hogy minden a várt módon működik-e.
1. [Készítse elő a hálózatot](site-recovery-network-design.md) a célhelyen a feladatátvétel előtt.  

A következő táblázat segítségével megismerheti a Azure Site Recovery által biztosított feladatátvételi beállításokat. Ezek a beállítások a különböző feladatátvételi forgatókönyvekhez is szerepelnek.

| Forgatókönyv | Alkalmazás-helyreállítási követelmény | Hyper-V munkafolyamata | VMware-munkafolyamat
|---|--|--|--|
|Tervezett feladatátvétel egy közelgő adatközpont leállása miatt| Az alkalmazás nulla adatvesztést okoz a tervezett tevékenységek végrehajtásakor| A Hyper-V esetében az ASR a felhasználó által megadott másolási gyakorisággal replikálja az adatforrásokat. A tervezett feladatátvétel felülbírálja a gyakoriságot, és replikálja a végső módosításokat a feladatátvétel megkezdése előtt. <br/> <br/> 1. Tervezze meg a karbantartási időszakot a vállalata változás-kezelési folyamata alapján. <br/><br/> 2. Értesítse a felhasználókat a közelgő állásidőről. <br/><br/> 3. A felhasználó felé irányuló alkalmazás offline állapotba helyezése.<br/><br/>4. Tervezett feladatátvétel kezdeményezése az ASR-portál használatával. A helyszíni virtuális gép automatikusan leáll.<br/><br/>Az alkalmazás tényleges adatvesztése = 0 <br/><br/>A helyreállítási pontok egy naplója is meg van határozva egy adatmegőrzési ablakban egy olyan felhasználó számára, aki régebbi helyreállítási pontot szeretne használni. (24 órás megőrzés a Hyper-V-ben). Ha a replikálás az adatmegőrzési időszak időkeretén túl leáll, az ügyfelek továbbra is képesek lesznek a feladatátvételre a legújabb elérhető helyreállítási pontok használatával. | A VMware esetében az ASR folyamatosan replikálja az adatforrást a CDP használatával. A feladatátvétel lehetővé teszi a felhasználó számára a legutóbbi adatfeladatátvételt (beleértve az alkalmazás utáni leállítás funkciót is)<br/><br/> 1. Karbantartási időszak megtervezése a módosítási felügyeleti folyamat alapján <br/><br/>2. értesítse a felhasználókat a közelgő állásidőről <br/><br/>3. A felhasználó felé irányuló alkalmazás offline állapotba helyezése.<br/><br/>4. Tervezett feladatátvétel kezdeményezése az ASR-portál használatával a legújabb pontra, miután az alkalmazás offline állapotú. Használja a "tervezett feladatátvétel" lehetőséget a portálon, és válassza ki a legkésőbbi feladatátvételi pontot. A helyszíni virtuális gép automatikusan leáll.<br/><br/>Az alkalmazás tényleges adatvesztése = 0 <br/><br/>Az adatmegőrzési időszak helyreállítási pontjainak naplóját olyan ügyfelek számára biztosítjuk, akik régebbi helyreállítási pontot kívánnak használni. (72 óra megőrzés a VMware esetében). Ha a replikálás az adatmegőrzési időszak időkeretén túl leáll, az ügyfelek továbbra is képesek lesznek a feladatátvételre a legújabb elérhető helyreállítási pontok használatával.
|Feladatátvétel egy nem tervezett adatközpont leállása miatt (természetes vagy informatikai katasztrófa) | Az alkalmazás minimális adatvesztése | 1. A szervezet BCP-tervének kezdeményezése <br/><br/>2. Indítsa el a nem tervezett feladatátvételt az ASR-portál használatával a legutóbbi vagy az adatmegőrzési időszak (napló) egy pontjára.| 1. Kezdeményezzen a szervezet BCP-tervét. <br/><br/>2. Indítsa el a nem tervezett feladatátvételt az ASR-portál használatával a legutóbbi vagy az adatmegőrzési időszak (napló) egy pontjára.


## <a name="run-a-failover"></a>Feladatátvétel futtatása
Ez az eljárás azt ismerteti, hogyan futtatható feladatátvétel egy [helyreállítási tervhez](site-recovery-create-recovery-plans.md). Azt is megteheti, hogy a feladatátvételt egyetlen virtuális géphez vagy fizikai kiszolgálóhoz futtatja a **replikált elemek** lapról az [itt](vmware-azure-tutorial-failover-failback.md#run-a-failover-to-azure)leírtak szerint.


![Feladatátvétel](./media/site-recovery-failover/Failover.png)

1. Válassza a **helyreállítási tervek** > *recoveryplan_name*lehetőséget. Kattintson a **feladatátvétel** elemre.
2. A **feladatátvételi** képernyőn válasszon ki egy **helyreállítási pontot** a feladatátvételhez. Az alábbi lehetőségek egyikét használhatja:
   1. **Legutóbbi**: Ez a beállítás elindítja a feladatot úgy, hogy először dolgozza fel a Site Recovery szolgáltatásnak elküldett összes adatfeldolgozást. Az adatfeldolgozás minden virtuális géphez létrehoz egy helyreállítási pontot. Ezt a helyreállítási pontot a virtuális gép használja a feladatátvétel során. Ez a beállítás a legalacsonyabb RPO (helyreállítási pont célkitűzés) adja meg, mivel a feladatátvételt követően létrehozott virtuális gép minden olyan adattal rendelkezik, amelyet a rendszer Site Recovery szolgáltatásba replikált a feladatátvétel elindítása után.
   1. **Legutóbb feldolgozott**: Ez a beállítás a helyreállítási terv összes virtuális gépe számára a Site Recovery szolgáltatás által már feldolgozott legújabb helyreállítási pontra hajtja végre a feladatátvételt. Ha a virtuális gép feladatátvételi tesztjét végzi, a rendszer a legutóbb feldolgozott helyreállítási pont időbélyegzőjét is megjeleníti. Ha feladatátvételt hajt végre egy helyreállítási tervben, az egyes virtuális gépekre kattintva megtekintheti a **helyreállítási pontok legújabb** csempéjét az információk lekéréséhez. Mivel a feldolgozatlan adatmennyiség feldolgozása nem történik meg, ez a beállítás alacsony RTO (helyreállítási idő célkitűzés) feladatátvételi lehetőséget biztosít.
   1. **Legújabb alkalmazás – konzisztens**: Ez a beállítás a helyreállítási terv összes virtuális gépe számára meghiúsul a legújabb, az alkalmazás konzisztens helyreállítási pontjára, amelyet Site Recovery szolgáltatás már feldolgozott. Ha a virtuális gép feladatátvételi tesztjét végzi, a rendszer a legújabb, alkalmazás-konzisztens helyreállítási pont időbélyegzőjét is megjeleníti. Ha feladatátvételt hajt végre egy helyreállítási tervben, az egyes virtuális gépekre kattintva megtekintheti a **helyreállítási pontok legújabb** csempéjét az információk lekéréséhez.
   1. A **legújabb több virtuális gépre feldolgozva**: Ez a beállítás csak olyan helyreállítási tervekhez érhető el, amelyek legalább egy virtuális géppel rendelkeznek, és több virtuális gépre kiterjedő konzisztencia van. A replikációs csoport részét képező virtuális gépek a legújabb közös, több virtuális gépre kiterjedő konzisztens helyreállítási pontra lesznek átirányítva. Más virtuális gépek feladatátvétele a legújabb feldolgozott helyreállítási pontra.  
   1. A **legújabb több virtuális gépre kiterjedő alkalmazás – konzisztens**: Ez a beállítás csak olyan helyreállítási tervekhez érhető el, amelyek legalább egy virtuális géppel rendelkeznek, és több virtuális gépre kiterjedő konzisztencia van. A replikációs csoport részét képező virtuális gépek a legújabb közös, több virtuális gépre kiterjedő, alkalmazás-konzisztens helyreállítási pontot használják. Más virtuális gépek feladatátvétele a legújabb alkalmazás-konzisztens helyreállítási pontra.
   1. **Egyéni**: Ha feladatátvételi tesztet végez egy virtuális gépen, akkor ezt a lehetőséget használhatja a feladatátvételre egy adott helyreállítási pontra.

      > [!NOTE]
      > A helyreállítási pont kiválasztásának lehetősége csak akkor érhető el, ha feladatátvételt végez az Azure-ba.
      >
      >


1. Ha a helyreállítási tervben szereplő virtuális gépek némelyike egy korábbi futtatás során feladatátvételt hajt végre, és most a virtuális gépek mind a forrás-, mind a célhelyen aktívak voltak, a **váltás iránya** beállítással határozhatja meg, hogy milyen irányba történjen a feladatátvétel.
1. Ha feladatátvételt végez az Azure-ba, és az adattitkosítás engedélyezve van a felhőben (csak akkor érvényes, ha VMM-kiszolgálóról védett Hyper-v virtuális gépeket használ), a **titkosítási kulcsban** válassza ki azt a tanúsítványt, amelyet a rendszer az adattitkosítás engedélyezésekor adott meg. a telepítőt a VMM-kiszolgálón.
1. Ha azt szeretné, hogy a feladatátvétel elindítása előtt a Site Recovery megkísérelje leállítani a forrás virtuális gépek leállítását, válassza a **leállított gép lehetőséget a feladatátvétel megkezdése előtt** . A feladatátvétel akkor is folytatódik, ha a Leállítás nem sikerül.  

    > [!NOTE]
    > Ha a Hyper-v virtuális gépek védettek, a leállítási lehetőség is megkísérli a szolgáltatásnak még el nem küldött helyszíni adatokat szinkronizálni a feladatátvétel elindítása előtt.
    >
    >

1. A feladatátvételi folyamatot a **Feladatok** lapon követheti nyomon. Ha hiba történik egy nem tervezett feladatátvétel során, a helyreállítási terv addig fut, amíg be nem fejeződik.
1. A feladatátvétel után ellenőrizze a virtuális gépet, ha bejelentkezik a szolgáltatásba. Ha másik helyreállítási pontra szeretne váltani a virtuális gépen, akkor használhatja a **helyreállítási pont módosítása** lehetőséget.
1. Ha elégedett a feladatátviteli virtuális géppel, **véglegesítheti** a feladatátvételt. **A commit parancs törli a szolgáltatásban elérhető összes helyreállítási pontot** , és a **helyreállítási pont módosítása** lehetőség már nem érhető el.

## <a name="planned-failover"></a>Tervezett feladatátvétel
A Site Recovery által védett virtuális gépek/fizikai kiszolgálók támogatják a **tervezett feladatátvételt**is. A tervezett feladatátvétel egy nulla adatvesztési feladatátvételi lehetőség. Tervezett feladatátvétel indításakor először a forrásként szolgáló virtuális gépek leállnak, a legfrissebb adatokat szinkronizálja a rendszer, majd elindítja a feladatátvételt.

> [!NOTE]
> A Hyper-v virtuális gépek egyik helyszíni helyről egy másikra történő feladatátvétele során vissza kell térnie az elsődleges helyszíni helyhez **, először vissza** kell térnie a virtuális gépre az elsődleges helyre, majd el kell indítania a feladatátvételt. Ha az elsődleges virtuális gép nem érhető el, akkor a **fordított replikálás** megkezdése előtt vissza kell állítania a virtuális gépet egy biztonsági másolatból.   
 
 
## <a name="failover-job"></a>Feladatátvételi feladatok

![Feladatátvétel](./media/site-recovery-failover/FailoverJob.png)

Feladatátvétel indításakor a következő lépések szükségesek:

1. Előfeltételek ellenőrzése: Ez a lépés biztosítja, hogy a feladatátvételhez szükséges összes feltétel teljesül
1. Feladatátvételi Ez a lépés dolgozza fel az adatmennyiséget, és készen áll az Azure-beli virtuális gépek létrehozásához. Ha a **legutóbbi** helyreállítási pontot választotta, ez a lépés egy helyreállítási pontot hoz létre a szolgáltatásnak eljuttatott adatokból.
1. Indítás: Ez a lépés egy Azure-beli virtuális gépet hoz létre az előző lépésben feldolgozott adatkezelési művelettel.

> [!WARNING]
> **Ne szakítsa meg a folyamatban lévő feladatátvételt**: A feladatátvétel elindítása előtt a virtuális gép replikációja leáll. Ha **megszakít** egy folyamatban lévő feladatot, a feladatátvétel leáll, de a virtuális gép nem fog elindulni a replikáláshoz. A replikáció nem indítható újra.
>
>

## <a name="time-taken-for-failover-to-azure"></a>Az Azure-ba történő feladatátvételhez szükséges idő

Bizonyos esetekben a virtuális gépek feladatátvétele egy további közbenső lépést igényel, amely általában körülbelül 8 – 10 percet vesz igénybe. A következő esetekben a feladatátvételhez szükséges idő a szokásosnál magasabb lesz:

* A 9,8-nál régebbi verziójú mobilitási szolgáltatást használó VMware virtuális gépek
* Fizikai kiszolgálók
* VMware Linux rendszerű virtuális gépek
* Fizikai kiszolgálóként védett Hyper-V virtuális gépek
* VMware virtuális gépek, amelyeknél a következő illesztőprogramok nem találhatók rendszerindító illesztőprogramként
    * storvsc
    * VMBus
    * storflt
    * Intelide
    * ATAPI
* Azok a VMware virtuális gépek, amelyeken nincs engedélyezve a DHCP szolgáltatás, függetlenül attól, hogy DHCP-vagy statikus IP-címeket használnak-e

Az összes többi esetben ez a közbenső lépés nem szükséges, és a feladatátvételhez szükséges idő alacsonyabb.

## <a name="using-scripts-in-failover"></a>Parancsfájlok használata a Feladatátvételben
Előfordulhat, hogy a feladatátvétel során bizonyos műveleteket szeretne automatizálni. Ehhez a [helyreállítási tervekben](site-recovery-create-recovery-plans.md) parancsfájlokat vagy [Azure Automation-runbookok](site-recovery-runbook-automation.md) használhat.

## <a name="post-failover-considerations"></a>Feladatátvételi megfontolások közzététele
Feladatátvétel után érdemes megfontolni a következő javaslatokat:
### <a name="retaining-drive-letter-after-failover"></a>Meghajtóbetűjel megőrzése a feladatátvétel után
A Azure Site Recovery kezeli a meghajtóbetűjelek megőrzését. [További információ](vmware-azure-exclude-disk.md#example-1-exclude-the-sql-server-tempdb-disk) arról, hogy hogyan történik, ha egyes lemezek kizárását választja.

## <a name="prepare-to-connect-to-azure-vms-after-failover"></a>Felkészülés az Azure virtuális gépekhez való kapcsolódásra a feladatátvételt követően

Ha a feladatátvételt követően RDP vagy SSH segítségével szeretne kapcsolódni az Azure-beli virtuális gépekhez, kövesse a táblázatban összefoglalt követelményeket, [itt](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).

Kövesse az [itt](site-recovery-failover-to-azure-troubleshoot.md) leírt lépéseket a feladatátvitelt követő csatlakozási problémák megoldása érdekében.


## <a name="next-steps"></a>További lépések

> [!WARNING]
> Ha a feladatátvételt követően a virtuális gépek és a helyszíni adatközpont elérhetővé válik, a VMware-alapú virtuális gépeket a helyszíni adatközpontba újra kell [**védetté**](vmware-azure-reprotect.md) tenni.

A [**tervezett feladatátvételi**](hyper-v-azure-failback.md) beállítással visszatérhet **a Hyper-** v rendszerű virtuális gépekről az Azure-ból a helyszínen.

Ha feladatátvételt végez egy Hyper-v virtuális gépen egy VMM-kiszolgáló által felügyelt helyszíni adatközpontban, és az elsődleges adatközpont elérhető, akkor a **visszirányú replikálás** beállítás használatával indítsa el a replikálást az elsődleges adatközpontba.
