---
title: SAP munkaterhelés-konfigurációk az Azure-beli rendelkezésre állási zónák |} A Microsoft Docs
description: Magas rendelkezésre állású architektúra és forgatókönyvek használatával az Azure-beli rendelkezésre állási zónák SAP NetWeaver
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 02/03/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a8e20d617da12aea857308f4020fbd0cfe711430
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2019
ms.locfileid: "56651311"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP munkaterhelés-konfigurációk az Azure-beli rendelkezésre állási zónák
[Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview) egyike az Azure által biztosított magas rendelkezésre állású funkciókat. Rendelkezésre állási zónák használatával javítja a rendelkezésre állás az SAP számítási feladatok Azure-ban. Ez a funkció már elérhető néhány [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/). A későbbiekben lesz elérhető több régióban.

A kép SAP magas rendelkezésre állású alapvető architektúráját mutatja be:

![Standard szintű magas rendelkezésre állású konfiguráció](./media/sap-ha-availability-zones/standard-ha-config.png)

Az SAP alkalmazásrétegre helyezünk üzembe egy Azure-ban [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Magas rendelkezésre állás az SAP Central Services érdekében külön rendelkezésre állási csoportban két virtuális gép üzembe helyezése. Egy magas rendelkezésre állású keretrendszer-infrastruktúra vagy szoftveres probléma esetén automatikus feladatátvétellel, az Windows Server feladatátvételi fürtszolgáltatás vagy a támasztja (Linux). Ezeket az üzemelő példányokat kapcsolatos további információkért lásd:

- [Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [A fájlmegosztás Windows feladatátvevő fürt a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure virtuális gépeken SAP-alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

SAP NetWeaver, S/4HANA vagy Hybris rendszerek DBMS rétege hasonló architektúrával vonatkozik. Az adatbázis-kezelő réteg egy aktív/passzív módban egy Feladatátvevőfürt-megoldást infrastruktúrával vagy szoftverekkel hibákkal szembeni telepít. A Feladatátvevőfürt-megoldást egy DBMS-specifikus feladatátvételi keretrendszer, a Windows Server feladatátvételi fürtszolgáltatási vagy támasztja lehet.

Az Azure-beli rendelkezésre állási zónák a azonos architektúra üzembe helyezéséhez kell néhány módosítást a korábban leírt architektúra. Ez a cikk azt ismerteti, hogy ezeket a módosításokat.

## <a name="considerations-for-deploying-across-availability-zones"></a>Szempontok a rendelkezésre állási zónában üzembe helyezésekor


A rendelkezésre állási zónák használata esetén, vegye figyelembe a következőket:

- Egy Azure-régión belüli különböző rendelkezésre állási zónák közötti vonatkozó garanciát nem jelentenek.
- A rendelkezésre állási zónák nem állnak ideális DR-megoldásként. Természeti katasztrófák jelentős károkat régiók, többek között power infrastruktúrák (nagy erőforrásigényű) kárt okozhat. Előfordulhat, hogy a különböző zónák közötti távolságot nem elég nagy legyen a megfelelő Vészhelyreállítási megoldást jelent.
- A hálózati késést, rendelkezésre állási zónák között nem azonos az összes Azure-régióban. Bizonyos esetekben telepítheti és az SAP alkalmazásrétegre futtatni különböző zónák között, mert az aktív az adatbázis-kezelő virtuális gép egyik zónából a hálózati késés fogadható el. Azonban az egyes Azure-régióban, az aktív az adatbázis-kezelő virtuális gép és az SAP alkalmazáspéldány között késés amikor különböző zónák telepítve, előfordulhat, hogy nem elfogadható az SAP business folyamatok. Ezekben az esetekben az üzembe helyezési architektúra eltérőnek kell lennie, az aktív/aktív architektúra az alkalmazás vagy az aktív/passzív architektúra, ahol a zónák közötti hálózati késés értéke túl magas.
- Helyéről a rendelkezésre állási zónák használatára, ha a hálózati késést a zónák közötti a döntéseiben alapjául. Hálózati késés a két területen fontos szerepet játszik:
    - A két adatbázis-kezelő példányt, amely a szinkron replikáció rendelkeznie kell a késés. Minél nagyobb a hálózati késés, nagyobb valószínűséggel a számítási feladat skálázhatóságát negatív hatással lesz.
    - A hálózati késés közötti különbség egy virtuális Gépet egy SAP párbeszédpanel példány a-zónát az aktív az adatbázis-kezelő példányban való futtatása és a egy hasonló virtuális Gépet egy másik zónában. Ahogy ezt a különbséget növekszik, az üzleti folyamatokat és a batch futási ideje befolyásolja feladatok is nő, függ, hogy fusson-e a zóna az adatbázis-kezelő vagy egy másik zónában.

Amikor üzembe helyezése az Azure virtuális gépek rendelkezésre állási zónák között, és ugyanazon Azure-régióban feladatátvételi megoldások létrehozására, bizonyos korlátozások vonatkoznak:

- Használjon [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) telepítésekor meg kell adnia az Azure-beli rendelkezésre állási zónák. 
- Az Azure-előfizetés alapján leképezése a fizikai zóna zóna enumerálások rögzítve van. Ha különböző előfizetésekhez használja az SAP-rendszerek központi telepítéséhez, szüksége definiálása az ideális zónák minden egyes előfizetés esetén.
- Nem telepíthet egy Azure rendelkezésre állási zónában belüli Azure rendelkezésre állási csoportokat. Válassza ki az egyik vagy másik virtuális gépek üzembe helyezési keretét.
- Nem használhat egy [Azure alapszintű Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) megoldásokat hozhat létre feladatátvevő fürt Windows Server feladatátvételi fürtszolgáltatás vagy a Linux támasztja alapján. Ehelyett kell használnia a [Azure Standard Load Balancer Termékváltozat](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones).



## <a name="the-ideal-availability-zones-combination"></a>Ideális megoldás a rendelkezésre állási zónák együttes használata
A rendelkezésre állási zónák használata mellett dönt, meg kell határoznia:

- A hálózati késést a három zónák egy Azure-régió között. Ez lehetővé teszi, hogy válassza ki a zónák a zónák közötti hálózati forgalmat a legalacsonyabb hálózati késéssel rendelkező.
- Virtuális gép virtuális gép késés egyikébe a zónák a kiválasztása, és a hálózati késés, Ön két zónákban közötti különbség.
- E virtuális gép típusát, amely üzembe kell helyeznie érhetők el a két zónák kiválasztott meghatározását. Az egyes virtuális gépek, különösen M sorozatú virtuális gépek, előfordulhat, hogy helyzet előáll, amelyben egyes termékváltozatok a következők csak két a három zónából állnak rendelkezésre.

## <a name="network-latency-between-and-within-zones"></a>Hálózati késés, illetve zónák között
Annak megállapításához, a késés, a különböző zónák között, kell tennie:

- Helyezze üzembe az összes három zónát a DBMS példány használni kívánt virtuális gép Termékváltozata. Győződjön meg arról, hogy [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) processzorfelhasználás, ha engedélyezve van.
- Ha megtalálta a két zónákat a legalacsonyabb hálózati késéssel rendelkező, üzembe helyezése egy másik három virtuális gépet a VM-termékváltozat a három rendelkezésre állási zónák között az alkalmazásrétegre virtuális Géphez használni kívánt. A két adatbázis-kezelő virtuális gép, a két adatbázis-kezelő zónában kiválasztott mérhetők a hálózati késést. 
- Használat **niping** mérési eszközként. Ez az eszköz, SAP, az SAP támogatási megjegyzések leírt [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A késési mérések feljegyezni parancsok koncentrálhat. Mivel **ping** nem működik az Azure gyorsított hálózatkezelés kódhoz tartozó elérési út, keresztül nem ajánlott, hogy használjuk.

A mértékek és a rendelkezésre állási zónában a VM-termékváltozatok rendelkezésre állásának alapján kell néhány döntéseket:

- Adja meg az adatbázis-kezelő réteg a ideális zónákat.
- Határozza meg, hogy az SAP aktív alkalmazásréteg szét egy, kettő vagy három zónakészleten zónák közötti hálózati késés a-zóna és különbségek alapján.
- Határozza meg, hogy szeretné-e üzembe helyezése egy aktív/passzív konfigurációt vagy egy aktív-aktív konfiguráció az alkalmazás szempontjából. (Ezek a beállítások magyarázatát a cikk későbbi részében.)

Ezek a döntések meghozatalában, is figyelembe kell venni fiók SAP hálózati késés javaslatok, SAP-jegyzetnek leírt [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

> [!IMPORTANT]
> A mértékek és hozott döntések megtette a mérések használt Azure-előfizetéshez tartozó érvényesek. Ha egy másik Azure-előfizetést használ, ismételje meg a mérési szeretne. A leképezés sorszámozott zónák más Azure-előfizetések eltérőek lehetnek.


> [!IMPORTANT]
> Valószínű, hogy a korábban ismertetett mérések nyújt minden Azure-régióban, amely támogatja a különböző eredményt [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Akkor is, ha a hálózati késés követelmények azonosak, szüksége lehet a különböző központi telepítési stratégiát különböző Azure-régióban fogad el, mert a zónák közötti hálózati késés eltérő lehet. Az egyes Azure-régióban teljesen más lehet a hálózati késést a három különböző zónák között. Más régiókban egységesebb lehet a hálózati késést a három különböző zónák között. A jogcímet, amely mindig van egy 1 – 2 ezredmásodperc közötti hálózati késés modul nem megfelelő. A hálózati késést, rendelkezésre állási zónában az Azure-régióban nem általánosíthatók.

## <a name="activeactive-deployment"></a>Aktív/aktív központi telepítés
Ez az üzembe helyezési architektúra aktív/aktív nevezik, mert a két vagy három zónák között az aktív SAP párbeszédpanel-példányok üzembe helyezésekor. Az SAP Central Services-példányhoz, sorba replikációt használ a két zónák közötti telepíti. Ugyanez vonatkozik az adatbázis-kezelő szintje, amely telepíti az SAP Central szolgáltatás ugyanazon zónák között.

Amikor kiválasztja ezt a konfigurációt, meg kell, hogy az ajánlat zónák közötti hálózati késés, amely a számítási feladatok és az adatbázis-kezelő a szinkron replikáció elfogadható keresnie a két rendelkezésre állási zónák a régióban. Azt is szeretné, hogy a különbözeti közötti hálózati késés a kiválasztott zónák belül, és a zónák közötti hálózati késés nem túl nagy. Ennek oka, hogy nem szeretné, hogy nagy változások, attól függően, hogy a feladat fut, a-zónát a DBMS-kiszolgálóval vagy a keresztben zónák, az üzleti folyamatokkal vagy kötegelt feladatok futtatása rendszerideje a. Különböző módszerekkel elfogadhatók, azonban nincs különbség a tényezők.

Az aktív/aktív központi két zónában egy egyszerűsített séma nézhet ki:

![Aktív/aktív zóna üzembe helyezés](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- Akkor kezelje az Azure-beli rendelkezésre állási zónák tartalék és frissítési tartományok az összes virtuális gép, mert a rendelkezésre állási csoportok az Azure-beli rendelkezésre állási zónák nem telepíthető.
- A terheléselosztók a feladatátvevő fürtök SAP Central Services és az adatbázis-kezelő rétegben kell használnia a [Standard Termékváltozat Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű Load Balancer több zónában nem fog működni.
- Az Azure virtuális hálózat üzembe helyezett SAP-rendszerhez, és az alhálózatok, üzemeltetésére van nyújtva zónák között. Nem kell külön az egyes zónák virtuális hálózatok.
- Az összes virtuális gépet telepít, meg kell használnia [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek zónaszintű telepítések esetén nem támogatott.
- Az Azure Premium Storage és [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatják a tárreplikáció bármilyen típusú zónák között. Az alkalmazás (adatbázis-kezelő vagy SAP Central Services) kell replikálni a fontos adatok.
- Ugyanez igaz a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows) vagy egy NFS-megosztás (Linux). Olyan technológia, amely replikálja ezeket olyan megosztott lemezzel vagy megosztások a zónák közötti használni kell. Ezek a technológiák támogatottak:
    - A Windows, egy adott megoldást, amely használja az SIOS DataKeeper, dokumentált módon [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - SUSE Linux rendszeren az NFS-megosztási leírtak szerint beépített [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
    Jelenleg a Microsoft Scale-Out File Server leírtak szerint használó megoldások [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), nem támogatott zónák között.
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [támasztja a SUSE Linux-fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok.
- Futási idő-konzisztenciát a kritikus üzleti folyamatokat eléréséhez közvetlen egyes batch-feladatok és a felhasználók számára, amelyek a zónához alkalmazáspéldányok megpróbálhatja az SAP DBMS aktív példányról a batch-kiszolgálócsoportok, bejelentkezési vagy RFC csoportok. Azonban a zónaszintű feladatátvétel esetén sem kell ezeket a csoportokat manuálisan át futó virtuális gépek, amelyek a zónához-példányok az aktív DB virtuális géppel.  
- Előfordulhat, hogy szeretné az egyes zónák inaktív párbeszédpanel-példányok üzembe. Ez a engedélyezéséhez egy azonnali vissza a korábbi erőforrás-kapacitása, ha egy részét az alkalmazáspéldányok által használt zóna nem működik.


## <a name="activepassive-deployment"></a>Aktív/passzív üzembe helyezés
Ha nem talál egy elfogadható különbözeti között egy zónán belül a hálózati késés és a zónák közötti hálózati forgalom késését, telepíthet egy olyan architektúra, amely rendelkezik egy aktív/passzív karaktert, a SAP alkalmazás réteg szempontjából. Megadhat egy *aktív* zónában a zóna kerül, ha a teljes alkalmazás réteg telepít, és megkísérli az aktív adatbázis-kezelő és az SAP Central Services-példányt is futtatni. Az ilyen konfiguráció kell, hogy nincs szélsőséges futási ideje változata létezik, attól függően, hogy a feladat fut, a zóna az aktív DBMS-példánnyal rendelkező vagy nem, az üzleti tranzakciók és a batch-feladatok.

Az alapszintű elrendezés az architektúra a következőhöz hasonló:

![Aktív/passzív zónát üzembe helyezés](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- A rendelkezésre állási csoportok az Azure-beli rendelkezésre állási zónák nem telepíthető. Tehát ebben az esetben tartozik egy frissítési és a tartalék tartomány az alkalmazásrétegre. Ennek oka az, egy zóna csak telepítik. Ez a konfiguráció, kívánatos valamivel kisebb, mint a referencia-architektúra, ami alapján, hogy telepít-e az alkalmazási rétegre egy Azure rendelkezésre állási csoportban.
- Ez az architektúra használatakor kell szorosan állapotának figyelése és az aktív az adatbázis-kezelő és az SAP Central Services példány törekedjen az üzembe helyezett alkalmazás rétegként ugyanabban a zónában. SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétel esetén érdemes győződjön meg arról, hogy manuálisan helyezésnél újra üzembe a zóna a SAP alkalmazás réteggel, a lehető leggyorsabban üzembe helyezve.
- A terheléselosztók a feladatátvevő fürtök SAP Central Services és az adatbázis-kezelő rétegben kell használnia a [Standard Termékváltozat Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű Load Balancer több zónában nem fog működni.
- Az Azure virtuális hálózat üzembe helyezett SAP-rendszerhez, és az alhálózatok, üzemeltetésére van nyújtva zónák között. Nem kell külön az egyes zónák virtuális hálózatok.
- Az összes virtuális gépet telepít, meg kell használnia [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek zónaszintű telepítések esetén nem támogatott.
- Az Azure Premium Storage és [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatják a tárreplikáció bármilyen típusú zónák között. Az alkalmazás (adatbázis-kezelő vagy SAP Central Services) kell replikálni a fontos adatok.
- Ugyanez igaz a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows) vagy egy NFS-megosztás (Linux). Olyan technológia, amely replikálja ezeket olyan megosztott lemezzel vagy megosztások a zónák közötti használni kell. Ezek a technológiák támogatottak:
    - A Windows, egy adott megoldást, amely használja az SIOS DataKeeper, dokumentált módon [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - SUSE Linux rendszeren az NFS-megosztási leírtak szerint beépített [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).
    
  Jelenleg a Microsoft Scale-Out File Server leírtak szerint használó megoldások [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), nem támogatott zónák között.
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [támasztja a SUSE Linux-fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok.
- Üzembe kell helyeznie a passzív inaktív virtuális gépeinek zóna (szempontból egy adatbázis-kezelő), így zóna hiba esetén az alkalmazás-erőforrások.
    - [Az Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) jelenleg nem tudja aktív virtuális gépek replikálása az inaktív virtuális gépek zónák között. 
- Az automation, amely lehetővé teszi, hogy zóna meghibásodása, automatikus indítását a második zónában az SAP alkalmazásrétegre kell fektet.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Magas rendelkezésre állású és vész-helyreállítási konfiguráció együttesen
A Microsoft a létesítmények közötti földrajzi távokat semmilyen információt nem osztja a gazdagép másik Azure rendelkezésre állási zónák egy Azure-régióban. Egyes ügyfeleink továbbra is, zónák egy kombinált magas rendelkezésre ÁLLÁS és Vészhelyreállítás konfigurációjához, amely szerint a nulla helyreállítási időkorlát (RPO) használ. Ez azt jelenti, hogy még katasztrófa utáni helyreállítás esetén adatbázisban véglegesített tranzakciók ne elvesznek. 

> [!NOTE]
> Azt javasoljuk, hogy csak bizonyos körülmények között az ilyen konfiguráció használata. Például használhat, ha az adatok nem hagyhatják el az Azure-régióban a biztonsági vagy megfelelőségi okokból. 

Íme egy példa milyen sebességgel nézhet ki:

![A zónák a magas rendelkezésre állású DR kombinált](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- Ön vagy feltételezi, hogy jelentős távolságra van egy rendelkezésre állási zónát üzemeltető a létesítmények közötti, vagy éppen kelljen egy bizonyos Azure-régióban maradjon. A rendelkezésre állási csoportok az Azure-beli rendelkezésre állási zónák nem telepíthető. Tehát ebben az esetben tartozik egy frissítési és a tartalék tartomány az alkalmazásrétegre. Ennek oka az, egy zóna csak telepítik. Ez a konfiguráció, kívánatos valamivel kisebb, mint a referencia-architektúra, ami alapján, hogy telepít-e az alkalmazási rétegre egy Azure rendelkezésre állási csoportban.
- Ez az architektúra használatakor kell szorosan állapotának figyelése és az aktív az adatbázis-kezelő és az SAP Central Services példány törekedjen az üzembe helyezett alkalmazás rétegként ugyanabban a zónában. SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétel esetén érdemes győződjön meg arról, hogy manuálisan helyezésnél újra üzembe a zóna a SAP alkalmazás réteggel, a lehető leggyorsabban üzembe helyezve.
- Éles környezetben az alkalmazáspéldányok a QA aktív alkalmazáspéldányok futtató virtuális gépek előre telepítve kell lennie.
- Egy zóna hiba esetén a QA alkalmazáspéldányok leállítja, majd indítsa el az éles példányokba helyette. Vegye figyelembe, hogy szeretné-e használni a virtuális nevét az alkalmazáspéldányok működnek.
- A terheléselosztók a feladatátvevő fürtök SAP Central Services és az adatbázis-kezelő rétegben kell használnia a [Standard Termékváltozat Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű Load Balancer több zónában nem fog működni.
- Az Azure virtuális hálózat üzembe helyezett SAP-rendszerhez, és az alhálózatok, üzemeltetésére van nyújtva zónák között. Nem kell külön az egyes zónák virtuális hálózatok.
- Az összes virtuális gépet telepít, meg kell használnia [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek zónaszintű telepítések esetén nem támogatott.
- Az Azure Premium Storage és [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatják a tárreplikáció bármilyen típusú zónák között. Az alkalmazás (adatbázis-kezelő vagy SAP Central Services) kell replikálni a fontos adatok.
- Ugyanez igaz a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows) vagy egy NFS-megosztás (Linux). Olyan technológia, amely replikálja ezeket olyan megosztott lemezzel vagy megosztások a zónák közötti használni kell. Ezek a technológiák támogatottak:
    - A Windows, egy adott megoldást, amely használja az SIOS DataKeeper, dokumentált módon [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk).
    - SUSE Linux rendszeren az NFS-megosztási leírtak szerint beépített [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs).

  Jelenleg a Microsoft Scale-Out File Server leírtak szerint használó megoldások [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share), nem támogatott zónák között.
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [támasztja a SUSE Linux-fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok.





## <a name="next-steps"></a>További lépések
Az alábbiakban további lépések az Azure rendelkezésre állási zónában üzembe helyezéséhez:

- [Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ az Azure-ban a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP magas rendelkezésre állás egy Windows feladatátvevő fürt és -fájlmegosztást az SAP ASCS/SCS-példányok Azure-infrastruktúra előkészítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






