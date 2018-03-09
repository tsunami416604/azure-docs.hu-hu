---
title: "Egy Azure-régión belül SAP HANA rendelkezésre állási |} Microsoft Docs"
description: "SAP HANA műveleteinek Azure natív virtuális gépeken"
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: 
author: msjuergent
manager: patfilot
editor: 
tags: azure-resource-manager
keywords: 
ms.service: virtual-machines-linux
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/5/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 28fe9bc7c8cb1d59df404b7dd7429def54648a18
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/08/2018
---
# <a name="sap-hana-availability-within-one-azure-region"></a>SAP HANA rendelkezésre állási egy Azure-régión belül
Ebben a szakaszban jelennek meg több forgatókönyveket, amelyek egy Azure-régión belül a rendelkezésre állási forgatókönyvek mutatják be. Azure sok régiókban, ahol a világ minden táján vesznek rendelkezik. Azure-régiók listáját, tekintse át a [Azure-régiókat](https://azure.microsoft.com/regions/) cikk. SAP HANA egy Azure-régión belül virtuális gépeken való telepítése a Microsoft kínál HANA példánnyal egyetlen virtuális gép telepítését. Vagy nagyobb rendelkezésre álláshoz két virtuális gépeken belül két HANA osztályt telepíthet egy [Azure rendelkezésre állási csoport](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets) használ, amely HANA replikációs a rendelkezésre állás érdekében. Azure rendelkezik nyilvános előnézete [Azure rendelkezésre állási zónák](https://docs.microsoft.com/azure/availability-zones/az-overview). A rendelkezésre állási zónák tárgyalja részletek még nem fog. Néhány általános gondolatait a rendelkezésre állási készletek és a rendelkezésre állási zónák használatát körül kivételével.

Mi az Azure rendelkezésre állási készletek és a rendelkezésre állási zónák közötti különbség? Az Azure régiókban, ahol rendelkezésre állási zónák fog kínál a memóriaterületnél több különböző adatközponthoz, áramforrásról, hűtési és hálózati ellátás független. Az ajánlat egyetlen Azure régión belül különböző zónákhoz oka, hogy ahhoz, hogy az alkalmazások központi telepítése a két vagy három rendelkezésre állási zónák kínált között. Feltételezve, hogy áramforrásokból és/vagy hálózati problémák hatással lenne egy rendelkezésre állási zóna infrastruktúra csak, az alkalmazások központi telepítésének belül egy Azure-régió, továbbra is teljes körűen használható. Végül néhány korlátozott kapacitás óta egy zóna az egyes virtuális gépek elvesztésével járhat. De a két zónában virtuális gépek továbbra is működik és elérhető. 
 
Mivel az Azure rendelkezésre állási csoport a logikai csoportosításhoz képessége, amelyik az Azure-ban segítségével győződjön meg arról, hogy a Virtuálisgép-erőforrások belül el egymástól különítve az Azure adatközponton belül telepítésekor hiba. Az Azure biztosítja, hogy a rendelkezésre állási csoportba helyezett virtuális gépek több fizikai kiszolgálón, számítási rackszekrényen, tárolási egységben és hálózati kapcsolón fussanak. Vagy valamilyen más Azure dokumentációja, ahogy azt nevezzük adatközpontokon különböző [frissítés és a tartalék tartományok](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). Ezek a gyakorlatok általában egy Azure-adatközpontban belül. Feltételezve, hogy áramforrásokból és/vagy hálózati problémák hatással lenne a datacenter vannak telepítve, egy Azure-régióban a kapacitás befolyásolhat.

Az elhelyezés, amelyek megfelelnek az Azure rendelkezésre állási zónák adatközpontok továbbítása különböző zónákban, amelyek a legtöbb alkalmazás és az Adatközpont között bizonyos távolságra elfogadható telepített szolgáltatások közötti hálózati késés közötti kompromisszumos megoldásnak. Így természeti katasztrófa ideális nem befolyásolná a kiemelt és hálózati ellátási és ebben a régióban zónákon rendelkezésre állásának infrastruktúra. Azonban, mint emlékművi természeti katasztrófa bemutatta, rendelkezésre állási zónák nem mindig valószínűleg létre tudja a kívánt módon egy régión belül a rendelkezésre állás biztosításához. Gondolja át, amely a sziget Puerto Rico elérte a 08/20/2017 és alapvetően a 90-miles széles sziget okozott egy közel 100 %-os kiesett Maria Hurrikán.   
  


## <a name="single-vm-scenario"></a>Egyetlen VM forgatókönyv
Ebben a forgatókönyvben egy SAP HANA-példány Azure virtuális gép hozott létre. Prémium szintű Azure Storage használta az operációsrendszer-lemez és az adatlemezek üzemeltetésére. 99,9 %-os az Azure-os üzemidőt SLA-t és az egyéb Azure összetevők SLA elegendő ahhoz, hogy a rendelkezésre állási SLA-k felé az ügyfelek teljesítéséhez. Ebben a forgatókönyvben kihasználhatja az Azure rendelkezésre állási csoport a virtuális gépek, az adatbázis-kezelő réteget futtató szükség van. Ebben a forgatókönyvben meg két különböző szolgáltatásait használják:

- Az Azure virtuális gép automatikus újraindítás (Azure Szolgáltatásjavításnak néven is hivatkozott)
- SAP HANA automatikus újraindítás letiltása

Az Azure virtuális gép automatikus újraindítása, vagy "szolgáltatásjavításnak" az egyik funkciója, amely két szinten működik az Azure-ban:

- Azure-kiszolgáló gazdagép, a kiszolgáló állomáson futó virtuális gép állapotának ellenőrzése
- Az Azure Fabric Controller állapotára és a kiszolgáló állomás rendelkezésre állása

Minden Azure-kiszolgáló-gazdagépen futó virtuális gép egy állapotának ellenőrzése funkció figyeli a állapotát a futtatott virtuális gép van. Virtuális gépek tartoznak a nem Kifogástalan állapotba, ha a virtuális gép újraindítását kezdeményezheti az Azure-gazdagép-ügynök, amely a virtuális gép állapotát ellenőrzi. Az Azure Fabric Controller a gazdagép állapotát ellenőrzi a gazdagép kapcsolatos hibákat jelző számos különböző paraméterek ellenőrzésével, de azt is ellenőrzi a kisegítő állomás a hálózaton keresztül. Például az arra utal, hogy probléma van a gazdagépen vezethet:

- A fogadó újraindul, és indítsa újra, ha a gazdagép hibás állapotot jelzi a gazdagépen futó virtuális gépek
- Rendszer újraindítása a gazdagépen és a virtuális gép van, amely eredetileg működtek megfelelő gazdagép a gazdagépen abban az esetben, ha a gazdagép nincs megfelelő állapotban a rendszer újraindítása után újra kell indítani. Ebben az esetben a gazdagép van folyamatban kell lennie kell megjelölni nem kifogástalan, és további központi telepítéseket, amíg nincs bejelölve, illetve lecserélik a nem használt.
- Azonnali újra kell indítani a virtuális gépek a megfelelő gazdagép azokban az esetekben, ahol a nem kifogástalan állapotú gazdagép problémák vannak az újraindítás folyamatban. 

A gazdagép és virtuális gép az Azure által biztosított figyelés, az Azure virtuális gépeken, amelyek a gazdagéppel kapcsolatos problémákat érinti automatikusan újraindulnak a megfelelő Azure-gazdagépre 

A második szolgáltatást használ, ilyen esetben arra, hogy a HANA szolgáltatás egy újraindított ilyen virtuális Gépen belül automatikusan elindul a rendszer a virtuális gép újraindítása után. A [HANA szolgáltatás automatikus újraindítás letiltása](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html) a figyelő szolgáltatások, a különböző HANA szolgáltatások segítségével konfigurálhatók.

Egyetlen VM példánkban sikerült beolvasása javítja a cold feladatátvételi csomópontot ad hozzá egy SAP HANA-konfigurációját. Vagy azt nevezik mint a SAP HANA-dokumentáció [állomás automatikus feladatátvételt](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/ae60cab98173431c97e8724856641207.html). Ez a konfiguráció a helyi telepítés olyan esetekben, ahol a kiszolgáló hardverének korlátozva, és egy önálló kiszolgáló csomópont az éles állomásoknak-állomás automatikus feladatátvételt csomópontként jelölt ki is célszerű. Azonban olyan esetekben, például ha az alkalmazás mögötti infrastruktúra az Azure gondoskodik a megfelelő célkiszolgáló sikeres újra kell indítania a virtuális gépek Azure, a SAP HANA állomás automatikus feladatátvételt forgatókönyv nem célszerű a telepítése. 

Ennek eredményeképpen nincs referencia-architektúrában, amely úgy rendelkezik egy készenléti csomópont a HANA állomás automatikus feladatátvételt kell. Ez az SAP HANA-kibővített konfiguráció is vonatkozik.


## <a name="availability-scenarios-involving-two-different-vms"></a>Használata esetén két különböző virtuális gépek rendelkezésre állási forgatókönyvek
Két Azure virtuális gépeken belül használata Azure rendelkezésre állási készletek lehetővé teszik a növelje a üzemidő között két virtuális gépeken, ha a virtuális gépek Azure rendelkezésre állási csoport belül egy Azure-régióban helyezi. A kiinduló beállítása az Azure-ban az itt látható grafikus néz: ![rétegeket két virtuális gépek](./media/sap-hana-availability-one-region/two_vm_all_shell.PNG)

Ahhoz, hogy bemutassa a másik rendelkezésre állási forgatókönyvek, a fenti rétegek néhány kivágási és a virtuális gépek, gazdagépek, a rétegek korlátozódik grafikus rendelkezésre állási csoportok és az Azure-régiókat. Az Azure Vnetekhez, erőforráscsoport-sablonok és előfizetések nem szerepet a leírt forgatókönyvek esetén.

### <a name="replicating-backups-to-second-virtual-machine"></a>Biztonsági mentések második virtuális gép replikálása
A legtöbb kezdetleges beállításokat egyik biztonsági mentések, különösen akkor tranzakciónapló biztonsági mentései kiadva egy virtuális gép egy másik Azure virtuális gép rendelkezik. A választott típustól függetlenül az Azure Storage rendelkezik. Ön felelős scripting ütemezett biztonsági mentések, a második virtuális gép első virtuális gépen végzett a másolat. Esetén a második virtuális gép példányait igénylő használva kell visszaállítása a teljes/növekményes különbségi és tranzakciónapló biztonsági mentései a van szüksége. Az architektúra alábbihoz hasonlóan fog kinézni: ![tárolóreplikálást két virtuális gépek](./media/sap-hana-availability-one-region/two_vm_storage_replication.PNG) 

A telepítő nem alkalmas túl nagy a helyreállítási Időkorlát és RTO alkalommal elérése érdekében. Különösen RTO alkalommal miatt kellene teljesen állítja vissza a teljes adatbázisról a másolt biztonsági tartoznának csökkenni fog. A telepítő azonban ez használható helyreállítani a fő példányokon nem kívánt adatok törlése a rendszerből. Ezzel le is tudja visszaállítása egy bizonyos idő bármikor hogy kinyeri az adatokat, és a törölt adatok importálása a fő példány. Ezért azt is célszerű ilyen biztonsági másolat módszer együttes használata egyéb magas rendelkezésre állású szolgáltatások. A időszak, amikor a rendszer csak a biztonsági mentések másolja, kaphat együtt egy a fő SAP HANA-nál kisebb méretű példányokat futtatja. De vegye figyelembe, hogy kisebb virtuális gépek van a VHD-k csatolható alacsonyabb értékre. Ellenőrizze [Azure Linux virtuális gépek méretei](https://docs.microsoft.com/azure/virtual-machines/linux/sizes) az egyes Virtuálisgép-típusokon határain.

### <a name="using-sap-hana-system-replication-without-automatic-failover"></a>SAP HANA replikációs automatikus feladatátvétel nélküli használata
A következő forgatókönyvek esetén SAP HANA replikációs használ. SAP kiadott dokumentáció található kezdve a cikk [replikációs](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html). Egy Azure-régió két Azure virtuális gépek között nincsenek két, különböző konfigurációt, néhány eltérések vannak a helyreállítási idő célkitűzése. A forgatókönyvek kialakítását nem rendelkezik az Automatikus feladatátvétel általában nem feltétlenül túl vonatkozó forgatókönyvek belül egy Azure-régiót. Amelyek oka, hogy a legtöbb hiba esetben az Azure-infrastruktúra az Azure szolgáltatásjavításnak lesz az elsődleges virtuális gép egy másik állomáson indítsa újra. Nincsenek csak néhány peremhálózati esetben ha ilyen konfiguráció segíthet hibát jelentő forgatókönyvek tekintetében. Bizonyos esetekben ügyfélként kívánt vagy megvalósításához, különösen a hatékonyság körül.

#### <a name="using-hana-system-replication-without-auto-failover-and-without-data-pre-load"></a>Replikációval HANA rendszer automatikus feladatátvétel és adatok előzetes betöltését nélkül 
Ez egy webfarmos amikor használni SAP HANA replikációs adatok áthelyezése szinkron módon történő elérése a helyreállítási pont célkitűzés (RPO) 0. A másik oldalon rendelkezik egy elég hosszú helyreállítási idő célkitűzés (RTO), tehát a felesleges feladatátvételi vagy az adatok a HANA példány gyorsítótárba előzetes betöltését. Ebben az esetben a további gazdasági meghajtóra történő által a konfigurációs lehetőségek közül választhat:

- A második virtuális gépen, amely a legtöbb a virtuális gép memóriáját egy másik SAP HANA-példány is futtathatja. Általában ilyen példány lenne egy példányát, amely esetén a második virtuális gép feladatátvételi le volt állítva. Igen a replikált adatok betölthető a célzott HANA-példány a második virtuális gép a gyorsítótárba.
- A második virtuális gépként használható kisebb Virtuálisgép-méretet. Feladatátvétel esetén kellene lennie a lépéseket, mielőtt a kézi feladatátvételre ahol a virtuális gép, a forrás virtuális gép méretét kellene átméretezésével. A forgatókönyv néz ki:

![Két virtuális gépeket tároló replikáció](./media/sap-hana-availability-one-region/two_vm_HSR_sync_nopreload.PNG)

> [!NOTE]
> Adatok előzetes betöltését a HANA replikációs cél, nélkül is szüksége legalább 64 GB memóriával és, hogy elég memória a sortárindex adatok megtartása a memóriában a cél-példány felett.

#### <a name="using-hana-system-replication-without-auto-failover-and-with-data-pre-load"></a>HANA replikációs használata nélkül Automatikus feladatátvétel és az adatok előzetes betöltését
A forgatókönyvhöz előtt bevezetett különbség, hogy az adatokat, amelyeket a rendszer replikálja a HANA-példányra, a második virtuális gép előre be van töltve. A két előnyeit, akkor a forgatókönyv nem kell előre betölteni adatok kizárható. Ebben az esetben a második virtuális gép egy másik SAP HANA-rendszeren nem futtatható. Sem használhatja kisebb Virtuálisgép-méretet. Ezért ez egy webfarmos ügyfelek miatt aligha lesz megvalósítva


### <a name="using-sap-hana-system-replication-with-automatic-failover"></a>Automatikus feladatátvétel SAP HANA replikációs használata

A szabványos rendelkezésre állási konfiguráció belül egy Azure-régió, a legtöbb ügyfél SAP HANA a végrehajtási, a konfiguráció ahol SLES Linux operációs rendszert futtató két Azure virtuális gépek van definiálva feladatátvevő fürt. A Linux SLES fürtben alapul a [támasztja](http://www.linux-ha.org/wiki/Pacemaker) keretrendszer együtt egy [STONITH](http://linux-ha.org/wiki/STONITH) eszköz. Egy SAP HANA szempontjából használt replikációs módjának szinkronizálva, és az automatikus feladatátvételre van konfigurálva. A második virtuális Gépet a SAP HANA-példány működik meleg készenléti csomópontja, amely az elsődleges SAP HANA-példány módosítása rekordok szinkron adatfolyam kap. Tranzakciók beolvasása véglegesítve lett az alkalmazás a HANA elsődleges csomóponton, az elsődleges HANA csomópont vár, az alkalmazásnak a véglegesítés megerősítéséhez, amíg az másodlagos SAP HANA-csomópont megerősítve, miután megkapta a végrehajtási rekord. SAP HANA két különböző szinkron replikáció módot. Részletek és eltérések meg e két szinkron replikáció mód, olvassa el a cikk [SAP HANA replikációs replikációs módjainak](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c039a1a5b8824ecfa754b55e0caffc01.html)

Az általános konfigurációs néz

![A tárolási replikációs és feladatátvételi két virtuális gépek](./media/sap-hana-availability-one-region/two_vm_HSR_sync_auto_pre_preload.PNG)

Ez a megoldás van kiválasztva, mivel lehetővé teszi az RPO eléréséhez = 0 és egy rendkívül alacsony RTO alkalommal. Az SAP HANA ügyfélkapcsolat konfigurálja úgy, hogy az SAP HANA-ügyfelek csatlakozhatnak a virtuális IP-címet a HANA rendszer replikációs konfiguráció. Ezzel a megoldással az alkalmazást a másodlagos csomópont feladatátvétel újrakonfigurálása érdekében. Ebben a megoldásban az Azure virtuális gép termékváltozatok az elsődleges vagy másodlagos kell azonosnak lennie.  


## <a name="next-steps"></a>További lépések
Ha részletes útmutatás a konfiguráció, az Azure-ban, olvassa el a cikkek:

- [A telepítő SAP HANA replikációs az Azure virtuális gépeken](sap-hana-high-availability.md)
- [A SAP, a rendszer replikációval SAP Hana-rész 4 – Azure magas rendelkezésre állás](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

Ha Azure-régiók közötti SAP HANA rendelkezésre állásáról további tájékoztatásra van szüksége, olvassa el:

- [Azure-régiók közötti SAP HANA rendelkezésre állása](https://docs.microsoft.com/en-us/azure/virtual-machines/workloads/sap/sap-hana-availability-across-regions) 

