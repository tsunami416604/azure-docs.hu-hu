---
title: AZ SAP HANA elérhetősége egy Azure-régión belül | Microsoft dokumentumok
description: Ismerteti az SAP HANA-műveletek et az Azure natív virtuális gépein egy Azure-régióban.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/27/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ef7161e653ec582708f242b67c643d960d75e27f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255461"
---
# <a name="sap-hana-availability-within-one-azure-region"></a>Az SAP HANA egyetlen Azure-régión belüli elérhetősége
Ez a cikk egy Azure-régión belül több rendelkezésre állási forgatókönyvet ismertet. Az Azure számos régióval rendelkezik, amelyek világszerte elterjedtek. Az Azure-régiók listáját az [Azure-régiók című témakörben található.](https://azure.microsoft.com/regions/) Az SAP HANA üzembe helyezése a virtuális gépeken egy Azure-régión belül, a Microsoft egyetlen virtuális gép üzembe helyezését egy HANA-példánysal. A nagyobb rendelkezésre állás érdekében két virtuális gépet telepíthet két HANA-példánysal egy [Azure rendelkezésre állási készletén](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) belül, amely hana rendszerreplikációt használ az elérhetőséghez. 

Jelenleg az Azure [kínál Azure rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Ez a cikk nem ismerteti részletesen a rendelkezésre állási zónákat. De tartalmaz egy általános vitát a rendelkezésre állási csoportok és a rendelkezésre állási zónák használatával kapcsolatban.

Az Azure-régiók, ahol rendelkezésre állási zónák kínálnak több adatközpontok rendelkezik. Az adatközpontok függetlenek az áramforrás, a hűtés és a hálózat ellátásában. Az egyetlen Azure-régión belüli különböző zónák felajánlásának oka az alkalmazások üzembe helyezése két vagy három rendelkezésre állási zónában, amelyek et kínálnak. Zónák közötti üzembe helyezés, a hálózati és hálózati problémák csak egy Azure availability zone infrastruktúrát érintő, az alkalmazás telepítése egy Azure-régióban továbbra is működőképes. Néhány kisebb kapacitás előfordulhat. Például az egyik zónában lévő virtuális gépek elveszhetnek, de a másik két zónában lévő virtuális gépek továbbra is működnének. 
 
Az Azure rendelkezésre állási csoport egy logikai csoportosítási képesség, amely segít biztosítani, hogy a virtuális gép erőforrásokat, amelyeket a rendelkezésre állási csoporton belül vannak elválasztva egymástól, ha azok üzembe helyezése egy Azure-adatközpontban. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási rackszekrényen, tárolási egységben és hálózati kapcsolón fussanak. Egyes Azure-dokumentációkban ezt a konfigurációt különböző [frissítési és tartalék tartományokban lévő elhelyezéseknek nevezzük.](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) Ezek az elhelyezések általában egy Azure-adatközponton belül találhatók. Feltételezve, hogy az energiaforrás és a hálózati problémák hatással lenne az adatközpont, amely üzembe helyezése, az összes kapacitásegy Azure-régióban hatással lenne.

Az Azure rendelkezésre állási zónákat képviselő adatközpontok elhelyezése kompromisszumot jelent a különböző zónákban üzembe helyezett szolgáltatások és az adatközpontok közötti távolság közötti elfogadható hálózati késés biztosítása között. A természeti katasztrófák ideális esetben nem befolyásolnák a régió összes rendelkezésre állási zónájának áramellátását, hálózati ellátását és infrastruktúráját. Azonban a monumentális természeti katasztrófák azt mutatják, rendelkezésre állási zónák előfordulhat, hogy nem mindig biztosítja a kívánt rendelkezésre állást egy régión belül. Gondolj on Hurrikán Maria, hogy elérje a sziget Puerto Rico szeptember 20, 2017. A hurrikán gyakorlatilag közel 100 százalékos áramszünetet okozott a 90 mérföld széles szigeten.

## <a name="single-vm-scenario"></a>Egyvirtuálisú forgatókönyv

Egy virtuális gép forgatókönyvében hozzon létre egy Azure virtuális gép az SAP HANA-példányhoz. Az Azure Premium Storage segítségével üzemeltetheti az operációs rendszer lemezét és az összes adatlemezt. Az Azure 99,9 százalékos rendelkezésre állási SLA-ja és más Azure-összetevők SLA-i elegendőek ahhoz, hogy az ügyfelek számára rendelkezésre álló SLA-kat teljesítse. Ebben a forgatókönyvben nem kell kihasználnia az Azure rendelkezésre állási készletét a DBMS-réteget futtató virtuális gépekhez. Ebben az esetben két különböző szolgáltatásra támaszkodhat:

- Az Azure virtuális gép automatikus újraindítása (más néven Az Azure szolgáltatás javítás)
- SAP HANA automatikus újraindítás

Az Azure VM automatikus újraindítása, vagy a szolgáltatás javítás, egy olyan funkció az Azure-ban, amely két szinten működik:

- Az Azure-kiszolgáló gazdagép ellenőrzi a kiszolgáló gazdagépen üzemeltetett virtuális gép állapotát.
- Az Azure-háló vezérlő figyeli a kiszolgálógazda állapotát és rendelkezésre állását.

Az állapot-ellenőrzési funkció figyeli az Azure-kiszolgáló gazdagépen üzemeltetett minden virtuális gép állapotát. Ha egy virtuális gép nem kifogástalan állapotba kerül, a virtuális gép újraindítását kezdeményezheti az Azure gazdagép, amely ellenőrzi a virtuális gép állapotát. A hálóvezérlő ellenőrzi a gazdagép állapotát, és számos különböző paramétert ellenőriz, amelyek a gazdahardverrel kapcsolatos problémákra utalhatnak. Azt is ellenőrzi a hozzáférhetőség a fogadó a hálózaton keresztül. A gazdagépvezetéssel kapcsolatos problémák jelzése a következő eseményekhez vezethet:

- Ha a gazdagép rossz állapotot jelez, a gazdagép újraindítása és az állomáson futó virtuális gépek újraindítása aktiválódik.
- Ha a gazdagép nem kifogástalan állapotban van a sikeres újraindítás után, a virtuális gépek, amelyek eredetileg a most nem kifogástalan állapotú csomóponton egy kifogástalan gazdakiszolgálóra történő újratelepítése történik. Ebben az esetben az eredeti állomás nem kifogástalanként van megjelölve. Nem lesz használható további központi telepítések, amíg nem törlődik, vagy cserélni.
- Ha a nem megfelelő állapotú gazdagép problémákat okoz az újraindítási folyamat során, a virtuális gépek azonnali újraindítása egy kifogástalan állapotú gazdagépen aktiválódik. 

Az Azure által biztosított gazdagép- és virtuálisgép-figyeléssel az Azure-beli virtuális gépek, amelyek gazdagépproblémákat tapasztalnak, automatikusan újraindulnak egy kifogástalan állapotú Azure-gazdagépen. 

>[!IMPORTANT]
>Az Azure szolgáltatás healing nem indítja újra a Linux virtuális gépek, ahol a vendég operációs rendszer kernel pánik állapotban van. A gyakran használt Linux-kiadások alapértelmezett beállításai nem automatikusan újraindítják a virtuális gépeket vagy a kiszolgálót, ahol a Linux kernel pánikállapotban van. Ehelyett az alapértelmezett előirányozza, hogy az operációs rendszer kernel pánik állapotban, hogy képes legyen csatolni a kernel hibakereső elemezni. Az Azure tiszteletben tartja ezt a viselkedést azáltal, hogy nem indítja újra automatikusan a virtuális gép a vendég operációs rendszer egy ilyen állapotban. Feltételezés, hogy az ilyen események rendkívül ritkák. Felülírhatja az alapértelmezett viselkedést a virtuális gép újraindításának engedélyezéséhez. Az alapértelmezett viselkedés módosításához engedélyezze a "kernel.panic" paramétert az /etc/sysctl.conf fájlban. A paraméterhez beállított idő másodpercben van megadva. A közös ajánlott értékek 20–30 másodpercet várnak, mielőtt elindítják az újraindítást ezen a paraméteren keresztül. Lásd <https://gitlab.com/procps-ng/procps/blob/master/sysctl.conf>még: .

A második szolgáltatás, amelyre ebben a forgatókönyvben támaszkodik az a tény, hogy a HANA szolgáltatás, amely fut egy újraindított virtuális gép automatikusan elindul a virtuális gép újraindítása után. Beállíthatja [a HANA szolgáltatás automatikus újraindítása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) a különböző HANA-szolgáltatások figyelő szolgáltatásain keresztül.

Javíthatja ezt az egyvirtuálisú forgatókönyvet egy hideg feladatátvételi csomópont hozzáadása az SAP HANA-konfigurációhoz. Az SAP HANA dokumentációjában ezt a beállítást [állomás automatikus feladatátvételnek nevezzük.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html) Ennek a konfigurációnak van értelme egy olyan helyszíni telepítési helyzetben, ahol a kiszolgáló hardvere korlátozott, és egy egykiszolgálós csomópontot kell az éles gazdagépek gazdagép-feladatátvételi csomópontjaként felajánlani. De az Azure-ban, ahol az Azure-infrastruktúra egy kifogástalan célkiszolgálót biztosít a sikeres virtuális gép újraindításához, nincs értelme az SAP HANA-állomás automatikus feladatátvételüzembe helyezése. Az Azure szolgáltatás javítási, nincs referencia architektúra, amely előre a hana-állomás automatikus feladatátvétel készenléti csomópont. 

### <a name="special-case-of-sap-hana-scale-out-configurations-in-azure"></a>Az SAP HANA kibővített konfigurációk különleges esete az Azure-ban
Az SAP HANA kibővített konfigurációk magas rendelkezésre állása az Azure virtuális gépek szolgáltatásának gyógyulására támaszkodik, és az SAP HANA-példány újraindítása, mivel a virtuális gép újra működik. A HANA rendszerreplikáción alapuló magas rendelkezésre állású architektúrák egy későbbi időpontban kerülnek bevezetésre. 


## <a name="availability-scenarios-for-two-different-vms"></a>Két különböző virtuális gép rendelkezésre állási forgatókönyvei

Ha két Azure-beli virtuális gépet használ egy Azure rendelkezésre állási csoporton belül, növelheti a két virtuális gép közötti rendelkezésre állást, ha azok egy Azure-beli rendelkezésre állási csoportban vannak elhelyezve egy Azure-régióban. Az Azure alapbeállítása így néz ki:

![Két virtuális gép diagramja az összes réteggel](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

A különböző rendelkezésre állási forgatókönyvek szemléltetéséhez a diagram néhány rétege kimarad. Az ábrán csak a virtuális gépeket, állomásokat, rendelkezésre állási csoportokat és Az Azure-régiókat ábrázoló rétegek jelennek meg. Az Azure virtuális hálózati példányok, erőforráscsoportok és előfizetések nem játszanak szerepet az ebben a szakaszban leírt forgatókönyvekben.

### <a name="replicate-backups-to-a-second-virtual-machine"></a>Biztonsági mentések replikálása egy második virtuális gépre

Az egyik legkezdetlegesebb beállítás a biztonsági mentések használata. Különösen előfordulhat, hogy a tranzakciónapló biztonsági mentések szállított egyik virtuális gép egy másik Azure-beli virtuális gép. Kiválaszthatja az Azure Storage típusát. Ebben a beállításban ön felelős az ütemezett biztonsági mentések, amelyek az első virtuális gép a második virtuális gép parancsfájlok. Ha a második virtuálisgép-példányok, vissza kell állítania a teljes, növekményes/különbözeti és tranzakciós napló biztonsági mentések a szükséges pontig. 

Az architektúra így néz ki:

![Két virtuális gép diagramja tárolóreplikációval](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

Ez a beállítás nem alkalmas a nagy helyreállítási pont célkitűzés (RPO) és a helyreállítási idő célkitűzés (RTO) elérésére. RTO alkalommal különösen szenvedne, mivel szükség van, hogy teljes mértékben visszaállítsa a teljes adatbázis segítségével a másolt biztonsági mentések. Ez a beállítás azonban akkor hasznos, ha a fő példányok nem kívánt adattörléséből helyreáll. Ezzel a beállítással bármikor visszaállíthatja az adott időpontot, kinyerheti az adatokat, és importálhatja a törölt adatokat a fő példányba. Ezért érdemes lehet egy biztonsági másolat módszert más magas rendelkezésre állású funkciókkal kombinálni. 

A biztonsági mentések másolása közben előfordulhat, hogy egy kisebb virtuális gép, mint a fő virtuális gép, amely az SAP HANA-példány fut. Ne feledje, hogy kisebb számú virtuális gépet csatlakoztathat a kisebb virtuális gépekhez. Az egyes virtuálisgép-típusok korlátairól az [Azure-beli Linuxos virtuális gépek méretei című témakörben](https://docs.microsoft.com/azure/virtual-machines/linux/sizes)talál további információt.

### <a name="sap-hana-system-replication-without-automatic-failover"></a>SAP HANA rendszer replikációja automatikus feladatátvétel nélkül

Az ebben a szakaszban ismertetett forgatókönyvek az SAP HANA rendszer replikációját használják. Az SAP dokumentációját a [Rendszer replikációja című témakörben találja.](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html) Az automatikus feladatátvétel nélküli forgatókönyvek nem gyakoriak egy Azure-régión belüli konfigurációk esetében. A konfiguráció automatikus feladatátvétel nélkül, bár elkerülve a Pacemaker telepítő, kötelezi, hogy a figyelés és a feladatátvétel manuálisan. Mivel ez veszi, és erőfeszítéseket is, a legtöbb ügyfél támaszkodik az Azure szolgáltatás gyógyulását helyett. Vannak olyan peremhálózati esetek, amikor ez a konfiguráció segíthet a hibaforgatókönyvek szempontjából. Vagy bizonyos esetekben az ügyfél nagyobb hatékonyságot szeretne megvalósítani.

#### <a name="sap-hana-system-replication-without-auto-failover-and-without-data-preload"></a>SAP HANA rendszer replikációja automatikus feladatátvétel és adatelőzetes betöltés nélkül

Ebben a forgatókönyvben az SAP HANA rendszer replikációját használja az adatok szinkron módon történő áthelyezéséhez 0 RPO eléréséhez. Másrészt van egy elég hosszú RTO, hogy nem kell sem feladatátvételvagy adatok előtöltése a HANA-példány gyorsítótárába. Ebben az esetben a következő műveletekkel további gazdaságossakodhat a konfigurációban:

- Futtasson egy másik SAP HANA-példányt a második virtuális gépben. Az SAP HANA-példány a második virtuális gép a virtuális gép memóriájának nagy részét veszi igénybe. Abban az esetben, ha a feladatátvétel a második virtuális gép, le kell állítania a futó SAP HANA-példány, amely az adatok teljes mértékben betöltve a második virtuális gép, hogy a replikált adatok betölthető a gyorsítótárba a megcélzott HANA-példány a második virtuális gép.
- Használjon kisebb virtuálisgép-méretet a második virtuális gépen. Feladatátvétel esetén van egy további lépés a manuális feladatátvétel előtt. Ebben a lépésben átméretezi a virtuális gép a forrás virtuális gép méretére. 
 
A forgatókönyv így néz ki:

![Két virtuális gép diagramja tárolóreplikációval](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Még akkor is, ha nem használja az adatok at a HANA rendszer replikációs cél, legalább 64 GB memóriára van szüksége. A 64 GB mellett elegendő memóriára is szüksége van ahhoz, hogy a sortároló adatok a célpéldány memóriájában maradjanak.

#### <a name="sap-hana-system-replication-without-auto-failover-and-with-data-preload"></a>SAP HANA rendszer replikációja automatikus feladatátvétel és adatelőzetes betöltés nélkül

Ebben a forgatókönyvben a hana-példánya a második virtuális gép ben replikált adatok előre betöltődnek. Ez kiküszöböli az adatok előtöltésének elneme előtti adatok két előnyét. Ebben az esetben nem futtathat egy másik SAP HANA-rendszert a második virtuális gépen. Kisebb virtuálisgép-méretet sem használhat. Ezért az ügyfelek ritkán valósítják meg ezt a forgatókönyvet.

### <a name="sap-hana-system-replication-with-automatic-failover"></a>SAP HANA rendszer replikációautomatikus feladatátvételsel

Egy Azure-régión belüli szabványos és leggyakoribb rendelkezésre állási konfigurációban két SLES Linuxot futtató Azure-beli virtuális gép rendelkezik feladatátvételi fürttel. Az SLES Linux-fürt a [Pacemaker keretrendszeren](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker) alapul, egy [STONITH-eszközzel](/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) együtt. 

Az SAP HANA szempontjából a használt replikációs mód szinkronizálva van, és egy automatikus feladatátvétel van konfigurálva. A második virtuális gép, az SAP HANA-példány egy készenléti csomópontként működik. A készenléti csomópont az elsődleges SAP HANA-példány módosítási rekordjainak szinkron folyamát fogadja. Mivel a hana elsődleges csomóponton az alkalmazás tranzakciókat véglegesít, az elsődleges HANA-csomópont megvárja az alkalmazás véglegesítésének megerősítését, amíg a másodlagos SAP HANA csomópont megerősíti, hogy megkapta a véglegesítési rekordot. Az SAP HANA két szinkron replikációs módot kínál. A két szinkron replikációs mód közötti különbségek leírását és leírását az SAP [HANA rendszer replikációs módjai nak replikációs módjai](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)című SAP-cikkben találja.

A teljes konfiguráció így néz ki:

![Két virtuális gép diagramja tárolóreplikációval és feladatátvételsel](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Ezt a megoldást választhatja, mert lehetővé teszi az RPO=0 és az alacsony RTO elérését. Konfigurálja az SAP HANA-ügyfélkapcsolatot úgy, hogy az SAP HANA-ügyfelek a virtuális IP-cím használatával csatlakozzanak a HANA rendszer replikációs konfigurációjához. Egy ilyen konfiguráció szükségtelenné teszi az alkalmazás újrakonfigurálását, ha a másodlagos csomópontfeladat-átvétel történik. Ebben a forgatókönyvben az Azure VM ska-k az elsődleges és másodlagos virtuális gépek azonosnak kell lennie.

## <a name="next-steps"></a>További lépések

A konfigurációk Azure-beli beállításával kapcsolatos részletes útmutatást a következő témakörökben talál:

- [SAP HANA-rendszerreplikáció beállítása az Azure virtuális gépein](sap-hana-high-availability.md)
- [Az SAP HANA magas rendelkezésre állása a rendszer replikációjával](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Az SAP HANA Azure-régiók közötti elérhetőségéről a következő témakörben talál további információt:

- [Az SAP HANA elérhetősége az Azure-régiók között](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

