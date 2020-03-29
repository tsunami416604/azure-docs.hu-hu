---
title: SAP-számítási feladatok konfigurációi az Azure rendelkezésre állási zónáival | Microsoft dokumentumok
description: Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver számára az Azure rendelkezésre állási zónáinak használatával
services: virtual-machines-windows,virtual-network,storage
documentationcenter: saponazure
author: msjuergent
manager: bburns
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 887caaec-02ba-4711-bd4d-204a7d16b32b
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 03/05/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: a7a92bef85cd4ee7530940a065135e88c7530781
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78675611"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP számításifeladat-konfigurációk az Azure Availability Zones szolgáltatással
[Az Azure rendelkezésre állási zónái](https://docs.microsoft.com/azure/availability-zones/az-overview) az Azure által biztosított magas rendelkezésre állású funkciók egyike. A rendelkezésre állási zónák használata javítja az SAP-számítási feladatok általános rendelkezésre állását az Azure-ban. Ez a funkció már elérhető egyes [Azure-régiókban.](https://azure.microsoft.com/global-infrastructure/regions/) A jövőben több régióban is elérhető lesz.

Ez az ábra az SAP magas rendelkezésre állásának alapvető architektúráját mutatja:

![Normál magas rendelkezésre állású konfiguráció](./media/sap-ha-availability-zones/standard-ha-config.png)

Az SAP alkalmazásréteg egyetlen Azure [rendelkezésre állási csoportban](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)van telepítve. Az SAP Központi szolgáltatások magas rendelkezésre állása érdekében két virtuális gépet telepíthet egy külön rendelkezésre állási csoportban. Használja a Windows Server feladatátvételi fürtözést vagy -pacemakert (Linux) magas rendelkezésre állású keretrendszerként, amely infrastruktúra- vagy szoftverprobléma esetén automatikus feladatátvételt biztosít. Ezekről a központi telepítésekről a következő témakörökben olvashat bővebben:

- [SAP ASCS/SCS-példány fürt fürt megosztott lemez használatával történő fürtjének fürtfeladat-átvételi fürtjének fürtje](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP ASCS/SCS-példány fürtje Windows feladatátvevő fürtön fájlmegosztás használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Az SAP NetWeaver magas rendelkezésre állása az Azure-beli virtuális gépeken a SUSE Linux Enterprise Server SAP-alkalmazásokhoz szolgáltatásban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver számára a Red Hat Enterprise Linux on](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Hasonló architektúra vonatkozik az SAP NetWeaver, S/4HANA vagy Hybris rendszerek DBMS-rétegére. Az ADATBÁZIS-réteget aktív/passzív módban, feladatátvevő fürtmegoldással telepíti az infrastruktúra vagy a szoftvermeghibásodás elleni védelem érdekében. A feladatátvevő fürtmegoldás lehet DBMS-specifikus feladatátvételi keretrendszer, Windows Server feladatátvételi fürtözés vagy pacemaker.

Ha ugyanazt az architektúrát szeretné üzembe helyezni az Azure rendelkezésre állási zónáival, néhány módosítást kell végrehajtania a korábban vázolt architektúrán. Ez a cikk ismerteti ezeket a változásokat.

## <a name="considerations-for-deploying-across-availability-zones"></a>A rendelkezésre állási zónák közötti üzembe helyezés szempontjai


A rendelkezésre állási zónák használatakor vegye figyelembe az alábbiakat:

- Nincs garancia az Azure-régión belüli különböző rendelkezésre állási zónák közötti távolságokra vonatkozóan.
- A rendelkezésre állási zónák nem ideális VÉSZ-megoldás. A természeti katasztrófák széles körű károkat okozhatnak a világ régióiban, beleértve a villamosenergia-infrastruktúrák súlyos károsodását is. Előfordulhat, hogy a különböző zónák közötti távolság nem elég nagy ahhoz, hogy megfelelő VÉSZ-megoldást képezzene.
- A hálózati késés a rendelkezésre állási zónák között nem ugyanaz az Azure-régiókban. Bizonyos esetekben telepítheti és futtathatja az SAP-alkalmazásréteget különböző zónákközött, mert a hálózati késés az egyik zónából az aktív DBMS virtuális gépbe elfogadható. Egyes Azure-régiókban azonban előfordulhat, hogy az aktív DBMS virtuális gép és az SAP-alkalmazáspéldány közötti késés, ha különböző zónákban van telepítve, előfordulhat, hogy nem elfogadható az SAP üzleti folyamatok számára. Ezekben az esetekben a központi telepítési architektúrának eltérőnek kell lennie, az alkalmazás aktív/aktív architektúrájával vagy egy aktív/passzív architektúrával, ahol a zónaközi hálózati késés túl magas.
- A rendelkezésre állási zónák használatának eldöntésekor a döntést a zónák közötti hálózati késésre alapozhatja. A hálózati késés két területen játszik fontos szerepet:
    - Késés a két DBMS-példány között, amelyeknek szinkron replikációra van szükségük. Minél magasabb a hálózati késés, annál valószínűbb, hogy befolyásolja a számítási feladatok méretezhetőségét.
    - A hálózati késés különbség egy SAP-párbeszédpanel-példányt futtató virtuális gép és az aktív DBMS-példány és egy másik zónában lévő hasonló virtuális gép közötti különbség. A különbség növekedésével az üzleti folyamatok és a kötegelt feladatok futási idejére gyakorolt hatás is nő, attól függően, hogy zónán kívül futnak-e a DBMS-sel vagy egy másik zónában.

Ha az Azure virtuális gépeket a rendelkezésre állási zónák között telepíti, és feladatátvételi megoldásokat hoz létre ugyanabban az Azure-régióban, bizonyos korlátozások érvényesek:

- [Az Azure-ban felügyelt lemezeket kell használnia,](https://azure.microsoft.com/services/managed-disks/) amikor az Azure rendelkezésre állási zónáiba telepíti. 
- A zónaenumerálások hozzárendelése a fizikai zónákhoz Azure-előfizetési alapon van rögzítve. Ha különböző előfizetéseket használ az SAP-rendszerek üzembe helyezéséhez, meg kell határoznia az egyes előfizetésekhez ideális zónákat.
- Az Azure rendelkezésre állási csoportjai csak akkor helyezhetők üzembe az Azure rendelkezésre állási zónáján belül, ha [az Azure proximity placement group](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)szolgáltatást használja. Az SAP DBMS-réteg és a központi szolgáltatások zónák közötti üzembe helyezésének, valamint az SAP-alkalmazásréteg rendelkezésre állási csoportok használatával történő központi telepítésének módját az Azure közelségelhelyezési csoportjai nak az [SAP-alkalmazásokkal való optimális hálózati késés érdekében](sap-proximity-placement-scenarios.md)című cikke dokumentálja. Ha nem kihasználva az Azure közelségelhelyezési csoportok, ki kell választania az egyik vagy a másik, mint a virtuális gépek üzembe helyezési keretrendszer.
- Az Azure Basic [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nem használható feladatátvevő fürtmegoldások létrehozásához Windows Server feladatátvételi fürtözés vagy Linux-pacemaker alapján. Ehelyett az [Azure Standard Load Balancer Termékváltozatot kell használnia.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)



## <a name="the-ideal-availability-zones-combination"></a>Az ideális elérhetőségi zónák kombinációja
Mielőtt eldöntené, hogyan használja a rendelkezésre állási zónákat, meg kell határoznia:

- A hálózati késés egy Azure-régió három zónája között. Ez lehetővé teszi, hogy válassza ki a zónák at a legkevesebb hálózati késés a zónaközi hálózati forgalom.
- A virtuális gép és a virtuális gép közötti késés különbsége az Ön által választott zónák egyikén belül, és a hálózati késés két kiválasztott zónán keresztül.
- Annak meghatározása, hogy a virtuális gép telepítéséhez szükséges virtuálisgép-típusok elérhetők-e a kiválasztott két zónában. Egyes virtuális gépek, különösen az M sorozatú virtuális gépek, előfordulhat, hogy olyan helyzetekben, ahol néhány SU-k érhetők el csak két a három zónából.

## <a name="network-latency-between-and-within-zones"></a>Hálózati késés a zónák között és azokon belül
A különböző zónák közötti késés meghatározásához a következőket kell tenni:

- Telepítse a virtuális gép termékváltozatának a KARSZtos példány mindhárom zónában használni kívánt virtuális gép termékváltozatának üzembe helyezése. Győződjön meg arról, hogy az [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van, ha ezt a mérést.
- Ha megtalálja a két zóna a legkisebb hálózati késés, üzembe helyezése további három virtuális gép a virtuális gép termékváltozat, amely szeretné használni, mint az alkalmazásréteg virtuális gép a három rendelkezésre állási zónák között. Mérje meg a hálózati késést a két DBMS-virtuális géphez a két kijelölt DBMS-zónában. 
- Használja **a niping-ot** mérőeszközként. Ez az eszköz az SAP-tól az SAP támogatási jegyzeteiben [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)ismerteti. Összpontosítson a késésméréshez dokumentált parancsokra. Mivel a **ping** nem működik az Azure accelerated networking kód elérési útjain, nem javasoljuk, hogy használja azt.

Ezeket a teszteket nem kell manuálisan elvégeznie. A PowerShell-eljárás [rendelkezésre állási zóna késési tesztje,](https://github.com/Azure/SAP-on-Azure-Scripts-and-Utilities/tree/master/AvZone-Latency-Test) amely automatizálja a leírt késési teszteket. 

A mérések és a virtuálisgép-termékkivék rendelkezésre állása alapján a rendelkezésre állási zónákban néhány döntést kell hoznia:

- Adja meg az ideális zónákat a DBMS-réteghez.
- Határozza meg, hogy az aktív SAP-alkalmazásréteget egy, két vagy mindhárom zónában szeretné-e elosztani a zónán belül a zónák közötti különbségek alapján.
- Határozza meg, hogy aktív/passzív vagy aktív/aktív konfigurációt kíván-e telepíteni alkalmazási szempontból. (Ezeket a konfigurációkat a cikk későbbi részében ismertetjük.)

Ezeknek a döntéseknek a meghozatalakor figyelembe kell venni az SAP hálózati késésre vonatkozó ajánlásait is, ahogy azt az SAP [megjegyzése #1100926.](https://launchpad.support.sap.com/#/notes/1100926/E)

> [!IMPORTANT]
> A mérések és a döntéseket, hogy érvényes az Azure-előfizetés, amelyet a mérések során használt. Ha egy másik Azure-előfizetést használ, meg kell ismételnie a méréseket. A felsorolt zónák leképezése eltérő lehet egy másik Azure-előfizetésesetében.


> [!IMPORTANT]
> A korábban ismertetett mérések várhatóan különböző eredményeket adnak minden Olyan Azure-régióban, amely támogatja [a rendelkezésre állási zónákat.](https://docs.microsoft.com/azure/availability-zones/az-overview) Még akkor is, ha a hálózati késési követelmények azonosak, előfordulhat, hogy különböző Azure-régiókban különböző telepítési stratégiákat kell alkalmaznia, mert a zónák közötti hálózati késés eltérő lehet. Egyes Azure-régiókban a három különböző zóna közötti hálózati késés jelentősen eltérő lehet. Más régiókban a három különböző zóna közötti hálózati késés egységesebb lehet. Az az állítás, hogy mindig van egy hálózati késés 1 és 2 ezredmásodperc között nem helyes. A hálózati késés az Azure-régiók rendelkezésre állási zónái között nem általánosítható.

## <a name="activeactive-deployment"></a>Aktív/aktív telepítés
Ezt a telepítési architektúrát aktívnak/aktívnak nevezzük, mert az aktív SAP-alkalmazáskiszolgálókat két vagy három zónára telepíti. Az SAP központi szolgáltatások példánya, amely enqueue replikációt használ, két zóna között lesz telepítve. Ugyanez igaz a DBMS-rétegre is, amely az SAP Központi szolgáltatással azonos zónákban lesz telepítve.

Ha ezt a konfigurációt mérlegeli, meg kell találnia a régió két rendelkezésre állási zónás hálózati késést kínáló két rendelkezésre állási zónát, amely elfogadható a számítási feladatok és a szinkron DBMS-replikáció számára. Azt is meg szeretné győződni arról, hogy a kijelölt zónákon belüli hálózati késés és a zónaközi hálózat késése közötti különbözet nem túl nagy. Ennek az az oka, hogy nem szeretne nagy változatokat, attól függően, hogy egy feladat zónán kívül fut-e a DBMS-kiszolgálóval vagy zónák között, az üzleti folyamatok vagy kötegelt feladatok futási idejében. Egyes eltérések elfogadhatóak, de a különbség tényezői nem.

Egy aktív/aktív telepítés egyszerűsített sémája két zónában a következőképpen nézhet ki:

![Aktív/aktív zóna telepítése](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Erre a konfigurációra a következő szempontok vonatkoznak:

- Nem használja az [Azure Proximity Placement Group,](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)az Azure rendelkezésre állási zónák, mint a hiba és a frissítési tartományok az összes virtuális gép, mert a rendelkezésre állási készletek nem telepíthető az Azure rendelkezésre állási zónákban.
- Ha a DBMS-réteg és a központi szolgáltatások zónaszintű központi telepítéseit szeretné kombinálni, de az azure-beli rendelkezésre állási készleteket szeretné használni az alkalmazásréteghez, az Azure közelségi csoportjait az Azure közelségi elhelyezési csoportjai című cikkben leírtak szerint kell használnia az [SAP-alkalmazásokkal való optimális hálózati késés érdekében.](sap-proximity-placement-scenarios.md)
- Az SAP Központi szolgáltatások feladatátvételi fürtjeinek és a DBMS-rétegnek a terheléselosztók esetében a [standard termékváltozat Azure load balancer-t kell használnia.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) Az alapszintű terheléselosztó nem működik zónákon keresztül.
- Az Azure virtuális hálózat, amely az SAP-rendszer üzemeltetésére telepített, annak alhálózataival együtt, zónák között van elosztva. Az egyes zónákhoz nem kell külön virtuális hálózatokat igényelni.
- Az összes üzembe helyezett virtuális géphez az Azure Felügyelt lemezek et kell [használnia.](https://azure.microsoft.com/services/managed-disks/) A nem felügyelt lemezek nem támogatottak a zónaszintű telepítésekhez.
- Az Azure Premium Storage és [az Ultra SSD-tárhely](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatja a zónák közötti tárhelyreplikációt. Az alkalmazásnak (DBMS vagy SAP Központi szolgáltatások) fontos adatokat kell replikálnia.
- Ugyanez igaz a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy a zónák közötti megosztásokat. Ezek a technológiák támogatottak:
  - Windows esetén a SIOS DataKeeper szolgáltatást használó fürt, a [fürtben egy SAP ASCS/SCS-példány egy Windows feladatátvételi fürtön, az Azure-ban megosztott fürt használatával.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
  - SUSE Linux esetén egy NFS-megosztás, amely az NFS magas rendelkezésre állású szolgáltatásában készült [a SUSE Linux Enterprise Server en lévő Azure-beli virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    
    Jelenleg a megoldás, amely a Microsoft Scale-Out File Server, dokumentált [Előkészíti az Azure-infrastruktúra SAP magas rendelkezésre állású egy Windows feladatátvételi fürt és fájlmegosztás SAP ASCS/SCS példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)használatával nem támogatott zónák között.
- A harmadik zóna az SBD-eszköz üzemeltetésére szolgál arra az esetre, ha [SUSE Linux pacemaker fürtöt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányokat hoz létre.
- A kritikus üzleti folyamatok futási idejű konzisztenciájának elérése érdekében megpróbálhat bizonyos kötegelt feladatokat és felhasználókat olyan alkalmazáspéldányokra irányítani, amelyek az aktív DBMS-példánysal zónán kívül vannak SAP kötegkiszolgálói csoportok, SAP-bejelentkezési csoportok vagy RFC-csoportok használatával. Azonban egy zónaszintű feladatátvétel esetén manuálisan kell áthelyezni ezeket a csoportokat az aktív DB virtuális géptel zónán kívül lévő virtuális gépeken futó példányokra.  
- Előfordulhat, hogy szeretné telepíteni alvó párbeszédpéldányok az egyes zónákban. Ez lehetővé teszi a korábbi erőforrás-kapacitás azonnali visszatérését, ha az alkalmazáspéldányok egy része által használt zóna üzemen kívül van.

> [!IMPORTANT]
> Ebben az aktív/aktív forgatókönyvben a Microsoft 2020/01/04-től további sávszélesség-díjakat jelent be. Ellenőrizze a dokumentumot [Sávszélesség árképzési részletek](https://azure.microsoft.com/pricing/details/bandwidth/). Az SAP alkalmazásréteg és az SAP DBMS-réteg közötti adatátvitel meglehetősen intenzív. Ezért az aktív /aktív forgatókönyv hozzájárulhat a költségekhez egy kicsit. Tartsa ellenőrzése ezt a cikket, hogy a pontos költségek 


## <a name="activepassive-deployment"></a>Aktív/passzív telepítés
Ha nem talál elfogadható különbözetet az egyik zónán belüli hálózati késés és a zónaközi hálózati forgalom késése között, üzembe helyezhet egy aktív/passzív karaktert tartalmazó architektúrát az SAP alkalmazásréteg szempontjából. Meg kell adnia egy *aktív* zónát, amely az a zóna, ahol a teljes alkalmazásréteget telepíti, és ahol az aktív DBMS és az SAP Központi szolgáltatások példányát is megpróbálja futtatni. Egy ilyen konfiguráció, meg kell győződnie arról, hogy nem rendelkezik szélsőséges futási idő eltérések, attól függően, hogy egy feladat fut-e a zónában az aktív DBMS-példány, vagy sem, az üzleti tranzakciók és kötegelt feladatok.

Az architektúra alapvető elrendezése így néz ki:

![Aktív/passzív zóna telepítése](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Erre a konfigurációra a következő szempontok vonatkoznak:

- Rendelkezésre állási csoportok nem telepíthető az Azure rendelkezésre állási zónákban. Ennek ellensúlyozására használhatja az Azure közelségi elhelyezési csoportjait az Azure közelségi elhelyezési csoportjai című cikkben dokumentált módon az [SAP-alkalmazásokkal való optimális hálózati késés érdekében.](sap-proximity-placement-scenarios.md)
- Ha ezt az architektúrát használja, szorosan figyelnie kell az állapotot, és meg kell próbálnia az aktív DBMS- és SAP-központi szolgáltatások példányait ugyanabban a zónában tartani, mint az üzembe helyezett alkalmazásréteget. Az SAP központi szolgáltatás vagy a DBMS-példány feladatátvétele esetén győződjön meg arról, hogy manuálisan is visszatud lépni a zónába az SAP alkalmazásréteg üzembe helyezésével a lehető leggyorsabban.
- Az SAP Központi szolgáltatások feladatátvételi fürtjeinek és a DBMS-rétegnek a terheléselosztók esetében a [standard termékváltozat Azure load balancer-t kell használnia.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) Az alapszintű terheléselosztó nem működik zónákon keresztül.
- Az Azure virtuális hálózat, amely az SAP-rendszer üzemeltetésére telepített, annak alhálózataival együtt, zónák között van elosztva. Az egyes zónákhoz nem kell külön virtuális hálózatokat igényelni.
- Az összes üzembe helyezett virtuális géphez az Azure Felügyelt lemezek et kell [használnia.](https://azure.microsoft.com/services/managed-disks/) A nem felügyelt lemezek nem támogatottak a zónaszintű telepítésekhez.
- Az Azure Premium Storage és [az Ultra SSD-tárhely](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatja a zónák közötti tárhelyreplikációt. Az alkalmazásnak (DBMS vagy SAP Központi szolgáltatások) fontos adatokat kell replikálnia.
- Ugyanez igaz a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy a zónák közötti megosztásokat. Ezek a technológiák támogatottak:
    - Windows esetén a SIOS DataKeeper szolgáltatást használó fürt, a [fürtben egy SAP ASCS/SCS-példány egy Windows feladatátvételi fürtön, az Azure-ban megosztott fürt használatával.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - SUSE Linux esetén egy NFS-megosztás, amely az NFS magas rendelkezésre állású szolgáltatásában készült [a SUSE Linux Enterprise Server en lévő Azure-beli virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)
    
  Jelenleg a megoldás, amely a Microsoft Scale-Out File Server, dokumentált [Előkészíti az Azure-infrastruktúra SAP magas rendelkezésre állású egy Windows feladatátvételi fürt és fájlmegosztás SAP ASCS/SCS példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)használatával nem támogatott zónák között.
- A harmadik zóna az SBD-eszköz üzemeltetésére szolgál arra az esetre, ha [SUSE Linux pacemaker fürtöt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányokat hoz létre.
- Alvó virtuális gépeket kell telepítenie a passzív zónában (DBMS szempontból), így zónahiba esetén elindíthatja az alkalmazás-erőforrásokat.
    - [Az Azure Site Recovery](https://azure.microsoft.com/services/site-recovery/) jelenleg nem tudja replikálni az aktív virtuális gépeket a zónák közötti alvó virtuális gépekre. 
- Be kell fektetnie az automatizálásba, amely lehetővé teszi, hogy zónahiba esetén automatikusan elindítsa az SAP alkalmazásréteget a második zónában.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>Kombinált magas rendelkezésre állás és vész-helyreállítási konfiguráció
A Microsoft nem oszt meg semmilyen információt a földrajzi távolságokról a különböző Azure-elérhetőségi zónákat tároló létesítmények között egy Azure-régióban. Ennek ellenére egyes ügyfelek zónákat használnak egy kombinált HA és DR konfigurációhoz, amely nulla helyreállításipont-célkitűzést (RPO) ígér. Ez azt jelenti, hogy nem veszíti el a véglegesített adatbázis-tranzakciók at még abban az esetben, vész-helyreállítási. 

> [!NOTE]
> Azt javasoljuk, hogy egy ilyen konfigurációt csak bizonyos körülmények között használjon. Például előfordulhat, hogy használja, ha az adatok nem hagyhatja el az Azure-régió biztonsági vagy megfelelőségi okokból. 

Íme egy példa arra, hogyan nézhet ki egy ilyen konfiguráció:

![Kombinált, nagy rendelkezésre állású DR zónákban](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Erre a konfigurációra a következő szempontok vonatkoznak:

- Vagy azt feltételezi, hogy jelentős távolság van a rendelkezésre állási zónát üzemeltető létesítmények között, vagy egy bizonyos Azure-régióban kell maradnia. Rendelkezésre állási csoportok nem telepíthető az Azure rendelkezésre állási zónákban. Ennek ellensúlyozására használhatja az Azure közelségi elhelyezési csoportjait az Azure közelségi elhelyezési csoportjai című cikkben dokumentált módon az [SAP-alkalmazásokkal való optimális hálózati késés érdekében.](sap-proximity-placement-scenarios.md)
- Ha ezt az architektúrát használja, szorosan figyelnie kell az állapotot, és meg kell próbálnia az aktív DBMS- és SAP-központi szolgáltatások példányait ugyanabban a zónában tartani, mint az üzembe helyezett alkalmazásréteget. Az SAP központi szolgáltatás vagy a DBMS-példány feladatátvétele esetén győződjön meg arról, hogy manuálisan is visszatud lépni a zónába az SAP alkalmazásréteg üzembe helyezésével a lehető leggyorsabban.
- Az aktív minőségbiztosítási alkalmazáspéldányokat futtató virtuális gépeken elő kell telepítenie az éles alkalmazáspéldányokat.
- Zónahiba esetén állítsa le a minőségbiztosítási alkalmazáspéldányokat, és indítsa el helyette a termelési példányokat. Vegye figyelembe, hogy ehhez az alkalmazáspéldányokhoz virtuális neveket kell használnia.
- Az SAP Központi szolgáltatások feladatátvételi fürtjeinek és a DBMS-rétegnek a terheléselosztók esetében a [standard termékváltozat Azure load balancer-t kell használnia.](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) Az alapszintű terheléselosztó nem működik zónákon keresztül.
- Az Azure virtuális hálózat, amely az SAP-rendszer üzemeltetésére telepített, annak alhálózataival együtt, zónák között van elosztva. Az egyes zónákhoz nem kell külön virtuális hálózatokat igényelni.
- Az összes üzembe helyezett virtuális géphez az Azure Felügyelt lemezek et kell [használnia.](https://azure.microsoft.com/services/managed-disks/) A nem felügyelt lemezek nem támogatottak a zónaszintű telepítésekhez.
- Az Azure Premium Storage és [az Ultra SSD-tárhely](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatja a zónák közötti tárhelyreplikációt. Az alkalmazásnak (DBMS vagy SAP Központi szolgáltatások) fontos adatokat kell replikálnia.
- Ugyanez igaz a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy a zónák közötti megosztásokat. Ezek a technológiák támogatottak:
    - Windows esetén a SIOS DataKeeper szolgáltatást használó fürt, a [fürtben egy SAP ASCS/SCS-példány egy Windows feladatátvételi fürtön, az Azure-ban megosztott fürt használatával.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
    - SUSE Linux esetén egy NFS-megosztás, amely az NFS magas rendelkezésre állású szolgáltatásában készült [a SUSE Linux Enterprise Server en lévő Azure-beli virtuális gépeken.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)

  Jelenleg a megoldás, amely a Microsoft Scale-Out File Server, dokumentált [Előkészíti az Azure-infrastruktúra SAP magas rendelkezésre állású egy Windows feladatátvételi fürt és fájlmegosztás SAP ASCS/SCS példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)használatával nem támogatott zónák között.
- A harmadik zóna az SBD-eszköz üzemeltetésére szolgál arra az esetre, ha [SUSE Linux pacemaker fürtöt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) vagy további alkalmazáspéldányokat hoz létre.





## <a name="next-steps"></a>További lépések
Íme néhány következő lépés az Azure rendelkezésre állási zónái közötti üzembe helyezéshez:

- [SAP ASCS/SCS-példány fürtjének fürtje egy Windows feladatátvételi fürtön az Azure-ban megosztott fürt használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Az Azure-infrastruktúra előkészítése az SAP magas rendelkezésre állására egy Windows feladatátvételi fürt és fájlmegosztás használatával SAP ASCS/SCS-példányokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






