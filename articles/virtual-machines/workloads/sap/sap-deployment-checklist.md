---
title: SAP-munkaterhelés-tervezés és üzembe helyezés ellenőrzőlista | Microsoft dokumentumok
description: Ellenőrzőlista az SAP-számítási feladatok Azure-ba történő üzembe helyezéséhez és a számítási feladatok üzembe helyezéséhez
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
ms.date: 02/13/2020
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7b8c1b0bcc74d73f1f869972488ba7c5dfe610d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80060066"
---
# <a name="sap-workloads-on-azure-planning-and-deployment-checklist"></a>SAP-munkaterhelések az Azure-ban: tervezési és üzembe helyezési ellenőrzőlista

Ez az ellenőrzőlista az SAP NetWeaver, S/4HANA és Hybris alkalmazásokat szolgáltatásként az Azure-infrastruktúrába áthelyező ügyfelek számára készült. A projekt időtartama alatt az ügyfélnek és/vagy az SAP-partnernek át kell tekintenie az ellenőrzőlistát. Fontos megjegyezni, hogy az ellenőrzések nagy része a projekt elején és a tervezési fázisban fejeződik be. A központi telepítés után a telepített Azure-infrastruktúrán vagy az SAP-szoftverkiadásokon végrehajtott egyszerű módosítások bonyolulttá válhatnak.

Tekintse át az ellenőrzőlistát a projekt legfontosabb mérföldköveinél. Ezzel lehetővé teszi a kis problémák észlelését, mielőtt azok nagy problémákká válnak. Annyi ideje is lesz, hogy újratervezze és tesztelje a szükséges módosításokat. Ne tekintse ezt az ellenőrzőlistát teljesnek. A helyzettől függően előfordulhat, hogy még több ellenőrzést kell végrehajtania.

Az ellenőrzőlista nem tartalmazza az Azure-tól független feladatokat. Például az SAP-alkalmazás felületek módosítása során az Azure platformra vagy egy tárhelyszolgáltatóra való áttérés során.

Ez az ellenőrzőlista már telepített rendszerekhez is használható. Új funkciók, például a Write Accelerator és a Availability Zones, és az új virtuálisgép-típusok a telepítés óta adhatók hozzá. Ezért érdemes rendszeresen áttekinteni az ellenőrzőlistát, hogy biztosan tisztában legyen az Azure platform új funkcióival.

## <a name="project-preparation-and-planning-phase"></a>A projekt előkészítési és tervezési fázisa
Ebben a fázisban megtervezi az SAP-számítási feladatok áttelepítését az Azure platformra. Legalább ebben a fázisban létre kell hoznia a következő dokumentumokat, és meg kell határoznia és meg kell vitatnia az áttelepítés következő elemeit:

1. Magas szintű tervezési dokumentum. Ennek a dokumentumnak a következőket kell tartalmaznia:
    - Az SAP-összetevők és -alkalmazások aktuális leltára, valamint az Azure célalkalmazás-készlete.
    - Felelősség-hozzárendelési mátrix (RACI), amely meghatározza az érintett felek felelősségi körét és hozzárendeléseit. Kezdje magas szinten, és dolgozzon részletesebb szinteken a tervezés és az első üzembe helyezések során.
    - Magas szintű megoldásarchitektúra.
    - A döntés, hogy mely Azure-régiók üzembe helyezését. Tekintse meg [az Azure-régiók listáját.](https://azure.microsoft.com/global-infrastructure/regions/) Ha meg szeretné tudni, hogy az egyes régiókban mely szolgáltatások érhetők el, olvassa el a [Termékek régiónként elérhető](https://azure.microsoft.com/global-infrastructure/services/).
    - Hálózati architektúra a helyszíni azure-hoz való csatlakozáshoz. Ismerkedjen meg az [Azure virtuális adatközpont-tervével.](https://docs.microsoft.com/azure/architecture/vdc/)
    - Biztonsági alapelvek a nagy hatású üzleti adatok Azure-beli futtatásához. Az adatbiztonságról az [Azure biztonsági dokumentációjával](https://docs.microsoft.com/azure/security/)ismerkedjen meg.
2.  Műszaki tervezési dokumentum. Ennek a dokumentumnak a következőket kell tartalmaznia:
    - A megoldás blokkdiagramja.
    - A számítási, tárolási és hálózati összetevők méretezése az Azure-ban. Az Azure-beli virtuális gépek SAP-méretezése az [SAP támogatási #1928533.](https://launchpad.support.sap.com/#/notes/1928533)
    - Üzletmenet-folytonosság i.
    - Részletes információk az operációs rendszer, az adatbázis, a kernel és az SAP támogatási csomagverzióiról. Nem feltétlenül igaz, hogy az SAP NetWeaver vagy az S/4HANA által támogatott minden operációs rendszer-kiadás támogatott az Azure virtuális gépeken. Ugyanez igaz a DBMS kiadásokra is. Ellenőrizze a következő forrásokigazításához, és szükség esetén frissítse az SAP-kiadások, DBMS-kiadások és operációs rendszer-kiadások az SAP és az Azure-támogatás biztosítása érdekében. Az SAP és az Azure által támogatott kiadási kombinációkkal kell rendelkeznie, hogy teljes körű támogatást kapjon az SAP-tól és a Microsofttól. Szükség esetén meg kell terveznie egyes szoftverösszetevők frissítését. A támogatott SAP, OS és DBMS szoftverekkel kapcsolatos további részletek itt találhatók:
        - [Az SAP támogatási megjegyzése #1928533](https://launchpad.support.sap.com/#/notes/1928533). Ez a megjegyzés határozza meg az Azure virtuális gépeken támogatott minimális operációsrendszer-kiadásokat. Azt is meghatározza a minimális adatbázis-kiadások szükséges a legtöbb nem HANA-adatbázisok. Végül biztosítja az SAP-méretezés az SAP által támogatott Azure virtuális gép típusok.
        - [Az SAP támogatási megjegyzése #2015553](https://launchpad.support.sap.com/#/notes/2015553). Ez a megjegyzés az Azure storage és a Microsofttal szükséges támogatási kapcsolat támogatási szabályzatait határozza meg.
        - [Az SAP támogatási megjegyzése #2039619](https://launchpad.support.sap.com/#/notes/2039619). Ez a feljegyzés határozza meg az Oracle támogatási mátrix az Azure-hoz. Az Oracle csak a Windows t és az Oracle Linuxot támogatja vendég operációs rendszerként az Azure SAP-munkaterhelésekhez. Ez a támogatási utasítás az SAP-példányokat futtató SAP alkalmazásrétegre is vonatkozik. Az Oracle azonban nem támogatja az SAP Central Services magas rendelkezésre állását az Oracle Linux-ban pacemakeren keresztül. Ha az ASCS magas rendelkezésre állására van szüksége Oracle Linux rendszeren, a SIOS Protection Suite for Linux-ot kell használnia. Részletes SAP-minősítési adatokat az SAP támogatási [megjegyzése #1662610 - A Linux siOS Protection Suite támogatási részletei](https://launchpad.support.sap.com/#/notes/1662610)című témakörben talál. Windows esetén az SAP által támogatott SAP központi szolgáltatások SAP-alapú feladatátvételi fürtözési megoldása az Oracle-rel együtt támogatja az ADATBÁZIS-réteget.
        - [Az SAP támogatási megjegyzése #2235581](https://launchpad.support.sap.com/#/notes/2235581). Ez a megjegyzés az SAP HANA támogatási mátrixát biztosítja a különböző operációs rendszer-kiadásokhoz.
        - Az SAP HANA által támogatott Azure-virtuális gépek és [hana nagy példányok](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-overview-architecture) az [SAP-webhelyen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure)vannak felsorolva.
        - [SAP termék rendelkezésre állási mátrixa](https://support.sap.com/en/).
        - [SAP támogatási megjegyzés #2555629 - SAP HANA 2.0 dinamikus rétegezés – hipervizor és felhőtámogatás](https://launchpad.support.sap.com/#/notes/2555629)
        - [SAP támogatási megjegyzés #1662610 - A Linux siOS Protection Suite támogatási részletei](https://launchpad.support.sap.com/#/notes/1662610)
        - SAP-megjegyzések más SAP-specifikus termékekhez.     
    - Azt javasoljuk, hogy szigorú háromrétegű tervek SAP éles rendszerekhez. Nem javasoljuk az ASCS és/vagy DBMS és/vagy alkalmazáskiszolgálók kombinálását egy virtuális gépen. Az SAP Központi szolgáltatások többSID-fürtkonfigurációinak használatát az Azure-beli Windows vendég operációs rendszerek is támogatják. De ez a konfiguráció nem támogatott az SAP Central Services Linux operációs rendszereken az Azure-ban. A Windows vendég operációs rendszer forgatókönyvének dokumentációját az alábbi cikkekben találja:
        - [Az SAP ASCS/SCS példány több SID-példány magas rendelkezésre állása windows Server feladatátvételi fürtözéssel és megosztott lemezzel az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-shared-disk)
        - [Az SAP ASCS/SCS példány több SID-példány magas rendelkezésre állása windows Server feladatátvételi fürtözéssel és fájlmegosztással az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-ascs-ha-multi-sid-wsfc-file-share)
    - Magas rendelkezésre állás és vész-helyreállítási architektúra.
        - Az RTO és az RPO alapján határozza meg, hogy a magas rendelkezésre állású és a vész-helyreállítási architektúrának hogyan kell kinéznie.
        - A zónán belüli magas rendelkezésre állás érdekében tekintse meg, hogy a kívánt ADATBÁZIS-kezelő mit kínál az Azure-ban. A legtöbb DBMS-csomag szinkron, éles készenléti állapotú szinkron metódusokat kínál, amelyeket éles rendszerekhez ajánlunk. Tekintse meg az SAP-val kapcsolatos dokumentációt a különböző adatbázisokhoz, kezdve [az Azure virtual machines DBMS üzembe helyezésének szempontjaival az SAP-munkaterhelésekhez](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) és a kapcsolódó dokumentumokhoz.
           A Windows Server feladatátvételi fürtözésének használata megosztott lemezkonfigurációval az ADATBÁZIS-réteghez, például az [SQL Server számára leírtak](https://docs.microsoft.com/sql/sql-server/failover-clusters/windows/always-on-failover-cluster-instances-sql-server?view=sql-server-2017)szerint, nem támogatott. Ehelyett használjon olyan megoldásokat, mint például:
           - [SQL Server Always On](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-alwayson-availability-groups)
           - [Oracle Data Guard](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/configure-oracle-dataguard)
           - [HANA rendszer replikációja](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
        - Az Azure-régiók ban a vészhelyreállításhoz tekintse át a különböző DBMS-szállítók által kínált megoldásokat. Legtöbbjük támogatja az aszinkron replikációt vagy a naplószállítást.
        - Az SAP-alkalmazásréteg esetében határozza meg, hogy futtatja-e az üzleti regressziós tesztrendszereket, amelyek ideális esetben az éles környezetek replikái, ugyanabban az Azure-régióban vagy a VÉSZ-régióban. A második esetben az üzleti regressziós rendszer az éles környezetek VÉSZ-célként célozhatja meg.
        - Ha úgy dönt, hogy nem helyezi el a nem éles rendszerek a VÉSZ-hely, tekintse meg az Azure Site Recovery, mint egy módszer az SAP-alkalmazásréteg replikálása az Azure DR régióban. További információt a [Vészhelyreállítás beállítása többrétegű SAP NetWeaver alkalmazás telepítéséhez](https://docs.microsoft.com/azure/site-recovery/site-recovery-sap)című témakörben talál.
        - Ha úgy dönt, hogy az [Azure rendelkezésre állási zónáinak](https://docs.microsoft.com/azure/availability-zones/az-overview)használatával kombinált HADR-konfigurációt használ, ismerkedjen meg az Azure-régiókkal, ahol rendelkezésre állási zónák érhetők el. Is vegye figyelembe a korlátozásokat, amelyek a két rendelkezésre állási zóna közötti megnövekedett hálózati késések által bevezethető.  
3.  Az összes SAP-felület (SAP és nem SAP) leltára.
4.  Alapszolgáltatási szolgáltatások tervezése. Ennek a tervnek a következő elemeket kell tartalmaznia:
    - Active Directory és DNS-tervezés.
    - Hálózati topoológia az Azure-on belül és a különböző SAP-rendszerek hozzárendelése.
    - Szerepköralapú hozzáférési struktúra az [Azure-ban](https://docs.microsoft.com/azure/role-based-access-control/overview) infrastruktúrát és SAP-alkalmazásokat kezelő csapatok számára.
    - Erőforráscsoport-topológia.
    - [Címkézési stratégia](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-using-tags#tags-and-billing).
    - A virtuális gépek és más infrastruktúra-összetevők és/vagy logikai nevek elnevezési konvenciói.
5.  Microsoft Premier támogatási szerződés. Azonosítsa a Microsoft technical account managert (TAM). Az SAP támogatási követelményeit az [SAP támogatási #2015553.](https://launchpad.support.sap.com/#/notes/2015553)
6.  Az Azure-előfizetések száma és az előfizetések alapkvótája. [Támogatási kérelmek et nyithat az Azure-előfizetések kvótáinak](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request) szükség szerint történő növeléséhez.
7.  Adatcsökkentési és adatáttelepítési terv az SAP-adatok Azure-ba való áttelepítéséhez. Az SAP NetWeaver rendszerek, SAP-irányelvek, hogyan korlátozhatja a nagy mennyiségű adat mennyisége. Tekintse meg [ezt az SAP-útmutatót](https://wiki.scn.sap.com/wiki/download/attachments/247399467/DVM_%20Guide_7.2.pdf?version=1&modificationDate=1549365516000&api=v2) az SAP ERP-rendszerek adatkezeléséről. A tartalom egy része általában a NetWeaver és az S/4HANA rendszerekre is vonatkozik.
8.  Automatikus telepítési megközelítés. Az Azure-beli infrastruktúra-üzembe helyezések automatizálásának célja, hogy determinisztikus módon telepítsen, és determinisztikus eredményeket érjen el. Sok ügyfél használja a PowerShell vagy a CLI-alapú parancsfájlok. De vannak különböző nyílt forráskódú technológiák, amelyek segítségével üzembe helyezheti az Azure-infrastruktúrát az SAP-hoz, és akár SAP-szoftvert is telepíthet. A GitHubon példákat találhat:
    - [Automatikus SAP-telepítések az Azure Cloudban](https://github.com/Azure/sap-hana)
    - [SAP HANA telepítés](https://github.com/AzureCAT-GSI/SAP-HANA-ARM)
9.  Határozza meg a rendszeres tervezési és üzembe helyezési felülvizsgálati ütemet Ön, mint az ügyfél, a rendszerintegrátor, a Microsoft és más érintett felek között.

 
## <a name="pilot-phase-strongly-recommended"></a>Kísérleti fázis (erősen ajánlott)
 
A projekttervezés és -előkészítés előtt vagy alatt futtathat egy próbaüzemet. A kísérleti fázisban tesztelheti a tervezési és előkészítési fázisban készített megközelítéseket és terveket is. És akkor bővíteni a kísérleti fázisban, hogy ez egy igazi bizonyíték a koncepció.

Azt javasoljuk, hogy a kísérleti telepítés során állítsa be és érvényesítse a teljes HADR-megoldást és a biztonsági tervet. Egyes ügyfelek ebben a fázisban méretezhetőségi teszteket végeznek. Más ügyfelek az SAP sandbox-rendszerek üzembe helyezését használják kísérleti fázisként. Feltételezzük, hogy már azonosított egy rendszert, amelyet át szeretne telepíteni az Azure-ba a próbapilótához.

1. Optimalizálja az Azure-ba irányuló adatátvitelt. Az optimális választás nagymértékben függ az adott forgatókönyvtől. A helyszíni transzfer az [Azure ExpressRoute-on](https://azure.microsoft.com/services/expressroute/) keresztül a leggyorsabb, ha az ExpressRoute-kapcsolat elég sávszélességgel rendelkezik. Más esetekben az interneten keresztül történő átvitel gyorsabb.
2. Az adatok exportálását és importálását magában foglalja az SAP-platform áttelepítése, tesztelje és optimalizálja az exportálási és importálási fázisokat. Nagy áttelepítések, amelyekben az SQL Server a célplatform, [javaslatokat](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAP-OS-DB-Migration-to-SQL-Server-8211-FAQ-v6-2-April-2017/ba-p/368070)találhat. Az áttelepítésfigyelő/SWPM akkor használható, ha nincs szüksége kombinált kiadási frissítésre. Használhatja az [SAP DMO](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) folyamat, ha kombinálja az áttelepítés egy SAP-kiadás frissítése. Ehhez meg kell felelnie a forrás és a cél DBMS platform kombináció bizonyos követelményeinek. Ezt a folyamatot a [SUM 2.0 SP03 Adatbázis-áttelepítési beállítás (DMO) dokumentálja.](https://launchpad.support.sap.com/#/notes/2631152)
   1.  Exportáljon forrásba, exportálja a fájlok feltöltését az Azure-ba, és importálja a teljesítményt. Az exportálás és az importálás közötti átfedés maximalizálása.
   2.  Értékelje ki az adatbázis mennyiségét a cél- és célplatformokon az infrastruktúra méretezése céljából.
   3.  Az időzítés ellenőrzése és optimalizálása.
1. Technikai ellenőrzés.
   1. Virtuálisgép-típusok.
        - Tekintse át az erőforrásokat az SAP támogatási megjegyzések, az SAP HANA hardverkönyvtárban, és az SAP PAM újra. Győződjön meg arról, hogy nincsenek módosítások az Azure-hoz támogatott virtuális gépeken, az ilyen virtuálisgép-típusok támogatott operációsrendszer-kiadásokban, valamint a támogatott SAP- és DBMS-kiadásokban.
        - Ellenőrizze újra az alkalmazás és az Azure-ban üzembe helyezett infrastruktúra méretezését. Ha meglévő alkalmazásokat helyez át, gyakran levezetheti a szükséges SAPS-t a használt infrastruktúrából és az [SAP benchmark weboldalból,](https://www.sap.com/dmc/exp/2018-benchmark-directory/#/sd) és összehasonlíthatja az SAP támogatási megjegyzésében felsorolt [SAP-#1928533.](https://launchpad.support.sap.com/#/notes/1928533) Is tartsa [ezt a cikket az SAPS minősítések](https://techcommunity.microsoft.com/t5/Running-SAP-Applications-on-the/SAPS-ratings-on-Azure-VMs-8211-where-to-look-and-where-you-can/ba-p/368208) szem előtt tartva.
        - Értékelje ki és tesztelje az Azure-beli virtuális gépek méretezését a tervezési fázisban kiválasztott virtuálisgép-típusok maximális tárolási átviteli és hálózati átviteli teljesítményével kapcsolatban. Az adatokat itt találja:
           -  [Windowsos virtuális gépek méretei az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Fontos figyelembe venni a *maximális nem gyorsítótárazott lemez átviteli sebesség* méretezéséhez.
           -  [Linuxos virtuális gépek méretei az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) Fontos figyelembe venni a *maximális nem gyorsítótárazott lemez átviteli sebesség* méretezéséhez.
   2. Tárterület
        - Legalább az [Azure Standard SSD-tároló](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-ssd) t sap-alkalmazásrétegeket képviselő virtuális gépekhez és a nem teljesítményérzékeny KM-ek üzembe helyezéséhez használja.
        - Általában nem javasoljuk az [Azure Standard HDD-lemezek](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#standard-hdd)használatát.
        - Az [Azure Premium Storage-t](https://docs.microsoft.com/azure/virtual-machines/windows/disks-types#premium-ssd) bármely távolról teljesítményérzékeny DBMS-virtuális géphez használhatja.
        - Azure [által kezelt lemezek használata](https://azure.microsoft.com/services/managed-disks/).
        - Használja az Azure Write Accelerator-t az M sorozatú DBMS-naplómeghajtókhoz. Vegye figyelembe az Írásgyorsító korlátait és használatát az [Írásgyorsítóban](https://docs.microsoft.com/azure/virtual-machines/linux/how-to-enable-write-accelerator)dokumentált módon.
        - A különböző DBMS-típusok esetében ellenőrizze az [általános SAP-vonatkozású DBMS dokumentációt](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general) és a DBMS-specifikus dokumentációt, amelyre az általános dokumentum mutat.
        - Az SAP HANA-ról további információt az [SAP HANA infrastruktúra-konfigurációk és az Azure-beli műveletek című](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations)témakörben talál.
        - Soha ne csatlakoztassa az Azure-adatlemezeket egy Azure Linux-alapú virtuális géphez az eszközazonosító használatával. Ehelyett használja az univerzálisan egyedi azonosítót (UUID). Legyen óvatos, ha például grafikus eszközökkel csatlakoztatja az Azure-adatlemezeket. Ellenőrizze az /etc/fstab bejegyzéseket, és győződjön meg arról, hogy az UUID-t használják a lemezek csatlakoztatásához. További részleteket ebben a [cikkben](https://docs.microsoft.com/azure/virtual-machines/linux/attach-disk-portal#connect-to-the-linux-vm-to-mount-the-new-disk)talál.
   3. Hálózati.
        - Tesztelje és értékelje ki a virtuális hálózati infrastruktúrát és az SAP-alkalmazások elosztását a különböző Azure virtuális hálózatokon vagy azokon belül.
        -  Értékelje ki a küllős virtuális hálózati architektúra megközelítését vagy a mikroszegmentálási megközelítést egyetlen Azure virtuális hálózaton belül. Az értékelés alapja:
               1. A [társviszonyban álló Azure virtuális hálózatok](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)közötti adatcsere költségei . A költségekről a [Virtuális hálózat díjszabása](https://azure.microsoft.com/pricing/details/virtual-network/)című témakörben talál további információt.
               2. Az Azure virtuális hálózatok közötti társviszony-létesítés gyors leválasztásának előnyei, nem pedig a hálózati biztonsági csoport módosítása a virtuális hálózaton belüli alhálózat elkülönítése érdekében. Ez a kiértékelés olyan esetekre szolgál, amikor a virtuális hálózat alhálózatában üzemeltetett alkalmazások vagy virtuális gépek biztonsági kockázatot jelentenek.
                3. A helyszíni, a külvilág és az Azure-ban épített virtuális adatközpont közötti hálózati forgalom központi naplózása és naplózása.
        - Értékelje ki és tesztelje az SAP-alkalmazásréteg és az SAP DBMS-réteg közötti adatelérési utat.
            -  Az [Azure hálózati virtuális készülékek](https://azure.microsoft.com/solutions/network-appliances/) elhelyezése az SAP-alkalmazás és az SAP NetWeaver, Hybris vagy S/4HANA alapú SAP-rendszerek DBMS-rétege közötti kommunikációs útvonalon nem támogatott.
            -  Az SAP-alkalmazásréteg és az SAP DBMS elhelyezése a különböző Azure virtuális hálózatokban, amelyek nem társviszony-létesített nem támogatott nem támogatott.
            -  [Az alkalmazásbiztonsági csoport és a hálózati biztonsági csoport szabályai](https://docs.microsoft.com/azure/virtual-network/security-overview) segítségével útvonalakat határozhat meg az SAP-alkalmazásréteg és az SAP DBMS-réteg között.
        - Győződjön meg arról, hogy az [Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van az SAP-alkalmazásrétegben és az SAP DBMS-rétegben használt virtuális gépeken. Ne feledje, hogy az Azure-ban az accelerated networking támogatásához különböző operációsrendszer-szintekre van szükség:
            - Windows Server 2012 R2 vagy újabb.
            - SUSE Linux 12 SP3 vagy újabb.
            - RHEL 7.4 vagy újabb.
            - Oracle Linux 7.5. Ha az RHCKL kernelt használja, a 3.10.0-862.13.1.el7 felszabadításszükséges. Ha oracle UEK kernelt használsz, az 5- ös kiadás szükséges.
        - Tesztelje és értékelje ki a hálózati késést az SAP-alkalmazásréteg virtuális gépei és a DBMS virtuális gépek között az SAP támogatási megjegyzések [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)szerint. Értékelje ki az eredményeket az [SAP támogatási #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)hálózati késési útmutatójával. A hálózati késésnek közepes vagy jó tartományban kell lennie. Kivételek vonatkoznak a virtuális gépek és a HANA nagy példány egységek közötti forgalomra, [a jelen cikkben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)dokumentált módon.
        - Győződjön meg arról, hogy az ILB-telepítések a Direct Server Return használatára vannak beállítva. Ez a beállítás csökkenti a késést, ha az Azure ILB-ket a DBMS-réteg magas rendelkezésre állású konfigurációihoz használják.
        - Ha az Azure Load Balancer-t linuxos vendég operációs rendszerekkel együtt használja, ellenőrizze, hogy a **Net.ipv4.tcp_timestamps** Linux hálózati paraméter **értéke 0.** Ez a javaslat ütközik az [SAP note #2382421](https://launchpad.support.sap.com/#/notes/2382421)régebbi verzióinak ajánlásaival. Az SAP-feljegyzés most frissül, és azt állítja, hogy ezt a paramétert **0-ra** kell állítani az Azure terheléselosztókkal való munkához.
        - Fontolja meg [az Azure közelségi elhelyezési csoportjainak](https://docs.microsoft.com/azure/virtual-machines/linux/co-location) használatát az optimális hálózati késés leolvasása érdekében. További információ: [Azure közelség-elhelyezési csoportok az SAP-alkalmazásokkal való optimális hálózati késésért.](sap-proximity-placement-scenarios.md)
   4. Magas rendelkezésre állás és vész-helyreállítási telepítések.
        - Ha az SAP-alkalmazásréteget egy adott Azure rendelkezésre állási zóna megadása nélkül telepíti, győződjön meg arról, hogy az SAP-párbeszédpaneleket vagy egy SAP-rendszer köztes szoftverpéldányait futtató összes virtuális gép egy [rendelkezésre állási csoportban](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability)van telepítve.
        - Ha nincs szüksége magas rendelkezésre állásra az SAP központi szolgáltatások és a DBMS, telepítheti ezeket a virtuális gépeket ugyanabban a rendelkezésre állási készletben, mint az SAP alkalmazásréteg.
        - Ha az SAP Központi szolgáltatások és a DBMS-réteg védelme a passzív replikáció használatával a magas rendelkezésre állás, helyezze a két csomópont az SAP Központi szolgáltatások egy külön rendelkezésre állási csoport és a két DBMS-csomópontok egy másik rendelkezésre állási csoportban.
        - Ha az Azure rendelkezésre állási zónákban telepíti, nem használhatja a rendelkezésre állási csoportok. De győződjön meg arról, hogy az aktív és a passzív központi szolgáltatások csomópontjai két különböző rendelkezésre állási zónák üzembe helyezése. Használja a rendelkezésre állási zónák, amelyek a legalacsonyabb késés között.
          Ne feledje, hogy az [Azure Standard Load Balancer-t](https://docs.microsoft.com/azure/load-balancer/load-balancer-standard-availability-zones) kell használnia a Windows vagy a Pacemaker feladatátvételi fürtök létrehozásához a DBMS és az SAP Központi szolgáltatások réteghez a rendelkezésre állási zónák között. Az [alapszintű terheléselosztó](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview) nem használható zónaszintű központi telepítésekhez.
   5. Időeltetési beállítások.
        - Ellenőrizze az SAP NetWeaver fejlesztői nyomkövetéseit az SAP-példányok, hogy megbizonyosodjon arról, nincs kapcsolat szünetek között a várólistára helyezett kiszolgáló és az SAP munkafolyamatok. A következő két beállítási paraméter beállításával elkerülheti ezeket a kapcsolatmegszakításokat:
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveTime = 120000. További információ: [KeepAliveTime](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957549(v=technet.10)).
            - HKLM\SYSTEM\CurrentControlSet\Services\Tcpip\Parameters\KeepAliveInterval = 120000. További információ: [KeepAliveInterval](https://docs.microsoft.com/previous-versions/windows/it-pro/windows-2000-server/cc957548(v=technet.10)).
        - A helyszíni SAP GUI-felületek és az Azure-ban üzembe helyezett SAP-alkalmazásrétegek közötti grafikus felhasználói felületidő-kihelyezések elkerülése érdekében ellenőrizze, hogy ezek a paraméterek az alapértelmezett.pfl vagy a példányprofilban vannak-e beállítva:
            - rdisp/keepalive_timeout = 3600
            - rdisp/keepalive = 20
        - Az SAP enqueue folyamata és az SAP munkafolyamatok közötti létrehozott kapcsolatok megszakításának elkerülése érdekében az **enque/encni/set_so_keepalive** paramétert **true**értékre kell állítania. Lásd még: [AZ SAP megjegyzése #2743751](https://launchpad.support.sap.com/#/notes/2743751).  
        - Ha windowsos feladatátvevő fürtkonfigurációt használ, győződjön meg arról, hogy a nem válaszoló csomópontokon való reagáláshoz szükséges idő megfelelően van beállítva az Azure-hoz. A [feladatátvevő fürt hálózati küszöbértékeinek hangolása](https://techcommunity.microsoft.com/t5/Failover-Clustering/Tuning-Failover-Cluster-Network-Thresholds/ba-p/371834) a paramétereket és a feladatátvételi érzékenységet befolyásoló tényezőket sorolja fel. Feltéve, hogy a fürtcsomópontok ugyanabban az alhálózatban vannak, módosítsa ezeket a paramétereket:
            - SameSubNetDelay = 2000
            - SameSubNetThreshold = 15
            - RoutingHistorylength = 30
    6. Operációs rendszer beállításai vagy javításai
        - A HANA SAP-on való futtatásához olvassa el ezeket a megjegyzéseket és dokumentációkat:
            -   [SAP támogatási megjegyzés #2814271 - Az SAP HANA biztonsági mentés sikertelen az Azure-ban az ellenőrzőösszeg-hibával](https://launchpad.support.sap.com/#/notes/2814271)
            -   [SAP támogatási megjegyzés #2753418 - Lehetséges teljesítménycsökkenés az időzítő tartaléka miatt](https://launchpad.support.sap.com/#/notes/2753418)
            -   [SAP támogatási megjegyzés #2791572 - Teljesítménycsökkenés a Hyper-V hiányzó VDSO-támogatása miatt az Azure-ban](https://launchpad.support.sap.com/#/notes/2791572)
            -   [SAP támogatási megjegyzés #2382421 - A hálózati konfiguráció optimalizálása HANA- és OS-szinten](https://launchpad.support.sap.com/#/notes/2382421)
            -   [SAP támogatási megjegyzés #2694118 - Red Hat Enterprise Linux HA bővítmény az Azure-ban](https://launchpad.support.sap.com/#/notes/2694118)
            -   [SAP támogatási megjegyzés #1984787 - SUSE LINUX Enterprise Server 12: Telepítési megjegyzések](https://launchpad.support.sap.com/#/notes/1984787)
            -   [SAP támogatási megjegyzés #2002167 - Red Hat Enterprise Linux 7.x: Telepítés és frissítés](https://launchpad.support.sap.com/#/notes/0002002167)
            -   [SAP támogatási megjegyzés #2292690 - SAP HANA DB: Ajánlott operációsrendszer-beállítások az RHEL 7-hez](https://launchpad.support.sap.com/#/notes/0002292690)
            -   [SAP támogatási megjegyzés #2772999 - Red Hat Enterprise Linux 8.x: Telepítés és konfiguráció](https://launchpad.support.sap.com/#/notes/2772999)
            -   [SAP támogatási megjegyzés #2777782 - SAP HANA DB: Ajánlott operációsrendszer-beállítások az RHEL 8-hoz](https://launchpad.support.sap.com/#/notes/2777782)
            -   [SAP támogatási megjegyzés #2578899 - SUSE Linux Enterprise Server 15: Telepítési megjegyzés](https://launchpad.support.sap.com/#/notes/2578899)
            -   [SAP támogatási megjegyzés #2455582 - Linux: A GCC 6.x-szel lefordított SAP-alkalmazások futtatása](https://launchpad.support.sap.com/#/notes/0002455582)
            -    [SAP támogatási megjegyzés #2729475 - HWCCT hiba "Hypervisor nem támogatott" az SAP HANA minősítéssel rendelkező Azure virtuális gépeken](https://launchpad.support.sap.com/#/notes/2729475)
1. Tesztelje a magas rendelkezésre állás és a vész-helyreállítási eljárások.
   1. Feladatátvételi helyzetek szimulálása a virtuális gépek (Windows vendég operációs rendszerek) leállításával vagy az operációs rendszerek pánik üzemmódba állításával (Linux vendég operációs rendszerek). Ez a lépés segít annak kidöntésén, hogy a feladatátvételi konfigurációk a tervezettnek dolgoznak-e.
   1. Mérje meg, hogy mennyi ideig tart a feladatátvétel végrehajtása. Ha az idők túl hosszúak, fontolja meg a következőket:
        - SUSE Linux esetén az Azure Fence-ügynök helyett SBD-eszközöket használjon a feladatátvétel felgyorsításához.
        - Az SAP HANA, ha az adatok újratöltése túl sokáig tart, fontolja meg a nagyobb tárolási sávszélesség kiépítése.
   3. Tesztelje a biztonsági mentési/visszaállítási sorrendet és időzítést, és szükség esetén elkell készítenie a javításokat. Győződjön meg arról, hogy a biztonsági mentési idő elegendő. A visszaállítási és idő-visszaállítási tevékenységeket is tesztelnie kell. Győződjön meg arról, hogy a visszaállítási idők az RTO SL-eken belül vannak, ahol az RTO egy adatbázisra vagy virtuális gép-visszaállítási folyamatra támaszkodik.
   4. Tesztelje a régiók közötti VÉSZ-funkciókat és architektúrát.
1. Biztonsági ellenőrzés.
   1. Tesztelje az Azure szerepköralapú hozzáférés-vezérlési (RBAC) architektúra érvényességét. A cél az, hogy külön, és korlátozza a hozzáférést és engedélyeket a különböző csapatok. Például az SAP-alap csapattagjai nak képesnek kell lennie a virtuális gépek üzembe helyezésére, és lemezek hozzárendelése az Azure Storage-ból egy adott Azure virtuális hálózathoz. De az SAP-alap csapata nem hozhat létre saját virtuális hálózatokat, és nem módosíthatja a meglévő virtuális hálózatok beállításait. A hálózati csapat tagjai nem telepíthetik a virtuális gépeket olyan virtuális hálózatokba, amelyekben az SAP-alkalmazás és a DBMS virtuális gépek futnak. A csapat tagjai nak sem szabad módosítaniuk a virtuális gépek attribútumait, vagy akár törölniük a virtuális gépeket vagy lemezeket.  
   1.  Ellenőrizze, hogy [a hálózati biztonsági csoport és az ASC-szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview) a várt módon működnek-e, és védje a védett erőforrásokat.
   1.  Győződjön meg arról, hogy minden titkosítandó erőforrás titkosítva van. Olyan folyamatokat definiáljon és valósítson meg, amelyek biztonsági másolatot adnak a tanúsítványokról, tárolják és hozzáférnek ezekhez a tanúsítványokhoz, és visszaállítják a titkosított entitásokat.
   1.  Az [Azure Disk Encryption az](https://docs.microsoft.com/azure/security/azure-security-disk-encryption-faq) operációs rendszer lemezein, ahol lehetséges, operációsrendszer-támogatási szempontból.
   1.  Győződjön meg arról, hogy nem használ túl sok titkosítási réteget. Bizonyos esetekben célszerű azure disk encryption használatával együtt a DBMS transzparens adattitkosítási módszerek védelme érdekében a különböző lemezek vagy összetevők ugyanazon a kiszolgálón.  Például egy SAP DBMS-kiszolgálón az Azure Disk Encryption (ADE) engedélyezhető az operációs rendszer rendszerindító lemezén (ha az operációs rendszer támogatja az ADE-t), és az adatlemez(ek) nem használják a DBMS adatmegőrzési fájlok.  Ilyen például az ADE használata a DBMS TDE titkosítási kulcsokat tartalmazó lemezen.
1. Teljesítménytesztelés. Az SAP-ban az SAP-tésésa és mérései alapján végezze el ezeket az összehasonlításokat:
   - Adott esetben hasonlítsa össze a 10 legfontosabb online jelentést a jelenlegi megvalósítása immára.
   - Adott esetben hasonlítsa össze az első 10 kötegelt feldolgozást a jelenlegi implementációval.
   - Hasonlítsa össze az sap-rendszerbe az interfészeken keresztül történő adatátvitelt. Összpontosítson azokra a felületekre, ahol tudja, hogy az átvitel különböző helyek között halad, például a helyszíni és az Azure-ba.


## <a name="non-production-phase"></a>Nem termelési fázis 
Ebben a fázisban feltételezzük, hogy egy sikeres próba- vagy koncepcióigazolás (POC) után elkezdi üzembe helyezni a nem éles SAP-rendszereket az Azure-ban. A POC során tanult és tapasztalt dolgokat beépítheti ebbe a telepítésbe. A POP-okra vonatkozó összes feltétel és lépés erre a telepítésre is vonatkozik.

Ebben a fázisban általában fejlesztési rendszereket, egységtesztelési rendszereket és üzleti regressziós tesztelési rendszereket telepít az Azure-ba. Azt javasoljuk, hogy legalább egy nem éles rendszer egy SAP alkalmazássorban rendelkezik a teljes magas rendelkezésre állású konfiguráció, amely a jövőbeli termelési rendszer lesz. Íme néhány további lépés, amelyet ebben a fázisban kell végrehajtania:  

1.  Mielőtt áthelyezi a rendszereket a régi platformról az Azure-ba, gyűjtse össze az erőforrás-felhasználási adatokat, például a PROCESSZOR-használatot, a tárolási átviteli és az IOPS-adatokat. Ezeket az adatokat különösen a DBMS rétegegységekből gyűjtheti, de az alkalmazásréteg-egységekből is. Is mérje a hálózati és tárolási késés.
2.  Rögzítse a rendszerek rendelkezésre állási használati időmintáit. A cél annak megállapítása, hogy a nem termelési rendszereknek egész nap rendelkezésre kell-e állniuk, vagy vannak olyan nem termelési rendszerek, amelyek egy hét vagy hónap bizonyos fázisaiban leállíthatók.
3.  Tesztelje és határozza meg, hogy saját operációsrendszer-lemezképeket szeretne-e létrehozni a virtuális gépekhez az Azure-ban, vagy az Azure megosztott képtárból szeretne-e lemezképet használni. Ha a megosztott képtárból származó képet használ, győződjön meg arról, hogy olyan lemezképet használ, amely tükrözi az operációs rendszer szállítójával kötött támogatási szerződést. Egyes operációs rendszer-gyártók, megosztott képtár lehetővé teszi, hogy a saját licenc képeket. Más operációs rendszerképek esetében a támogatás az Azure által megadott árban is szerepel. Ha úgy dönt, hogy saját operációsrendszer-lemezképeket hoz létre, az alábbi cikkekben talál dokumentációt:
    -   [Általános lemezkép létrehozása az Azure-ban üzembe helyezett Windows virtuális gépről](https://docs.microsoft.com/azure/virtual-machines/windows/capture-image-resource)
    -   [Általános rendszerkép létrehozása az Azure-ban üzembe helyezett Linux virtuális gépről](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image)
3.  Ha suse-t és Red Hat Linux-lemezképeket használ a megosztott képtárból, akkor a Megosztott képtárban a Linux-gyártók által biztosított SAP-lemezképeket kell használnia.
4.  Győződjön meg arról, hogy megfelel az SAP támogatási követelményeinek a Microsoft támogatási szerződések. Lásd: [SAP támogatási megjegyzés #2015553](https://launchpad.support.sap.com/#/notes/2015553). Hana nagy példányok esetén lásd: [Bevezetési követelmények.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-onboarding-requirements)
4.  Győződjön meg arról, hogy a megfelelő személyek kapnak [tervezett karbantartási értesítéseket,](https://azure.microsoft.com/blog/a-new-planned-maintenance-experience-for-your-virtual-machines/) hogy a legjobb állásidőket választhassa ki.
5.  Gyakran ellenőrizze az Azure-bemutatók csatornákon, például [a Channel 9](https://channel9.msdn.com/) új funkciók, amelyek vonatkozhatnak a központi telepítések.
6.  Ellenőrizze az Azure-hoz kapcsolódó SAP-megjegyzéseket, például [a támogatási #1928533,](https://launchpad.support.sap.com/#/notes/1928533)az új vm-ska-khoz, valamint az újonnan támogatott operációsrendszer- és DBMS-kiadásokhoz. Hasonlítsa össze az új virtuálisgép-típusok és a régebbi virtuálisgép-típusok díjszabását, így a legjobb ár/teljesítmény aránnyal rendelkező virtuális gépeket telepítheti.
7.  Ellenőrizze újra az SAP támogatási jegyzeteket, az SAP HANA hardverkönyvtárat és az SAP PAM-et. Győződjön meg arról, hogy nem történt változás az Azure-hoz támogatott virtuális gépeken, a virtuális gépeken támogatott operációsrendszer-kiadásokban, valamint a támogatott SAP- és DBMS-kiadásokban.
8.  Tekintse meg [az SAP-webhelyen](https://www.sap.com/dmc/exp/2014-09-02-hana-hardware/enEN/iaas.html#categories=Microsoft%20Azure) az azure-beli HANA-tanúsítvánnyal rendelkező sk-eket. Hasonlítsa össze az új sk-ek díjszabását a tervezett árakkal. Végül, hogy a szükséges változtatásokat, hogy használja azis, hogy a legjobb ár / teljesítmény arány.
9.  A központi telepítési parancsfájlok adaptálása az új virtuálisgép-típusok használatához és a használni kívánt új Azure-funkciók beépítéséhez.
10. Az infrastruktúra üzembe helyezése után tesztelje és értékelje a hálózati késést az SAP-alkalmazásréteg virtuális gépei és a DBMS virtuális gépek között, az SAP támogatási megjegyzések szerint [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926.](https://launchpad.support.sap.com/#/notes/1100926/E) Értékelje ki az eredményeket az [SAP támogatási #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)hálózati késési útmutatójával. A hálózati késésnek közepes vagy jó tartományban kell lennie. Kivételek vonatkoznak a virtuális gépek és a HANA nagy példány egységek közötti forgalomra, [a jelen cikkben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)dokumentált módon. Győződjön meg arról, hogy az [Azure Virtual Machines DBMS-telepítés sap-számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general#azure-network-considerations) és [az SAP HANA-infrastruktúra-konfigurációkhoz és -műveletekhez az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-vm-operations) szereplő szempontok ban említett korlátozások egyike sem vonatkozik a központi telepítésre.
11. Győződjön meg arról, hogy a virtuális gépek a megfelelő [Azure közelségi elhelyezési csoportban](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)vannak telepítve, az Azure közelségi elhelyezési csoportjaiban leírtak [szerint az SAP-alkalmazásokkal való optimális hálózati késés érdekében.](sap-proximity-placement-scenarios.md)
11. Végezze el a koncepcióigazolási fázisösszes többi ellenőrzését a munkaterhelés alkalmazása előtt.
12. A számítási feladatok alkalmazása kor rögzítse a rendszerek erőforrás-felhasználását az Azure-ban. Hasonlítsa össze ezt a felhasználást a régi platform rekordjaival. Módosítsa a virtuális gép méretezését a jövőbeli üzemelő példányok, ha azt látja, hogy nagy különbségek vannak. Ne feledje, hogy a virtuális gépek leépítésekor, tárolása korés hálózati sávszélessége is csökkenni fog.
    - [A Windows rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json)
    - [A Linux rendszerű virtuális gépek méretei az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/sizes?toc=%2fazure%2fvirtual-network%2ftoc.json) 
13. Kísérletezzen a rendszermásolási funkciókkal és folyamatokkal. A cél az, hogy megkönnyítsék a fejlesztési rendszer vagy a tesztrendszer másolását, így a projektcsapatok gyorsan beszerezhetik az új rendszereket. Fontolja meg [az SAP LaMa](https://wiki.scn.sap.com/wiki/display/ATopics/SAP+Landscape+Management+%28SAP+LaMa%29+at+a+Glance) használatát ezekhez a feladatokhoz.
14. Optimalizálja és élesítse csapata Azure-szerepköralapú hozzáférését, engedélyeit és folyamatait, hogy biztosan különítse el a feladatokat. Ugyanakkor győződjön meg arról, hogy minden csapat el tudja végezni a feladatait az Azure-infrastruktúrában.
15. Gyakorolja, tesztelje és dokumentálja a magas rendelkezésre állású és vész-helyreállítási eljárásokat, hogy a munkatársak végrehajthassák ezeket a feladatokat. Azonosítsa a hiányosságokat, és alakítsa ki az új Azure-funkciókat, amelyeket integrál az üzembe helyezési szolgáltatásokba.

 
## <a name="production-preparation-phase"></a>A gyártás előkészítése 
Ebben a fázisban gyűjtse össze, amit tapasztalt és megtanult a nem éles környezetek során, és alkalmazza azt a jövőbeli éles környezetek. Emellett elő kell készítenie az aktuális üzemeltetési hely és az Azure közötti adatátvitel munkáját.

1.  Mielőtt az Azure-ba költözne, végezze el az éles rendszerek szükséges SAP-kiadási frissítéseit.
1.  Állapodjon meg a cégtulajdonosokkal a funkcionális és üzleti tesztekről, amelyeket a termelési rendszer migrációja után kell elvégezni.
1.  Győződjön meg arról, hogy ezek a tesztek befejeződtek az aktuális üzemeltetési helyen lévő forrásrendszerekkel. A rendszer Azure-ba való áthelyezése után először ne végezzen teszteket.
1.  Tesztelje az éles rendszerek Azure-ba való áttelepítésének folyamatát. Ha nem helyezi át az összes éles rendszert az Azure-ba ugyanabban az időszakban, hozzon létre élesrendszerek csoportjait, amelyeknek ugyanazon az üzemeltetési helyen kell lenniük. Tesztelje az adatok áttelepítését. Íme néhány gyakori módszer:
    - Használja a DBMS-módszereket, például a biztonsági mentést/visszaállítást az SQL Server Always On, HANA system replication vagy log shipping használatával az Azure-beli adatbázis-tartalom magolásához és szinkronizálásához.
    - Kisebb adatbázisok hoz használjon biztonsági mentést/visszaállítást.
    - Az SAP-áttelepítési figyelő, amely integrálva van az SAP SWPM, heterogén áttelepítések végrehajtásához.
    - Használja az [SAP DMO-folyamatot,](https://blogs.sap.com/2013/11/29/database-migration-option-dmo-of-sum-introduction/) ha az áttelepítést egy SAP-kiadási frissítéssel kombinálnia kell. Ne feledje, hogy a forrás-ADATBÁZIS-rendszer és a cél adatbázis-rendszer nem minden kombinációja támogatott. További információkat a DMO különböző kiadásaihoz tartozó SAP-támogatási megjegyzésekben talál. Például [a SUM 2.0 SP04 adatbázis-áttelepítési lehetősége (DMO).](https://launchpad.support.sap.com/#/notes/2644872)
    - Tesztelje, hogy az adatátviteli átviteli teljesítmény jobb-e az interneten vagy az ExpressRoute-on keresztül, ha biztonsági másolatokat vagy SAP-exportálási fájlokat kell áthelyeznie. Ha az interneten keresztül helyez át adatokat, előfordulhat, hogy módosítania kell a hálózati biztonsági csoport/alkalmazás biztonsági csoport néhány olyan szabályát, amelyet a jövőbeli éles rendszerekhez be kell tartania.
1.  Mielőtt áthelyezi a rendszereket a régi platformról az Azure-ba, gyűjtse össze az erőforrás-felhasználási adatokat. A hasznos adatok közé tartozik a PROCESSZOR-használat, a tárolási átviteli és az IOPS-adatok. Ezeket az adatokat különösen a DBMS rétegegységekből gyűjtheti, de az alkalmazásréteg-egységekből is. Is mérje a hálózati és tárolási késés.
1.  Ellenőrizze újra az SAP támogatási jegyzeteket és a szükséges operációsrendszer-beállításokat, az SAP HANA hardverkönyvtárat és az SAP PAM-et. Győződjön meg arról, hogy nem történt változás az Azure-hoz támogatott virtuális gépek, a támogatott operációs rendszer kiadásaiban a virtuális gépeken, és a támogatott SAP és DBMS kiadások.
1.  Frissítse a központi telepítési parancsfájlokat, hogy figyelembe vegye a virtuálisgép-típusokra és az Azure-funkciókra vonatkozó legújabb döntéseket.
1.  Az infrastruktúra és az alkalmazások telepítése után ellenőrizze, hogy:
    - A megfelelő virtuálisgép-típusok a megfelelő attribútumokkal és tárolóméretekkel vannak telepítve.
    - A virtuális gépek a megfelelő és a kívánt operációs rendszer kiadások és javítások, és egységesek.
    - A virtuális gépek szükség szerint és egységes módon edzettek.
    - A megfelelő alkalmazáskiadások és javítások telepítése és telepítése megtörtént.
    - A virtuális gépek a tervek nek megfelelően vannak telepítve az Azure rendelkezésre állási készleteibe.
    - Az Azure Premium Storage késésre érzékeny lemezekhez használatos, vagy ahol az [egyvm-sLA 99,9%-os](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) szükséges.
    - Az Azure Write Accelerator megfelelően van telepítve.
        - Győződjön meg arról, hogy a virtuális gépeken belül a tárolóhelyek vagy a csíkkészletek megfelelően vannak kialakítva az írási gyorsítót igénylő lemezek között.
        - Ellenőrizze a [konfiguráció szoftver RAID Linux .](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid)
        - Ellenőrizze az [LVM konfigurációját linuxos virtuális gépeken az Azure-ban.](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm)
    - [Az Azure által felügyelt lemezek](https://azure.microsoft.com/services/managed-disks/) kizárólag használatosak.
    - Virtuális gépek vannak telepítve a megfelelő rendelkezésre állási csoportok és rendelkezésre állási zónák.
    - [Az Azure Accelerated Networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) engedélyezve van az SAP-alkalmazásrétegben és az SAP DBMS-rétegben használt virtuális gépeken.
    - Nincs [Azure hálózati virtuális készülékek](https://azure.microsoft.com/solutions/network-appliances/) a kommunikációs útvonal között az SAP-alkalmazás és a DBMS-réteg SAP NetWeaver, Hybris vagy S/4HANA.
    - Az alkalmazásbiztonsági csoport ra és a hálózati biztonsági csoport szabályai a kívánt és tervezett kommunikációt teszik lehetővé, és szükség esetén blokkolják a kommunikációt.
    - Az időszüntetési beállítások a korábbiaknak megfelelően vannak beállítva.
    - A virtuális gépek a megfelelő [Azure közelségi elhelyezési csoportba](https://docs.microsoft.com/azure/virtual-machines/linux/co-location)vannak telepítve, az Azure közelségi elhelyezési csoportjaiban leírtak [szerint az SAP-alkalmazásokkal való optimális hálózati késés érdekében.](sap-proximity-placement-scenarios.md)
    - Az SAP alkalmazásréteg virtuális gépei és a DBMS virtuális gépek közötti hálózati késéstesztelése és érvényesítése az SAP támogatási megjegyzésekben [#500235](https://launchpad.support.sap.com/#/notes/500235) és [#1100926](https://launchpad.support.sap.com/#/notes/1100926/E)leírtak szerint történik. Értékelje ki az eredményeket az [SAP támogatási #1100926](https://launchpad.support.sap.com/#/notes/1100926/E)hálózati késési útmutatójával. A hálózati késésnek közepes vagy jó tartományban kell lennie. Kivételek vonatkoznak a virtuális gépek és a HANA nagy példány egységek közötti forgalomra, [a jelen cikkben](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/hana-network-architecture#networking-architecture-for-hana-large-instance)dokumentált módon.
    - A titkosítást szükség esetén és a megfelelő titkosítási módszerrel vezették be.
    - Az újonnan telepített infrastruktúra csatlakoztatható, és más alkalmazások is csatlakoztathatók.
1.  Hozzon létre egy forgatókönyvet a tervezett Azure-karbantartásra való reagáláshoz. Határozza meg, hogy milyen sorrendben kell újraindítani a rendszereket és a virtuális gépeket a tervezett karbantartáshoz.
    

## <a name="go-live-phase"></a>Go-live fázis
A go-live fázisban mindenképpen kövesse a korábbi fázisokban kifejlesztett forgatókönyveket. Hajtsa végre a tesztelt és gyakorolt lépéseket. Ne fogadja el a konfigurációk és folyamatok last minute módosításait. Hajtsa végre az alábbi lépéseket is:

1. Ellenőrizze, hogy az Azure Portal figyelése és más figyelési eszközök működnek-e. A Windows Performance Monitor (perfmon) for Windows és a SAR for Linux használatát javasoljuk.
    - CPU számlálók.
        - Átlagos CPU-idő, összesen (az összes CPU)
        - Átlagos processzoridő, minden egyes processzor (128 processzor M128 virtuális gépeken)
        - CPU kernel idő, minden egyes processzor
        - CPU felhasználói idő, minden egyes processzor
    - Memory.
        - Szabad memória
        - Memórialap /másodperc
        - Memóriaoldal ki/másodperc
    - Lemez.
        - Lemez olvasása k/s-ban, egyenként
        - Lemez olvasása/másodperc, egyes lemezekenként
        - Lemez olvasása mikroszekundum/olvasás, egyes lemezek
        - Lemezírás kbps-ben, egyes lemezeken
        - Lemez írása/másodperc, egyes lemezekenként
        - Lemez írása mikroszekundum/olvasás, egyes lemezekenként
    - Hálózati.
        - Hálózati csomagok/másodperc
        - Hálózati csomagok kimenő/másodperc
        - Hálózati KB/másodperc
        - Hálózati KB ki/másodperc
1.  Az adatok áttelepítése után végezze el az összes olyan érvényesítési tesztet, amelyről megállapodott a cégtulajdonosokkal. Csak akkor fogadja el az érvényesítési teszt eredményeit, ha rendelkezik az eredeti forrásrendszerek eredményeivel.
1.  Ellenőrizze, hogy az összeköttetések működnek-e, és hogy más alkalmazások képesek-e kommunikálni az újonnan telepített éles rendszerekkel.
1.  Ellenőrizze az átviteli és korrekciós rendszert az SAP stms tranzakción keresztül.
1.  Adatbázis-biztonsági mentések végrehajtása a rendszer éles kiadása után.
1.  Virtuálisgép-biztonsági mentések végrehajtása az SAP alkalmazásréteg virtuális gépeiszámára a rendszer éles kiadása után.
1.  Az SAP-rendszerek, amelyek nem voltak részei a jelenlegi éles fázisban, de hogy kommunikálnak az SAP-rendszer, amely az Azure-ba költözött ebben a működés közben fázisban, alaphelyzetbe kell állítania a gazdagép névpuffer sm51.For SAP systems that is weren't part of the current go-live phase but that communicate with the SAP systems that you moved to Azure during this go-live phase, you need to reset the host name buffer in SM51. Ezzel eltávolítja az Azure-ba áthelyezett alkalmazáspéldányok nevéhez társított régi gyorsítótárazott IP-címeket.  


## <a name="post-production"></a>Utómunka
Ez a fázis a rendszer felügyeletéről, működtetéséről és felügyeletéről szól. Az SAP szempontjából a szokásos feladatok, amelyeket a régi üzemeltetési helyen kellett elvégeznie, érvényesek. Végezze el az Azure-specifikus feladatokat is:

1. Tekintse át az Azure-számlákat a magas töltésű rendszerekhez.
2. Optimalizálja az ár/teljesítmény hatékonyságát a virtuális gép és a tárolási oldalon.
3. Optimalizálja az időpontokat, amikor le állíthatja a rendszereket.  


## <a name="next-steps"></a>További lépések
Olvassa el a következő cikkeket:

- [Az Azure virtuális gépek tervezése és megvalósítása az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/planning-guide)
- [Az Azure virtuális gépek üzembe helyezése az SAP NetWeaver számára](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/deployment-guide)
- [Az Azure Virtual Machines DBMS üzembe helyezésének szempontjai az SAP-számítási feladatokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/dbms_guide_general)

