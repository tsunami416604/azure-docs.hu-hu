---
title: SAP munkaterhelés-konfigurációk az Azure-beli rendelkezésre állási zónák |} A Microsoft Docs
description: Magas rendelkezésre állású architektúra és forgatókönyvek esetében az SAP NetWeaver az Azure rendelkezésre állási zónák használata
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
ms.openlocfilehash: e319fb5bc1e0ed3354a09b685405656c7f3131bc
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55858805"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP munkaterhelés-konfigurációk az Azure-beli rendelkezésre állási zónák

Az Azure-ajánlatok a magas rendelkezésre állású-funkciók egyikét [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). A rendelkezésre állási zónák használatával javítja a rendelkezésre állás az SAP számítási feladatok Azure-ban. A rendelkezésre állási zónák érhetők el több [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/) már. Több régió követi. 

SAP magas rendelkezésre állású alapvető architektúrájának írható le ezt az ábrát a név jelenik meg:

![Standard szintű magas rendelkezésre ÁLLÁS konfigurálása](./media/sap-ha-availability-zones/standard-ha-config.png)

Az SAP alkalmazásrétegre helyezünk üzembe egy Azure-ban [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). A magas rendelkezésre állás az SAP Central Services helyezzen üzembe egy külön rendelkezésre állási csoportban két virtuális gépet. Egy magas rendelkezésre állású keretrendszer infrastruktúra vagy szoftveres probléma esetén automatikus feladatátvétellel, az Windows feladatátvételi fürt szolgáltatások vagy a támasztja (Linux). Ilyen központi telepítések részletező dokumentumok itt találhatók:

- [Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [A fájlmegosztás Windows feladatátvevő fürt a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure virtuális gépeken SAP-alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

SAP NetWeaver, S/4HANA vagy Hybris rendszerek DBMS rétege hasonló architektúrával vonatkozik. Az adatbázis-kezelő réteg egy aktív/passzív módban egy Feladatátvevőfürt-megoldást infrastruktúrával vagy szoftverekkel hibákkal szembeni telepít. A Feladatátvevőfürt-megoldást vagy az adatbázis-kezelő feladatátvételi keretrendszer, a Windows feladatátvételi fürt szolgáltatások vagy támasztja lehet. 

Az Azure rendelkezésre állási zónák használata azonos architektúra üzembe helyezéséhez, néhány módosítást a fent vázolt architektúra van szükség. Ez a dokumentum ismerteti ezeket a módosításokat.

## <a name="considerations-deploying-across-availability-zones"></a>Szempontok a rendelkezésre állási zónában üzembe helyezése

A rendelkezésre állási zónák használata esetén néhány dolgot figyelembe venni:

- Az Azure rendelkezésre állási zónák nem garantálja a bizonyos egy Azure-régióban a különböző zónák közötti távolság.
- Az Azure rendelkezésre állási zónák nem állnak ideális DR-megoldásként. Természeti katasztrófák széles okozhat az egyes régiók, többek között power infrastruktúra nehéz kárt okoznának terjednek. Különböző zónák közötti távolság nem elég nagy ahhoz, hogy a megfelelő Vészhelyreállítási megoldás lehet, hogy lehet.
- A hálózati késést, rendelkezésre állási zónák között változik, a különböző Azure-régióban. Előfordulhatnak olyan esetek, ahol telepítheti és az SAP alkalmazásrétegre futtatható különböző zónák között, mint a hálózati késés egy zóna aktív az adatbázis-kezelő virtuális gép egy folyamat az üzletmenetre, fogadható el. De az egyes Azure-régióban a aktív az adatbázis-kezelő virtuális gép és az SAP alkalmazáspéldány üzembe helyezett különböző zónák között késés túl zavaró és az SAP business folyamatok fogadható el. Ennek eredményeképpen az üzembe helyezési architektúra eltérőnek kell lennie az aktív/aktív architektúra az alkalmazás vagy az aktív/passzív architektúra, ahol a zónák közötti késés értéke túl magas.
- Döntse el, melyik konfiguráció is használhat az Azure rendelkezésre állási zónák, a hálózati késéssel mérni a különböző zónák között. A hálózati késést a két területen fontos szerepet játszik:
    - A két adatbázis-kezelő példányt, amely egy létre szinkron replikáció rendelkeznie kell a késés. Minél nagyobb a hálózati késés, a számítási feladatok hatását méretezhetőségét, annál nagyobb a lehetséges
    - A hálózati késés közötti különbség egy virtuális Gépet egy SAP párbeszédpanel példány a-zónát az aktív az adatbázis-kezelő példányban való futtatása és a egy hasonló virtuális Gépet egy másik zónában. Minél nagyobb ez különbség, minél nagyobb az üzleti folyamatokat és kötegelt feladatok, függ, hogy fusson-e a futási idő gyakorolt a-zóna az adatbázis-kezelő vagy egy másik zónában.


Bizonyos korlátozások vonatkoznak az Azure virtuális gépek rendelkezésre állási zónák és ugyanazon Azure-régióban feladatátvételi megoldásokat létrehozó telepítésekor. Ezek a korlátozások a következők terjed ki:

- Használatával [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) kötelező az Azure rendelkezésre állási zónában történő üzembe helyezéséhez 
- Az Azure-előfizetés alapján leképezése a fizikai zóna zóna enumerálások rögzítve van. Különböző előfizetésekben használatakor az SAP-rendszerek telepítése a minden egyes előfizetés esetén ideális zónák meghatározásához szüksége.
- Nem telepíthet egy Azure rendelkezésre állási zónán belüli Azure rendelkezésre állási csoportokat. Válasszon egy Azure rendelkezésre állási zónában vagy az Azure rendelkezésre állási csoport, a virtuális gépek deployment keretrendszer.
- Nem használhat egy [Azure alapszintű Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) megoldásokat hozhat létre feladatátvevő fürt feladatátvételi fürtszolgáltatások Windows vagy Linux rendszerű támasztja alapján. Ehelyett kell használnia a [Azure Standard Load Balancer Termékváltozat](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-availability-zones-combination"></a>Ideális a rendelkezésre állási zónák kombinációja
Úgy dönt, hogy miként lehet hasznosítani a rendelkezésre állási zónák, mielőtt kell, amelyen vizsgálat végrehajtása:

- A hálózati késést, egy Azure-régióhoz a három különböző zónák között. Tehát, hogy a zónák a legalacsonyabb hálózati késéssel a közötti hálózati forgalom zóna rendelkező választhat
- A különbség az Ön által választott zónák telefonszámaira belüli virtuális gép virtuális gép késés és a hálózati késést a választott két zónák között
- Hogy a virtuális gép típusokat, hogy telepíteni kell érhetők el a két zónák tetszőleges nyilatkozat. Különösen az M sorozat virtuális gépei fog olyan helyzetekben, ahol a különböző M sorozatú virtuális gépek Termékváltozatait gazdakiszolgálói lesznek csak a három zóna közül kettő a hibát

### <a name="network-latency-between-zones-and-within-zone"></a>Zónán belül és a zónák közötti hálózati késés
Ismerje meg, hogy a késés, a különböző zónák között van, akkor kell:

- Helyezze üzembe az összes három zónát a DBMS példány használni kívánt virtuális gép Termékváltozata. Győződjön meg arról, hogy [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van ez a mérés végrehajtásakor.
- A két zónákat a legalacsonyabb hálózati késéssel rendelkező megkereséséhez, üzembe helyezése egy másik három virtuális gépet a VM-termékváltozat használja, mint a három rendelkezésre állási zónák között a virtuális gép alkalmazási rétegre szeretne. A két különböző "DBMS" zónában a választott mérhetők a két "adatbázis-kezelő virtuális gép" a hálózati késést. 
- Mérhető eszközként használni **niping**. Egy eszközt, a SAP, amely az SAP támogatási megjegyzések leírtak szerint működik [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A parancsok késési mérések feljegyezni SAP koncentrálhat. Használatával **ping** nem ajánlott, mivel **ping** nem működik az Azure-on keresztül a gyorsított hálózati kódhoz tartozó elérési út.

A mértékek és a rendelkezésre állási zónában a VM-termékváltozatok rendelkezésre állásának alapján kell a döntéseket:

- Az ideális zónák a DBMS-réteg megadása
- Válasz a kérdésre Ha azt szeretné, az SAP aktív alkalmazásréteg szét egy, két vagy három zónát alapján különbségek a hálózati késés a-zóna és a zónák között
- Ha egy aktív/passzív vagy egy aktív-aktív konfiguráció az alkalmazás szempontjából telepítendő, (tekintse meg később) kérdésre

Ezek a döntések meghozatalában is visszahívása SAP hálózati késés javaslatok SAP-jegyzetnek leírt [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Vegye figyelembe

> [!IMPORTANT]
> A mértékek és hozott döntések, így ezeknek a méréseknek használt Azure-előfizetéshez tartozó érvényesek. Ha más Azure-előfizetések használata esetén ismételje meg a mérés, mivel a leképezés sorszámozott zónák eltérőek lehetnek egy másik Azure-előfizetés szeretné.


> [!IMPORTANT]
> Valószínű, hogy a méreteket, ahogy a fenti megjelennek a különböző eredményeket minden Azure-régióban, amely támogatja a [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). Akkor is, ha a hálózati késés követelmények azonosak, szüksége lehet a különböző központi telepítési stratégiát különböző Azure-régióban fogad el, mivel a zónák közötti hálózati késés eltérő lehet. Az egyes Azure-régióban, a hálózati késést a három különböző zónák között, hogy a teljesen más várható. Mivel a más régiókban a hálózati késést a három különböző zónák között egységesebb. A jogcím, mivel **mindig** egy 1 ezredmásodpercre 2 ezredmásodperc, a zónák közötti hálózati késés **helytelen**. A hálózati késést, rendelkezésre állási zónában az Azure-régióban nem általánosíthatók.


## <a name="activeactive-deployment"></a>Aktív/aktív központi telepítés
Az üzembe helyezési architektúra neve aktív/aktív, mert két vagy három zónák között az aktív SAP párbeszédpanel-példányok üzembe helyezésekor. A sorba replikációs SAP Central Services rendszer fog üzembe helyezni két zónák között. Ugyanez vonatkozik az adatbázis-kezelő szintje, amely telepíthető, mint az SAP Central szolgáltatás ugyanazon zónákban történik.

Az ilyen konfiguráció fontolóra, meg kell keresnie a két rendelkezésre állási zónák közötti zóna hálózati késés a szinkron replikációhoz az adatbázis-kezelő és a számítási feladatok számára elfogadható kínáló régiója. Továbbá azt szeretné, hogy a különbözeti közötti hálózati késés a kiválasztott zónák belül, és a különböző zóna hálózati késés nem túl nagy. Az utóbbi OK, hogy nem szeretné túl nagy változások, az üzleti folyamatok futtatási idejének vagy kötegelt feladatok, függ, hogy egy feladat fut a-zónát az adatbázis-kezelő kiszolgáló vagy zóna között. Különböző módszerekkel elfogadhatók, azonban nincs különbség a tényezők.

Az aktív/aktív központi két zónában egy egyszerűsített séma nézhet:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- Kezeli az Azure rendelkezésre állási zónák tartalék és frissítési tartományok az összes virtuális gép, mert a rendelkezésre állási csoportok nem telepíthetők az Azure rendelkezésre állási zónák
- Az Azure load Balancer terheléselosztók használ a feladatátvevő fürtök, az SAP Central Services és az adatbázis-kezelő réteg kell lennie a [Standard Termékváltozatú terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű load balancer nem fog működni a zónák között
- Az Azure virtuális hálózat és az alhálózatok, az SAP-rendszer üzemeltetéséhez telepített zónákban kiterjesztve tárolja. Ön **nem kell** külön az egyes zónák virtuális hálózatok
- Minden virtuális gép központi telepítése, használata [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek használata nem támogatott a zónaszintű központi telepítésekhez
- Az Azure Premium storage vagy [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatnak bármilyen típusú tárreplikáció zónák között. Ennek eredményeképpen feladata az alkalmazás (adatbázis-kezelő vagy SAP Central Services) fontos adatok replikálása
- Ugyanez vonatkozik a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows), vagy az NFS-megosztások (Linux). Eljárás, amelyet a rendszer replikál egy megosztott lemez vagy fájlmegosztás a zónák közötti kell. Jelenleg a következő technológiákat támogatja:
    - A Windows, egy adott megoldást, amely használja az SIOS Datakeeper leírtak szerint [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) támogatott zónák között
    - SUSE Linux rendszeren az NFS-megosztási leírtak szerint beépített [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) támogatott
    - Jelenleg a megoldás szolgáltatással Windows kibővített fájlkiszolgálókon (SOFS) leírtak szerint [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **zónák között nem támogatott**
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [SUSE Linux-alapú támasztja fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok
- Futási idő-konzisztenciát a kritikus üzleti folyamatokat eléréséhez közvetlen egyes batch-feladatok és a felhasználók számára, amelyek a zónához alkalmazáspéldányok megpróbálhatja SAP Batch kiszolgálócsoportok, bejelentkezési vagy RFC csoportok használatával az aktív az adatbázis-kezelő példánnyal. Azonban a zónaszintű feladatátvétel kell ezeket a csoportokat manuálisan át futó virtuális gépek a-zónát az aktív DB Virtuálisgép-példányok.  
- Döntse el, hogy szeretné-e inaktív párbeszédpanel példányaihoz kell helyeznie azonnal az előbbi erőforrás-kapacitása, ha egy részét az alkalmazáspéldányok által használt zóna nem működik a zónák mindegyikében üzembe helyezése


## <a name="activepassive-deployment"></a>Aktív/passzív üzembe helyezés
Ha nem találja a hálózati késés belül egy zónát, és több zóna hálózati forgalom között egy elfogadható eltelt, telepítheti az architektúra van egy aktív/passzív karaktert, a SAP alkalmazás réteg szempontjából. Megadhat egy "aktív" zónában, amely a zóna, amelyen központi telepítését a teljes alkalmazás réteget, és ahol megkísérli futtatni a aktív az adatbázis-kezelő és az SAP Central Services példánynak egyaránt. Az ilyen konfiguráció akkor győződjön meg arról, extrém futási idő változata nem kell az üzleti tranzakciók és a kötegelt feladatok, függő, hogy a feladat fut-e a-zónát az aktív DBMS-példányt, vagy nem.

Az ilyen architektúra alapszintű elrendezését hasonlóan néz ki:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- A rendelkezésre állási csoportok nem telepíthető az Azure rendelkezésre állási zónákban. Ezért ebben az esetben tartozik egy frissítési és a tartalék tartomány az alkalmazásrétegre. Oka, hogy csak telepítik egy zónát. Ez a konfiguráció egy kis mértékben setback, a referencia-architektúra, amely úgy rendelkezik, hogy telepít-e az alkalmazási rétegre egy Azure rendelkezésre állási csoportban képest.
- Az ilyen architektúra működő, kell végezzen alapos figyelést, és próbálja meg az aktív az adatbázis-kezelő és az SAP Central services-példányok az üzembe helyezett alkalmazás rétegként ugyanabban a zónában. SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétel esetén győződjön meg arról, hogy manuálisan helyezésnél újra üzembe a zóna az SAP alkalmazásrétegre, amilyen hamar csak lehet telepítve a kívánt
- Az Azure load Balancer terheléselosztók használ a feladatátvevő fürtök, az SAP Central Services és az adatbázis-kezelő réteg kell lennie a [Standard Termékváltozatú terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű load balancer nem fog működni a zónák között
- Az Azure virtuális hálózat és az alhálózatok, az SAP-rendszer üzemeltetéséhez telepített zónákban kiterjesztve tárolja. Ön **nem kell** külön az egyes zónák virtuális hálózatok
- Az összes virtuális gépet telepít, meg kell használnia [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek használata nem támogatott a zónaszintű központi telepítésekhez
- Az Azure Premium storage vagy [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatnak bármilyen típusú tárreplikáció zónák között. Ennek eredményeképpen feladata az alkalmazás (adatbázis-kezelő vagy SAP Central Services) fontos adatok replikálása
- Ugyanez vonatkozik a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows), vagy az NFS-megosztások (Linux). Eljárás, amelyet a rendszer replikál egy megosztott lemez vagy fájlmegosztás a zónák közötti kell. Jelenleg a következő technológiákat támogatja:
    - A Windows, egy adott megoldást, amely használja az SIOS Datakeeper leírtak szerint [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) támogatott zónák között
    - SUSE Linux rendszeren az NFS-megosztási leírtak szerint beépített [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) támogatott
    - Jelenleg a megoldás szolgáltatással Windows kibővített fájlkiszolgálókon (SOFS) leírtak szerint [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **zónák között nem támogatott**
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [SUSE Linux-alapú támasztja fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok
- A zóna hiba esetén az alkalmazás-erőforrások indul el (a egy adatbázis-kezelő szempontjából) a passzív zónát inaktív virtuális gépek üzembe helyezése
    - Nem használhat [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) az aktív virtuális gépek replikálása az inaktív virtuális gépek zónák között. Nem sikerül teljesítéséhez egy adott függvény ezen a ponton a időben történő, Azure Site Recovery
- Az automation, amely lehetővé teszi, hogy zóna meghibásodása, automatikus indítását a második zónában az SAP alkalmazásrétegre helyre költeni

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Magas rendelkezésre állású és vész-helyreállítási konfiguráció együttesen
A Microsoft nem oszt meg semmilyen információt a létesítmények közötti földrajzi távolságtól a gazdagép másik Azure rendelkezésre állási zónák Azure-régióban. Annak ellenére, hogy ezt a tényt, az egyes ügyfelek egy kombinált magas rendelkezésre ÁLLÁS és Vészhelyreállítás konfigurációjához, amely szerint a zónák vannak kihasználva egy **R**helyreállítási **P**onthoz **O**bjective (RPO) értéke nulla. Ami azt jelenti, hogy kell nem vesznek el még akkor is, a vész-helyreállítási eset adatbázisban véglegesített tranzakciók. 

> [!NOTE]
> Ilyen konfiguráció ajánlott csak bizonyos körülmények között. Például esetekben, ahol adatok nem hagyhatják el az Azure-régió biztonsági és megfelelőségi okok miatt. 

Milyen sebességgel nézhet például mutatják be ezt az ábrát:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- Ön vagy feltételezve, hogy egy rendelkezésre állási zónát üzemeltető a létesítmények közötti jelentős távolságra van. Vagy egy bizonyos Azure-régióban maradjon, kényszerítve. A rendelkezésre állási csoportok nem telepíthető az Azure rendelkezésre állási zónákban. Ezért ebben az esetben befejezése esetén egy frissítési és a tartalék tartomány az alkalmazásréteg számára. Oka, hogy csak telepítik egy zónát. Ez az egy setback képest a referencia-architektúra, amely úgy rendelkezik, hogy telepít-e az alkalmazási rétegre egy Azure rendelkezésre állási csoportban.
- Az ilyen architektúra működő, kell végezzen alapos figyelést, és próbálja meg az aktív az adatbázis-kezelő és az SAP Central services-példányok az üzembe helyezett alkalmazás rétegként ugyanabban a zónában. SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétel esetén győződjön meg arról, hogy manuálisan helyezésnél újra üzembe a zóna az SAP alkalmazásrétegre, amilyen hamar csak lehet telepítve a kívánt
- Éles környezetben az alkalmazáspéldányok a QA aktív alkalmazáspéldányok futtató virtuális gépek előre telepítve van.
- A zónaszintű hiba esetén a QA alkalmazáspéldányok leállítja, majd indítsa el az éles példányokba helyette. Ne feledje, hogy együttműködik az alkalmazáspéldányok működnek virtuális nevét kell
- Az Azure load Balancer terheléselosztók használ a feladatátvevő fürtök, az SAP Central Services és az adatbázis-kezelő réteg kell lennie a [Standard Termékváltozatú terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű load balancer nem fog működni a zónák között
- Az Azure virtuális hálózat és az alhálózatok, az SAP-rendszer üzemeltetéséhez telepített zónákban kiterjesztve tárolja. Ön **nem kell** külön az egyes zónák virtuális hálózatok
- Az összes virtuális gépet telepít, meg kell használnia [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek használata nem támogatott a zónaszintű központi telepítésekhez
- Az Azure Premium storage vagy [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatnak bármilyen típusú tárreplikáció zónák között. Ennek eredményeképpen feladata az alkalmazás (adatbázis-kezelő vagy SAP Central Services) fontos adatok replikálása
- Ugyanez vonatkozik a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows), vagy az NFS-megosztások (Linux). Eljárás, amelyet a rendszer replikál egy megosztott lemez vagy fájlmegosztás a zónák közötti kell. Jelenleg a következő technológiákat támogatja:
    - A Windows, egy adott megoldást, amely használja az SIOS Datakeeper leírtak szerint [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) támogatott zónák között
    - SUSE Linux rendszeren az NFS-megosztási leírtak szerint beépített [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) támogatott
    - Ezen a ponton az időben, a szolgáltatással Windows kibővített fájlkiszolgálókon (SOFS) leírtak szerint megoldás [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **zónák között nem támogatott**
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [SUSE Linux-alapú támasztja fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok





## <a name="next-steps"></a>További lépések
Ellenőrizze a következő lépések az Azure rendelkezésre állási zónában üzembe helyezéséhez:

- [Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ az Azure-ban a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP magas rendelkezésre állás egy Windows feladatátvevő fürt és -fájlmegosztást az SAP ASCS/SCS-példányok Azure-infrastruktúra előkészítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






