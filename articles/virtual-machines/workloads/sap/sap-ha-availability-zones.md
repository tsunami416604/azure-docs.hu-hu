---
title: SAP munkaterhelés-konfigurációk Azure Availability Zonesokkal | Microsoft Docs
description: Magas rendelkezésre állású architektúra és forgatókönyvek az SAP NetWeaver Azure Availability Zones használatával
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ead1dfdce4bf3a803eee46a536dc7062626640d9
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234238"
---
# <a name="sap-workload-configurations-with-azure-availability-zones"></a>SAP számításifeladat-konfigurációk az Azure Availability Zones szolgáltatással
[Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) az Azure által biztosított magas rendelkezésre állási funkciók egyike. A Availability Zones használata javítja az Azure-beli SAP-munkaterhelések teljes rendelkezésre állását. Ez a funkció már elérhető néhány [Azure-régióban](https://azure.microsoft.com/global-infrastructure/regions/). A jövőben több régióban is elérhető lesz.

Ez a kép az SAP magas rendelkezésre állásának alapvető architektúráját mutatja be:

![Szabványos magas rendelkezésre állási konfiguráció](./media/sap-ha-availability-zones/standard-ha-config.png)

Az SAP-alkalmazás rétege egy Azure-beli [rendelkezésre állási csoporton](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)belül van üzembe helyezve. Az SAP központi szolgáltatások magas rendelkezésre állása érdekében két virtuális gépet helyezhet üzembe egy különálló rendelkezésre állási csoporton belül. A Windows Server feladatátvételi fürtszolgáltatást vagy a pacemakert (Linux) magas rendelkezésre állású keretrendszerként használhatja az infrastruktúra vagy a szoftver meghibásodása esetén. További információ ezekről az üzembe helyezésekről:

- [SAP-ASCS/SCS-példány fürtözött Windows feladatátvevő fürtön fürt megosztott lemezének használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [SAP ASCS/SCS-példány fürthöz való fürtözése Windows feladatátvevő fürtön a fájlmegosztás használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)
- [Magas rendelkezésre állás az Azure-beli virtuális gépeken futó SAP NetWeaver számára SUSE Linux Enterprise Server SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)
- [Az Azure Virtual Machines magas rendelkezésre állása az SAP NetWeaver-on Red Hat Enterprise Linux](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-rhel)

Hasonló architektúra vonatkozik az SAP NetWeaver, S/4HANA vagy Hybris rendszerek adatbázis-kezelő rétegére. Az adatbázis-kezelő réteget aktív/passzív üzemmódban helyezheti üzembe az infrastruktúra vagy a szoftver meghibásodása elleni védelemmel ellátott feladatátvevő fürt megoldásával. A feladatátvevő fürt megoldását az adatbázis-kezelői szolgáltatással kapcsolatos feladatátvételi keretrendszer, a Windows Server feladatátvételi fürtszolgáltatás vagy a pacemaker lehet.

Ha Azure Availability Zones használatával szeretné telepíteni ugyanazt az architektúrát, végre kell végeznie néhány módosítást a korábban ismertetett architektúrán. Ez a cikk ezeket a módosításokat ismerteti.

## <a name="considerations-for-deploying-across-availability-zones"></a>A Availability Zones-ben való üzembe helyezés szempontjai


Availability Zones használatakor vegye figyelembe a következőket:

- Az Azure-régión belül a különböző Availability Zones közötti távolságok nem garantálják a garanciát.
- Availability Zones nem ideális DR-megoldás. A természeti katasztrófák jelentős károkat okozhatnak a globális régiókban, beleértve az energiaellátási infrastruktúrák nagy mennyiségű károsodását is. Előfordulhat, hogy a különböző zónák távolsága nem elég nagy ahhoz, hogy megfelelő DR-megoldást jelentsen.
- A Availability Zones közötti hálózati késés nem azonos az összes Azure-régióban. Bizonyos esetekben az SAP-alkalmazás rétegét különböző zónákon keresztül telepítheti és futtathatja, mert az egyik zónából az aktív adatbázis-kezelő virtuális gépre irányuló hálózati késés elfogadható. Bizonyos Azure-régiókban azonban előfordulhat, hogy az aktív adatbázis-kezelő virtuális gép és az SAP-alkalmazás példánya eltérő zónákban való telepítésekor nem fogadható el az SAP üzleti folyamatok számára. Ezekben az esetekben az üzembe helyezési architektúrának eltérőnek kell lennie az alkalmazás aktív/aktív architektúrája, illetve egy aktív/passzív architektúra esetében, ahol a hálózati késés túl magas.
- Ha eldönti, hogy hol kívánja használni a Availability Zones-t, alapozza meg döntését a zónák közötti hálózati késéssel kapcsolatban. A hálózati késés két területen játszik fontos szerepet:
    - A szinkron replikálást igénylő két adatbázis-kezelő példány közötti késés. Minél nagyobb a hálózati késés, annál valószínűbb, hogy a számítási feladatok méretezhetőségét fogja érinteni.
    - A hálózati késések közötti különbség egy olyan virtuális gép között, amely egy, az aktív adatbázis-kezelő példánnyal és egy másik zónában található hasonló virtuális géppel fut a zónában. Mivel ez a különbség növekszik, az üzleti folyamatok és a kötegelt feladatok futási idejének hatása is növekszik, attól függően, hogy a zónában futnak-e az adatbázis-kezelővel vagy egy másik zónában.

Ha Azure-beli virtuális gépeket helyez üzembe Availability Zones és ugyanazon az Azure-régióban található feladatátvételi megoldásokat hoz létre, bizonyos korlátozások érvényesek:

- A Azure Availability Zones üzembe helyezéséhez az [Azure Managed Diskst](https://azure.microsoft.com/services/managed-disks/) kell használnia. 
- A zónák enumerálásának fizikai zónákhoz való hozzárendelése egy Azure-előfizetésen keresztül történik. Ha különböző előfizetéseket használ az SAP-rendszerek üzembe helyezéséhez, minden előfizetéshez meg kell határoznia az ideális zónákat.
- Az Azure-beli rendelkezésre állási csoportok nem helyezhetők üzembe az Azure-beli rendelkezésre állási zónán belül, kivéve, ha az Azure-beli [Proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) Az SAP adatbázis-kezelő réteg és a központi szolgáltatások zónákon belüli üzembe helyezésének módja, valamint az SAP-alkalmazás rétegének üzembe helyezése a rendelkezésre állási csoportok használatával, valamint a virtuális gépek közelségének biztosítása [ Csoportok az SAP-alkalmazásokkal való optimális hálózati késés érdekében](sap-proximity-placement-scenarios.md). Ha nem használ Azure Proximity-elhelyezési csoportokat, ki kell választania egyet vagy a másikat a virtuális gépek üzembe helyezési keretrendszereként.
- A Windows Server feladatátvételi fürtszolgáltatás vagy a Linux pacemaker használatával nem használhat [Azure Alapszintű Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) a feladatátvevő fürtre vonatkozó megoldások létrehozásához. Ehelyett az [Azure standard Load BALANCER SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)-t kell használnia.



## <a name="the-ideal-availability-zones-combination"></a>Az ideális Availability Zones kombináció
Mielőtt eldönti, hogyan használja a Availability Zones, meg kell határoznia a következőket:

- Egy Azure-régió három zónája közötti hálózati késés. Ez lehetővé teszi, hogy kiválassza a legkisebb hálózati késéssel rendelkező zónákat a zónák közötti hálózati forgalomban.
- A virtuális gép és a virtuális gép közötti késések közötti különbség a választott zónák egyikén belül, és a hálózat késése két különböző zónában.
- Annak megállapítása, hogy a telepítendő virtuálisgép-típusok elérhetők-e a két kiválasztott zónában. Egyes virtuális gépek, különösen az M sorozatú virtuális gépek esetében előfordulhatnak olyan helyzetek, amikor néhány SKU csak kettőben érhető el a három zónában.

## <a name="network-latency-between-and-within-zones"></a>Hálózati késés a zónák és a körzetek között
A különböző zónák közötti késés megállapításához a következőket kell tennie:

- Telepítse a virtuális gép SKU-t, amelyet az adatbázis-kezelő példányhoz kíván használni mindhárom zónában. Győződjön meg arról, hogy az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van a mérés során.
- Ha megtalálta a legkisebb hálózati késéssel rendelkező két zónát, helyezzen üzembe egy másik három virtuális GÉPET, amelyet az alkalmazás rétegbeli virtuális gépként kíván használni a három Availability Zones. Mérje meg a hálózati késleltetést a két adatbázis-kezelő virtuális gépen a kiválasztott két adatbázis-kezelő zónában. 
- **Niping** használata mérési eszközként. Az SAP-től származó eszközt az SAP-támogatási megjegyzések [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)című témakör írja le. Koncentráljon a késési mérésekhez dokumentált parancsokra. Mivel a **ping** nem működik az Azure gyorsított hálózati kód elérési útjain, nem ajánlott használni.

A mérések és a virtuális gépek Availability Zonesban való rendelkezésre állása alapján bizonyos döntéseket kell meghoznia:

- Határozza meg az adatbázis-kezelő réteg ideális zónáit.
- Döntse el, hogy az aktív SAP-alkalmazás rétegét egy, kettő vagy mindhárom zónában szeretné-e terjeszteni a zóna hálózati késése és a zónák közötti különbségek alapján.
- Döntse el, hogy aktív/passzív konfigurációt vagy aktív/aktív konfigurációt szeretne üzembe helyezni egy alkalmazási pontból. (Ezeket a konfigurációkat a cikk későbbi részében ismertetjük.)

A döntések meghozatala során az SAP hálózati késéssel kapcsolatos javaslatait is figyelembe kell vennie az SAP-Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)dokumentációjában leírtaknak megfelelően.

> [!IMPORTANT]
> A mérések és döntések érvényesek a mérések során használt Azure-előfizetésre. Ha másik Azure-előfizetést használ, meg kell ismételnie a mértékeket. A enumerált zónák leképezése más Azure-előfizetések esetében eltérő lehet.


> [!IMPORTANT]
> A korábban ismertetett mérések várhatóan különböző eredményeket biztosítanak minden olyan Azure-régióban, amely támogatja a [Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview). Ha a hálózati késésre vonatkozó követelmények azonosak, előfordulhat, hogy különböző központi telepítési stratégiákat kell elfogadnia különböző Azure-régiókban, mert a zónák közötti hálózati késés eltérő lehet. Egyes Azure-régiókban a három különböző zóna hálózati késése rendkívül eltérő lehet. Más régiókban a három különböző zóna közötti hálózati késés nagyobb egység lehet. Az a jogcím, hogy mindig van egy 1 és 2 ezredmásodperc közötti hálózati késés, nem megfelelő. Az Azure-régiók Availability Zones közötti hálózati késés nem általánosítható.

## <a name="activeactive-deployment"></a>Aktív/aktív üzembe helyezés
Az üzembe helyezési architektúra neve aktív/aktív, mert az aktív SAP-alkalmazás-kiszolgálókat két vagy három zónában helyezi üzembe. A sorba helyezni replikálást használó SAP központi szolgáltatási példány két zóna között lesz telepítve. Ugyanez vonatkozik az adatbázis-kezelő rétegre is, amely az SAP Central szolgáltatással megegyező zónákban lesz telepítve.

Ebben a konfigurációban meg kell keresnie a régióban a két Availability Zones, amely a munkaterheléshez és a szinkron adatbázis-kezelői replikáláshoz elfogadható, zónák közötti hálózati késést kínál. Győződjön meg arról is, hogy a kiválasztott zónákon belüli hálózati késés és a zóna közötti hálózati késés nem túl nagy. Ennek az az oka, hogy a nagyméretű változatokat nem kívánja használni, attól függően, hogy egy adott feladat a zónában fut-e az adatbázis-kezelő kiszolgálóval vagy a különböző zónákkal az üzleti folyamatok vagy a Batch-feladatok futási idején. Bizonyos változatok elfogadhatók, de a különbségi tényezőket nem.

A két zónába tartozó aktív/aktív telepítés egyszerűsített sémája a következőképpen néz ki:

![Aktív/aktív zóna üzembe helyezése](./media/sap-ha-availability-zones/active_active_zones_deployment.png)

Ehhez a konfigurációhoz a következő szempontokat kell figyelembe venni:

- Ha nem használja az [Azure Proximity](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)beosztási csoportot, akkor a Azure Availability Zones az összes virtuális gép meghibásodási és frissítési tartománya kezeli, mivel a rendelkezésre állási csoportok nem helyezhetők üzembe Azure Availability Zones.
- Ha az adatbázis-kezelő réteg és a központi szolgáltatások esetében az Azure-beli rendelkezésre állási csoportokat kívánja egyesíteni, de az alkalmazási réteghez szeretné használni az Azure-ban elérhető csoportok használatát, az Azure Proximity-elhelyezési csoportok című cikkben leírtak szerint az [optimális hálózati késés SAP](sap-proximity-placement-scenarios.md)-alkalmazásokkal.
- Az SAP Central Services és az adatbázis-kezelő réteg feladatátvevő fürtjének terheléselosztó esetén a [szabványos SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)kell használnia. Az alapszintű Load Balancer nem fog működni a zónák között.
- Az SAP-rendszer üzemeltetéséhez üzembe helyezett Azure-beli virtuális hálózat az alhálózatokkal együtt a zónák között van kiterjesztve. Minden zónához nincs szükség külön virtuális hálózatokra.
- Az összes telepített virtuális gép esetében az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)-t kell használnia. A nem felügyelt lemezek nem támogatottak a zónákon üzemelő példányok esetében.
- Az Azure Premium Storage és a [Ultra SSD Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatja a zónák közötti bármilyen típusú replikációt. Az alkalmazásnak (adatbázis-kezelői vagy SAP központi szolgáltatásoknak) replikálnia kell a fontos adatforrásokat.
- Ugyanez érvényes a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy megosztásokat a zónák között. Ezek a technológiák támogatottak:
  - Windows rendszer esetén a SIOS DataKeeper használó fürtözött megoldás a [Windows feladatátvevő fürtön lévő SAP ASCS/SCS-példányok fürtözött megosztott lemezzel való használatával, az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)dokumentálva.
  - SUSE Linux esetében az NFS-megosztások, amelyek a [magas rendelkezésre állású NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)-ben, a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken.
    
    A Microsoft kibővített fájlkiszolgálót használó megoldás jelenleg a [Windows feladatátvevő fürt és az SAP ASCS/SCS-példányok esetében az Azure-infrastruktúra előkészítése a magas rendelkezésre álláshoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)című cikkben ismertetett módon nem támogatott a zónák között.
- A harmadik zóna a SBD eszköz futtatására szolgál, ha [SUSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) -fürtöt vagy további alkalmazás-példányokat hoz létre.
- A kritikus fontosságú üzleti folyamatok futtatási időkonzisztenciájának eléréséhez próbáljon meg bizonyos batch-feladatokat és felhasználókat olyan alkalmazás-példányokhoz irányítani, amelyek az aktív adatbázis-kezelői példánnyal rendelkeznek az SAP batch Server-csoportok, az SAP-bejelentkezési csoportok vagy az RFC-csoportok használatával. A zónák közötti feladatátvétel esetén azonban ezeket a csoportokat manuálisan kell áthelyeznie az aktív adatbázis-virtuálisgép-zónán belüli virtuális gépeken futó példányokra.  
- Előfordulhat, hogy minden zónában érdemes lehet alvó párbeszédpanel-példányokat telepíteni. Ezzel a megoldással azonnal visszatérhet a korábbi erőforrás-kapacitáshoz, ha az alkalmazás példányainak részeként használt zóna kívül esik a szolgáltatáson.


## <a name="activepassive-deployment"></a>Aktív/passzív üzembe helyezés
Ha nem talál elfogadható különbözetet az egyik zónán belüli hálózati késés és a zónák közötti hálózati forgalom késése között, olyan architektúrát helyezhet üzembe, amely aktív/passzív karaktert tartalmaz az SAP-alkalmazás rétegének nézetében. Definiáljon egy *aktív* zónát, amely az a zóna, amelyben a teljes alkalmazás réteget üzembe helyezi, és ahol megkísérli az aktív adatbázis-kezelő és az SAP központi szolgáltatások példányának futtatását. Ilyen konfiguráció esetén meg kell győződnie arról, hogy nem rendelkezik a szélsőséges futásidejű változatokkal, attól függően, hogy a feladat a zónában fut-e az aktív adatbázis-kezelői példánnyal, vagy sem, az üzleti tranzakciókban és a kötegelt feladatokban.

Az architektúra alapszintű elrendezése a következőképpen néz ki:

![Aktív/passzív zónák üzembe helyezése](./media/sap-ha-availability-zones/active_passive_zones_deployment.png)

Ehhez a konfigurációhoz a következő szempontokat kell figyelembe venni:

- A rendelkezésre állási készletek nem helyezhetők üzembe Azure Availability Zonesban. Ennek kompenzálására az Azure Proximity-elhelyezési csoportok az Azure Proximity-elhelyezési csoportok című cikkben ismertetett módon használhatók az [SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.
- Ha ezt az architektúrát használja, szorosan figyelnie kell az állapotot, és meg kell őriznie az aktív adatbázis-kezelő és az SAP központi szolgáltatások példányait ugyanabban a zónában, mint a telepített alkalmazási réteget. Az SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétele esetén győződjön meg arról, hogy a lehető leggyorsabban üzembe helyezett SAP-alkalmazás réteggel manuálisan tud visszaadni a zónába.
- Az SAP Central Services és az adatbázis-kezelő réteg feladatátvevő fürtjének terheléselosztó esetén a [szabványos SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)kell használnia. Az alapszintű Load Balancer nem fog működni a zónák között.
- Az SAP-rendszer üzemeltetéséhez üzembe helyezett Azure-beli virtuális hálózat az alhálózatokkal együtt a zónák között van kiterjesztve. Minden zónához nincs szükség külön virtuális hálózatokra.
- Az összes telepített virtuális gép esetében az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)-t kell használnia. A nem felügyelt lemezek nem támogatottak a zónákon üzemelő példányok esetében.
- Az Azure Premium Storage és a [Ultra SSD Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatja a zónák közötti bármilyen típusú replikációt. Az alkalmazásnak (adatbázis-kezelői vagy SAP központi szolgáltatásoknak) replikálnia kell a fontos adatforrásokat.
- Ugyanez érvényes a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy megosztásokat a zónák között. Ezek a technológiák támogatottak:
    - Windows rendszer esetén a SIOS DataKeeper használó fürtözött megoldás a [Windows feladatátvevő fürtön lévő SAP ASCS/SCS-példányok fürtözött megosztott lemezzel való használatával, az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)dokumentálva.
    - SUSE Linux esetében az NFS-megosztások, amelyek a [magas rendelkezésre állású NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)-ben, a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken.
    
  A Microsoft kibővített fájlkiszolgálót használó megoldás jelenleg a [Windows feladatátvevő fürt és az SAP ASCS/SCS-példányok esetében az Azure-infrastruktúra előkészítése a magas rendelkezésre álláshoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)című cikkben ismertetett módon nem támogatott a zónák között.
- A harmadik zóna a SBD eszköz futtatására szolgál, ha [SUSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) -fürtöt vagy további alkalmazás-példányokat hoz létre.
- Az alvó üzemmódú virtuális gépeket a passzív zónában kell üzembe helyezni (egy adatbázis-kezelői pontból), így a zóna meghibásodása esetén elindíthatja az alkalmazás erőforrásait.
    - [Azure site Recovery](https://azure.microsoft.com/services/site-recovery/) jelenleg nem tudja replikálni az aktív virtuális gépeket a zónák közötti, alvó állapotban lévő virtuális gépekre. 
- Olyan automatizálást kell befektetni, amely lehetővé teszi, hogy a zóna meghibásodása esetén automatikusan elindítsa az SAP-alkalmazás réteget a második zónában.

## <a name="combined-high-availability-and-disaster-recovery-configuration"></a>A magas rendelkezésre állás és a vész-helyreállítási konfiguráció együttese
A Microsoft nem osztja meg az Azure-régió különböző Azure Availability Zoneseit üzemeltető létesítmények földrajzi távolságával kapcsolatos információkat. Egyes ügyfelek azonban egy kombinált HA-és DR-konfiguráció zónáit használják, amelyek nulla értékű helyreállítási időkorlátot (RPO) ígérnek. Ez azt jelenti, hogy a vész-helyreállítás esetén sem kell elveszíteni a véglegesített adatbázis-tranzakciókat. 

> [!NOTE]
> Javasoljuk, hogy csak bizonyos esetekben használjon konfigurációt. Előfordulhat például, hogy az Azure-régió biztonsági vagy megfelelőségi okokból nem hagyhatják el az adatközpontot. 

Íme egy példa arra, hogyan néz ki egy ilyen konfiguráció:

![Kombinált magas rendelkezésre állású DR zónákban](./media/sap-ha-availability-zones/combined_ha_dr_in_zones.png)

Ehhez a konfigurációhoz a következő szempontokat kell figyelembe venni:

- Feltételezi, hogy jelentős távolság van a rendelkezésre állási zónát üzemeltető létesítmények között, vagy ha egy bizonyos Azure-régióban szeretne maradni. A rendelkezésre állási készletek nem helyezhetők üzembe Azure Availability Zonesban. Ennek kompenzálására az Azure Proximity-elhelyezési csoportok az Azure Proximity-elhelyezési csoportok című cikkben ismertetett módon használhatók az [SAP-alkalmazásokkal való optimális hálózati késés](sap-proximity-placement-scenarios.md)érdekében.
- Ha ezt az architektúrát használja, szorosan figyelnie kell az állapotot, és meg kell őriznie az aktív adatbázis-kezelő és az SAP központi szolgáltatások példányait ugyanabban a zónában, mint a telepített alkalmazási réteget. Az SAP Central szolgáltatás vagy az adatbázis-kezelő példány feladatátvétele esetén győződjön meg arról, hogy a lehető leggyorsabban üzembe helyezett SAP-alkalmazás réteggel manuálisan tud visszaadni a zónába.
- Az aktív QA alkalmazás példányait futtató virtuális gépeken előre telepítve kell lennie az éles üzemben lévő példányoknak.
- Zóna meghibásodása esetén állítsa le a QA-alkalmazás példányait, és indítsa el helyette az éles példányokat. Vegye figyelembe, hogy a munka elvégzéséhez virtuális neveket kell használnia az alkalmazás példányaihoz.
- Az SAP Central Services és az adatbázis-kezelő réteg feladatátvevő fürtjének terheléselosztó esetén a [szabványos SKU Azure Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones)kell használnia. Az alapszintű Load Balancer nem fog működni a zónák között.
- Az SAP-rendszer üzemeltetéséhez üzembe helyezett Azure-beli virtuális hálózat az alhálózatokkal együtt a zónák között van kiterjesztve. Minden zónához nincs szükség külön virtuális hálózatokra.
- Az összes telepített virtuális gép esetében az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/)-t kell használnia. A nem felügyelt lemezek nem támogatottak a zónákon üzemelő példányok esetében.
- Az Azure Premium Storage és a [Ultra SSD Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-ultra-ssd) nem támogatja a zónák közötti bármilyen típusú replikációt. Az alkalmazásnak (adatbázis-kezelői vagy SAP központi szolgáltatásoknak) replikálnia kell a fontos adatforrásokat.
- Ugyanez érvényes a megosztott sapmnt könyvtárra, amely egy megosztott lemez (Windows), egy CIFS-megosztás (Windows) vagy egy NFS-megosztás (Linux). Olyan technológiát kell használnia, amely replikálja ezeket a megosztott lemezeket vagy megosztásokat a zónák között. Ezek a technológiák támogatottak:
    - Windows rendszer esetén a SIOS DataKeeper használó fürtözött megoldás a [Windows feladatátvevő fürtön lévő SAP ASCS/SCS-példányok fürtözött megosztott lemezzel való használatával, az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)dokumentálva.
    - SUSE Linux esetében az NFS-megosztások, amelyek a [magas rendelkezésre állású NFS](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-nfs)-ben, a SUSE Linux Enterprise Server-on található Azure-beli virtuális gépeken.

  A Microsoft kibővített fájlkiszolgálót használó megoldás jelenleg a [Windows feladatátvevő fürt és az SAP ASCS/SCS-példányok esetében az Azure-infrastruktúra előkészítése a magas rendelkezésre álláshoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)című cikkben ismertetett módon nem támogatott a zónák között.
- A harmadik zóna a SBD eszköz futtatására szolgál, ha [SUSE Linux pacemaker](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse-pacemaker#create-azure-fence-agent-stonith-device) -fürtöt vagy további alkalmazás-példányokat hoz létre.





## <a name="next-steps"></a>További lépések
Íme néhány további lépés a Azure Availability Zones üzembe helyezéséhez:

- [Az SAP ASCS/SCS-példányok fürtözése Windows feladatátvevő fürtön az Azure-ban megosztott fürtözött lemez használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-shared-disk)
- [Azure-infrastruktúra előkészítése az SAP magas rendelkezésre állásához Windows feladatátvevő fürt és fájlmegosztás használatával SAP ASCS/SCS-példányok esetén](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-infrastructure-wsfc-file-share)






