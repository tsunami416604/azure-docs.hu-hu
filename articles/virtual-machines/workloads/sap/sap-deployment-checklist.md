---
title: SAP számítási feladatok tervezési és üzembe helyezési ellenőrzőlistája | Microsoft Docs
description: Ellenőrzőlista az SAP számítási feladatok tervezéséhez és üzembe helyezéséhez az Azure-ban
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
ms.date: 07/15/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: abe04a88f3337ed26b98815b5633fd5d719b7ce3
ms.sourcegitcommit: b2db98f55785ff920140f117bfc01f1177c7f7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/16/2019
ms.locfileid: "68234226"
---
# <a name="sap-workload-on-azure-planning-and-deployment-checklist"></a>SAP számítási feladatok az Azure tervezési és üzembe helyezési ellenőrzőlistáján 

Ez az ellenőrzőlista olyan ügyfelek számára készült, akik SAP NetWeaver-, S/4HANA-és Hybris-alkalmazásokat helyeznek üzembe az Azure-infrastruktúra szolgáltatásként.  Ezt a feladatlistát az ügyfélnek és/vagy az SAP-partnernek kell áttekintenie a projekt időtartama alatt. Fontos, hogy számos ellenőrzés a projekt elején, a tervezési fázisban legyen elvégezve. Az üzembe helyezést követően az üzembe helyezett Azure-infrastruktúra vagy az SAP-szoftverek elemeinek alapvető változásai összetettek lehetnek. Tekintse át ezt a feladatlistát a teljes projektben található kulcsfontosságú mérföldkövek között.  Kis problémák észlelhetők, mielőtt nagy problémákba lépnek, és elegendő idő áll rendelkezésre a szükséges módosítások teszteléséhez. Az ellenőrzőlista nem rendelkezik a jogcímek befejezésének módjával. Az egyéni helyzettől függően számos további ellenőrzést kell végrehajtania. 

Az összeállított ellenőrzőlista nem tartalmazza az Azure-tól független feladatokat.  Példa: Az SAP-alkalmazási felületek az Azure nyilvános felhőbe vagy egy szolgáltatóra való áttérés során változnak.    

Ezt az ellenőrzőlistát a már telepített rendszerekhez is használhatja. A üzembe helyezése óta új funkciók, például írásgyorsító, Availability Zones és új virtuálisgép-típusok is hozzáadhatók.  Ezért érdemes rendszeresen áttekinteni az ellenőrzőlistát, hogy biztosan tisztában legyen az Azure platform új szolgáltatásaival. 

## <a name="project-preparation-and-planning-phase"></a>Projekt előkészítési és tervezési fázisa
Ebben a fázisban az SAP számítási feladatok Azure-beli nyilvános felhőbe való áttelepítését tervezzük. A felsorolt entitások és elemek minimális készlete, például:

1. Magas szintű tervezési dokumentum – a dokumentumnak a következőket kell tartalmaznia:
    1. Az SAP-összetevők és-alkalmazások, valamint az Azure-beli célalkalmazás-leltár aktuális leltára
    2. Hozzon létre és dolgozzon ki egy felelősségi hozzárendelési mátrixot (RÁCOK), amely meghatározza a különböző érintett felek feladatait és hozzárendeléseit. A tervezés és az első központi telepítések szintjének megkezdése a magas szinten, és több részletességi szinten
    2. Magas szintű megoldási architektúra
    3. Döntés az Azure-régiókban való üzembe helyezéshez. Az Azure-régiók listájáért keresse fel az [Azure](https://azure.microsoft.com/global-infrastructure/regions/)-régiókat. Az egyes Azure-régiókban elérhető szolgáltatások esetében keresse meg a [rendelkezésre álló termékek régiónként](https://azure.microsoft.com/global-infrastructure/services/) .
    4. Hálózati architektúra a helyszíni környezetből az Azure-ba való kapcsolódáshoz. Ismerkedjen meg az Azure-beli [Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/) -tervezettel
    5. A nagyvállalati adathatások Azure-ban való futtatásának biztonsági alapelvei. Olvasási anyagok az [Azure Security dokumentációjában](https://docs.microsoft.com/azure/security/)
2.  Technikai tervezési dokumentum – a következőket tartalmazza:
    1.  Egy megoldás-blokk diagram 
    2.  A számítási, tárolási és hálózatkezelési összetevők méretezése az Azure-ban. Az Azure-beli virtuális gépek SAP-méretezéséhez tekintse meg az SAP-támogatási Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533) 
    3.  Üzletmenet-folytonosság és vész-helyreállítási architektúra
    4.  Részletes operációsrendszer-, adatbázis-, kernel-és SAP-támogatási csomagok verziói. Nem az, hogy az SAP NetWeaver vagy az S/4HANA által támogatott operációsrendszer-kiadásokat az Azure-beli virtuális gépek támogatják. Ugyanez érvényes az adatbázis-kezelői kiadások esetében is. Az SAP-és az Azure-ban támogatott ablakban az SAP-kiadások, az adatbázis-kezelői kiadások és az operációs rendszerek kiadásának összehangolásához és szükség esetén a következő források beolvasására van szükség. Kötelező, hogy az SAP és az Azure által támogatott kiadási kombinációk révén teljes körű támogatást kapjon az SAP és a Microsoft számára. Szükség esetén meg kell terveznie a szoftverek egyes összetevőinek frissítését. A támogatott SAP-, operációsrendszer-és adatbázis-kezelő szoftverekről a következő helyen talál további információt:
        1.  SAP-támogatás Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Ez a Megjegyzés az Azure-beli virtuális gépek által támogatott minimális operációsrendszer-kiadásokat határozza meg. Emellett a legtöbb nem HANA-adatbázishoz szükséges minimális adatbázis-kiadásokat is meghatározza. A Megjegyzés a különböző SAP által támogatott Azure VM-típusok SAP-méretezését is megjeleníti.
        2.  SAP-támogatás Megjegyzés [#2039619](https://launchpad.support.sap.com/#/notes/2039619). A Megjegyzés az Oracle támogatási mátrixát határozza meg az Azure-ban. Vegye figyelembe, hogy az Oracle csak a Windows és Oracle Linux operációs rendszert támogatja az Azure-ban az SAP-munkaterheléshez. Ez a támogatási utasítás az SAP-példányokat futtató SAP-alkalmazás rétegére is vonatkozik. Az Oracle azonban nem támogatja az SAP központi szolgáltatások magas rendelkezésre állását Oracle Linux a Pacemakeren keresztül. Ha magas rendelkezésre állásra van szüksége a Oracle Linux ASCS, a Linux rendszerhez készült SIOS Protection Suite-t kell használnia. Az SAP-tanúsítványok részletes adatainak megtekintéséhez tekintse meg a következőt: SAP support Megjegyzés [#1662610 – támogatás a Linux rendszerhez készült SIOS Protection Suite számára](https://launchpad.support.sap.com/#/notes/1662610). A Windows esetében az SAP központi szolgáltatásokhoz támogatott Windows feladatátvevő fürt feladatátvételi megoldását az Oracle és az adatbázis-kezelői réteg együttes használata támogatja. 
        3.  SAP-támogatás Megjegyzés [#2235581](https://launchpad.support.sap.com/#/notes/2235581) a különböző operációsrendszer-kiadásokban lévő SAP HANA támogatási mátrixának beszerzéséhez
        4.  SAP HANA támogatott Azure-beli virtuális gépek és [HANA nagyméretű példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) [itt](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) vannak felsorolva
        5.  [SAP-termék rendelkezésre állási mátrixa](https://support.sap.com/en/)
        6.  További SAP-megjegyzések más SAP-specifikus termékekhez  
    5.  Javasoljuk, hogy az SAP éles rendszerek esetében szigorú 3 szintű terveket tervezzen. A ASCS és az alkalmazás-kiszolgálók ugyanazon a virtuális gépen való egyesítése nem ajánlott.  Az SAP Central Services több SID-alapú fürtjének konfigurációjának használatával a Windows operációs RENDSZERként használható az Azure-ban. Míg az SAP Central Services multi-SID fürtjének konfigurációja nem támogatott az Azure-beli Linux operációs rendszereken. A Windows vendég operációs rendszerre vonatkozó dokumentáció a következő címen található:
        1.  [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és a megosztott lemezzel az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        2.  [SAP ASCS/SCS instance multi-SID magas rendelkezésre állás a Windows Server feladatátvételi fürtszolgáltatással és fájlmegosztás az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    6.  Magas rendelkezésre állás és vész-helyreállítási architektúra
        1.  A RTO és a RPO alapján határozza meg, hogy a magas rendelkezésre állást és a vész-helyreállítási architektúrát hogyan kell kinéznie
        2.  Ha a magas rendelkezésre állást szeretné megtekinteni ugyanazon a zónán belül, a kívánt adatbázis-kezelőt az Azure-ban találja. A legtöbb adatbázis-kezelő szinkron módszert kínál a szinkron gyors készenléti állapothoz, ezért éles rendszerekhez ajánlott. Tekintse meg az SAP-hez kapcsolódó dokumentációt is a különböző adatbázisokhoz, az [Azure Virtual Machines adatbázis](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) -kezelői környezettel kapcsolatos megfontolások alapján
            1.  Ha a Windows feladatátvételi fürtszolgáltatást megosztott lemezes konfigurációval használja az adatbázis-kezelő réteghez, például a SQL Server [itt](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017) leírt módon, **nem** támogatott. Ehelyett a következőkhöz hasonló megoldásokat:
                1.  [SQL Server AlwaysOn](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups) 
                2.  [Oracle-adatvédelem](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
                3.  [HANA rendszerreplikáció](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        3.  A különböző Azure-régiókban való vész-helyreállításhoz tekintse meg, hogy milyen lehetőségeket kínál a különböző adatbázis-kezelők gyártói. A legtöbb esetben támogatja az aszinkron replikálást vagy a naplózást
        4.  Az SAP-alkalmazás rétegében adja meg, hogy az üzleti regressziós teszt rendszereit futtatja-e, amely ideális esetben az éles környezetek replikái, ugyanabban az Azure-régióban vagy a DR régióban. Az utóbbi esetben megcélozhatja, hogy az üzleti regressziós rendszer DR célként legyen az éles környezetben
        5.  Ha úgy dönt, hogy nem helyezi el a nem üzemi rendszereket a DR-helyen, tekintse át az Azure Site Recovery az SAP-alkalmazás rétegének az Azure DR régióba való replikálásának életképes módszerét. Lásd még: vész [-helyreállítás beállítása többrétegű SAP NetWeaver-alkalmazás telepítéséhez](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap) 
        6.  Ha a kombinált HA/DR konfiguráció használatát választja, a [Azure Availability Zones](https://docs.microsoft.com/azure/availability-zones/az-overview) kihasználja az Azure-régiókat, ahol Availability Zones elérhetők, és olyan korlátozásokkal, amelyeket a hálózati késések nagyobb számával lehet bevezetni két Availability Zones között  
3.  Az ügyfélnek/partnernek leltárt kell készítenie az összes SAP-csatolóról (SAP és nem SAP). 
4.  Az alapszolgáltatások kialakításának kialakítása – ez a kialakítás olyan elemeket tartalmaz, mint például a
    1.  Active Directory és DNS-kialakítás
    2.  Hálózati topológia az Azure-on belül és különböző SAP-rendszerek hozzárendelése
    3.  [Szerepköralapú hozzáférési](https://docs.microsoft.com/azure/role-based-access-control/overview) struktúra az Azure-beli infrastruktúrát és SAP-alkalmazásokat kezelő különböző csapatok számára
    3.  Erőforráscsoport-topológia 
    4.  [Címkézési stratégia](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing)
    5.  A virtuális gépek és egyéb infrastruktúra-összetevők és/vagy logikai nevek elnevezési konvenciója
5.  Microsoft Premier szintű támogatás szerződés – az MS Technical Account Manager (TAM) azonosítása. Az SAP-támogatással kapcsolatos támogatási követelményekért tekintse meg az SAP-támogatási Megjegyzés [#2015553](https://launchpad.support.sap.com/#/notes/2015553) 
6.  Adja meg a különböző előfizetésekhez tartozó Azure-előfizetések és a fő kvóták számát. [Támogatási kérések megnyitása az Azure](https://docs.microsoft.com/azure/azure-supportability/resource-manager-core-quotas-request) -előfizetések igény szerinti növeléséhez 
7.  Adatmennyiség-csökkentési és adatáttelepítési terv az SAP-beli Azure-ba való Migrálás során. Az SAP NetWeaver Systems esetében az SAP a nagy számú adatmennyiség megtartására vonatkozó útmutatást tartalmaz. Az SAP az SAP ERP rendszerekben az adatkezeléssel kapcsolatos részletes [útmutatót](https://help.sap.com/http.svc/rc/2eb2fba8f8b1421c9a37a8d7233da545/7.0/en-US/Data_Management_Guide_Version_70E.PDF) tette közzé. Bizonyos tartalmak azonban általában a NetWeaver és S/4HANA rendszerekre vonatkoznak.
8.  Az automatizált üzembe helyezési módszer meghatározása és eldöntése. Az Azure-beli infrastruktúra-telepítések mögötti automatizálás célja, hogy determinisztikus módon telepítsen, és determinisztikus eredményeket kapjon. Számos ügyfél használja a Power shellt vagy a CLI-alapú parancsfájlokat. Vannak azonban olyan nyílt forráskódú technológiák, amelyek segítségével üzembe helyezhetők az Azure-infrastruktúra az SAP számára, és akár SAP-szoftverek is telepíthetők. Példákat a GitHubban találhat:
    1.  [Automatizált SAP-üzembe helyezések az Azure-felhőben](https://github.com/Azure/sap-hana)
    2.  [SAP HANA telepítés](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Az ügyfél, a rendszerintegrátor, a Microsoft és az egyéb érintett felek közötti rendszeres tervezési és üzembe helyezési felülvizsgálati lépésszám megadása

 
## <a name="pilot-phase-strongly-recommended"></a>Kísérleti fázis (erősen ajánlott)
 
A próbaüzem a projekt megtervezése és előkészítése előtt vagy párhuzamosan is futtatható. A fázis a tervezési és előkészítési fázisban végrehajtott megközelítések és kialakítás tesztelésére is használható. A kísérleti fázist a fogalmak valódi bizonyítására lehet kiterjeszteni. Javasoljuk, hogy állítson be és ellenőrizzen egy teljes HA/DR-megoldást, valamint a biztonsági tervezést a kísérleti üzembe helyezés során. Egyes ügyfelek esetében a skálázhatósági tesztek is elvégezhetők ebben a fázisban. Más ügyfeleink a SAP homokozó rendszerek üzembe helyezését kísérleti fázisként használják. Ezért feltételezzük, hogy egy olyan rendszert azonosítottan, amelyet át szeretne telepíteni az Azure-ba egy próba futtatása céljából.

1. Optimalizálja az adatátvitelt az Azure-ba. Nagymértékben függ az ügyfelektől a helyszíni [Azure ExpressRoute](https://azure.microsoft.com/services/expressroute/) keresztül történő átviteltől, ha az expressz áramkörnek elég sávszélessége volt. Más ügyfelekkel az interneten keresztül úgy alakítottuk ki, hogy gyorsabb legyen
2. SAP heterogén platform áttelepítése esetén, amely magában foglalja az adatbázis-adatmennyiség exportálását és importálását, az exportálási és importálási fázisok tesztelését és optimalizálását. A SQL Server a célként megadott platformot érintő nagyméretű áttelepítések esetén [itt](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)találhat javaslatokat. Az áttelepítési figyelő/SWPM megközelítését arra az esetre használhatja, ha nincs szükség összetett kiadási frissítésre vagy [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) folyamatra, ha kombinálja az áttelepítést egy SAP kiadási frissítéssel, és teljesíti a forrás-és a cél dokumentálva, például az [2,0-os összegű SP03-adatbázis-áttelepítési lehetőségben (DMO)](https://launchpad.support.sap.com/#/notes/2631152). 
   1.  Exportálás a forrásba, a fájlok feltöltése az Azure-ba és az importálási teljesítmény.  Az Exportálás és az importálás közötti átfedés maximalizálása
   2.  A cél és a célként megadott platform közötti adatbázis-mennyiség kiértékelése az infrastruktúra méretének megfelelően    
   3.  Az időzítés ellenőrzése és optimalizálása 
3. Technikai ellenőrzés 
   1. Virtuális gépek típusai
      1.  Ellenőrizze az SAP-támogatási megjegyzések, SAP HANA hardverek és az SAP PAM erőforrásait, és győződjön meg arról, hogy nem történt változás az Azure-beli támogatott virtuális gépeken, a virtuálisgép-típusok támogatott operációsrendszer-kiadásai, valamint a támogatott SAP-és adatbázis-kezelői kiadások
      2.  Ellenőrizze újra az alkalmazás méretezését és az Azure-ban üzembe helyezett infrastruktúrát. Meglévő alkalmazások áthelyezésekor gyakran a szükséges SAP-t származtathatja a használt infrastruktúrából és az [SAP teljesítményteszt](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) weboldaláról, és összehasonlíthatja az SAP-támogatási megjegyzésekben felsorolt NEDV-számokkal [#1928533](https://launchpad.support.sap.com/#/notes/1928533). Vegye figyelembe [ezt a cikket](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) is
      3.  Értékelje ki és tesztelje az Azure-beli virtuális gépek méretezését a tervezési fázisban kiválasztott különböző virtuálisgép-típusok maximális tárolási teljesítményével és hálózati teljesítményével kapcsolatban. Az itt található adatértékek a következő címen találhatók:
          1.  [A Windows rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). Fontos figyelembe venni a nem **gyorsítótárazott lemez maximális átviteli sebességét** a méretezéshez
          2.  [A Linux rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Fontos figyelembe venni a nem **gyorsítótárazott lemez maximális átviteli sebességét** a méretezéshez
   2. Storage
      1.  Az [Azure standard SSD Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) -t minimálisan használhatja az SAP-alkalmazási rétegeket és a nem teljesítményre érzékeny adatbázis-kezelő rendszerekhez tartozó virtuális gépek számára
      2.  Javasoljuk, hogy az [Azure standard HDD-lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd) általános használatát ne használja
      2.  Az [Azure Premium Storage](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) használata bármely olyan adatbázis-kezelő virtuális gép számára, amely távoli teljesítményre érzékeny
      2.  Az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) használata
      3.  Az M-sorozatú adatbázis-kezelői naplók Azure írásgyorsító használhatók. Vegye figyelembe az írási gyorssegéd korlátozásait és használatát a [írásgyorsítóban](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator) dokumentált módon.
      4.  A különböző adatbázis-kezelői típusok esetében tekintse meg az [általános SAP-hez kapcsolódó adatbázis](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) -kezelői dokumentációt és az adatbázis-kezelői specifikus dokumentumait.
      5.  SAP HANA további részleteket az Azure-beli [SAP HANA infrastruktúra-konfigurációk és-műveletek](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) dokumentációjában talál.
      6.  Az eszköz AZONOSÍTÓjának használatával soha ne csatlakoztassa az Azure-beli adatlemezeket egy Azure-beli linuxos virtuális géphez. Ehelyett használja az univerzálisan egyedi azonosítót (UUID). Ügyeljen arra, hogy az Azure-adatlemezek csatlakoztatásához grafikus eszközöket használjon, például:. Ellenőrizze az/etc/fstab bejegyzéseit, és győződjön meg arról, hogy a lemezek csatlakoztatva vannak az UUID használatával.
          1.  További információk találhatók [Itt](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)
   3. Hálózat
      1.  Tesztelje és értékelje ki a VNet-infrastruktúrát és az SAP-alkalmazások elosztását a különböző Azure-beli virtuális hálózatokon keresztül vagy azokon belül.
          1.  Értékelje ki a hub és a küllős virtuális hálózati architektúra, illetve a-szegmentálás megközelítését egyetlen Azure-beli virtuális hálózaton belül
              1.  Az [Azure-virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)közötti adatcsere miatti költségek. A költségek ellenőrzési [Virtual Network díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/)
              2.  Az Azure-beli virtuális hálózatok közötti társítás gyors leválasztásának előnye a NSG módosítása a virtuális hálózaton belüli alhálózat elkülönítéséhez olyan esetekben, amikor a virtuális hálózat alhálózatán üzemeltetett alkalmazások vagy virtuális gépek biztonsági kockázatot jelentenek
              3.  A hálózati forgalom központi naplózása és naplózása a helyszíni, a külvilág és az Azure-ban létrehozott virtuális adatközpont között
          2.  Az SAP-alkalmazás réteg és az SAP adatbázis-kezelő réteg közötti adatelérési út kiértékelése és tesztelése. 
              1.  Az Azure-beli [hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) az SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP-rendszerek adatbázis-kezelő rétege közötti kommunikációs útvonalon való elhelyezése egyáltalán nem támogatott
              2.  Az SAP-alkalmazás rétegének és az SAP adatbázis-kezelők különböző Azure-beli virtuális hálózatokban való elhelyezése nem támogatott
              3.  Az [Azure ASG és a NSG szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview) az SAP-alkalmazási réteg és az SAP adatbázis-kezelő réteg közötti útvonalak definiálásához támogatottak.
          3.  Győződjön meg arról, hogy az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van az SAP-alkalmazás rétegében és az SAP adatbázis-kezelő rétegében használt virtuális gépeken. Ne feledje, hogy az Azure-ban a gyorsított hálózatkezelés támogatásához különböző operációsrendszer-szintekre van szükség:
              1.  Windows Server 2012 R2 vagy újabb kiadás
              2.  SUSE Linux 12 SP3 vagy újabb kiadás
              3.  RHEL 7,4 vagy újabb kiadás
              4.  Oracle Linux 7,5. A RHCKL kernel használatával a kiadásnak 3.10.0-862.13.1. el7 kell lennie. Az Oracle UEK kernel 5. kiadásának használata kötelező
          4.   Tesztelje és értékelje ki a hálózati késést az SAP Application Layer VM és az adatbázis-kezelő virtuális gép között az SAP-támogatás Megjegyzés [#500235](https://launchpad.support.sap.com/#/notes/500235) és az SAP-támogatási Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Értékelje ki az eredményeket az SAP-támogatási Megjegyzés hálózati késési útmutatója [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késésnek közepes és jó tartományban kell lennie. Kivételek vonatkoznak a virtuális gépek és a HANA nagyméretű példányok közötti adatforgalomra az [itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) dokumentált módon.
          5.   Győződjön meg arról, hogy a ILB üzemelő példányok a közvetlen kiszolgáló visszaadásának használatára vannak beállítva. Ez a beállítás csökkenti a késést azokban az esetekben, amikor az Azure-ILB a magas rendelkezésre állású konfigurációkhoz használják az adatbázis-kezelő rétegben
          6.   Ha Azure Load Balancert használ a Linux vendég operációs rendszerekkel együtt, akkor ellenőrizze, hogy a **net. IPv4. TCP _timestamps** a Linux hálózati paraméter értéke **0**. Az SAP Megjegyzés régebbi verzióiban [#2382421](https://launchpad.support.sap.com/#/notes/2382421). Az SAP-Megjegyzés addig frissül, hogy tükrözze azt a tényt, hogy a paramétert 0-ra kell beállítani, hogy az Azure Load Balancerrel együtt működjön.
          7.   Vegye fontolóra az [Azure Proximity-elhelyezési csoport](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) használatát az [Azure Proximity-elhelyezési csoportok](sap-proximity-placement-scenarios.md) című cikkben leírtak szerint, hogy optimális hálózati késést biztosítson az SAP-alkalmazások számára a legoptimálisabb hálózati késés eléréséhez.
   4. Magas rendelkezésre állású és vész-helyreállítási üzemelő példányok. 
      1. Ha az SAP-alkalmazás rétegét egy adott Azure rendelkezésre állási zóna meghatározása nélkül helyezi üzembe, győződjön meg arról, hogy minden olyan virtuális gép, amely az SAP-példányt vagy az egyetlen SAP-rendszer közbenső példányait futtatja egy [rendelkezésre állási csoportba](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability). 
         1.   Ha nem igényel magas rendelkezésre állást az SAP központi szolgáltatásai és az adatbázis-kezelő rendszer számára, akkor ezek a virtuális gépek az SAP-alkalmazás rétegével megegyező rendelkezésre állási csoportba helyezhetők.
      2. Ha a magas rendelkezésre álláshoz passzív replikákkal biztosít hozzáférést az SAP központi szolgáltatásaihoz és az adatbázis-kezelő réteghez, a két csomóponttal rendelkezik az SAP központi szolgáltatásaihoz egy különálló rendelkezésre állási készletben és egy másik rendelkezésre állási csoport két adatbázis-kezelő csomópontjában.
      3. Ha Azure Availability Zonesbe helyez üzembe, nem használhat rendelkezésre állási csoportokat. Azonban meg kell győződnie arról, hogy az aktív és a passzív központi szolgáltatások csomópontjait két különböző Availability Zones helyezi üzembe, ami a zónák közötti legkisebb késést mutatja.
         1.   Ne feledje, hogy az [Azure standard Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) -t kell használnia ahhoz, hogy Windows vagy pacemaker feladatátvételi fürtöket hozzon létre az adatbázis-kezelő szolgáltatás és az SAP központi szolgáltatások rétege számára Availability Zoneson keresztül. Az alapszintű [Load Balancer](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview#skus) nem használhatók a zónákhoz való központi telepítéshez 
   5. Időtúllépési beállítások
      1. Ellenőrizze a különböző SAP-példányok SAP NetWeaver fejlesztői nyomkövetését, és győződjön meg arról, hogy a sorba helyezni-kiszolgáló és az SAP-munkafolyamatok közötti kapcsolat nem szakad meg. A következő két beállításjegyzék-paraméter beállításával elkerülhetők a kapcsolatok.
         1.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000 – lásd még [Ez a cikk](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10))
         2.   HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000 – lásd még [Ez a cikk](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)) 
      2. Ha el szeretné kerülni, hogy az Azure-ban üzembe helyezett SAP-alkalmazási rétegek és az Azure-ban telepített SAP-alkalmazások rétegei között ne legyen időtúllépés a GUI-ban, akkor győződjön meg arról, hogy a következő paraméterek vannak beállítva az alapértelmezett. PFL vagy a példány profiljában:
         1.   rdisp/keepalive_timeout = 3600
         2.   rdisp/életben tartás = 20
      3. Az SAP-sorba helyezni folyamat és az SAP-workprocesse közötti létesített kapcsolatok megzavarásának megelőzése érdekében a enque/encni/set_so_keepalive paramétert "true" értékre kell állítani. Lásd még: [SAP-megjegyzés #2743751](https://launchpad.support.sap.com/#/notes/2743751)  
      3. Ha Windows feladatátvevő fürtöt használ, győződjön meg arról, hogy a nem válaszoló csomópontokra való reagálás időpontja helyesen van beállítva az Azure-hoz. A feladatátvételi [fürt hálózati küszöbértékeit](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) felsoroló Microsoft-cikk felsorolja a paramétereket és azt, hogy ezek milyen hatással vannak a feladatátvételre. Feltételezve, hogy a fürtcsomópontok ugyanabban az alhálózatban találhatók, a következő paramétereket kell módosítani:
         1.   SameSubNetDelay = 2000
         2.   SameSubNetThreshold = 15
         3.   RoutingHistorylength = 30
4. Magas rendelkezésre állású és vész-helyreállítási eljárások tesztelése
   1. Feladatátvételi helyzetekben szimulálhatja a virtuális gépek (Windows vendég operációs rendszer) leállítását, vagy az operációs rendszerek pánik módba helyezését (Linux vendég operációs rendszer), hogy kiderítse, hogy a feladatátvételi konfigurációk a tervezett módon működnek-e. 
   2. A feladatátvétel végrehajtásához szükséges idő mérése. Ha az idő túl sokáig tart, vegye figyelembe a következőket:
      1.   SUSE Linux esetén a feladatátvétel felgyorsításához használja a SBD-eszközöket az Azure kerítési ügynök helyett.
      2.   SAP HANA esetén, ha az Újratöltés túl sokáig tart, érdemes több tárolási sávszélességet kiépíteni
   3. A biztonsági mentési/visszaállítási folyamat és az időzítés tesztelése, ha szükséges. Ügyeljen arra, hogy ne csak a biztonsági mentési idők legyenek elegendőek. Tesztelje a visszaállítást is, és végezze el a visszaállítási tevékenységek időzítését. Győződjön meg arról, hogy a visszaállítási időpontok a RTO SLA-n belül vannak, ahol a RTO adatbázis vagy virtuális gép visszaállítási folyamatán alapul.
   4. A DR funkció és az architektúra közötti tesztelés
5. Biztonsági ellenőrzések
   1.  Tesztelje a megvalósított Azure szerepköralapú hozzáférés-(RBAC-) architektúra érvényességét. A cél az, hogy elkülönítse és korlátozza a különböző csapatok hozzáférését és engedélyeit. Az SAP-csapat tagjai például telepíthetnek virtuális gépeket, és az Azure Storage-ban lévő lemezeket egy adott Azure-beli virtuális hálózatba oszthatják ki. Az SAP-alapú csapat azonban nem hozhat létre saját virtuális hálózatokat, és nem módosíthatja a meglévő virtuális hálózatok beállításait. A hálózati csapat tagjai a másik oldalon nem telepíthetnek virtuális hálózatokat, ahol az SAP-alkalmazás és az adatbázis-kezelő virtuális gépek futnak. A hálózati csapat tagjai sem változtathatják meg a virtuális gépek attribútumait, vagy akár virtuális gépeket vagy lemezeket is törölhetnek.  
   2.  Ellenőrizze, hogy a [NSG és az ASC](https://docs.microsoft.com/azure/virtual-network/security-overview) szabályok a várt módon működnek-e, és védi-e a védett erőforrásokat
   3.  Győződjön meg arról, hogy az összes titkosítani kívánt erőforrás titkosítva van. A tanúsítványok biztonsági mentésére, tárolására és elérésére szolgáló folyamatok meghatározása és végrehajtása, valamint a titkosított entitások visszaállítása. 
   4.  [Azure Disk Encryption](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) és/vagy operációsrendszer-lemezek használata, ha lehetséges, az operációs rendszer támogatási pontjáról
   5.  Győződjön meg arról, hogy a rendszer nem használ túl sok titkosítási réteget. Az Azure Disk Encryption használatának korlátozott értelme, majd az adatbázis-kezelő rendszer átlátható adatbázis-titkosítási módszereinek egyike.
6. Teljesítmény tesztelése
   1.  SAP-nyomkövetés és-mérések alapján, az SAP-ben az első 10 online jelentés összevetése a jelenlegi megvalósításig 
   2.  SAP-nyomkövetés és-mérések alapján az SAP-ben az első 10 batch-feladat összehasonlítása az aktuális megvalósításig, ahol alkalmazható 
   3.  SAP-nyomkövetés és-mérések alapján, az SAP-rendszerbe való adatátvitelek összehasonlításával. Olyan felületekre koncentrálhat, amelyekkel biztos lehet abban, hogy az átvitel mostantól különböző helyszíneken zajlik, például a helyszínről az Azure-ba 


## <a name="non-production-phase"></a>Nem éles fázis 
Ebben a fázisban feltételezzük, hogy sikeres próbaüzem vagy PoC után a nem éles környezetben üzemelő SAP-rendszereket üzembe helyezi az Azure-ban. A fogalmak igazolásával kapcsolatos összes megismerést és tapasztalatot az ilyen üzembe helyezéshez kell igazítani. A PoC-ban felsorolt feltételek és lépések az ilyen típusú üzemelő példányokon is érvényesek. Ebben a fázisban általában fejlesztési rendszerek, egység-tesztelési rendszerek és üzleti regressziós tesztek rendszereit telepíti az Azure-ba. Azt javasoljuk, hogy egy SAP-alkalmazási sorban legalább egy nem éles rendszernek a teljes magas rendelkezésre állási konfigurációval kell rendelkeznie, mivel a jövőbeli éles rendszer lesz. A fázisban megfontolandó további lépések:  

1.  Mielőtt a régi platformról áthelyezi a rendszereket az Azure-ba, az erőforrás-használati adatokat, például a CPU-használatot, a tárolási teljesítményt és a IOPS adatokat gyűjt Különösen az adatbázis-kezelő réteg egységei, de az alkalmazási réteg egységei is. A hálózati és a tárolási késést is méri.
2.  Rögzítse a rendszerek rendelkezésre állási használati idejét. A cél annak megállapítása, hogy nem üzemi rendszereknek kell-e elérhetővé 7x24, vagy hogy vannak-e olyan nem üzemi rendszerek, amelyek egy hét vagy hónap bizonyos fázisaiban leállíthatók
3.  Tesztelje és határozza meg, hogy szeretne-e saját operációsrendszer-lemezképeket létrehozni az Azure-beli virtuális gépekhez, illetve hogy szeretne-e lemezképet használni az Azure lemezkép-katalógusában. Ha az Azure Galleryből rendszerképet használ, győződjön meg arról, hogy a megfelelő rendszerképet használja, amely az operációs rendszer gyártójával kapcsolatos támogatási szerződést tükrözi. Egyes operációsrendszer-gyártók esetében az Azure Galleries a saját licencek lemezképeit kínálja fel. Más operációsrendszer-lemezképek esetében az Azure által idézett ár tartalmazza a támogatást. Ha úgy dönt, hogy saját operációsrendszer-lemezképeket hoz létre, a következő cikkekben talál dokumentációt:
    1.  Az Azure-ban üzembe helyezett, Windows rendszerű virtuális gépek általánosított lemezképét a [jelen dokumentáció](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource) alapján hozhatja létre
    2.  Az Azure-ban üzembe helyezett Linux rendszerű virtuális gépek általánosított lemezképét a [jelen dokumentáció](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image) alapján hozhatja létre
3.  Ha a SUSE és a Red Hat Linux rendszerképeket az Azure virtuálisgép-katalógusból használja, akkor az Azure-beli virtuálisgép-katalógusban található linuxos gyártók által biztosított SAP-lemezképeket kell használnia.
4.  Győződjön meg arról, hogy megfelel az SAP-nak a Microsoft támogatási szerződéseit érintő támogatási követelményeinek. Az SAP-támogatási megjegyzésekben [#2015553](https://launchpad.support.sap.com/#/notes/2015553)található információk. A HANA nagyméretű példányai esetében tekintse meg a dokumentum-előkészítési [követelményeket](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Győződjön meg arról, hogy a megfelelő személyek bekérik a [tervezett karbantartási értesítéseket](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/), így kiválaszthatja az állásidőt és a virtuális gépek újraindítását időben
5.  A Microsoft-bemutatók Azure-dokumentációjának folyamatos megtekintése olyan csatornákon, mint például a [Channel9](https://channel9.msdn.com/) az üzembe helyezésekre alkalmazható új funkciók esetében
6.  Tekintse át az Azure-hoz kapcsolódó SAP-megjegyzéseket, például a támogatási Megjegyzés [#1928533](https://launchpad.support.sap.com/#/notes/1928533) az új VM SKU-hoz, vagy az újonnan támogatott operációs rendszer és az adatbázis-kezelő kiadás Hasonlítsa össze az új virtuálisgép-típusokat a korábbi virtuálisgép-típusokkal szemben a díjszabásban, hogy a virtuális gépeket a legjobb ár/teljesítmény aránysal tudja üzembe helyezni
7.  Ellenőrizze az SAP-támogatási megjegyzések, SAP HANA hardverek és az SAP PAM erőforrásait az Azure-beli támogatott virtuális gépeken, a támogatott operációsrendszer-kiadásokban a virtuális gépeken, valamint a támogatott SAP-és adatbázis-kezelői kiadásokban.
8.  [Itt](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) megtekintheti az új HANA Certified SKU-ket az Azure-ban, és összehasonlíthatja a díjszabást a tervezettek szerint, és végül megváltoztathatja az egységeket a jobb ár-teljesítmény arány 
9.  Az üzembe helyezési parancsfájlok az új virtuálisgép-típusok kihasználása és a használni kívánt Azure új funkcióinak beépítése
10. Az infrastruktúra üzembe helyezése után tesztelje és értékelje ki a hálózati késést az SAP Application Layer VM és az adatbázis-kezelő virtuális gép között az SAP-támogatás Megjegyzés [#500235](https://launchpad.support.sap.com/#/notes/500235) és az SAP-támogatási Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Értékelje ki az eredményeket az SAP-támogatási Megjegyzés hálózati késési útmutatója [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késésnek közepes és jó tartományban kell lennie. A kivételek a virtuális gépek és a HANA nagyméretű példányok közötti forgalomra vonatkoznak az [itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)dokumentált módon. Győződjön meg arról, hogy az [azure Virtual Machines adatbázis-kezelő rendszerbe állítása az SAP](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) -munkaterheléshez és [a SAP HANA infrastruktúra-konfigurációk és az Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) -beli műveletek tekintetében nem említett korlátozások egyikét alkalmazza a telepítésre
11. Győződjön meg arról, hogy a virtuális gépek üzembe helyezése a megfelelő [Azure Proximity-elhelyezési csoport](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) használatával történik az SAP-alkalmazásokkal kapcsolatos [optimális hálózati késés](sap-proximity-placement-scenarios.md) érdekében.
11. Az összes többi ellenőrzés végrehajtása a számítási feladatok fázisának igazolása előtt a munkaterhelés alkalmazása előtt
12. A számítási feladatok alkalmazása esetén jegyezze fel ezeknek a rendszereknek az erőforrás-felhasználását az Azure-ban, és hasonlítsa össze a régi platformról kapott rekordokkal. A jövőbeli központi telepítések VM-méretezésének módosítása, ha úgy látja, hogy nagyobb eltérések vannak. Ne feledje, hogy a virtuális gépek leépítésének, tárolásának és hálózati sávszélességének csökkentése a következők esetén is csökkenni fog:
    1.  [A Windows rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json). 
    2.  [A Linux rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. A rendszer másolási funkcióinak és folyamatainak használata. A cél az, hogy megkönnyítse egy fejlesztési rendszer vagy egy tesztelési rendszer másolását, így a projektcsapat új rendszerek gyors elérését teszi elérhetővé. Tekintse át az [SAP láma](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) eszközt az ilyen feladatok végrehajtásához.
14. Optimalizálja és élesítse a csapat Azure-beli szerepkör-alapú hozzáféréseit, engedélyeit és folyamatait, hogy megbizonyosodjon róla, hogy az egyik oldalon van-e a kötelessége elkülönítése. A másik oldalon szeretné, hogy az összes csapat engedélyezve legyen az Azure-infrastruktúrában feladataik elvégzésére.
15. Magas rendelkezésre állású és vész-helyreállítási eljárások gyakorlása, tesztelése és dokumentálása, amelyek lehetővé teszik, hogy a munkatársak végrehajtsák ezeket a feladatokat. Azonosítsa a hiányosságokat és alkalmazkodjon az új Azure-funkciókhoz, amelyeket integrál az üzembe helyezésbe

 
## <a name="production-preparation-phase"></a>Éles előkészítési fázis 
Ebben a fázisban szeretné összegyűjteni a nem éles környezetben üzemelő példányok összes tapasztalatát és megismerését, és alkalmazza őket a jövőbeli éles környezetekben. Emellett az előző fázisokra is elő kell készítenie a jelenlegi üzemeltetési hely és az Azure közötti adatátvitel folyamatát. 

1.  Az Azure-ba való áttérés előtt az éles rendszerek szükséges SAP-kiadási frissítéseinek használata
2.  Fogadja el az üzleti tulajdonosok azon működési és üzleti teszteket, amelyeket az éles rendszer áttelepítése után kell elvégezni
    1.  Győződjön meg arról, hogy az összes teszt végrehajtása a jelenlegi üzemeltetési helyen található forrásoldali rendszerekkel történik. El szeretné kerülni a tesztek elvégzését a rendszer első alkalommal történő áthelyezése után
2.  Tesztelje az éles környezetbe történő áttelepítési folyamatot Ha nem helyezi át az összes éles rendszert az Azure-ba ugyanabban az időkereten belül, olyan éles rendszerek csoportjait kell létrehoznia, amelyeknek ugyanabban az üzemeltetési helyen kell lenniük. Az adatáttelepítés gyakorlása és tesztelése. Gyakori módszerek listája, például:
    1.  Az adatbázis-tartalmak SQL Server AlwaysOn, a HANA rendszerreplikációval vagy a naplóba való beküldéssel, valamint az Azure-beli adatbázisok tartalmának szinkronizálásával, például a Backup/Restore használatával
    2.  A kisebb adatbázisok biztonsági mentésének és visszaállításának használata
    3.  Az SAP SWPM eszközön megvalósított SAP Migration monitor használata heterogén Migrálás elvégzéséhez
    4.  Ha egy SAP-kiadási frissítéssel kell kombinálni, használja az [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) folyamatot. Ne feledje, hogy a forrás és a cél-adatbázis közötti összes kombináció nem támogatott. További információt a DMO különböző kiadásaira vonatkozó SAP-támogatási megjegyzésekben találhat. Például: adatbázis-áttelepítési [beállítás (DMO), SUM 2,0 SP04](https://launchpad.support.sap.com/#/notes/2644872)
    5.  Ellenőrizze, hogy az interneten vagy a ExpressRoute keresztül történő adatátvitel jobb-e az átviteli sebességben abban az esetben, ha biztonsági mentéseket vagy SAP-exportálási fájlokat kell áthelyeznie. Ha az interneten keresztül helyezi át az adatátvitelt, előfordulhat, hogy módosítania kell néhány NSG-vagy ASG-szabályt, amelyet a jövőbeli üzemi rendszerek esetében meg kell hoznia.
3.  Mielőtt a régi platformról áthelyezi a rendszereket az Azure-ba, az erőforrás-használati adatokat, például a CPU-használatot, a tárolási teljesítményt és a IOPS adatokat gyűjt Különösen az adatbázis-kezelő réteg egységei, de az alkalmazási réteg egységei is. A hálózati és a tárolási késést is méri.
4.  Ellenőrizze az SAP-támogatási megjegyzések, SAP HANA hardverek és az SAP PAM erőforrásait az Azure-beli támogatott virtuális gépeken, a támogatott operációsrendszer-kiadásokban a virtuális gépeken, valamint a támogatott SAP-és adatbázis-kezelői kiadásokban. 
4.  Az üzembe helyezési parancsfájlok adaptálása a virtuálisgép-típusokon és az Azure-funkciókon eldöntött legújabb módosításokkal
5.  Az infrastruktúra és az alkalmazás üzembe helyezése után a következő ellenőrzéseket végezheti el a hitelesítés érdekében:
    1.  A megfelelő virtuálisgép-típusok üzembe helyezése a megfelelő attribútumokkal és tárolási méretekkel
    2.  Győződjön meg arról, hogy a virtuális gépek a megfelelő és a kívánt operációsrendszer-kiadásokkal és-javításokkal rendelkeznek, és egységesek
    3.  Győződjön meg arról, hogy a virtuális gépek megerősítése kötelező és egységes
    4.  Győződjön meg arról, hogy a megfelelő alkalmazás-kiadások és javítások telepítve és telepítve lettek
    5.  A virtuális gépek üzembe helyezése az Azure rendelkezésre állási csoportjaiba lett tervezve
    6.  Az Azure Premium Storage használták a késleltetésre érzékeny lemezekhez, vagy ha az [egyetlen VM 99,9%](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) -os SLA-ra van szükség
    7.  Az Azure-írásgyorsító helyes üzembe helyezésének keresése
        1.  Győződjön meg arról, hogy a virtuális gépen, a tárolóhelyeken vagy a Stripe-készletekben megfelelően lett létrehozva az Azure írásgyorsító támogatását igénylő lemezek között
            1.  [A szoftveres RAID konfigurálásának engedélyezése Linux rendszeren](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
            2.  [Az LVM konfigurálása Linux rendszerű virtuális gépen az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    8.  Az [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) használatát kizárólag
    9.  A virtuális gépek üzembe helyezése a megfelelő rendelkezésre állási csoportokban és Availability Zones
    10. Győződjön meg arról, hogy az [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van az SAP-alkalmazás rétegében és az SAP adatbázis-kezelő rétegében használt virtuális gépeken.
    11. Az Azure-beli [hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) nem helyezhetők el az SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP-rendszerek adatbázis-kezelői rétegének kommunikációs útvonalán
    12. A ASG és a NSG szabályok lehetővé teszik a kommunikációt a kívánt módon, illetve a tervezett és a kommunikáció letiltását
    13. A korábban leírt időtúllépési beállítások helyesen lettek beállítva.
    14. Győződjön meg arról, hogy a virtuális gépek üzembe helyezése a megfelelő [Azure Proximity-elhelyezési csoport](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) használatával történik az SAP-alkalmazásokkal kapcsolatos [optimális hálózati késés](sap-proximity-placement-scenarios.md) érdekében.
    15. Tesztelje és értékelje ki a hálózati késést az SAP Application Layer VM és az adatbázis-kezelő virtuális gép között az SAP-támogatás Megjegyzés [#500235](https://launchpad.support.sap.com/#/notes/500235) és az SAP-támogatási Megjegyzés [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). Értékelje ki az eredményeket az SAP-támogatási Megjegyzés hálózati késési útmutatója [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E). A hálózati késésnek közepes és jó tartományban kell lennie. Kivételek vonatkoznak a virtuális gépek és a HANA nagyméretű példányok közötti adatforgalomra az [itt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance) dokumentált módon.
    15. Győződjön meg arról, hogy szükség van-e a titkosítás üzembe helyezésére, és szükség van-e a titkosítási módszerre
    16. Annak megállapítása, hogy a felületek és más alkalmazások kapcsolódhatnak-e az újonnan üzembe helyezett infrastruktúrához
6.  Hozzon létre egy forgatókönyvet az Azure tervezett karbantartására való reagáláshoz. A tervezett karbantartás esetén a rendszer és a virtuális gépek újraindítási sorrendjének meghatározása
    

## <a name="go-live-phase"></a>Go élő fázis
A Go-Live fázisban meg kell győződnie arról, hogy a korábbi fázisokban milyen forgatókönyveket fejlesztett ki. Hajtsa végre a tesztelt és betanított lépéseket. Ne fogadja el az utolsó perces módosításokat a konfigurációk és a folyamat során. Ezen kívül a következő mértékeket alkalmazza:

1. Ellenőrizze, hogy a Azure Portal figyelési és egyéb figyelési eszközök működnek-e.  Az ajánlott eszközök a perfmon (Windows) vagy a SAR (Linux): 
    1.  CPU-számlálók 
        1.  Átlagos CPU-idő – összesen (az összes processzor)
        2.  Átlagos CPU-idő – minden egyes processzor (így 128 processzor a m128 virtuális gépen)
        3.  CPU-idő kernele – minden egyes processzor
        4.  CPU-idő felhasználója – minden egyes processzor
    5.  Memory (Memória) 
        1.  Szabad memória
        2.  Memória lap/mp
        3.  Memória (oldal/mp)
    4.  Lemez 
        1.  Olvasási sebesség (kb/s) – egyedi lemez 
        2.  Másodpercenkénti olvasási sebesség/mp – egyedi lemez
        3.  Lemez olvasása MS/Read – egyéni lemezen
        4.  Lemez írása kb/s – egyéni lemezre 
        5.  Lemez írása/mp – egyedi lemez
        6.  Lemez írása MS/Read – egyéni lemez
    5.  Network (Hálózat) 
        1.  Hálózati csomagok/s
        2.  Kimenő hálózati csomagok másodpercenként
        3.  Hálózati kb/s
        4.  Kimenő hálózati sebesség (kb/s) 
2.  Az adatok áttelepítése után végezze el az összes ellenőrző tesztet, amelyet a vállalat tulajdonosai elfogadtak. Csak akkor fogadja el az ellenőrzési teszt eredményeit, ha az eredeti forrásoldali rendszerek eredményei vannak.
3.  Annak megállapítása, hogy a felületek működnek-e, és hogy más alkalmazások képesek-e kommunikálni az újonnan telepített éles rendszerekkel
4.  Az átviteli és a javítási rendszerek ellenőrzése SAP Transaction STM
5.  Adatbázis biztonsági mentése a rendszer éles környezetbe való kiadása után
6.  VIRTUÁLIS gépek biztonsági mentésének elvégzése az SAP-alkalmazás rétegbeli virtuális gépei számára a rendszer éles környezetbe való kiadása után
7.  Azon SAP-rendszerek esetében, amelyek nem részei a jelenlegi Go-Live fázisnak, de kommunikálnak az Azure-ba a Go-Live fázisban áthelyezett SAP-rendszerekkel, alaphelyzetbe kell állítania az állomásnév pufferét a SM51-ben. Ez a lépés megkerüli az Azure-ba áthelyezett alkalmazás-példányok neveivel társított régi gyorsítótárazott IP-címeket.  


## <a name="post-production"></a>Utómunka
Ebben a fázisban minden a rendszer figyelésével, működésével és felügyeletével foglalkozik. SAP-szempontból a régi üzemeltetési hellyel elvégezhető szokásos feladatok érvényesek. Az Azure-specifikus feladatok a következők:

1. Azure-számlák elemzése nagy díjszabású rendszerekhez
2. Az ár/teljesítmény hatékonyságának optimalizálása a virtuális gép és a tároló oldalán
3. Az időrendszerek optimalizálása leállítható  


## <a name="next-steps"></a>További lépések
Olvassa el a dokumentációt:

- [Azure Virtual Machines az SAP NetWeaver tervezése és megvalósítása](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Azure Virtual Machines üzembe helyezés az SAP NetWeaver-ben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

