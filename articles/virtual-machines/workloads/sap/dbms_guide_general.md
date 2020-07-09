---
title: Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez | Microsoft Docs
description: Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez
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
ms.date: 12/04/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 581efde3128294a326bdfd08e622a8dcabe5784d
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84232651"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Az Azure Virtual Machines adatbázis-kezelő üzembe helyezésének szempontjai az SAP-munkaterheléshez
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

Ez az útmutató azon dokumentáció részét képezi, amely bemutatja, hogyan implementálhatja és telepítheti az SAP-szoftvereket Microsoft Azureon. Az útmutató elolvasása előtt olvassa el a [tervezési és megvalósítási útmutatót][planning-guide]. Ez a dokumentum a Microsoft Azure virtuális gépeken (VM-EK) az Azure infrastruktúra-szolgáltatás (IaaS) képességeinek használatával ismerteti az SAP-hez kapcsolódó adatbázis-kezelő rendszerek általános telepítési szempontjait.

A tanulmány kiegészíti az SAP-telepítési dokumentációt és az SAP-megjegyzéseket, amelyek az SAP-szoftverek telepítésének és üzembe helyezésének elsődleges erőforrásait jelölik az adott platformokon.

Ebben a dokumentumban az Azure-beli virtuális gépeken futó SAP-vel kapcsolatos adatbázis-kezelő rendszerek futtatásával kapcsolatos szempontokat ismertetjük. Ebben a fejezetben az egyes adatbázis-kezelő rendszerekre is hivatkozunk. Ehelyett a jelen dokumentumon belül az adott adatbázis-kezelő rendszerek kezelése történik.

## <a name="definitions"></a>Definíciók
A dokumentum teljes egészében a következő kifejezéseket használjuk:

* **IaaS**: infrastruktúra-szolgáltatás.
* **Péter**: szolgáltatásként szolgáló platform.
* **SaaS**: szolgáltatott szoftver.
* **SAP-összetevő**: egy egyéni SAP-alkalmazás, például az ERP központi összetevő (ECC), az üzleti raktár (BW), a Solution Manager vagy a Enterprise Portal (EP). Az SAP-összetevők a hagyományos ABAP vagy Java-technológiákon, vagy egy nem NetWeaver-alapú alkalmazáson, például üzleti objektumokon alapulnak.
* **SAP-környezet**: egy vagy több SAP-összetevő logikailag csoportosítva olyan üzleti funkciók végrehajtásához, mint a fejlesztés, a minőségbiztosítás, a képzés, a vész-helyreállítás vagy a gyártás.
* **SAP-környezet**: Ez a kifejezés a teljes SAP-eszközre vonatkozik az ügyfél informatikai környezetében. Az SAP-környezet minden éles és nem üzemi környezetet magában foglal.
* **SAP-rendszer**: az adatbázis-kezelő réteg és az alkalmazási réteg kombinációja, például egy SAP ERP fejlesztési rendszer, egy SAP Business Warehouse test rendszer vagy egy SAP CRM termelési rendszer. Az Azure üzemelő példányok esetében a két réteget a helyszíni és az Azure közötti felosztás nem támogatja. Ennek eredményeképpen az SAP-rendszer üzembe helyezése a helyszínen történik, vagy az Azure-ban van üzembe helyezve. Az SAP-környezetek különböző rendszereit üzembe helyezheti az Azure-ban vagy a helyszínen. Például üzembe helyezheti az SAP CRM fejlesztési és tesztelési rendszereit az Azure-ban, de üzembe helyezheti az SAP CRM üzemi rendszerét a helyszínen.
* **Létesítmények**közötti: olyan forgatókönyv, amelyben a virtuális gépeket egy olyan Azure-előfizetéshez telepítik, amely helyek közötti, többhelyes vagy Azure ExpressRoute kapcsolattal rendelkezik a helyszíni adatközpontok és az Azure között. A közös Azure-dokumentációban az ilyen típusú központi telepítések a létesítmények közötti forgatókönyvekben is szerepelnek. 

    A kapcsolódás oka a helyszíni tartományok, a helyszíni Active Directory és a helyszíni DNS kiterjesztése az Azure-ba. A helyszíni környezet az előfizetés Azure-eszközeire van kiterjesztve. Ezzel a bővítménnyel a virtuális gépek a helyszíni tartomány részei lehetnek. A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és szolgáltatásokat futtathatnak ezeken a virtuális gépeken, például az adatbázis-kezelő szolgáltatásokban. A helyszíni és az Azure-ban üzembe helyezett virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a forgatókönyv az SAP-eszközök Azure-beli üzembe helyezéséhez használt leggyakoribb forgatókönyv. További információ: [a VPN Gateway tervezése és kialakítása](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Az SAP Systems létesítmények közötti telepítések olyanok, ahol az SAP-rendszereket futtató Azure-beli virtuális gépek egy helyszíni tartomány tagjai, és a termelési SAP-rendszerek támogatják. A létesítmények közötti konfigurációk a részek üzembe helyezésére és az SAP-tájak Azure-ba való betöltésére is használhatók. A teljes SAP-környezetnek az Azure-ban való futtatásához a virtuális gépeknek egy helyszíni tartományhoz és Active Directory/LDAP-hez kell tartoznia. 
>
> A dokumentáció korábbi verzióiban a hibrid – IT-forgatókönyvek is megemlítve lettek. A *hibrid* kifejezés abban a tényben gyökerezik, hogy a helyszínen és az Azure-ban több helyszíni kapcsolat áll fenn. Ebben az esetben a hibrid azt is jelenti, hogy az Azure-beli virtuális gépek a helyszíni Active Directory részei.
>
>

Bizonyos Microsoft-dokumentációk többek között egy kicsit másképpen ismertetik a létesítmények közötti forgatókönyveket, különösen a magas rendelkezésre állást biztosító adatbázis-konfigurációk esetében Az SAP-vel kapcsolatos dokumentumok esetében a létesítmények közötti forgatókönyv a telephelyek közötti vagy a privát [ExpressRoute](https://azure.microsoft.com/services/expressroute/) -kapcsolatra, valamint egy, a helyszíni és az Azure közötti elosztott SAP-környezetre vezethető vissza.

## <a name="resources"></a>Erőforrások
Az Azure-beli SAP-munkaterheléseken más cikkek is elérhetők. Az Azure-beli SAP-számítási [feladatok első lépései: első lépések](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) , majd válassza ki a kívánt területét.

A következő SAP-megjegyzések az Azure-beli SAP-vel kapcsolatosak, a jelen dokumentumban foglalt területek tekintetében.

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: támogatott termékek és Azure-beli virtuális gépek típusai |
| [2015553] |SAP on Microsoft Azure: támogatási előfeltételek |
| [1999351] |Az SAP-hez készült bővített Azure monitoring hibaelhárítása |
| [2178632] |Az SAP-hez Microsoft Azure legfontosabb monitorozási metrikák |
| [1409604] |Virtualizáció Windows rendszeren: továbbfejlesztett figyelés |
| [2191498] |SAP Linuxon az Azure-ban: továbbfejlesztett monitorozás |
| [2039619] |SAP-alkalmazások Microsoft Azure az Oracle Database használatával: támogatott termékek és verziók |
| [2233094] |DB6: SAP-alkalmazások az Azure-ban az IBM DB2-vel Linux, UNIX és Windows rendszeren: további információk |
| [2243692] |Linux on Microsoft Azure (IaaS) VM: SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7. x: telepítés és frissítés |
| [2069760] |Oracle Linux 7. x SAP telepítése és frissítése |
| [1597355] |A Linux rendszerhez készült swap-space-javaslat |
| [2171857] |Oracle Database 12c: fájlrendszer-támogatás Linux rendszeren |
| [1114181] |Oracle Database 11g: fájlrendszer-támogatás Linux rendszeren |


A Linux rendszerhez készült SAP-megjegyzésekkel kapcsolatos információkért tekintse meg az [SAP Community wikit](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes).

Szükség van a Microsoft Azure architektúra és a Microsoft Azure virtuális gépek üzembe helyezésének és működtetésének módjára. További információ: Azure- [dokumentáció](https://docs.microsoft.com/azure/).

Általánosságban elmondható, hogy a Windows, a Linux és az adatbázis-kezelő rendszer telepítése és konfigurációja lényegében ugyanaz, mint bármely virtuális gép vagy operációs rendszer nélküli gép, amelyet a helyszínen telepít. Az Azure IaaS használata során különböző architektúra-és rendszerkezelési implementációs döntések vannak. Ez a dokumentum ismerteti a speciális architektúra-és rendszerkezelési különbségeket, amelyeket az Azure IaaS használatakor elő kell készíteni.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Egy virtuális gép tárolási szerkezete RDBMS üzemelő példányokhoz
Ennek a fejezetnek a követéséhez olvassa el és Ismerje meg a [telepítési útmutató][deployment-guide] [ebben a fejezetben][deployment-guide-3] ismertetett információkat. A fejezet elolvasása előtt meg kell ismernie és ismernie kell a különböző virtuálisgép-sorozatokat, valamint a standard és a Premium Storage közötti különbségeket. 

Az Azure Storage Azure-beli virtuális gépekhez való megismeréséhez lásd:

- Az [Azure Windows rendszerű virtuális gépek felügyelt lemezeinek bemutatása](../../windows/managed-disks-overview.md).
- [Bevezetés a Managed Disks szolgáltatásba az Azure Linux rendszerű virtuális gépekhez](../../linux/managed-disks-overview.md).

Alapszintű konfigurációban általában ajánlott egy üzembe helyezési struktúra, amelyben az operációs rendszer, az adatbázis-KEZELŐrendszer és a végső SAP bináris fájljai el vannak különítve az adatbázisfájloktől. Javasoljuk, hogy az Azure-beli virtuális gépeken futó SAP-rendszerek rendelkezzenek az alapszintű VHD-vel, illetve a lemezzel, az operációs rendszerrel, az adatbázis-kezelő rendszer végrehajtható fájljaival és az SAP-végrehajtható fájlokkal. 

Az adatbázis-kezelői és a naplófájlok tárolása a standard Storage vagy a Premium Storage szolgáltatásban történik. Ezek külön lemezeken tárolódnak, és logikai lemezként vannak csatolva az eredeti Azure operációs rendszer rendszerképének virtuális géphez. A Linux rendszerű központi telepítések esetében különböző javaslatok vannak dokumentálva, különösen SAP HANA.

Amikor megtervezi a lemez elrendezését, keresse meg az alábbi elemek közötti legjobb egyensúlyt:

* Az adatfájlok száma.
* A fájlokat tartalmazó lemezek száma.
* Egyetlen lemez IOPS kvótái.
* Az adatátviteli sebesség lemezenként.
* A virtuálisgép-méretekben lehetséges további adatlemezek száma.
* A virtuális gép által biztosított teljes tárolási teljesítmény.
* A különböző Azure Storage-típusok késése megadható.
* VM SLA-kat.

Az Azure IOPS-kvótát kényszerít ki adatlemezként. Ezek a kvóták eltérnek a standard szintű és a Premium Storage-ban üzemeltetett lemezek esetében. Az I/O-késés is eltér a két tárolási típus között. A Premium Storage jobb I/O-késést biztosít. 

A különböző virtuálisgép-típusok csak korlátozott számú adatlemezzel rendelkezhetnek. Egy másik korlátozás, hogy csak bizonyos virtuálisgép-típusok használhatják a Premium Storage-t. Általában úgy dönt, hogy egy bizonyos virtuálisgép-típust használ a CPU-és a memória-követelmények alapján. Érdemes figyelembe venni a IOPS, a késés és a lemez átviteli sebességének követelményeit is, amelyek általában a lemezek számával vagy a Premium Storage-lemezek típusával vannak méretezve. A IOPS száma és az egyes lemezek által megvalósított átviteli sebesség a lemez méretét, különösen a prémium szintű tárolást is előírhatja.

> [!NOTE]
> Az adatbázis-kezelő üzembe helyezése esetén ajánlott a Premium Storage használata bármilyen adatfájlhoz, tranzakciós naplóhoz vagy a fájlok visszaállításához. Nem számít, hogy éles vagy nem üzemi rendszereket kíván üzembe helyezni.

> [!NOTE]
> Az [Azure egyedi virtuálisgép](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)-szolgáltatói szerződésének kihasználása érdekében minden csatolt lemeznek a Premium Storage-típusnak kell lennie, amely tartalmazza az alap VHD-t.

> [!NOTE]
> Az Azure-adatközpontok melletti közös helyen található, harmadik féltől származó adatközpontokban található, a fő adatbázisfájlok, például az adatfájlok és a naplófájlok üzemeltetése nem támogatott. Az SAP-alapú számítási feladatokhoz csak a natív Azure-szolgáltatásként jelölt tárolók támogatottak az SAP-adatbázisok adat-és tranzakciós naplófájljaiban.

Az adatbázisfájlok, valamint a naplófájlok és az ismételt fájlok elhelyezését, valamint a használt Azure-tároló típusát a IOPS, a késés és az átviteli sebesség követelményei határozzák meg. Ahhoz, hogy elegendő IOPS legyen, több lemezt kell használnia, vagy nagyobb Premium Storage-lemezt kell használnia. Ha több lemezt használ, hozzon létre egy szoftveres szalagot az adatfájlokat vagy a naplót és a visszaadott fájlokat tartalmazó lemezek között. Ilyen esetekben a IOPS és a lemez átviteli sebessége (SLA) a mögöttes Premium Storage-lemezeken, vagy a standard szintű IOPS maximálisan elérhető halmozódnak fel az eredményül kapott csíkkészleteket.

Amint azt már említettük, ha a IOPS-követelmény meghaladja az egyetlen VHD-t, akkor az adatbázisfájlok számának kiegyensúlyozására van szükség a virtuális merevlemezek IOPS. A IOPS-terhelés lemezek közötti elosztásának legegyszerűbb módja, ha a különböző lemezeken lévő szoftvereket kíván létrehozni. Ezután helyezzen el egy több adatfájlt az SAP adatbázis-kezelő rendszerből a szoftveres szalagról kivésett logikai egységeken. A Stripe-ben lévő lemezek számát a IOPs igények, a lemez átviteli sebessége és a szükséges mennyiségi követelmények vezérlik.


---
> ![Windows][Logo_Windows] Windows
>
> Javasoljuk, hogy a Windows Storage Spaces használatával hozzon létre több Azure VHD-t tartalmazó szalagos készleteket. Használjon legalább Windows Server 2012 R2 vagy Windows Server 2016 rendszert.
>
> ![Linux][Logo_Linux] Linux
>
> Csak a MDADM és a Logical Volume Manager (LVM) támogatott a szoftveres RAID Linuxon való létrehozásához. További információkért lásd:
>
> - [Szoftveres RAID konfigurálása Linux rendszeren a](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) MDADM használatával
> - [Az LVM konfigurálása linuxos virtuális gépen az Azure-ban az](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) LVM használatával
>
>

---

> [!NOTE]
> Mivel az Azure Storage megtartja a virtuális merevlemezek három lemezképét, nem érdemes redundanciát beállítani a szalagon. A csíkozást csak úgy kell konfigurálnia, hogy az I/o-t a különböző VHD-k között osszák el.
>

### <a name="managed-or-nonmanaged-disks"></a>Felügyelt vagy nem felügyelt lemezek
Az Azure Storage-fiók egy adminisztratív szerkezet, valamint a korlátozások tárgya is. A korlátozások eltérnek a standard Storage-fiókok és a Premium Storage-fiókok között. További információ a képességekről és a korlátozásokról: az [Azure Storage skálázhatósági és teljesítménybeli céljai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets).

A standard szintű tároláshoz ne feledje, hogy a Storage-fiók IOPS korlátja van. Tekintse meg a **teljes kérelmek arányát** tartalmazó sort az [Azure Storage skálázhatósági és teljesítményi célpontjai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)című cikkben. Az Azure-előfizetéshez tartozó Storage-fiókok száma is kezdeti korláttal rendelkezik. A virtuális merevlemezek elosztása a nagyobb SAP-környezethez különböző tárolási fiókok között, hogy elkerülje a tárolási fiókok korlátait. Ez unalmas feladat, ha több száz virtuális géppel dolgozik, és több mint ezer VHD-t használ.

Mivel az adatbázis-kezelők standard szintű tárolását az SAP-munkaterhelésekkel együtt nem ajánlott használni, a standard szintű tárolásra vonatkozó referenciák és javaslatok erre a rövid [cikkre korlátozódnak.](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

A Microsoft a különböző Azure Storage-fiókokban lévő virtuális merevlemezek tervezésével és üzembe helyezésével kapcsolatos adminisztratív munka elkerülése érdekében az [azure Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017-ben mutatkozott be. A Managed Disks szolgáltatás a standard Storage és a Premium Storage esetében érhető el. A felügyelt lemezek fő előnyei a nem felügyelt lemezekhez képest a következők:

- A felügyelt lemezek esetében az Azure a telepítéskor automatikusan elosztja a különböző virtuális merevlemezeket a különböző tárolási fiókok között. Így a Storage-fiók korlátozza az adatmennyiséget, az I/O-teljesítményt és a IOPS-t.
- A felügyelt lemezek használata esetén az Azure Storage tiszteletben tartja az Azure rendelkezésre állási csoportjaival kapcsolatos fogalmakat. Ha a virtuális gép egy Azure-beli rendelkezésre állási csoport részét képezi, a virtuális gép alapszintű VHD-je és csatolt lemeze különböző hiba-és frissítési tartományokra lesz telepítve.


> [!IMPORTANT]
> Az Azure Managed Disks előnyeinek kihasználásával ajánlott az Azure Managed Disks használata az adatbázis-kezelő üzembe helyezéséhez és az SAP-környezetek általános telepítéséhez.
>

A nem felügyelt lemezek felügyelt lemezekre való átalakítását lásd:

- [Windows rendszerű virtuális gép konvertálása nem felügyelt lemezekről a felügyelt lemezekre](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks).
- [Linux rendszerű virtuális gép konvertálása nem felügyelt lemezekről a felügyelt lemezekre](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks).


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Gyorsítótárazás a virtuális gépek és az adatlemezek esetében
Ha lemezeket csatlakoztat a virtuális gépekhez, kiválaszthatja, hogy a virtuális gép és az Azure Storage-ban található lemezek közötti I/O-forgalom gyorsítótárazva van-e. A standard és a Premium Storage két különböző technológiát használ az ilyen típusú gyorsítótárhoz.

A következő javaslatok ezeket az I/O-tulajdonságokat feltételezik a szabványos adatbázis-kezelő rendszerekben:

- Ez többnyire egy adatbázis adatfájljainak olvasási munkaterhelése. Ezek az olvasási teljesítmény kritikus fontosságúak az adatbázis-kezelő rendszer számára.
- Az adatfájlok írása az ellenőrzőpontokon vagy állandó streamen alapuló adatsorokban történik. Egy nap átlaga kevesebb írást végez, mint az olvasás. Az adatfájlok olvasásával szemben ezek az írások aszinkron módon jelennek meg, és nem tartanak fenn felhasználói tranzakciókat.
- A tranzakciónapló vagy a fájlok visszaállítása alig szól. A kivételek nagy méretű I/o-műveletek, amikor tranzakciós napló biztonsági másolatokat hajt végre.
- A tranzakció vagy a naplófájlok visszaállításának fő terhelése az írás. A számítási feladat jellegétől függően akár 4 KB-os, akár más esetekben az i/O-méretek 1 MB vagy több.
- A lemezen lévő összes írást megbízható módon kell megőrizni.

A standard szintű tároláshoz a lehetséges gyorsítótár-típusok a következők:

* None
* Olvasás
* Olvasás/írás

A konzisztens és determinisztikus teljesítmény érdekében állítsa be a szabványos tárterület gyorsítótárazását az összes olyan lemez esetében, amely az adatbázis-kezelői szolgáltatással kapcsolatos adatfájlokat tartalmaz, a fájlok naplózása és visszavonása, valamint a tábla tárterülete **nincs**. Az alapszintű virtuális merevlemez gyorsítótárazása az alapértelmezettnél továbbra is megmarad.

A Premium Storage esetében a következő gyorsítótárazási lehetőségek léteznek:

* None
* Olvasás
* Olvasás/írás
* Nincs + írásgyorsító, amely csak az Azure M sorozatú virtuális gépekhez használható
* Olvasási és írásgyorsító, amely csak az Azure M sorozatú virtuális gépekhez használható

A Premium Storage esetében javasoljuk, hogy az olvasási gyorsítótárazást használja az SAP-adatbázis **adatfájljaihoz** , és válassza **a nincs gyorsítótárazás lehetőséget a naplófájl (ok) lemezei**számára.

Az M sorozatú üzemelő példányok esetében ajánlott az Azure írásgyorsító használata az adatbázis-kezelő üzembe helyezéséhez. Az Azure írásgyorsító részleteiről, korlátozásáról és üzembe helyezéséről az [Írásgyorsító engedélyezése](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)című témakörben olvashat.


### <a name="azure-nonpersistent-disks"></a>Azure nem állandó lemezek
Az Azure-beli virtuális gépek nem állandó lemezeket kínálnak a virtuális gép üzembe helyezésekor. A virtuális gépek újraindításakor a rendszer az ezen meghajtókon lévő összes tartalmat törli. Mivel az adatfájlok és az adatbázis-fájlok naplózása és megismétlése nem lehetséges, a nem megőrzött meghajtókon nem kell elhelyezni. Bizonyos adatbázisok esetében kivételek lehetnek, ahol a nem megőrzött meghajtók alkalmasak lehetnek a tempdb és a temp tablespaces-re. Kerülje ezeket a meghajtókat A-sorozatú virtuális gépekhez, mert ezek a nem megőrzött meghajtók korlátozottak az adott virtuálisgép-családdal való átviteli sebességben. 

További információ: [az ideiglenes meghajtó megértése az Azure-beli Windows rendszerű virtuális gépeken](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/).

---
> ![Windows][Logo_Windows] Windows
>
> A D meghajtó egy Azure-beli virtuális gépen egy nem megőrzött meghajtó, amelyet az Azure számítási csomópontján lévő helyi lemezek támogatnak. Mivel a rendszer nem őrzi meg a t, a D meghajtón lévő tartalomon végrehajtott módosítások elvesznek a virtuális gép újraindításakor. A változások közé tartoznak a tárolt fájlok, a létrehozott könyvtárak és a telepített alkalmazások.
>
> ![Linux][Logo_Linux] Linux
>
> A Linux Azure virtuális gépek automatikusan csatlakoztatnak egy meghajtót a/mnt/Resource-on, amely az Azure számítási csomópontján helyi lemezek által támogatott, nem őrzött meghajtó. Mivel a szolgáltatás nem áll fenn, a/mnt/Resource lévő tartalmakon végrehajtott módosítások elvesznek a virtuális gép újraindításakor. A változások közé tartoznak a tárolt fájlok, a létrehozott könyvtárak és a telepített alkalmazások.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>Microsoft Azure Storage rugalmasság
Microsoft Azure Storage az alapszintű virtuális merevlemezt az operációs rendszer és a csatlakoztatott lemezek vagy Blobok alapján tárolja legalább három különálló tárolási csomóponton. Ezt a típusú tárolót helyileg redundáns tárolónak (LRS) nevezzük. A LRS az Azure összes tárolási típusának alapértelmezett értéke.

Más redundancia-módszerek is vannak. További információ: [Azure Storage-replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>A Premium Storage a javasolt tárolási típus az adatbázis-kezelő virtuális gépekhez, valamint az adatbázist és a naplófájlokat tároló lemezek tárolására és a fájlok visszaállítására. Az egyetlen elérhető redundancia-módszer a Premium Storage LRS. Ennek eredményeképpen olyan adatbázis-metódusokat kell konfigurálnia, amelyek lehetővé teszik az adatbázis-adatreplikálást egy másik Azure-régióban vagy rendelkezésre állási zónában. Az adatbázis-metódusok közé tartoznak a SQL Server always on, az Oracle-adatvédelem és a HANA rendszer replikálása.


> [!NOTE]
> Az adatbázis-kezelő üzembe helyezése esetén a Geo-redundáns tárolás (GRS) használata nem ajánlott a standard szintű tároláshoz. A GRS súlyosan befolyásolja a teljesítményt, és nem veszi figyelembe az írási sorrendet a virtuális géphez csatolt különböző VHD-k között. Nem kell megbecsülni az írási sorrendet a különböző VHD-k esetében, amelyek esetleg inkonzisztens adatbázisokra vezetnek a replikálási cél oldalán. Ez a helyzet akkor fordul elő, ha az adatbázis és a napló, illetve az ismételt fájlok több virtuális merevlemez között oszlanak el, a forrás virtuális gép oldalán általában.



## <a name="vm-node-resiliency"></a>VM-csomópont rugalmassága
Az Azure számos különböző SLA-t kínál a virtuális gépekhez. További információkért tekintse [meg a Virtual Machinesra vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)legújabb kiadását. Mivel az adatbázis-kezelő réteg általában kritikus fontosságú a rendelkezésre álláshoz egy SAP-rendszeren, meg kell ismernie a rendelkezésre állási csoportokat, a rendelkezésre állási zónákat és a karbantartási eseményeket. Ezen fogalmakkal kapcsolatos további információkért lásd: [Windows rendszerű virtuális gépek rendelkezésre állásának kezelése az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) , és [Az Azure-ban elérhető linuxos virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).

Az üzemi adatbázis-kezelői forgatókönyvek SAP-munkaterheléssel való használatának minimális javaslata a következő:

- Két virtuális gép üzembe helyezése egy különálló rendelkezésre állási csoportba ugyanabban az Azure-régióban.
- Futtassa ezt a két virtuális gépet ugyanabban az Azure-beli virtuális hálózaton, és csatolja őket ugyanahhoz az alhálózathoz.
- Az adatbázis-metódusok használatával a gyors készenléti állapotot a második virtuális géppel is megtarthatja. A metódusok a következők lehetnek: SQL Server always on, Oracle-adatvédelem vagy HANA rendszerbeli replikálás.

Egy másik Azure-régióban is üzembe helyezhet egy harmadik virtuális gépet, és ugyanazokat az adatbázis-metódusokat használva egy másik Azure-régióban is megadhat egy aszinkron replikát.

Az Azure rendelkezésre állási csoportok beállításával kapcsolatos információkért tekintse meg [ezt az oktatóanyagot](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Azure hálózati megfontolások
Nagyméretű SAP-telepítések esetén használja az [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)tervét. A virtuális hálózati konfigurációhoz, valamint az engedélyek és a szerepkör-hozzárendelések használata a szervezet különböző részein.

Ezek az ajánlott eljárások több száz ügyfél-telepítés eredménye:

- Az SAP-alkalmazás üzembe helyezésének virtuális hálózatai nem férnek hozzá az internethez.
- Az adatbázis virtuális gépei ugyanabban a virtuális hálózatban futnak, mint az alkalmazási réteg.
- A virtuális hálózaton belüli virtuális gépeken a magánhálózati IP-cím statikus kiosztása van. További információt [az IP-címek típusai és a kiosztási módszerek az Azure-ban](../../../virtual-network/public-ip-addresses.md)című témakörben talál.
- Az adatbázis-kezelői virtuális gépekre és *rendszerre* irányuló útválasztási korlátozások nincsenek beállítva a helyi adatbázis-kezelő virtuális gépekre telepített tűzfalakkal. Ehelyett a forgalom útválasztása [hálózati biztonsági csoportokkal (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)van definiálva.
- Az adatbázis-kezelő rendszerbeli virtuális gépre irányuló forgalom elkülönítéséhez és elkülönítéséhez rendeljen hozzá különböző hálózati adaptereket a virtuális géphez. Minden hálózati adapter egy másik IP-címet kap, és minden hálózati adapter egy másik virtuális hálózati alhálózathoz van rendelve. Minden alhálózat különböző NSG-szabályokkal rendelkezik. A hálózati forgalom elkülönítése vagy elkülönítése az Útválasztás mértéke. A hálózati átviteli sebességre vonatkozó kvóták beállítása nem használható.

> [!NOTE]
> Statikus IP-címek az Azure-ban való hozzárendelésével hozzárendelheti őket az egyes virtuális hálózati adapterekhez. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy virtuális hálózati adapterhez. Bizonyos Azure-szolgáltatások, például a Azure Backup támaszkodnak arra, hogy legalább az elsődleges virtuális NIC DHCP-re van beállítva, és nem statikus IP-címekre. További információ: az Azure-beli [virtuális gépek biztonsági mentésének hibája](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking). Ha több statikus IP-címet szeretne hozzárendelni egy virtuális GÉPHEZ, rendeljen több virtuális hálózati adaptert egy virtuális GÉPHEZ.
>


> [!IMPORTANT]
> A [hálózati virtuális berendezések](https://azure.microsoft.com/solutions/network-appliances/) konfigurálása az SAP-alkalmazás és az SAP NetWeaver-, Hybris-vagy S/4HANA-alapú SAP-rendszer adatbázis-kezelői rétege közötti kommunikációs útvonalon nem támogatott. Ez a korlátozás működési és teljesítménybeli okokból áll fenn. Az SAP-alkalmazás rétege és az adatbázis-kezelő réteg közötti kommunikációs útvonalnak közvetlennek kell lennie. A korlátozás nem tartalmazza az [Application Security Group (ASG) és a NSG szabályokat,](https://docs.microsoft.com/azure/virtual-network/security-overview) ha ezek a ASG-és NSG-szabályok engedélyezik a közvetlen kommunikáció elérési útját. 
>
> Egyéb forgatókönyvek, amelyekben a hálózati virtuális berendezések nem támogatottak:
>
> * A Linux pacemaker-fürtcsomópontok és SBD-eszközöket képviselő Azure-beli virtuális gépek közötti kommunikációs útvonalak a [magas rendelkezésre állású SAP NetWeaver Azure-beli virtuális gépeken SUSE Linux Enterprise Server SAP-alkalmazásokhoz](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)című témakörben leírtak szerint.
> * Az Azure-beli virtuális gépek és a Windows Server Kibővíthető fájlkiszolgáló (SOFS) közötti kommunikációs útvonalak az Azure-beli [fájlmegosztás használatával egy Windows feladatátvevő fürtön a fürt SAP ASCS/SCS-példányának](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share)leírtak szerint. 
>
> A kommunikációs útvonalon található hálózati virtuális berendezések könnyedén megduplázzák két kommunikációs partner közötti hálózati késést. Emellett az SAP-alkalmazás és az adatbázis-kezelő réteg közötti kritikus útvonalakon is korlátozhatják az átviteli sebességet. Bizonyos felhasználói helyzetekben a hálózati virtuális készülékek a pacemaker Linux-fürtök meghibásodását okozhatják. Ezek olyan esetek, amikor a Linux pacemaker-fürtcsomópontok közötti kommunikáció egy hálózati virtuális berendezésen keresztül kommunikál a SBD-eszközzel.
>

> [!IMPORTANT]
> Egy másik kialakítás, amely *nem* támogatott, az SAP-alkalmazás rétegének és az adatbázis-kezelő rétegnek a különböző Azure- [beli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuális hálózatokra való elkülönítése, amelyek nincsenek egymással társítva. Azt javasoljuk, hogy a különböző Azure-beli virtuális hálózatok használata helyett az SAP-alkalmazás rétegét és az adatbázis-kezelő réteget az Azure virtuális hálózatban lévő alhálózatok használatával különítse el. 
>
> Ha úgy dönt, hogy nem követi a javaslatot, hanem elkülöníti a két réteget különböző virtuális hálózatokra, a két virtuális hálózatot [egymással kell összeállítani.](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) 
>
> Vegye figyelembe, hogy a két egymásra épülő Azure- [beli](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) virtuális hálózat közötti hálózati forgalomra az átvitel költségei vonatkoznak. A sok terabájtot tartalmazó hatalmas adatmennyiség az SAP-alkalmazás rétege és az adatbázis-kezelő réteg között van cserélve. Jelentős költségek halmozódnak fel, ha az SAP-alkalmazás réteget és az adatbázis-kezelő réteget két, egymástól független Azure-beli virtuális hálózat között különíti el.

Hozzon létre két virtuális gépet az üzemi adatbázis-kezelő üzembe helyezéséhez egy Azure rendelkezésre állási csoporton belül. Külön útválasztást is használhat az SAP-alkalmazás rétegéhez, valamint a felügyeleti és műveleti forgalmat a két adatbázis-kezelő virtuális gép számára. Lásd a következő képet:

![Két virtuális gép diagramja két alhálózatban](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Forgalom átirányítása Azure Load Balancer használatával
Az Azure Load Balancer konfigurálásához olyan funkciókban használt magánhálózati virtuális IP-címeket kell használni, mint a SQL Server always on vagy a HANA rendszer-replikáció. A terheléselosztó mintavételes portokkal határozza meg az aktív adatbázis-kezelő csomópontot, és kizárólag az adott Active Database-csomópontra irányítja át a forgalmat. 

Ha feladatátvétel van az adatbázis-csomóponton, nincs szükség az SAP-alkalmazás újrakonfigurálására. Ehelyett a leggyakoribb SAP-alkalmazás-architektúrák újrakapcsolódnak a magánhálózati virtuális IP-címhez. Eközben a terheléselosztó a csomópont feladatátvételét úgy hajtja végre, hogy átirányítja a forgalmat a magánhálózati virtuális IP-címről a második csomópontra.

Az Azure két különböző [terheléselosztó SKU](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)-t kínál: egy alapszintű SKU-t és egy standard SKU-t. Ha az Azure rendelkezésre állási zónáit nem szeretné központilag telepíteni, az alapszintű Load Balancer SKU is rendben van.

Az adatbázis-kezelői virtuális gépek és az SAP-alkalmazás rétege mindig a terheléselosztó felé irányítja a forgalmat? A válasz a terheléselosztó konfigurálásának módjától függ. 

Ekkor a rendszer mindig a terheléselosztó használatával irányítja át az adatbázis-kezelő virtuális gépre irányuló bejövő forgalmat. Az adatbázis-kezelő virtuális gépről az alkalmazás rétegbeli virtuális gépre irányuló kimenő forgalmi útvonal a terheléselosztó konfigurációjától függ. 

A terheléselosztó a DirectServerReturn lehetőséget kínálja. Ha ez a beállítás be van állítva, az adatbázis-kezelő virtuális gépről az SAP-alkalmazás réteg felé irányuló forgalmat a terheléselosztó *nem* irányítja át. Ehelyett közvetlenül az alkalmazás rétegére kerül. Ha a DirectServerReturn nincs konfigurálva, a rendszer átirányítja az SAP-alkalmazás rétegének visszaküldött forgalmát a terheléselosztó használatával.

Azt javasoljuk, hogy a DirectServerReturn az SAP-alkalmazási réteg és az adatbázis-kezelő réteg között elhelyezni kívánt terheléselosztó kombinációjában konfigurálja. Ez a konfiguráció csökkenti a két réteg közötti hálózati késést.

Ennek a konfigurációnak a SQL Server always on használatával történő beállításával kapcsolatos példát a [ILB-figyelő konfigurálása always on rendelkezésre állási csoportok számára az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)című témakörben talál.

Ha közzétett GitHub JSON-sablonokat használ az SAP-infrastruktúra Azure-beli üzembe helyezésére, tanulmányozza ezt a [sablont egy SAP 3 szintű rendszerhez](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Ebben a sablonban a terheléselosztó helyes beállításait is láthatja.

### <a name="azure-accelerated-networking"></a>Azure gyorsított hálózatkezelés
Az Azure-beli virtuális gépek közötti hálózati késés további csökkentése érdekében javasoljuk, hogy az [Azure gyorsított hálózatkezelést](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)válassza. Akkor használja, ha Azure-beli virtuális gépeket telepít egy SAP-munkaterheléshez, különösen az SAP-alkalmazás rétegéhez és az SAP adatbázis-kezelő rétegéhez.

> [!NOTE]
> Nem minden VM-típus támogatja a gyorsított hálózatkezelést. Az előző cikk felsorolja a gyorsított hálózatkezelést támogató virtuálisgép-típusokat.
>

---
> ![Windows][Logo_Windows] Windows
>
> Ha szeretné megtudni, hogyan helyezhet üzembe virtuális gépeket gyorsított hálózatkezeléssel Windows rendszeren, tekintse meg [a Windows rendszerű virtuális gép gyorsított hálózatkezeléssel történő létrehozását](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)ismertető témakört.
>
> ![Linux][Logo_Linux] Linux
>
> A Linux-disztribúcióval kapcsolatos további információkért lásd: [linuxos virtuális gép létrehozása gyorsított hálózatkezeléssel](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli).
>
>

---

> [!NOTE]
> SUSE, Red Hat és Oracle Linux esetén a gyorsított hálózatkezelés támogatott a legutóbbi kiadásokban. A korábbi kiadások, például a SLES 12 SP2 vagy a RHEL 7,2 nem támogatják az Azure gyorsított hálózatkezelést.
>


## <a name="deployment-of-host-monitoring"></a>A gazdagép figyelésének üzembe helyezése
Az SAP-alkalmazások Azure-beli virtuális gépeken történő éles használatához az SAP-nek képesnek kell lennie az Azure-beli virtuális gépeket futtató fizikai gazdagépekről származó figyelési adatok lekérésére. A SAPOSCOL és az SAP-gazdagép ügynöke számára olyan speciális SAP-gazdagépek javítási szintje szükséges, amely engedélyezi ezt a funkciót. A pontos javítási szintet a [1409604]-es SAP-Megjegyzés dokumentálja.

További információ a SAPOSCOL és az SAP-állomás ügynökeit tároló összetevők üzembe helyezéséről, valamint ezeknek az összetevőknek a életciklus-kezeléséről: [telepítési útmutató][deployment-guide].


## <a name="next-steps"></a>További lépések
Egy adott adatbázis-kezelő rendszerről további információt a következő témakörben talál:

- [SQL Server rendszerű Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_sqlserver.md)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_oracle.md)
- [IBM DB2 Azure Virtual Machines adatbázis-kezelő üzembe helyezés az SAP-munkaterheléshez](dbms_guide_ibm.md)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_sapase.md)
- [SAP-maxDB, élő gyorsítótár és a Content Server üzembe helyezése az Azure-ban](dbms_guide_maxdb.md)
- [SAP HANA az Azure-műveletek útmutatójában](hana-vm-operations.md)
- [SAP HANA magas rendelkezésre állás az Azure Virtual Machines szolgáltatásban](sap-hana-availability-overview.md)
- [Biztonsági mentési útmutató Azure-beli virtuális gépek SAP HANAához](sap-hana-backup-guide.md)

