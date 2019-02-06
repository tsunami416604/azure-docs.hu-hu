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
ms.openlocfilehash: 409a304296d3fdff897a203177e2c150162755c6
ms.sourcegitcommit: 947b331c4d03f79adcb45f74d275ac160c4a2e83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/05/2019
ms.locfileid: "55746217"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP munkaterhelés-konfigurációk az Azure-beli rendelkezésre állási zónák

Az Azure-ajánlatokat javíthatja a rendelkezésre állás az SAP számítási feladatok Azure-ban magas rendelkezésre állású funkciókat egyike [Azure-beli rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). A rendelkezésre állási zónák érhetők el a különböző [Azure-régiók](https://azure.microsoft.com/global-infrastructure/regions/) már. Több régió követi. 

SAP magas rendelkezésre állású alapvető architektúrájának írható le ezt az ábrát a név jelenik meg:

![Standard szintű magas rendelkezésre ÁLLÁS konfigurálása](./media/sap-ha-availability-zones/standard-ha-config.png)

Az SAP alkalmazásrétegre helyezünk üzembe egy Azure-ban [rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). SAP Central Services magas rendelkezésre állás érdekében helyezzen üzembe egy külön rendelkezésre állási csoportot két virtuális gépet, és egy magas rendelkezésre állású keretrendszer, amely lehetővé teszi az Automatikus feladatátvétel esetén az infrastruktúra telepítéséhez használhatja Windows feladatátvevő fürt szolgáltatások vagy támasztja (Linux) vagy szoftver probléma. Ilyen például a központi telepítések lista részletező dokumentációja:

- [Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [A fájlmegosztás Windows feladatátvevő fürt a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server az Azure virtuális gépeken SAP-alkalmazások](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Az Azure virtuális gépek magas rendelkezésre állás az SAP NetWeaver a Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

SAP NetWeaver, S/4HANA vagy Hybris rendszerek DBMS rétege hasonló architektúrával vonatkozik. Egy aktív/passzív módban egy Feladatátvevőfürt-megoldást, az adatbázis-kezelő feladatátvételi keretrendszereket, Windows feladatátvevő fürt szolgáltatások vagy támasztja használó feladatátvételi tevékenységek esetén infrastruktúrával vagy szoftverekkel kezdeményezni az adatbázis-kezelő réteg telepítése Hiba történt. 

Az Azure rendelkezésre állási zónák ugyanazon architektúra üzembe helyezéséhez a leírt architektúra néhány módosítást kell végezni. Ez a dokumentum a különböző részeit ismerteti ezeket a módosításokat.

## <a name="considerations-deploying-across-availability-zones"></a>Szempontok a rendelkezésre állási zónában üzembe helyezése

A rendelkezésre állási zónák használja, néhány dolgot figyelembe venni. A szempontok lista, például:

- Az Azure rendelkezésre állási zónák nem biztosít bármely garantálható az egyes egy Azure-régióban a különböző zónák közötti távolság
- Az Azure rendelkezésre állási zónák nem állnak ideális DR-megoldásként. Az időpontokat, ahol hatalmas természeti katasztrófák széles okozó megállítását kárt egyes régiókban a világ, beleértve a nagy kárt power infrastruktúra, különböző zónák közötti távolság előfordulhat, hogy nem lehet elég nagy ahhoz, hogy a megfelelő Vészhelyreállítási megoldás
- A hálózati késést, több különböző Azure rendelkezésre állási zónában a különböző Azure-régióban Azure régiók eltér. Előfordulhatnak olyan esetek, ahol az üzembe helyezett különböző zónák között, mert a hálózati késés egyik zónából az aktív az adatbázis-kezelő virtuális gép továbbra is elfogadható egy folyamat az üzletmenetre, SAP alkalmazásrétegre futtathatja. De bizonyos esetekben néhány Azure régióban, ahol a késés a aktív az adatbázis-kezelő virtuális gép egy zóna és a egy másik zónában az SAP alkalmazáspéldány között lehet-e túl zavaró, és nem fogadható el, az SAP business folyamatok. Ennek eredményeképpen az üzembe helyezési architektúra eltérőnek kell lennie az aktív/aktív architektúra az alkalmazás vagy az aktív/passzív architektúra, ahol a zónák közötti késés értéke túl magas.
- Győződjön meg arról, melyik konfiguráció is használhat az Azure rendelkezésre állási zónák a döntés. A hálózati késés, a különböző zónák között mérés alapján. A hálózati késés két különböző területein fontos szerepet játszik:
    - A két adatbázis-kezelő példányt, amely egy létre szinkron replikáció rendelkeznie kell a késés. Minél nagyobb a hálózati késés, a számítási feladatok hatását méretezhetőségét, annál nagyobb a lehetséges
    - A hálózati késés közötti különbség ugyanabban a zónában, mint az aktív DBMS-példány egy SAP párbeszédpanel példányt futtató virtuális gép és a egy hasonló virtuális Gépet egy másik zónában. Minél nagyobb ez a különbség, annál magasabb gyakorolt futtatáskor az üzleti folyamatok és kötegelt feladatok, függ, hogy fusson-e az adatbázis-kezelő az ugyanabban a zónában, vagy egy másik zónában


Az Azure-funkciók használata, kezdődően, amely is használható, ha az Azure virtuális gépek üzembe helyezéséhez a zónák és a Feladatátvevőfürt-megoldásokat létrehozó különböző rendelkezésre állási zónában, ugyanabban az Azure-régióban bizonyos korlátozások vonatkoznak. Ezek a korlátozások sorolja fel, például:

- Használatával [Azure managed disks](https://azure.microsoft.com/services/managed-disks/) kötelező az Azure rendelkezésre állási zónában történő üzembe helyezéséhez 
- Az Azure-előfizetés alapján leképezése a fizikai zóna zóna enumerálások rögzítve van. Ha eltérő előfizetésekben az SAP-rendszerek központi telepítéséhez használ, szeretné-e a ideális zónákat az egyes előfizetésekhez külön-külön megadása
- Nem telepíthet egy Azure rendelkezésre állási zónán belüli Azure rendelkezésre állási csoportokat. Válasszon egy Azure rendelkezésre állási zónában vagy az Azure rendelkezésre állási csoport, a virtuális gépek deployment keretrendszer.
- Nem használhat egy [Azure alapszintű Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) megoldásokat hozhat létre feladatátvevő fürt feladatátvételi fürtszolgáltatások Windows vagy Linux rendszerű támasztja alapján. Ehelyett kell használnia a [Azure Standard Load Balancer Termékváltozat](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="ideal-zone-combination"></a>Ideális zóna kombinációja
Döntse el, a rendelkezésre állási zónák használata, kell, amelyen vizsgálat végrehajtása:

- A hálózati késést, egy Azure-régióhoz a három különböző zónák között. Tehát, hogy kiválaszthatja a zónák közötti hálózati forgalom zóna a legalacsonyabb hálózati késéssel rendelkező
- A különbség az Ön által választott zónák telefonszámaira belüli virtuális gép virtuális gép késés és a hálózati késést a választott két zónák között
- Egy nyilatkozatot, hogy a virtuális gép típusokat, hogy szeretne-e a kiválasztott rendelkezésre állási zónák között telepített érhetők el a két zónákat a választott. Különösen az M sorozat virtuális gépei fog észlel olyan helyzetekben, ahol a különböző M sorozatú virtuális gépek Termékváltozatait gazdakiszolgálói lesznek a három zóna közül kettő érhető el

### <a name="network-latency-between-zones-and-within-zone"></a>Zónán belül és a zónák közötti hálózati késés
Ismerje meg, hogy a késés, a különböző zónák között van, akkor kell:

- Helyezze üzembe az összes három zónát a DBMS példány használni kívánt virtuális gép Termékváltozata. Győződjön meg arról, hogy [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van ez a mérés végrehajtásakor
- A két zónákat a legalacsonyabb hálózati késéssel rendelkező megkereséséhez, üzembe helyezése egy másik három virtuális gépet a VM-termékváltozat használja, mint a három rendelkezésre állási zónák között a virtuális gép alkalmazási rétegre szeretne. A két különböző "DBMS" zónában a választott mérhetők a két "adatbázis-kezelő virtuális gép" a hálózati késést. 
- Mérhető eszközként használni **niping**. Egy eszközt, a SAP, amely az SAP támogatási megjegyzések leírtak szerint működik [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A parancsok késési mérések feljegyezni SAP koncentrálhat. Használatával **ping** egy ajánlott eszköz óta nem **ping** nem működik az Azure-on keresztül a gyorsított hálózati kódhoz tartozó elérési út.

A mértékek és a virtuális gépek Termékváltozatait a különböző zónák rendelkezésre állásának alapján kell a döntéseket:

- Az ideális zónák a DBMS-réteg megadása
- Válaszolja meg a kérdést, hogy a hálózati késés zónán belül különbségek alapján, vagy több zónában, kiadhatja az SAP aktív alkalmazásréteg egy, két vagy három különböző zónák között
- Válaszolja meg a kérdést, (tekintse meg később) egy aktív/passzív vagy egy aktív-aktív konfiguráció az alkalmazás szempontjából telepíteni szeretné-e

Ezek a döntések meghozatalában is visszahívása SAP hálózati késés javaslatok SAP-jegyzetnek leírt [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E).

### <a name="be-aware-of"></a>Vegye figyelembe

> [!IMPORTANT]
> A mértékek és hozott döntések, így ezeknek a méréseknek használt Azure-előfizetéshez tartozó érvényesek. Meg kell ismételni a mérések, mert az előfizetés függő enumerálás zónák leképezése módosíthatja egy másik előfizetés egy másik Azure-előfizetése


> [!IMPORTANT]
> Valószínű, hogy a méreteket, ahogy a fenti megjelennek a különböző eredményeket minden Azure-régióban, amely támogatja a [rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). A követelmények a hálózati késés nem változott, mellett is szüksége lehet alkalmazkodnak a különböző központi telepítési stratégiát különböző Azure-régióban, mivel a zónák közötti hálózati késés eltérő lehet. Az egyes Azure-régióban, a három különböző zónák közötti hálózati késés, hogy a teljesen más várható. Míg más régiókban a hálózati késést a három különböző zónák között egységesebb. A jogcím, mivel **mindig** egy 1 ezredmásodpercre 2 ezredmásodperc, a zónák közötti hálózati késés **helytelen**. A hálózati késést, rendelkezésre állási zónában az Azure-régióban nem általánosíthatók.


## <a name="activeactive-deployment"></a>Aktív/aktív központi telepítés
Az üzembe helyezési architektúra neve aktív/aktív, mert két vagy három zónák között az aktív SAP párbeszédpanel-példányok üzembe helyezésekor. A sorba replikációs SAP Central Services rendszer fog üzembe helyezni két zónák között. Ugyanez vonatkozik az adatbázis-kezelő szintje, amely telepíthető, mint az SAP Central szolgáltatás ugyanazon zónákban történik.

Az ilyen konfiguráció fontolóra, meg kell keresnie a két rendelkezésre állási zónák közötti zóna hálózati késés a szinkron replikációhoz az adatbázis-kezelő és a számítási feladatok számára elfogadható kínáló régiója. Továbbá azt szeretné, hogy a különbözeti közötti hálózati késés a kiválasztott zónák belül, és a különböző zóna hálózati késés nem túl nagy. Az utóbbi OK, hogy az üzleti folyamatok futtatási idejének a túl nagy változások nem szeretné, vagy a batch a függő a dolgozza fel, hogy egy feladat fut a-zónát az adatbázis-kezelő kiszolgáló vagy zóna között. Különböző módszerekkel elfogadhatók, azonban nincs különbség a tényezők.

Az aktív/aktív központi két zónában egy egyszerűsített séma nézhet:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- Kezeli az Azure rendelkezésre állási zónák tartalék és frissítési tartományok az összes virtuális gép, mert a rendelkezésre állási csoportok nem telepíthetők az Azure rendelkezésre állási zónák
- Az Azure load Balancer terheléselosztók használ a feladatátvevő fürtök, az SAP Central Services, valamint az adatbázis-kezelő réteg, kell lennie a [Standard Termékváltozatú terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű load balancer nem fog működni a zónák között
- Az Azure virtuális hálózat és az alhálózatok, az SAP-rendszer üzemeltetéséhez telepített zónákban kiterjesztve tárolja. Ön **nem kell** külön az egyes zónák virtuális hálózatok
- Minden virtuális gép központi telepítése, használata [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek használata nem támogatott a zónaszintű központi telepítésekhez
- Az Azure Premium storage vagy [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatnak bármilyen típusú tárreplikáció zónák között. Ennek eredményeképpen feladata az alkalmazás (adatbázis-kezelő vagy SAP Central Services) fontos adatok replikálása
- Ugyanez vonatkozik a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows), vagy az NFS-megosztások (Linux). Eljárás, amelyet a rendszer replikál egy megosztott lemez vagy fájlmegosztás a zónák közötti kell. Jelenleg a következő technológiákat támogatja:
    - A Windows, egy adott megoldást, amely használja az SIOS Datakeeper leírtak szerint [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) támogatott zónák között
    - SUSE Linux rendszeren az NFS-megosztások beépített leírtak szerint [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) támogatott
    - Ezen a ponton az időben, a szolgáltatással Windows kibővített fájlkiszolgálókon (SOFS) leírtak szerint megoldás [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **zónák között nem támogatott**
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [SUSE Linux-alapú támasztja fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok
- Futási idő konzisztencia bizonyos kritikus fontosságú üzleti tranzakciók és/vagy a feladatok eléréséhez. Próbálkozzon a közvetlen egyes batch-feladatok és a felhasználók közvetlenül az adott alkalmazás-példányok zónában az aktív DBMS-példánnyal rendelkező SAP Batch kiszolgálócsoportok, bejelentkezési vagy RFC csoportok használatával. Azonban a zónaszintű feladatátvétel esetén kell ezeket a csoportokat manuálisan át párbeszédpanel-példányokra, amelyek szerepelnek a fennmaradó van(nak) 
- Döntse el, hogy szeretné-e inaktív párbeszédpanel példányaihoz kell helyeznie azonnal korábbi erőforrás-kapacitása, abban az esetben egy, az alkalmazáspéldányok részeként üzembe helyezett zóna nem működik a zónák mindegyikében üzembe


## <a name="activepassive-deployment"></a>Aktív/passzív üzembe helyezés
Azokban az esetekben, ahol már nem jelentenek a hálózati késés belül egy zónát, és több zóna hálózati forgalom között egy elfogadható eltelt telepítheti az architektúra van egy aktív/passzív karaktert, a SAP alkalmazás réteg szempontjából. Megadhat egy "aktív" zónában, amelyet a zóna telepít, a teljes alkalmazás réteget, és ahol megkísérli futtatni az aktív DBMS-példány és az aktív SAP Central Services-példányt. Az ilyen konfiguráció biztosíthatja, hogy nem rendelkezik a szélsőséges futási idő különbség az üzleti tranzakciók és kötegelt feladatok, hogy egy feladat fut-e ugyanabban a zónában, az aktív DBMS-példánnyal rendelkező vagy nem függ.

Az ilyen architektúra alapszintű elrendezését hasonlóan néz ki:

![active_active_xone_deployment](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- A rendelkezésre állási csoportok nem telepíthető az Azure rendelkezésre állási zónákban. Ezért ebben az esetben befejezése esetén egy frissítési és a tartalék tartomány az alkalmazásréteg számára. Oka, hogy csak telepítik egy zónát. Ez a konfiguráció egy kis mértékben setback képest a referencia-architektúra, amely úgy rendelkezik, hogy telepít-e az alkalmazási rétegre egy Azure rendelkezésre állási csoportban.
- Az ilyen architektúra működő, kell végezzen alapos figyelést, és próbálja meg az aktív az adatbázis-kezelő és az SAP Central services-példányok az üzembe helyezett alkalmazás rétegként ugyanabban a zónában. SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétel esetén győződjön meg arról, hogy manuálisan helyezésnél újra üzembe a zóna az SAP alkalmazásrétegre, amilyen hamar csak lehet telepítve a kívánt
- Az Azure load Balancer terheléselosztók használ a feladatátvevő fürtök, az SAP Central Services, valamint az adatbázis-kezelő réteg, kell lennie a [Standard Termékváltozatú terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű load balancer nem fog működni a zónák között
- Az Azure virtuális hálózat és az alhálózatok, az SAP-rendszer üzemeltetéséhez telepített zónákban kiterjesztve tárolja. Ön **nem kell** külön az egyes zónák virtuális hálózatok
- Az összes virtuális gépet telepít, meg kell használnia [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek használata nem támogatott a zónaszintű központi telepítésekhez
- Az Azure Premium storage vagy [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatnak bármilyen típusú tárreplikáció zónák között. Ennek eredményeképpen feladata az alkalmazás (adatbázis-kezelő vagy SAP Central Services) fontos adatok replikálása
- Ugyanez vonatkozik a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows), vagy az NFS-megosztások (Linux). Eljárás, amelyet a rendszer replikál egy megosztott lemez vagy fájlmegosztás a zónák közötti kell. Jelenleg a következő technológiákat támogatja:
    - A Windows, egy adott megoldást, amely használja az SIOS Datakeeper leírtak szerint [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) támogatott zónák között
    - SUSE Linux rendszeren az NFS-megosztások beépített leírtak szerint [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) támogatott
    - Ezen a ponton az időben, a szolgáltatással Windows kibővített fájlkiszolgálókon (SOFS) leírtak szerint megoldás [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **zónák között nem támogatott**
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [SUSE Linux-alapú támasztja fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok
- A zóna hiba esetén az alkalmazás-erőforrások indul el (a egy adatbázis-kezelő szempontjából) a passzív zónát inaktív virtuális gépek üzembe helyezése
    - Nem használhat [Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) az aktív virtuális gépek replikálása az inaktív virtuális gépek zónák között. Nem sikerül teljesítéséhez egy adott függvény ezen a ponton a időben történő, Azure Site Recovery
- Az automation, amely lehetővé teszi, hogy zóna meghibásodása, automatikus indítását a második zónában az SAP alkalmazásrétegre helyre költeni

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Magas rendelkezésre állású és vész-helyreállítási konfiguráció együttesen
Annak ellenére, hogy a Microsoft nem lehetővé teszi bármilyen információ az a Funkciók, amelyek egy bizonyos Azure-régióban különböző Azure rendelkezésre állási zónák közötti földrajzi távolságtól, az egyes ügyfelek kombinált magas rendelkezésre ÁLLÁSÚ és Vészhelyreállítási konfiguráció zónák vannak kihasználva, biztosítja a **R**helyreállítási **P**onthoz **O**bjective (RPO) értéke nulla. Ami azt jelenti, hogy kell nem vesznek el még akkor is, a vész-helyreállítási eset adatbázisban véglegesített tranzakciók. 

> [!NOTE]
> Ilyen konfiguráció ajánlott csak bizonyos körülmények között. Például esetekben, ahol adatok nem hagyhatják el az Azure-régió biztonsági és megfelelőségi okok miatt. 

Milyen sebességgel nézhet például mutatják be ezt az ábrát:

![combined_ha_dr_in_zones](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

A következő szempontokat kell figyelembe ehhez a konfigurációhoz:

- Ön vagy feltételezve, hogy egy rendelkezésre állási zónát üzemeltető a létesítmények közötti jelentős távolságra van. Vagy egy bizonyos Azure-régióban maradjon, kényszerítve. A rendelkezésre állási csoportok nem telepíthető az Azure rendelkezésre állási zónákban. Ezért ebben az esetben befejezése esetén egy frissítési és a tartalék tartomány az alkalmazásréteg számára. Oka, hogy csak telepítik egy zónát. Ez az egy setback képest a referencia-architektúra, amely úgy rendelkezik, hogy telepít-e az alkalmazási rétegre egy Azure rendelkezésre állási csoportban.
- Az ilyen architektúra működő, kell végezzen alapos figyelést, és próbálja meg az aktív az adatbázis-kezelő és az SAP Central services-példányok az üzembe helyezett alkalmazás rétegként ugyanabban a zónában. SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétel esetén győződjön meg arról, hogy manuálisan helyezésnél újra üzembe a zóna az SAP alkalmazásrétegre, amilyen hamar csak lehet telepítve a kívánt
- Éles környezetben az alkalmazáspéldányok a QA aktív alkalmazáspéldányok futtató virtuális gépek előre telepítve van.
- A zónaszintű hiba esetén a QA alkalmazáspéldányok leállítja, majd indítsa el az éles példányokba helyette. Ne feledje, hogy együttműködik az alkalmazáspéldányok működnek virtuális nevét kell
- Az Azure load Balancer terheléselosztók használ a feladatátvevő fürtök, az SAP Central Services, valamint az adatbázis-kezelő réteg, kell lennie a [Standard Termékváltozatú terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones). Az alapszintű load balancer nem fog működni a zónák között
- Az Azure virtuális hálózat és az alhálózatok, az SAP-rendszer üzemeltetéséhez telepített zónákban kiterjesztve tárolja. Ön **nem kell** külön az egyes zónák virtuális hálózatok
- Az összes virtuális gépet telepít, meg kell használnia [Azure managed disks](https://azure.microsoft.com/services/managed-disks/). Nem felügyelt lemezek használata nem támogatott a zónaszintű központi telepítésekhez
- Az Azure Premium storage vagy [Ultranagy SSD-tárolóval](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatnak bármilyen típusú tárreplikáció zónák között. Ennek eredményeképpen feladata az alkalmazás (adatbázis-kezelő vagy SAP Central Services) fontos adatok replikálása
- Ugyanez vonatkozik a megosztott sapmnt könyvtár, amely egy megosztott lemezt (Windows), egy CIFS megosztáson (Windows), vagy az NFS-megosztások (Linux). Eljárás, amelyet a rendszer replikál egy megosztott lemez vagy fájlmegosztás a zónák közötti kell. Jelenleg a következő technológiákat támogatja:
    - A Windows, egy adott megoldást, amely használja az SIOS Datakeeper leírtak szerint [egy SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürtre a fürt fürt megosztott lemez használatával az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk) támogatott zónák között
    - SUSE Linux rendszeren az NFS-megosztások beépített leírtak szerint [magas rendelkezésre állás NFS, a SUSE Linux Enterprise Server Azure virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs) támogatott
    - Ezen a ponton az időben, a szolgáltatással Windows kibővített fájlkiszolgálókon (SOFS) leírtak szerint megoldás [előkészítése Azure-infrastruktúra az SAP az SAP ASCS/SCS példányhoz egy Windows feladatátvevő fürt és a fájlmegosztást a magas rendelkezésre állású](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share) **zónák között nem támogatott**
- A harmadik zónákban történik az SBD eszköz futtatására, abban az esetben létrehozhat egy [SUSE Linux-alapú támasztja fürt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányok





## <a name="next-steps"></a>További lépések
Ellenőrizze a következő lépések az Azure rendelkezésre állási zónában üzembe helyezéséhez:

- [Egy Windows rendszerű feladatátvevő fürtre a fürt megosztott lemezt használ az Azure-ban a fürt egy SAP ASCS/SCS példányhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP magas rendelkezésre állás egy Windows feladatátvevő fürt és -fájlmegosztást az SAP ASCS/SCS-példányok Azure-infrastruktúra előkészítése](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






