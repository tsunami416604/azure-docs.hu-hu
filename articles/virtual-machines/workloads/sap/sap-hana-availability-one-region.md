---
title: SAP HANA rendelkezésre állási egy Azure-régión belüli |} A Microsoft Docs
description: Az Azure natív, egy Azure-régióban található virtuális gépein az SAP HANA operations ismerteti.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4f2defd60ec6b835ec856c9253a92f1d6817e861
ms.sourcegitcommit: 7ad9db3d5f5fd35cfaa9f0735e8c0187b9c32ab1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/27/2018
ms.locfileid: "39326003"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA rendelkezésre állási egy Azure-régióban
Ez a cikk bemutatja egy Azure-régión belül több rendelkezésre állási forgatókönyvek esetén. Az Azure számos régióban, az egész világon elosztva rendelkezik. Az Azure-régiók listáját lásd: [Azure-régiók](https://azure.microsoft.com/regions/). A Microsoft SAP HANA-beli virtuális gépek üzembe helyezése egy Azure-régióban elérhető az egyetlen virtuális gép a HANA-példány telepítését. A magasabb rendelkezésre állás érdekében két virtuális gépet a két HANA példányok telepíthet egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) , amely a HANA-rendszerreplikálást használja a rendelkezésre állás érdekében. 

Jelenleg Azure kínál [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Ez a cikk nem foglalkozik a rendelkezésre állási zónák részletesen. Azonban a rendelkezésre állási zónák és a rendelkezésre állási csoportok használatával kapcsolatos általános vita tartalmazza.

Hol érhetők el rendelkezésre állási zónák Azure-régióban van több adatközpontban. Az adatközpontokban a ellátás áramforrásról, hűtéssel és hálózati függetlenek egymástól. Ajánlat egy adott Azure-régión belül különböző zónákhoz tartozhatnak az az oka, hogy helyezzen üzembe alkalmazásokat érhetők el két vagy három rendelkezésre állási zónák között. Zónákban telepítése problémák a power és a hálózat csak egy Azure rendelkezésre állási zónában infrastruktúrát érintő, az alkalmazás központi telepítése egy Azure-régióban továbbra is működőképes. Néhány csökkentett kapacitás fordulhatnak elő. Például egy zónában lévő virtuális gépek elveszhetnek, de a két zónában lévő virtuális gépek továbbra is lenne, helyezheti üzembe. 
 
Az Azure rendelkezésre állási csoportban egy logikai csoportosítási funkció, amelynek segítségével, győződjön meg arról, hogy, amelyet a rendelkezésre állási csoportban lévő Virtuálisgép-erőforrások hiba logikailag egymástól legyenek üzembe helyezve az Azure-adatközpont. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási rackszekrényen, tárolási egységben és hálózati kapcsolón fussanak. Az egyes Azure dokumentációja, ez a konfiguráció nevezzük Elhelyezés a különböző [frissítési és tartalék tartományok](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Ezek a gyakorlatok általában az Azure-adatközpont. Feltételezve, hogy power tápforrással és hálózati problémák hatással lenne az Adatközpont végzi, a kapacitás egy Azure-régióban az összes befolyásolhat.

Azure-beli rendelkezésre állási zónák képviselő adatközpontok elhelyezését továbbítása elfogadható hálózati késés különböző zónák és a egy adatközpontok közötti távolságot a telepített szolgáltatások közötti kompromisszumot. Természeti katasztrófa ideális esetben nem lenne hatással power, hálózati ellátási, és infrastruktúra minden rendelkezésre állási zónák ebben a régióban. Azonban mint emlékművi természeti katasztrófa rendelkezik, a rendelkezésre állási zónák esetleg nem mindig nyújt a rendelkezésre állási, amelyeket szeretne egy régión belül. Gondolja át, amely 2017. szeptember 20. elérte a Puerto Rico sziget Hurrikán Maria. A Hurrikán alapvetően az okozza, közel 100 %-os kieső a 90 kiterjedő mérföld sziget a.

## <a name="single-vm-scenario"></a>Egyetlen virtuális forgatókönyv

Egyetlen virtuális forgatókönyvekben hozzon létre egy Azure virtuális Gépen, az SAP HANA-példány. Az Azure Premium Storage segítségével az operációsrendszer-lemez és az adatlemezek. Az Azure üzemidő 99,9 %-os garantált szolgáltatási SZINTJÉTŐL és az SLA-k a többi Azure-összetevőket is használhatók, hogy az ügyfelek számára a rendelkezésre állási SLA-k teljesítése. Ebben a forgatókönyvben nem használhatja az Azure rendelkezésre állási csoport a DBMS réteget futtató virtuális gépekhez szükséges van. Ebben a forgatókönyvben, két különböző szolgáltatásait használják:

- Az Azure virtuális gép automatikus újraindítás letiltása (más néven Azure felültelepítés)
- SAP HANA automatikus újraindítás

Az Azure virtuális gép automatikus újraindítása, vagy felültelepítés, egy funkciót az Azure-ban, amely két szinten működik:

- Az Azure-kiszolgáló állomás a server-gazdagépen futó virtuális gép állapotát ellenőrzi.
- Az Azure fabric controller állapotát és a kiszolgáló gazdagépet elérhetőségét figyeli.

Állapot ellenőrzése funkcionalitást minden az Azure-kiszolgáló gazdagépeken futó virtuális gép állapotát figyeli. Ha egy virtuális gép nem Kifogástalan állapotba esik, a virtuális gép újraindítása elindíthatóak az Azure-beli gazdagéppel ügynök, amely a virtuális gép állapotát ellenőrzi. A hálóvezérlő számos különböző paraméterek, amelyek esetleg jelzik a gazdagép hardverének problémák ellenőrzésével a gazdagép állapotát ellenőrzi. Azt is ellenőrzi a a kisegítő lehetőségek az állomás a hálózaton keresztül. A következő események vezethet arra utalhat, hogy a gazdagép problémáit:

- A gazdagép hibás állapotot jelez, ha a gazdagép újraindítását és a gazdagépen futó virtuális gépek újraindítását aktiválódik.
- Ha a gazdagép nem kifogástalan állapotban sikeres újraindítás után, a rendszer kezdeményezi a újbóli üzembe helyezés, a virtuális gépek, amelyek eredetileg egy kifogástalan állapotú gazdagépre szerveren most már nem megfelelő állapotú a csomóponton. Ebben az esetben az eredeti gazdára nem kifogástalan állapotúként van megjelölve. Ez nem használható további telepítések amíg nem törlik vagy cseréje.
- Ha a hibás állapotú gazdagép meghibásodik az újraindítás során, akkor aktiválódik, azonnal újraindítja a virtuális gépek az kifogástalan állapotú gazdagépeken. 

A gazdagép és virtuális gép figyelése az Azure által biztosított Azure virtuális gépek, hogy a gazdagéppel kapcsolatos problémákat automatikusan újraindulnak, egy megfelelő Azure-gazdagépen. 

>[!IMPORTANT]
>Az Azure felültelepítés nem indítja újra Linux rendszerű virtuális gépek, ahol a vendég operációs rendszer kernelén Pánik állapotban van. A gyakran használt Linux-kiadások, az alapértelmezett beállítások nem automatikusan újraindítása folyamatban van virtuális gépeket vagy a kiszolgálóra, amelyen a Linux kernel Pánik állapotban. Ehelyett az alapértelmezett úgy rendelkezik, hogy az operációs rendszer rendszermag Pánik állapotban kell tudni csatolni a kernel-hibakereső elemzéséhez. Az Azure automatikusan újraindítja a vendég operációs rendszer virtuális gép állapotban egy ilyen van érvényesítenie ezt a viselkedést. Feltételezzük, rendkívül ritka ilyen esemény biztosítják. Ahhoz, hogy a virtuális gép újraindítását az alapértelmezett viselkedést felülírhatja. Módosíthatja az alapértelmezett viselkedés engedélyezéséhez /etc/sysctl.conf "kernel.panic" paraméter. Ez a paraméter beállított időtartam másodpercen belül van. Közös javasolt értékek: 20-30 másodpercen keresztül ezt a paramétert az újraindítás elindítása előtt várja. Lásd még: <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>.

A második szolgáltatása, amely ebben a forgatókönyvben támaszkodnak, az a tény, hogy újraindítja a HANA-szolgáltatás, amely után a virtuális gép automatikusan elindul, fut a virtuális gép újraindítása sikeresen megtörtént. Beállíthat [HANA szolgáltatás automatikus újraindítás](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) HANA szolgáltatások különböző figyelő-szolgáltatásokon keresztül.

Ebben a forgatókönyvben egy virtuális javíthatja a ritkán használt feladatátvevő csomópont hozzáadása az SAP HANA-konfigurációjához. Az SAP HANA-dokumentáció a beállítás neve [gazdagép automatikus feladatátvétele](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Ebben a konfigurációban előfordulhat, hogy van értelme egy helyszíni üzembe helyezési helyzetben, ahol a Kiszolgálóhardver korlátozva, és a gazdagépként egyetlen kiszolgálóból álló csomópont dedikált üzemi gazdagépekhez több automatikus feladatátvételi csomópontra. De az Azure-ban, ahol az alapul szolgáló infrastruktúra az Azure biztosít a megfelelő célkiszolgáló sikeres virtuális gép újraindítását, hogy értelmetlen üzembe helyezéséhez SAP HANA gazdagép automatikus feladatátvétele. Azure-szolgáltatás javítása, mert nem nincs referenciaarchitektúra a készenléti csomópontokat a HANA gazdagép automatikus feladatátvétele úgy rendelkezik. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Különleges eset az SAP HANA kibővített konfigurációkhoz az Azure-ban
Azure virtuális gépek és az újraindítás, a SAP HANA-példány javítása, a virtuális gép működik, és újra futtatni a szolgáltatás magas rendelkezésre állás az SAP HANA kibővített konfigurációkhoz van szüksége. Magas rendelkezésre állású architektúra alapján a HANA-Rendszerreplikálást szeretnék bevezetni egy későbbi időpontban. 


## <a name="availability-scenarios-for-two-different-vms"></a>Két különböző virtuális gépek rendelkezésre állási forgatókönyvek

Belüli Azure rendelkezésre állási csoport két Azure virtuális gépek használatakor, növelheti a hasznos üzemidő között ezek két virtuális gépet, ha egy Azure rendelkezésre állási csoportot egy Azure-régióban helyezi. Az Alapszintű telepítés az Azure-ban jelenne meg:

![Két virtuális gép összes rétege ábrája](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

A különböző rendelkezésre állási forgatókönyvek bemutatják, hogy az ábrán a rétegek néhány nincs megadva. Az ábrán csak olyan rétegek, amelyek jelzik a virtuális gépek, gazdagépek, a rendelkezésre állási csoportok és Azure-régióban. Azure Virtual Network-példányok, erőforráscsoportok és előfizetések nem szerepet játszanak az ebben a szakaszban leírt forgatókönyveket.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Biztonsági mentések egy második virtuális gép replikálása

A legtöbb kezdetleges mátrixok egyik biztonsági másolatok használatához. Különösen lehetséges, hogy tranzakciós naplók biztonsági mentését egy virtuális gépről egy másik Azure virtuális géphez rendszerrel szállított. Az Azure Storage-típust választhat. Ez a beállítás Ön felelős scripting kell elvégezni, a második virtuális gép az első virtuális gép ütemezett biztonsági mentések másolatát. Ha a második Virtuálisgép-példányok használatára van szüksége, vissza kell állítania a teljes, a növekményes/differenciális és tranzakciónapló biztonsági mentései a pont, amely van szüksége. 

Az architektúra hasonlít:

![Két virtuális gépet a tárreplikáció ábrája](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

A telepítés még nem kiválóan alkalmas a nagyszerű helyreállítási időkorlátot (RPO) és a helyreállítási időre vonatkozó célkitűzés (RTO) időpont eléréséhez. RTO alkalommal különösen csökkenne az teljes mértékben a teljes adatbázis visszaállítása a másolt biztonsági másolatok használatával szükségessége miatt. Azonban ez a beállítás akkor hasznos, a fő-példányokon nem kívánt adatok törlése. Ezzel a beállítással tetszőleges időpontban egy bizonyos ponton visszaállítása, az adatok kinyerése és a törölt adatok importálása a fő példányt. Ezért célszerű biztonsági másolatot metódus használata egyéb magas rendelkezésre állású funkciókkal. 

Miközben a biztonsági másolatok másolódnak, lehet, mint a fő virtuális gép, amelyen az SAP HANA-példány fut, kisebb méretű virtuális Gépet használ. Ne feledje, hogy csatlakoztathat egy kevesebb VHD-k kisebb méretű virtuális gépet. Az egyes Virtuálisgép-típusok a korlátozásokkal kapcsolatos további információkért lásd: [az Azure-ban Linux rendszerű virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes).

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA rendszerreplikáció nélkül Automatikus feladatátvétel

Ebben a szakaszban leírt forgatókönyveket az SAP HANA-rendszerreplikálást használja. Az SAP dokumentációjában talál [replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Automatikus feladatátvétel nélkül forgatókönyvek nem állnak közös konfigurációk egy Azure-régióban. A konfiguráció automatikus feladatátvétellel, anélkül, ha a telepítés pedig támasztja, elkerülve gazdálkodót figyelheti és feladatátvételi manuálisan. Óta a vesz igénybe, és erőfeszítéseket is a legtöbb ügyfél hagyatkoznia ehelyett javítási Azure-szolgáltatás. Vannak bizonyos szélsőséges eseteket, ahol ez a konfiguráció segíthet meghibásodási helyzet tekintetében. Vagy bizonyos esetekben egy ügyfél több hatékonyságának kihasználására – érdemes.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA rendszerreplikáció automatikus feladatátvétel és preload adatok nélkül

Ebben a forgatókönyvben a SAP HANA rendszerreplikáció adatok áthelyezése a 0-RPO eléréséhez szinkron módon használhatja. Másrészről van egy elég hosszú, nem kell feladatátvevő vagy a HANA-példány gyorsítótárba konfigurálások adatok RTO. Ebben az esetben is lehet elérése érdekében további a konfiguráció a következő műveletek végrehajtásával:

- A második virtuális gép egy másik SAP HANA-példány futtatása. Az SAP HANA-példány, a második virtuális gépen a virtuális gép memóriájának a legtöbb vesz igénybe. Abban az esetben, ha a második virtuális géphez feladatátvétel esetén kell állítsa le a futó SAP HANA-példány, amely rendelkezik az adatok, a második virtuális gép teljes mértékben beöltődött, hogy a replikált adatok tölthetők az a második virtuális gép a célként megadott HANA-példányok a gyorsítótárba.
- A második virtuális gép egy kisebb Virtuálisgép-méretet használja. Ha feladatátvétel történik, hogy egy további lépést a manuális feladatátvétel előtt. Ebben a lépésben méretezze át a virtuális Gépet a forrásoldali virtuális gép méretét. 
 
A forgatókönyv hasonlít:

![Két virtuális gépet a tárreplikáció ábrája](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Akkor is, ha az adatok előzetes betöltés végén a HANA system replikációs cél nem használja, szüksége van legalább 64 GB memóriával. Elegendő memória 64 GB-os tartani a célpéldány memóriájának sortárindex adatok mellett is szükséges.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA rendszerreplikáció nélkül Automatikus feladatátvétel és preload adatok

Ebben a forgatókönyvben előre feltölti a második virtuális gép a HANA-példány a replikált adatokat. Ezzel elkerülhető, hogy az adatok nem konfigurálások két előnyeit. Ebben az esetben a második virtuális gép egy másik SAP HANA-rendszeren nem futtatható. Még nem használható a kisebb Virtuálisgép-mérettel. Ezért ügyfelek ritkán a forgatókönyv megvalósításához.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA rendszerreplikáció automatikus feladatátvétellel

A standard és a leggyakrabban használt a rendelkezésre állási konfiguráció egy Azure-régión belüli, két Azure virtuális gépek, SLES Linux operációs rendszert futtató rendelkezik definiált feladatátvevő fürtben. A SLES Linux-fürt alapul a [támasztja](http://www.linux-ha.org/wiki/Pacemaker) keretrendszer együtt egy [STONITH](http://linux-ha.org/wiki/STONITH) eszköz. 

Az SAP HANA szempontból a használt replikációs módjának szinkronizálva van, és az Automatikus feladatátvétel van beállítva. A második virtuális Gépet az SAP HANA-példány meleg készenléti csomópontként működik. A készenléti csomópont változásrekordokat szinkron adatfolyamot fogad az elsődleges SAP HANA-példány. Tranzakció véglegesítve lett az alkalmazás a HANA elsődleges csomópontban, az elsődleges HANA csomópont vár, az alkalmazásnak a véglegesítés megerősítéséhez, mindaddig, amíg a másodlagos SAP HANA-csomópont megerősíti, hogy érkezett-e a végrehajtási rekord. SAP HANA két szinkron replikáció módot kínál. Részletek, valamint e két szinkron replikáció mód közötti különbségek leírását lásd: az SAP-cikk [replikációs mód az SAP HANA-rendszerreplikálást](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html).

A teljes konfiguráció hasonlóan néz ki:

![Két virtuális gépet tároló replikációs és feladatátvételi ábrája](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Ez a megoldás választhatja azt is, mivel lehetővé teszi az elérése a helyreállítási Időkorlátot = 0 és a egy alacsony RTO. Állítsa az SAP HANA-ügyfélkapcsolat, hogy az SAP HANA-ügyfelek csatlakozhat a virtuális IP-cím a HANA system replikációs konfiguráció. Ilyen konfiguráció nincs szükség, ha egy feladatátvételt a másodlagos csomópontra, konfigurálja újra az alkalmazást. Ebben a forgatókönyvben ugyanazt az elsődleges és másodlagos virtuális gépek az Azure virtuális gépek Termékváltozatait kell lennie.

## <a name="next-steps"></a>További lépések

Ezek a konfigurációk, az Azure-ban való beállításának részletes útmutatásért lásd:

- [Azure virtuális gépeken futó SAP HANA rendszerreplikáció beállítása](sap-hana-high-availability.md)
- [Magas rendelkezésre állás az SAP Hana rendszerreplikáció használatával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

SAP HANA rendelkezésre állása az Azure-régiók kapcsolatos további információkért lásd:

- [SAP HANA rendelkezésre állása az Azure-régiók](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

