---
title: Szempontok az SAP számítási feladatok Azure virtuális gépek DBMS üzembe helyezéshez |} A Microsoft Docs
description: Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 8b19c0fd8af2792a4ffb877e5c6a7fc6b3f94511
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59699253"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Az SAP számítási feladatok Azure virtuális gépek DBMS üzembe szempontjai
[1114181]:https://launchpad.support.sap.com/#/notes/1114181
[1409604]:https://launchpad.support.sap.com/#/notes/1409604
[1597355]:https://launchpad.support.sap.com/#/notes/1597355
[1928533]:https://launchpad.support.sap.com/#/notes/1928533
[1984787]:https://launchpad.support.sap.com/#/notes/1984787
[1999351]:https://launchpad.support.sap.com/#/notes/1999351
[2002167]:https://launchpad.support.sap.com/#/notes/2002167
[2015553]:https://launchpad.support.sap.com/#/notes/2015553
[2039619]:https://launchpad.support.sap.com/#/notes/2039619
[2069760]:https://launchpad.support.sap.com/#/notes/2069760
[2171857]:https://launchpad.support.sap.com/#/notes/2171857
[2178632]:https://launchpad.support.sap.com/#/notes/2178632
[2191498]:https://launchpad.support.sap.com/#/notes/2191498
[2233094]:https://launchpad.support.sap.com/#/notes/2233094
[2243692]:https://launchpad.support.sap.com/#/notes/2243692
[deployment-guide]:deployment-guide.md
[deployment-guide-3]:deployment-guide.md#b3253ee3-d63b-4d74-a49b-185e76c4088e
[planning-guide]:planning-guide.md

[Logo_Linux]:media/virtual-machines-shared-sap-shared/Linux.png
[Logo_Windows]:media/virtual-machines-shared-sap-shared/Windows.png


[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-rm-include.md)]

Ez az útmutató megvalósításához és központi telepítése a Microsoft Azure-on SAP software módjáról a dokumentációban részét képezi. Az útmutató olvasása előtt olvassa el a [tervezési és megvalósítási útmutató][planning-guide]. Ez a dokumentum az Azure-infrastruktúrát használják, mint a szolgáltatás (IaaS) képességeket a Microsoft Azure virtuális gépeken (VM) kapcsolatos SAP DBMS-rendszerek általános központi telepítés szempontjait ismerteti.

A tanulmány az SAP-telepítési dokumentációt és SAP-megjegyzések, amelyek tartalmazzák a telepítések és SAP-szoftverek központi telepítései az elsődleges erőforrásokat a megadott platformok egészíti ki.

Ebben a dokumentumban megfontolásokkal kapcsolatos SAP DBMS-rendszerek Azure-beli virtuális gépeken futó jelennek meg. Ez a fejezet az adott DBMS-rendszerekre való néhány hivatkozások találhatók. Ehelyett az adott DBMS-rendszerek kezelése a tanulmány után ez a dokumentum.

## <a name="definitions"></a>Meghatározások
A dokumentumban a kifejezések használata:

* **IaaS**: Infrastruktúra-szolgáltatás.
* **PaaS**: Szolgáltatásként nyújtott platformon.
* **SaaS**: Szolgáltatott szoftver.
* **Az SAP-összetevő**: Egy különálló SAP alkalmazások például ERP központi összetevő (ECC), a Business Warehouse (BW), a megoldás Manager vagy a vállalati portál (EP). SAP-összetevők a hagyományos ABAP és Java-technológiák, illetve egy nem NetWeaver-alapú alkalmazás, például az üzleti objektumok is alapulhatnak.
* **Az SAP-környezet**: Egy vagy több SAP összetevő logikusan, például fejlesztési, minőségbiztosítási, képzés, vész-helyreállítási vagy éles üzleti függvény végrehajtásához.
* **SAP-rendszeren**: Ez a kifejezés hivatkozik a teljes SAP-eszközök az ügyfél informatikai környezetét. Az SAP-rendszeren, tartalmazza az összes éles és éles környezetekben.
* **SAP-rendszerhez**: Egy adatbázis-kezelő réteg és a egy alkalmazás réteget, például az SAP ERP fejlesztési rendszer együttes használata az SAP Business Warehouse teszt rendszer, vagy az SAP CRM éles rendszer. Az Azure-környezetek e két réteget a helyszíni és az Azure közötti elosztásával nem támogatott. Ennek eredményeképpen az SAP-rendszerhez, üzembe helyezett helyszíni vagy annak az Azure-ban üzembe helyezett. Telepíthet egy Azure-ban vagy a helyszíni SAP-rendszeren, a különböző rendszerek. Például, sikerült központi telepítése a SAP CRM fejlesztési és rendszerek tesztelése az Azure-ban, de helyszíni üzembe helyezése az SAP CRM éles rendszer.
* **Létesítmények közötti**: Egy forgatókönyv, ahol a virtuális gépek Azure-előfizetéssel, amely rendelkezik a site-to-site, többhelyes üzembe, vagy Azure ExpressRoute-kapcsolat a helyszíni adatok között központok és az Azure ismerteti. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések is rendelkezésre állnak, létesítmények közötti forgatókönyvek leírtak szerint. 

    A kapcsolat az az oka, hogy kiterjesztése a helyszíni tartományokra, a helyszíni Active Directory és a helyszíni DNS az Azure-bA. Az Azure-előfizetés objektumok kiterjed a helyszíni környezet változásaihoz. Ez a bővítmény a virtuális gépeket a helyszíni tartomány része lehet. Tartományi felhasználókat a helyszíni tartomány elérheti a kiszolgálók és szolgáltatások a virtuális gépeken, például adatbázis-kezelő szolgáltatások futtatásához. Virtuális gépek közötti kommunikációt és a névfeloldás üzembe helyezte a helyszínen és az Azure-ban üzembe helyezett virtuális gépek lehetséges. Ebben a forgatókönyvben a leggyakoribb forgatókönyv üzembe helyezéséhez az Azure-beli SAP-eszközök használatban. További információkért lásd: [tervezéssel és kialakítással VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Létesítmények közötti üzemelő példányok, SAP-rendszereit, hol találhatók az SAP-rendszereit futtató Azure virtuális gépeket a helyszíni tartomány tagjai és éles SAP-rendszerek esetén támogatottak. Létesítmények közötti konfigurációkat üzembe helyezésének részek támogatottak, vagy hajtsa végre az SAP-környezetünk az Azure-bA. A teljes SAP-rendszeren az Azure-ban futó részét képezi, a helyszíni tartomány virtuális gépek és a Active Directory LDAP szükséges. 
>
> A dokumentáció korábbi verziói, a hibrid-informatikai alkalmazási helyzetek vannak megemlítve. Az előfizetési időszak *hibrid* rootolva van, az a tény, hogy nincs-e a létesítmények közötti kapcsolatok a helyszíni és az Azure között. Ebben az esetben a hibrid is jelenti, hogy a virtuális gépek az Azure-ban a helyszíni Active Directory részét képezik.
>
>

Bizonyos Microsoft-dokumentációt létesítmények közötti forgatókönyv különösen az adatbázis-kezelő magas rendelkezésre állású konfigurációk esetén egy kicsit másképp ismerteti. Az SAP-kapcsolatos dokumentumok esetében a létesítmények közötti forgatókönyv forrni kezd a site-to-site, vagy privát [ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolatot és a egy SAP-rendszeren, amely a helyszíni és az Azure között.

## <a name="resources"></a>További források
Érhetők el további cikkek az SAP számítási feladatok Azure-ban. Kezdje [SAP számítási feladatok Azure-ban: Első lépések](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) majd érdeklődési körét.

Az alábbi SAP-megjegyzések tekintetében a dokumentumban leírt terület kapcsolódó Azure-beli SAP.

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure-beli Virtuálisgép-típusok |
| [2015553] |A Microsoft Azure-beli SAP: Támogatás előfeltételei |
| [1999351] |Továbbfejlesztett Azure monitoring for SAP hibaelhárítási |
| [2178632] |Főbb figyelési metrikákat az SAP a Microsoft Azure |
| [1409604] |A Windows-virtualizálás: Speciális figyelés |
| [2191498] |SAP használata Linux az Azure-ral: Speciális figyelés |
| [2039619] |SAP-alkalmazások a Microsoft Azure-ban, az Oracle-adatbázishoz: Támogatott termékek és termékverziók |
| [2233094] |DB6: SAP-alkalmazások az IBM DB2-höz használatával Linux, UNIX és a Windows Azure-ban: További információ |
| [2243692] |A Microsoft Azure (IaaS) virtuális gép Linux: Az SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési jegyzetek |
| [2002167] |Red Hat Enterprise Linux 7.x: Telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítése és frissítése |
| [1597355] |Linux-lapozóterület javaslat |
| [2171857] |Oracle Database 12c: Fájlrendszer-támogatás Linux rendszeren |
| [1114181] |Oracle Database 11g: Fájlrendszer-támogatás Linux rendszeren |


Információ az SAP-megjegyzések Linux: a [az SAP közösségi wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Szüksége lesz egy Microsoft Azure-architektúra és a Microsoft Azure virtuális gépeken telepített és üzemeltetett hogyan gyakorlati ismerete. További információkért lásd: [Azure dokumentációja](https://docs.microsoft.com/azure/).

Általánosságban véve a Windows, Linux- és adatbázis-kezelő rendszer telepítési és konfigurációs lényegében ugyanaz, mint a virtuális gépekhez vagy helyszíni telepítése operációs rendszer nélküli gépen. Néhány architektúra és a rendszer felügyeleti megvalósítási döntést kell meghoznia, amelyek eltérnek az Azure IaaS használata esetén. Ez a dokumentum ismerteti a specifikus architekturális és a rendszer felügyeleti különbségek a elő kell készíteni az Azure IaaS használata esetén.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>A virtuális gépek RDBMS központi telepítésekhez tárolószerkezet
Kövesse az ebben a fejezetben, olvassa el, és megismerheti a megjelenő információk [ebben a fejezetben] [ deployment-guide-3] , a [üzembe helyezési útmutató][deployment-guide]. Ismerik az információ a különböző Virtuálisgép-sorozat és a standard és prémium szintű storage, ez a fejezet elolvasása előtt közötti különbségeket kell. 

Az Azure Storage-ról Azure-beli virtuális gépek című témakörben talál:

- [Bevezetés a managed Disks szolgáltatásba az Azure Windows virtuális gépek](../../windows/managed-disks-overview.md).
- [Bevezetés a managed Disks szolgáltatásba az Azure Linux virtuális gépek](../../linux/managed-disks-overview.md).

Alapszintű konfiguráció esetén általában javasoljuk, hogy egy üzembe helyezési struktúrája, ahol az operációs rendszer, az adatbázis-kezelő és végleges SAP bináris fájlokat nem azonosak az adatbázisfájlokat. Azt javasoljuk, hogy Azure-beli virtuális gépeken futó SAP-rendszerek rendelkezik-e az alap VHD-t, vagy lemezt, az operációs rendszer, az adatbázis felügyeleti rendszer futtatható fájljainak és a SAP végrehajtható fájlok telepítése. 

Az adatbázis-kezelő adathoz és naplófájlhoz standard storage vagy prémium szintű storage vannak tárolva. Ezek különböző lemezen tárolt és az eredeti Azure operációs rendszer lemezképének virtuális gép logikai lemezként csatolt. A Linux-környezetek különböző javaslatok vannak dokumentálva, különösen az SAP Hana-hoz.

Ha azt tervezi, hogy a lemez elrendezése, egyensúlyba ezek az elemek között:

* Az adatfájlok számát.
* A lemezek, amelyek tartalmazzák a fájlok számát.
* Az egyetlen lemez iops-érték kvóták.
* Lemezenként fájlmegosztásra.
* A lehetséges Virtuálisgép-méret szerint további adatlemezek száma.
* A teljes tárterületek átviteli sebességének egy virtuális Gépet biztosít.
* A késést tud biztosítani a különféle Azure Storage.
* Virtuális gép SLA-k.

Azure-adatlemez IOPS kvóta kényszeríti. Kvóta eltérőek a standard storage és a premium storage üzemeltetett lemezeket. I/o-késés is eltér a két tárolási típusok között. A Premium storage a jobb i/o-késés tesz lehetővé. 

A virtuális gépek különböző típusairól mindegyike rendelkezik egy korlátozott számú adatlemez csatolható. Egy másik korlátozás, hogy csak bizonyos virtuális gépek típusai prémium szintű storage is használható. Általában úgy dönt, hogy-Virtuálisgép bizonyos típusú a CPU és memória-követelmények alapján. Emellett érdemes lehet az IOPS, késés és lemez átviteli sebességet megkövetelő, amely általában vannak méretezve, a lemezek számát vagy a prémium szintű tárolólemezeket típusát. A lemez mérete, különösen a premium storage függhet, IOPS és az átviteli sebesség érhető el, hogy az egyes lemezek számát.

> [!NOTE]
> A DBMS üzembe helyezési bármilyen adatokat, a tranzakciós napló, prémium szintű tárolás használatát javasoljuk vagy fájlok visszaállítása. Nem számít, hogy éles, akár az éles rendszerek üzembe szeretné.

> [!NOTE]
> Kihasználhatja az Azure-ból a egyedi [egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/), csatolt összes lemezt kell lennie a prémium szintű tárolási típust, amely tartalmazza az alap VHD-t.

> [!NOTE]
> Fő adatbázis-fájlokat tartalmazó, például az adathoz és naplófájlhoz tárolási hardverhez, amely csak az Azure-beli adatközpontok szomszédos közös elhelyezésű külső adatközpontok található SAP adatbázisai nem támogatott. Az SAP-feladatokat csak tárolási jelölt natív Azure-szolgáltatás, az adatok és a tranzakciós naplófájlok az SAP-adatbázisok esetében támogatott.

Az elhelyezési az adatbázisfájlokat és a napló- és ismét: fájlok és a típus használata az Azure Storage IOPS, késés és a teljesítménybeli követelmények határozza meg. Ahhoz, hogy elegendő iops-t, előfordulhat, hogy kényszerített több lemezt, vagy egy nagyobb prémium szintű tárolólemez használatával. Ha használ több lemez, hozhat létre egy szoftverfrissítési stripe a lemezeket, az adatfájlok, illetve a napló tartalmaz, és ismételje meg a fájlok között. Ezekben az esetekben, az IOPS és az adatátviteli sebességet SLA-k az alapul szolgáló prémium szintű Storage lemezek vagy a standard szintű storage-lemez elérhető maximális iops értéke a következők halmozódnak az eredményül kapott stripe-készlet.

Mint már említettük Ha az IOPS követelmény nagyobb, mint egy virtuális Merevlemezzel biztosíthat, egyenleg iops-érték szükséges ahhoz, hogy az adatbázisfájlok több VHD-k számát. Az IOPS osszák szét a lemezeket a legegyszerűbb módja, hogy a build szoftver paritásos különböző lemezek. Ezután elhelyezheti egy számot, az SAP DBMS-adatfájlok ki a szoftver stripe faragottnak LUN-okra. a stripe a lemezek számát a növekvő igények szerint határozzák meg az IOPs a növekvő igények szerint, a lemez átviteli igényeknek és a kötet.


- - -
> ![Windows][Logo_Windows] Windows
>
> Azt javasoljuk, hogy használja-e Windows tárolóhelyek paritásos létrehozása több Azure virtuális merevlemezek között. Legalább Windows Server 2012 R2 vagy Windows Server 2016-ban.
>
> ![Linux][Logo_Linux] Linux
>
> Csak a MDADM és a logikai kötet-kezelő (LVM) hozhat létre egy szoftveres RAID Linux rendszeren támogatott. További információkért lásd:
>
> - [Szoftveres RAID linuxon konfigurálása](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) MDADM használatával
> - [LVM konfigurálása az Azure-beli Linuxos virtuális gépre](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) LVM használatával
>
>

- - -

> [!NOTE]
> Azure Storage úgy biztosítja a VHD-ket három képét, mert konfigurálása a redundancia stripe-e, amikor nincs értelme. Csak csíkozást konfigurálása, hogy az i/o vannak elosztva a különböző virtuális merevlemezeket kell.
>

### <a name="managed-or-nonmanaged-disks"></a>Felügyelt vagy nem felügyelt lemezek
Az Azure storage-fiók egy felügyeleti szerkezet, és korlátozásokat a tárgyat. Korlátozások a standard szintű tárfiókok és a premium storage-fiókok eltérő. Információk a képességei és korlátozásai: [Azure Storage méretezhetőségi és teljesítménycéljai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

Standard szintű tárolóra vonatkozó ne feledje, hogy nincs-e korlátozni az iops-értékkel / storage-fiókot. Tekintse meg a tartalmazó sort **teljes kérelmek gyakorisága** cikkben [Azure Storage méretezhetőségi és teljesítménycéljai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets). Emellett van egy kezdeti, a storage-fiókok Azure-előfizetésenként számára vonatkozó határértéket. Egyenleg virtuális merevlemezek a különböző tárfiókok között nagyobb SAP-rendszeren elkerülje a tárfiókok korlátairól. Ez akkor fárasztó munka, ha a több száz virtuális gépeket, amelyek több mint Kaliforniában VHD-k beszélünk.

Standard szintű storage használatával egy SAP számítási feladatok együtt DBMS-környezetekben nem ajánlott, mert a hivatkozásokat, és a standard szintű tárolóba javaslatok korlátozva, ebben a rövid [cikk](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

A tervezési és üzembe helyezése a VHD-k különböző Azure storage-fiókok teljes adminisztratív munka elkerülése érdekében a Microsoft bevezette [Azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017-ben. A felügyelt lemezek a standard storage és a premium storage érhetők el. A felügyelt lemezek nem felügyelt lemezek képest főbb előnyei a következők:

- Felügyelt lemezek Azure elosztja a különböző virtuális merevlemezeket különböző storage-fiókok automatikus üzembe helyezéskor. Ily módon a tárfiók korlátozza az adatmennyiség, i/o-teljesítményt, és iops-érték nem találati.
- Felügyelt lemezeket használ, az Azure Storage figyelembe veszi az Azure rendelkezésre állási készlet fogalmakat. Ha a virtuális gép az Azure rendelkezésre állási csoport része, a virtuális alaplemez és a egy virtuális gép csatlakoztatott lemez a rendszer üzembe helyezi a különböző hibatűrési és frissítési tartományokban.


> [!IMPORTANT]
> Adja meg az Azure Managed Disks előnyét, javasoljuk, hogy az Azure Managed Disks szolgáltatást az adatbázis-kezelő központi telepítések és az SAP-környezetekhez általában.
>

Konvertálás nem felügyeltről felügyelt lemezzé, tekintse meg:

- [Windows virtuális gép átalakítása nem felügyeltről felügyelt felügyelt lemezekre](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Nem felügyelt lemezek Linux rendszerű virtuális gép konvertálása a felügyelt lemezek](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Virtuális gépek és az adatlemezek gyorsítótárazás
Ha csatlakoztatja a virtuális lemezek, kiválaszthatja a i/o-forgalom a virtuális gép és az Azure storage-ban található lemezek között van gyorsítótárba kerüljenek-e. Standard és prémium szintű storage két különböző technológiák használata, az ilyen típusú gyorsítótár.

Az alábbi javaslatok a standard szintű adatbázis-kezelő azt feltételezik következő i/o-jellemzőkkel:

- Többnyire az adatfájlokat az adatbázis elleni olvasási munkaterhelés. Ezek olvasási teljesítmény kritikus fontosságú az adatbázis-kezelő rendszer is.
- Ellenőrzőpontokat vagy állandó adatfolyam alapú adatlöketekkel szemben az adatfájlok írása történik. Naponta átlagolva, olvasási-nál kevesebb írások vannak. Leváló olvasási adatokat fájlokból e írási műveletek aszinkron, és tartsa be a felhasználói tranzakciókat.
- A tranzakcióból alig minden olvasási naplózás vagy a visszaállítási fájlok vannak. Kivétel a nagy i/o tranzakciónapló biztonsági mentései végrehajtásakor.
- A tranzakció vagy ismétlés naplófájlok fő terhelés írások. A számítási feladat jellegétől függ rendelkezhet i 4 KB-os, vagy a más esetekben, az i/o-méret 1 MB vagy annál kisebb.
- Az összes írási művelet a lemezen egy megbízható módon kell őrizhető meg.

A standard szintű storage a lehetséges gyorsítótár típusok a következők:

* None
* Olvasás
* Olvasás/Írás

Egységes és determinisztikus teljesítmény eléréséhez, állítsa be, a gyorsítótárazás a standard szintű storage esetében a fájlokat az adatbázis-kezelő kapcsolatos adatokat tartalmazó összes lemez jelentkezik, és vonja vissza a fájlok és a hely a tábla **NONE**. A gyorsítótárazás az alap VHD-t az alapértelmezett maradhat.

A premium storage a következő gyorsítótárazási lehetőségek érhetők el:

* None
* Olvasás
* Olvasás/írás
* Nincs + Írásgyorsító, amely csak az Azure M sorozatú virtuális gépek
* Olvasás + Írásgyorsító, amely csak az Azure M sorozatú virtuális gépek

A premium storage, javasoljuk, hogy használjon **olvasási gyorsítótárazás az adatfájlok** az SAP adatbázis-, és válassza a **naplófájlokhoz, a lemezek gyorsítótárazása**.

M-sorozat üzemelő példánya esetében javasoljuk, hogy a DBMS üzembe helyezési Azure Write Accelerator használjon. Tekintse meg a részleteket, korlátozások és központi telepítését az Azure Írásgyorsító, [Írásgyorsító engedélyezése](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator).


### <a name="azure-nonpersistent-disks"></a>Az Azure nonpersistent lemezek
Az Azure virtuális gépek ajánlat nonpersistent lemezek, virtuális gép üzembe helyezése után. A virtuális gép újraindítása esetén az adott meghajtókat minden tartalom törli. Ez egy tekintve, hogy az adatfájlok és az adatbázisok napló- és ismét: fájlok semmilyen körülmények között találhatók, ezek típusoszlopok meghajtókon. Előfordulhat, kivételek bizonyos adatbázisok esetén, ahol ezek a típusoszlopok meghajtók tempdb és ideiglenes táblahelyeket alkalmas lehet. Ne használja A-sorozatú virtuális gépek adott meghajtókat, mivel ezek állandó meghajtók korlátozott, hogy a Virtuálisgép-család átviteli. 

További információkért lásd: [megismerheti az Azure-beli Windows virtuális gépeken az ideiglenes meghajtó](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

- - -
> ![Windows][Logo_Windows] Windows
>
> Egy Azure-beli virtuális gépen a D meghajtón a típusoszlopok meghajtó, az Azure-beli számítási csomópont által az egyes helyi lemezek biztonsági mentése. Mert az állandó, a tartalmat a D meghajtón végzett módosítások elvesznek, ha a virtuális gép újraindul. A változások a következők voltak tárolt fájlok létrehozott címtárakat és a telepített alkalmazások.
>
> ![Linux][Logo_Linux] Linux
>
> Linux rendszerű Azure virtuális gépek automatikusan, amely az Azure számítási csomópont helyi lemezek által támogatott típusoszlopok meghajtó /mnt/resource meghajtót csatlakoztatni. Mert az állandó, a/mnt/resource tartalmak végzett módosítások elvesznek, ha a virtuális gép újraindul. A változások a következők voltak tárolt fájlok létrehozott címtárakat és a telepített alkalmazások.
>
>

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>A Microsoft Azure Storage rugalmasságot
Legalább három külön tárolócsomópontok az alap VHD-OS és a csatlakoztatott lemezek és a blobok, a Microsoft Azure Storage tárolja. A tárolás típusát a helyileg redundáns tárolás (LRS) nevezzük. LRS az alapértelmezett érték az Azure-on minden alkalmazástípus esetében.

Nincsenek más redundancia módszereket. További információkért lásd: [Azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>A Premium storage szolgáltatás adatbázis-kezelő virtuális gépek és a lemezeken, amelyeket az adatbázis és a naplófájlok tárolásához, majd ismételje meg a fájlok tárolási ajánlott típusát. A premium storage csak akkor érhető el a redundancia metódus LRS. Ennek eredményeképpen kell adatbázis replikálása egy másik Azure régióban vagy a rendelkezésre állási zónában történő engedélyezéséhez adatbázis módszerek konfigurálása. SQL Server Always On Oracle Data Guard és HANA-Rendszerreplikálást adatbázis módszerekre.


> [!NOTE]
> A DBMS üzembe helyezési a standard szintű tárolóra vonatkozó georedundáns tárolás (GRS) használata nem ajánlott. GRS jelentősen befolyásolja a teljesítményt, és nem fogadja el az írási sorrend különböző virtuális merevlemezek, virtuális géphez csatolt között. A replikációs cél oldalon nem érvényesítenie a az írási sorrend között különböző virtuális merevlemezek potenciálisan inkonzisztens adatbázisok vezet. Ebben a helyzetben következik be, ha adatbázis és a napló és a visszaállítási vannak elosztva több VHD-k, mint általában a helyzet, a forrás virtuális gép oldalán.



## <a name="vm-node-resiliency"></a>A virtuális gép csomópont rugalmasságát
Az Azure-beli virtuális gépek több különböző SLA-kat kínál. További információkért tekintse meg a legújabb kiadásával [virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Az adatbázis-kezelő réteg fontos általában rendelkezésre állás az SAP-rendszerhez, mert a rendelkezésre állási csoportok, a rendelkezésre állási zónák és a karbantartási események kell. További információ ezekről a fogalmakról: [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és [az Azure-ban Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Az SAP számítási feladatok az éles adatbázis-kezelő forgatókönyvek esetén a minimális javaslat, hogy:

- Két virtuális gépek üzembe helyezése egy külön rendelkezésre állási ugyanazon Azure-régióban.
- E két virtuális gép futtatása az azonos Azure virtuális hálózatban és hálózati adapterrel kívül ugyanannyi alhálózatot kapcsolt.
- Adatbázis módszerekkel tartsa a készenléti a második virtuális géppel. Módszerek az SQL Server Always On, a Oracle Data Guard vagy a HANA-Rendszerreplikálást lehet.

Is telepíthet egy külső virtuális Gépet egy másik Azure-régióban és az azonos adatbázis módszerek használatával adjon meg egy aszinkron replika egy másik Azure-régióban.

Az Azure rendelkezésre állási csoportok beállítása a további információkért lásd: [ebben az oktatóanyagban](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Azure-beli hálózati megfontolások
A nagyméretű SAP üzemelő példánya esetében használja a tervezet- [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter). Ezzel a virtuális hálózati konfiguráció és az engedélyek és a szerepkör-hozzárendelések a szervezet különböző részeit.

Ajánlott eljárások rendszereit több száz eredménye:

- Az SAP-alkalmazást helyezünk üzembe a virtuális hálózat nem rendelkezik internet-hozzáférés.
- Az adatbázis, virtuális gépek futtatása az alkalmazásrétegre ugyanazon a virtuális hálózaton.
- A virtuális gépek a virtuális hálózaton belül egy privát IP-cím statikus foglalási rendelkezik. További információkért lásd: [IP-címtípusokat és foglalási módszereket az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm).
- Az adatbázis-kezelő virtuális gépek útválasztási korlátozások *nem* rendelkező tűzfal van telepítve a helyi adatbázis-kezelő virtuális gépeken. Ehelyett forgalom-útválasztást van definiálva [hálózati biztonsági csoportok (NSG-k)](https://docs.microsoft.com/azure/virtual-network/security-overview).
- Külön, és az adatbázis-kezelő virtuális gép forgalom elkülönítése, rendelje hozzá a virtuális gép különböző hálózati adapterein. Minden hálózati adapter egy másik IP-címet kap, és minden hálózati adapter van rendelve egy másik virtuális hálózat alhálózatához. Minden alhálózati rendelkezik a különböző NSG-szabályokat. Az elkülönítés vagy a hálózati forgalom elkülönítése mérőszáma útválasztás. A hálózati átviteli sebesség kvóták beállítása nem használatos.

> [!NOTE]
> Azure-on keresztül statikus IP-címet rendelni azt jelenti, hogy hozzárendelhetők a virtuális hálózati adapterekhez. Statikus IP-címek belül a vendég operációs rendszer nem rendel hozzá egy virtuális hálózati adapterhez. Egyes Azure-szolgáltatásokhoz hasonlóan az Azure Backup támaszkodhat, hogy legalább az elsődleges virtuális hálózati adapter van beállítva, a DHCP és a statikus IP-címeket. További információkért lásd: [elhárítása Azure virtuális gépek biztonsági mentésének](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Több statikus IP-cím hozzárendelése virtuális Géphez, több virtuális hálózati adapterrel kell rendelni egy virtuális Gépet.
>


> [!IMPORTANT]
> Konfigurálás [hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) az SAP-alkalmazás és az adatbázis-kezelő réteg az olyan SAP NetWeaver - közötti kommunikációs útvonal, a Hybris-, vagy az SAP S/4HANA-alapú rendszer nem támogatott. Ez a korlátozás a teljesítmény és okból is. A SAP alkalmazás réteget és az adatbázis-kezelő réteg közötti kommunikációs útvonal egy közvetlen kell lennie. A korlátozás nem tartalmazza a [alkalmazásbiztonsági csoport (Alkalmazásbiztonsági) és NSG-szabályok](https://docs.microsoft.com/azure/virtual-network/security-overview) Ha ezen Alkalmazásbiztonsági és NSG-t szabályok lehetővé teszik a közvetlen kommunikációt elérési útja. 
>
> Más esetekben, ahol nem támogatottak a hálózati virtuális berendezések szerepelnek:
>
> * Azure virtuális gépek, amelyek Linux támasztja közötti kommunikációs útvonalak fürt csomópontjai és SBD eszközök leírtak szerint [magas rendelkezésre állás az SAP NetWeaver SUSE Linux Enterprise Server, SAP-alkalmazások az Azure Virtual machines](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse).
> * Azure virtuális gépek és a Windows Server kibővített fájlkiszolgáló (SOFS) közötti kommunikációs útvonalak beállítása akár leírtak szerint [egy SAP ASCS/SCS példányhoz Windows feladatátvevő fürtre a fürt az Azure-beli fájlmegosztás használatával](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share). 
>
> Hálózati virtuális készülékekre történő irányításához kommunikációs útvonalak is könnyen double két kommunikációs partnerek között a hálózati késést. Akkor is korlátozhatja a kritikus útvonalakat az SAP alkalmazásrétegre és az adatbázis-kezelő réteg közötti átviteli sebesség. Néhány forgatókönyvet hálózati virtuális berendezések okozhat sikertelen támasztja Linux-fürtöket. Ezek olyan esetekben, ahol a Linux támasztja fürtcsomópontok közti kommunikációra kommunikációhoz SBD eszközére egy hálózati virtuális készüléken keresztül.
>

> [!IMPORTANT]
> Egy másik Tervező, amely a *nem* az SAP alkalmazásrétegre és az adatbázis-kezelő réteg elkülönítése különböző Azure virtuális hálózatokra, amely nem támogatott a [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) egymással. Azt javasoljuk, hogy Ön elkülönítse az SAP alkalmazásréteg és adatbázis-kezelő réteg helyett az Azure virtuális hálózatban lévő alhálózat használatával más Azure virtuális hálózatok használatával. 
>
> Ha nem kívánja a javasolt, és ehelyett elkülöníthető a két réteg eltérő virtuális hálózatokra, a két virtuális hálózat lehet [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview). 
>
> Vegye figyelembe, hogy a hálózati forgalmat két közötti [társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) adatátvitel költségeit az Azure virtuális hálózatok. A SAP alkalmazás réteget és az adatbázis-kezelő réteg között cseréje hatalmas adatmennyiséget, amely több terabájt áll. Ha az SAP alkalmazásréteg és adatbázis-kezelő réteg két Azure virtuális társhálózatok között elkülönülnek, felhalmozhat jelentős költségekkel jár.

Két virtuális gépet az éles üzemben futó DBMS üzembe helyezési belüli Azure rendelkezésre állási beállítása. Az SAP-alkalmazási rétegre és a felügyeleti és műveletek forgalmat a két adatbázis-kezelő virtuális gép külön útválasztási is használhatja. Lásd a következő képet:

![Két virtuális gépet, a két alhálózat ábrája](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Forgalom átirányítása az Azure Load Balancer használatával
Az a Funkciók, például az SQL Server Always On vagy a HANA-Rendszerreplikálást használt privát virtuális IP-címek használatához az Azure load balancer konfigurálása. A load balancer mintavételi portokat határozza meg az adatbázis-kezelő aktív csomópontját, és átirányítja a forgalmat, kizárólag az aktív adatbázisba csomópont használ. 

Az adatbázis-csomópont feladatátvitel történik, ha nincs az SAP alkalmazás újrakonfigurálása szükség. Ehelyett a leggyakrabban használt SAP architektúrák újból a privát virtuális IP-cím alapján. A load balancer, a csomópont feladatátvételhez reagál, a második csomópont a magánhálózati virtuális IP-cím alapján a forgalom átirányításával.

Az Azure két különböző kínál [betölteni a terheléselosztó termékváltozatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview): egy alapszintű vagy standard Termékvátozatú. Hacsak nem szeretné telepíteni az Azure rendelkezésre állási zónák között, pontosan hajtja végre az alapszintű load balancer Termékváltozat.

Az adatbázis-kezelő virtuális gépek és az SAP alkalmazásrétegre mindig a terheléselosztón keresztül irányítja folyamatosan közötti adatforgalom? A válasz attól függ, hogyan konfigurálja a terheléselosztó. 

Jelenleg a bejövő forgalom az adatbázis-kezelő virtuális gép mindig irányítja a terheléselosztón keresztül. A kimenő forgalmat útvonal az adatbázis-kezelő virtuális gép virtuális Gépet a terheléselosztó a konfiguráció függ az alkalmazásrétegre. 

A terheléselosztó lehetővé teszi egy, a DirectServerReturn. Ha ez a lehetőség van konfigurálva, van-e az adatbázis-kezelő virtuális gépről az SAP alkalmazásrétegre irányított forgalom *nem* a terheléselosztón keresztül irányítja. Ehelyett azt közvetlenül kerül az alkalmazásrétegre. Ha DirectServerReturn nincs konfigurálva, a visszatérő forgalom, a SAP alkalmazás réteghez továbbítódik a terheléselosztón keresztül.

Azt javasoljuk, hogy DirectServerReturn konfigurál, hogy legyenek elhelyezve a SAP alkalmazás réteget és az adatbázis-kezelő réteg között terheléselosztókhoz együtt. Ez a konfiguráció csökkenti a hálózati késést a két réteg között.

Egy példa bemutatja, hogyan állítsa be ezt a konfigurációt az SQL Server Always On: [Always On rendelkezésre állási csoportokra vonatkozó ILB figyelő konfigurálása az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Ha közzétett GitHub JSON-sablonok referenciaként az SAP az Azure-beli infrastruktúra-megoldásokat használ, ez tanulmánya [sablon egy SAP 3 szintű rendszer](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Ez a sablon is megjelenik a megfelelő beállításokat a terheléselosztóhoz.

### <a name="azure-accelerated-networking"></a>Az Azure gyorsított hálózatkezelés
További csökkentése érdekében az Azure virtuális gépek közötti hálózati késés, azt javasoljuk, hogy a kiválasztott [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Azure virtuális gépeken futó SAP számítási feladat, különösen az SAP alkalmazásrétegre és az SAP DBMS-réteget a központi telepítésekor, használja azt.

> [!NOTE]
> Nem minden Virtuálisgép-típusokat támogatja a gyorsított hálózatkezelés. Az előző cikkben, amely támogatja a gyorsított hálózatkezelés virtuális gép típusok listája.
>

- - -
> ![Windows][Logo_Windows] Windows
>
> A gyorsított hálózatkezelés a Windows virtuális gépek üzembe helyezése kapcsolatban lásd: [hozzon létre egy Windows virtuális gép gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell).
>
> ![Linux][Logo_Linux] Linux
>
> További információ a Linux-disztribúció: [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

- - -

> [!NOTE]
> SUSE, a Red Hat és az Oracle Linux esetében a gyorsított hálózatkezelés az újabb verziókban is támogatja. Korábbi kiadások, mint az SLES 12 SP2 vagy RHEL 7.2 nem támogatják az Azure gyorsított hálózatkezelés.
>


## <a name="deployment-of-host-monitoring"></a>Host monitoring telepítése
Üzemi használatra, SAP-alkalmazások Azure-beli virtuális gépeken SAP használatához az lehessen monitorozási adatai a fizikai gazdagépeket, az Azure virtuális gépeket futtató gazdagép. Egy adott gazdagép-ügynök SAP javítási szintje szükség, amely lehetővé teszi, hogy ez a funkció a SAPOSCOL és SAP-gazdagép-ügynök. A pontos javítási szintet leírása itt található SAP-Jegyzetnek [1409604].

Az összetevők telepítési állapotát, hogy a gazdagép adatok SAPOSCOL és SAP-gazdagép-ügynök és életciklus-felügyeletét, ezen összetevők további információkért lásd: a [üzembe helyezési útmutató][deployment-guide].


## <a name="next-steps"></a>További lépések
Egy adott DBMS további információkért lásd:

- [SQL Server Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_sqlserver.md)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_oracle.md)
- [IBM DB2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_ibm.md)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_sapase.md)
- [SAP maxDB, a gyorsítótár Live és a webtartalom-kiszolgáló üzembe helyezés az Azure-ban](dbms_guide_maxdb.md)
- [SAP HANA az Azure-műveletek útmutatójában](hana-vm-operations.md)
- [SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek](sap-hana-availability-overview.md)
- [Azure virtuális gépeken futó SAP Hana biztonsági mentési útmutató](sap-hana-backup-guide.md)

