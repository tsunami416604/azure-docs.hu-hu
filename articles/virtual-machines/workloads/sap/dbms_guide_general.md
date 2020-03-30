---
title: Az Azure Virtual Machines DBMS üzembe helyezésének szempontjai az SAP-munkaterheléshez | Microsoft dokumentumok
description: Az Azure Virtual Machines DBMS üzembe helyezésének szempontjai az SAP-munkaterheléshez
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
ms.openlocfilehash: a852ddc68a6f51e677e5ff2e641ada25f4bf0105
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "70101363"
---
# <a name="considerations-for-azure-virtual-machines-dbms-deployment-for-sap-workload"></a>Az Azure Virtual Machines DBMS üzembe helyezésének szempontjai az SAP-munkaterheléshez
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

Ez az útmutató az SAP-szoftverek Microsoft Azure-beli implementiszatáról és központi telepítéséről szóló dokumentáció része. Mielőtt elolvasna ebből az útmutatóból, olvassa el a [Tervezési és megvalósítási útmutatót.][planning-guide] Ez a dokumentum az SAP-val kapcsolatos DBMS-rendszerek általános üzembe helyezési szempontjait ismerteti a Microsoft Azure virtuális gépeken az Azure-infrastruktúra szolgáltatásként (IaaS) képességeinek használatával.

A papír kiegészíti az SAP telepítési dokumentációt és az SAP Notes-t, amelyek az SAP-szoftverek telepítésének és központi telepítésének elsődleges erőforrásait képviselik az adott platformokon.

Ebben a dokumentumban az SAP-val kapcsolatos DBMS-rendszerek Azure-beli virtuális gépeken való futtatásával kapcsolatos szempontok kerülnek bevezetésre. Ebben a fejezetben kevés hivatkozás található konkrét DBMS-rendszerekre. Ehelyett az adott DBMS rendszerek et ebben a dokumentumban kezeli a dokumentum, a dokumentum után.

## <a name="definitions"></a>Meghatározások
A dokumentumban a következő kifejezéseket használjuk:

* **IaaS**: Infrastruktúra mint szolgáltatás.
* **PaaS**: Platform szolgáltatásként.
* **SaaS**: Szoftver, mint szolgáltatás.
* **SAP-összetevő:** Egyedi SAP-alkalmazás, például ERP központi összetevő (ECC), Üzleti raktár (BW), Solution Manager vagy Enterprise Portal (EP). Az SAP-összetevők hagyományos ABAP vagy Java technológiákon vagy nem NetWeaver-alapú alkalmazásokon, például üzleti objektumokon alapulhatnak.
* **SAP-környezet:** Egy vagy több SAP-összetevő logikailag csoportosítva egy üzleti funkció, például fejlesztés, minőségbiztosítás, képzés, vész-helyreállítási vagy éles környezet.
* **SAP fekvő:** Ez a kifejezés az ügyfél informatikai környezetében lévő teljes SAP-eszközökre vonatkozik. Az SAP-környezet tartalmazza az összes éles és nem termelési környezetben.
* **SAP-rendszer:** Egy DBMS-réteg és egy SAP ERP fejlesztési rendszer, egy SAP Business Warehouse tesztrendszer vagy egy SAP CRM termelési rendszer alkalmazásrétegének kombinációja. Az Azure-telepítések, a két réteg felosztása a helyszíni és az Azure nem támogatott. Ennek eredményeképpen az SAP-rendszer vagy a helyszínen van telepítve, vagy az Azure-ban van telepítve. Az SAP-környezet különböző rendszereit az Azure-ban vagy a helyszíni környezetben is üzembe helyezheti. Például telepítheti az SAP CRM fejlesztési és tesztelési rendszerek et az Azure-ban, de üzembe helyezheti az SAP CRM éles rendszert a helyszínen.
* **Telephelyek közötti:** Egy olyan forgatókönyvet ismertet, amelyben a virtuális gépek olyan Azure-előfizetésre vannak telepítve, amely a helyszíni adatközpontok és az Azure-adatközpontok és az Azure között helyek ről helyekre, több telephelyre vagy Azure ExpressRoute-kapcsolattal rendelkezik. A közös Azure-dokumentációban az ilyen típusú központi telepítések is le, mint a telephelyek közötti forgatókönyvek. 

    A kapcsolat oka a helyszíni tartományok, a helyszíni Active Directory és a helyszíni DNS Azure-ra való kiterjesztése. A helyszíni környezet az előfizetés Azure-eszközeire is kiterjed. Ezzel a bővítőhevel a virtuális gépek a helyszíni tartomány részét lehet tenni. A helyszíni tartomány tartományi felhasználói hozzáférhetnek a kiszolgálókhoz, és futtathatnak szolgáltatásokat ezeken a virtuális gépeken, például a DBMS-szolgáltatásokon. A helyszínen üzembe helyezett virtuális gépek és az Azure-ban üzembe helyezett virtuális gépek közötti kommunikáció és névfeloldás lehetséges. Ez a forgatókönyv a leggyakoribb forgatókönyv az SAP-eszközök azure-beli üzembe helyezéséhez. További információ: [Tervezés és tervezés VPN-átjáróhoz.](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design)

> [!NOTE]
> Az SAP-rendszerek telephelyközi üzembe helyezései azok, ahol az SAP-rendszereket futtató Azure virtuális gépek egy helyszíni tartomány tagjai, és éles SAP-rendszerek esetén támogatottak. A létesítmények közötti konfigurációk támogatottak az alkatrészek vagy a teljes SAP-környezetek Azure-ba való üzembe helyezéséhez. Még a teljes SAP-környezet futtatásához az Azure-ban szükséges, hogy ezek a virtuális gépek egy helyszíni tartomány és az Active Directory/LDAP részét képezik. 
>
> A dokumentáció korábbi verzióiban a hibrid-IT forgatókönyveket említették. A *hibrid* kifejezés abban gyökerezik, hogy a helyszíni és az Azure közötti több telephelyen átnyúló kapcsolat áll fenn. Ebben az esetben a hibrid azt is jelenti, hogy az Azure-ban a virtuális gépek a helyszíni Active Directory részét képezik.
>
>

Egyes Microsoft-dokumentációk egy kicsit eltérően ismertetik a telephelyek közötti forgatókönyveket, különösen a DBMS magas rendelkezésre állású konfigurációk esetében. Az SAP-val kapcsolatos dokumentumok esetében a létesítmények közötti forgatókönyv a helyek közötti vagy privát [ExpressRoute-kapcsolatra](https://azure.microsoft.com/services/expressroute/) és a helyszíni és az Azure közötti SAP-környezetre vezethető vissza.

## <a name="resources"></a>Források
Az Azure-beli SAP-számítási feladatokhoz más cikkek is elérhetők. Kezdje az [SAP-munkaterheléssel az Azure-ban: Első lépések,](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) majd válassza ki az érdeklődési területet.

A következő SAP-megjegyzések az Azure-beli SAP-hoz kapcsolódnak a jelen dokumentumban szereplő terület tekintetében.

| Megjegyzés száma | Cím |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-ban: Támogatott termékek és Azure virtuálisgép-típusok |
| [2015553] |SAP a Microsoft Azure-ban: Támogatási előfeltételek |
| [1999351] |Továbbfejlesztett Azure-figyelés az SAP-hoz – hibaelhárítás |
| [2178632] |Az SAP legfontosabb figyelési mutatói a Microsoft Azure-ban |
| [1409604] |Virtualizáció Windowsrendszeren: Továbbfejlesztett figyelés |
| [2191498] |SAP Linuxon az Azure-ral: Továbbfejlesztett figyelés |
| [2039619] |SAP-alkalmazások a Microsoft Azure-ban az Oracle adatbázis használatával: Támogatott termékek és verziók |
| [2233094] |DB6: SAP-alkalmazások az Azure-ban az IBM DB2 Linux, UNIX és Windows rendszerhez: További információ |
| [2243692] |Linux a Microsoft Azure (IaaS) virtuális gépen: SAP licencproblémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési megjegyzések |
| [2002167] |Red Hat Enterprise Linux 7.x: Telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítés és frissítés |
| [1597355] |Swap-space ajánlás Linux |
| [2171857] |Oracle Database 12c: Fájlrendszer támogatása Linuxon |
| [1114181] |Oracle Database 11g: Fájlrendszer támogatása Linuxon |


Az sap-jegyzetek Linuxhoz című témakörben további információt az [SAP közösségi wikiben](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes)talál.

A Microsoft Azure architektúrájának és a Microsoft Azure virtuális gépek üzembe helyezésének és működtetésének módjára vonatkozó ismeretekre van szüksége. További információt az [Azure dokumentációjában](https://docs.microsoft.com/azure/)talál.

Általánosságban elmondható, hogy a Windows, Linux és DBMS telepítése és konfigurálása lényegében megegyezik a helyszíni telepített virtuális gépekkel vagy csupasz fémgépekkel. Vannak olyan architektúra- és rendszerfelügyeleti megvalósítási döntések, amelyek az Azure IaaS használatakor eltérőek. Ez a dokumentum ismerteti az Azure IaaS használatakor előkészíteni kívánt architekturális és rendszerkezelési különbségeket.


## <a name="storage-structure-of-a-vm-for-rdbms-deployments"></a><a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>Virtuális gép tárolási struktúrája rdBMS-telepítésekhez
A fejezet követéséhez olvassa el és ismerje meg a [telepítési útmutató][deployment-guide] [jelen fejezetében][deployment-guide-3] található információkat. Meg kell értenie, és ismeri a különböző virtuális gép sorozat és a különbségek a standard és a prémium szintű storage, mielőtt elolvassa ezt a fejezetet. 

Az Azure-beli virtuális gépekhez való Azure Storage szolgáltatásról a következő témakört olvashatja:

- [Bevezetés az Azure Windows virtuális gépek felügyelt lemezeibe.](../../windows/managed-disks-overview.md)
- [Bevezetés az Azure Linux virtuális gépek felügyelt lemezeibe.](../../linux/managed-disks-overview.md)

Alapkonfigurációban általában olyan központi telepítési struktúrát ajánlunk, amelyben az operációs rendszer, a DBMS és az esetleges SAP bináris fájlok elkülönülnek az adatbázisfájloktól. Azt javasoljuk, hogy az Azure virtuális gépeken futó SAP-rendszerek rendelkeznek az operációs rendszerrel, az adatbázis-kezelő rendszer végrehajtható fájljaival és az SAP-végrehajtható fájlokkal telepített alap virtuális merevlemezzel vagy lemezzel. 

A DBMS-adatok és naplófájlok normál vagy prémium szintű tárhelyen tárolódnak. Ezek külön lemezeken tárolódnak, és logikai lemezekként vannak csatolva az eredeti Azure operációs rendszer rendszerkép virtuális géphez. Linux-telepítések esetén a különböző javaslatok vannak dokumentálva, különösen az SAP HANA.

A lemezelrendezés megtervezésekor keresse meg a legjobb egyensúlyt az elemek között:

* Az adatfájlok száma.
* A fájlokat tartalmazó lemezek száma.
* Egyetlen lemez IOPS-kvótái.
* Az adatátviteli szint lemezenként.
* A virtuális gép méreténként lehetséges további adatlemezek száma.
* A virtuális gép által biztosított teljes tárolási átviteli kapacitás.
* A késés különböző Azure Storage-típusok nyújthat.
* VM SLAs.

Az Azure adatlemezenként iOPS-kvótát kényszerít ki. Ezek a kvóták a szabványos és prémium szintű storage-ban tárolt lemezek esetében eltérőek. I/O késés is eltér a két tárolási típus között. A prémium szintű storage jobb I/O-késleltetést biztosít. 

A különböző virtuálisgép-típusok mindegyike korlátozott számú adatlemezzel rendelkezik, amelyet csatolhat. Egy másik korlátozás, hogy csak bizonyos virtuálisgép-típusok használhatják a prémium szintű storage. Általában úgy dönt, hogy egy bizonyos virtuálisgép-típus t, a CPU és a memória követelmények alapján. Azt is figyelembe veheti az IOPS, késés, és a lemez átviteli követelmények, amelyek általában a lemezek száma vagy a prémium szintű tárolólemezek típusa. Az IOPS-ok száma és az egyes lemezek által elérhető átviteli kapacitás diktálhatja a lemez méretét, különösen a prémium szintű tárolás esetén.

> [!NOTE]
> A DBMS-telepítések esetében javasoljuk, hogy prémium szintű tárhelyet használjon minden adathoz, tranzakciós naplóhoz vagy újrafájlokhoz. Nem számít, hogy éles vagy nem éles rendszereket szeretne-e telepíteni.

> [!NOTE]
> Az Azure [egyedi, egyedi virtuális gép SLA-jának](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)előnyeihez az összes csatlakoztatott lemeznek a prémium szintű tárolási típusnak kell lennie, amely tartalmazza az alap virtuális merevlemezt.

> [!NOTE]
> Az Azure-adatközpontok melletti, harmadik féltől származó adatközpontokban található SAP-adatbázisok fő adatbázis-fájljainak, például az adatoknak és a naplófájloknak a tároláson való tárolása nem támogatott. Az SAP-számítási feladatok esetében csak a natív Azure-szolgáltatásként képviselt tárhely támogatott az SAP-adatbázisok adat- és tranzakciós naplófájljaiban.

Az adatbázisfájlok, valamint a napló- és újrafájlok elhelyezését, valamint a használt Azure Storage típusát az IOPS, a késés és az átviteli igény határozza meg. Ahhoz, hogy elegendő IOPS,előfordulhat, hogy kénytelen használni több lemezt, vagy egy nagyobb prémium szintű tárolólemez. Ha több lemezt használ, hozzon létre egy szoftvercsíkot az adatfájlokat tartalmazó lemezekre, illetve a napló- és újrafájlokra. Ilyen esetekben az iOPS és a lemez átviteli átviteli SL-ek az alapul szolgáló prémium szintű tárolólemezek vagy a standard tárolólemezek maximálisan elérhető IOPS-ok az eredményül kapott csíkkészlethez.

Ahogy már említették, ha az IOPS-követelmény meghaladja, amit egy virtuális merevlemez nyújthat, egyensúlyba hozza az adatbázisfájlok hoz szükséges IOPS-ok száma több virtuális merevlemezek között. Az IOPS-terhelés lemezek közötti elosztásának legegyszerűbb módja egy szoftvercsík létrehozása a különböző lemezeken. Ezután helyezzen el az SAP DBMS számos adatfájlját a szoftvercsíkból kivájt lun-okon. A sávban lévő lemezek számát az I/O-k, a lemezátviteli igények és a kötetigények határozzák meg.


---
> ![Windows][Logo_Windows] Windows
>
> Azt javasoljuk, hogy a Windows Storage Spaces használatával hozzon létre csíkkészletek et több Azure-beli VIRTUÁLIS ÖN.We recommend that you use Windows Storage Spaces to create stripe sets across multiple Azure VHDs. Legalább Windows Server 2012 R2 vagy Windows Server 2016.
>
> ![Linux][Logo_Linux] Linux
>
> Csak az MDADM és a Logical Volume Manager (LVM) támogatott a szoftveres RAID Linuxon történő létrehozásához. További információkért lásd:
>
> - [Szoftveres RAID konfigurálása Linuxon az](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) MDADM használatával
> - [LVM konfigurálása Linux os virtuális gépen az Azure-ban](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) lvm használatával
>
>

---

> [!NOTE]
> Mivel az Azure Storage három lemezképet tárol a Virtuálisszolgáltatásokról, nincs értelme redundanciát beállítani, ha csíkot szeretne. Csak konfigurálnia kell a csíkozást, hogy az I/O-k a különböző Virtuális szolgáltatások között oszlanak meg.
>

### <a name="managed-or-nonmanaged-disks"></a>Felügyelt vagy nem felügyelt lemezek
Az Azure storage-fiók egy felügyeleti konstrukció, és egyben korlátozások tárgya is. A korlátozások a normál szintű tárfiókok és a prémium szintű tárfiókok között különböznek. A képességekről és korlátozásokról az [Azure Storage méretezhetőségi és teljesítménycélok című](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)témakörben talál.

A szabványos tár, ne feledje, hogy van egy korlát az IOPS tárfiókonként. Tekintse meg a **teljes kérelemarányt** tartalmazó sort az [Azure Storage méretezhetőségi és teljesítménycélok című](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)cikkében. Az Azure-előfizetésenkénti tárfiókok száma is kezdeti korlátot biztosít. Kiegyenlítése VHD-k a nagyobb SAP-környezet különböző tárfiókok között, hogy ne ütközz a tárfiókok korlátait. Ez fárasztó munka, ha beszélünk néhány száz virtuális gépek több mint ezer VHDs.

Mivel a dbMS-telepítések szabványos tárházának használata az SAP-számítási feladatokkal együtt nem ajánlott, a szabványos tárolásra vonatkozó hivatkozások és javaslatok erre a rövid [cikkre korlátozódnak.](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

A virtuális merevlemezek tervezésének és üzembe helyezésének felügyeleti munkája érdekében a Microsoft 2017-ben bevezette az [Azure Managed Disks szolgáltatást.](https://azure.microsoft.com/services/managed-disks/) A felügyelt lemezek szabványos tárterülethez és prémium szintű tároláshoz érhetők el. A felügyelt lemezek fő előnyei a nem felügyelt lemezekhez képest a következők:

- Felügyelt lemezek esetén az Azure a különböző virtuális merevlemezeket automatikusan elosztja a különböző tárfiókok között a telepítés időpontjában. Ily módon az adatmennyiség, az I/O-átviteli és az IOPS-érték tárfiók-korlátokat nem jelenik meg.
- Felügyelt lemezek használatával az Azure Storage tiszteletben tartja az Azure rendelkezésre állási csoportjainak fogalmait. Ha a virtuális gép egy Azure rendelkezésre állási csoport része, a virtuális gép alap virtuális merevlemeze és csatlakoztatott lemeze különböző hiba- és frissítési tartományokba kerül.


> [!IMPORTANT]
> Tekintettel az Azure felügyelt lemezek előnyeire, azt javasoljuk, hogy az Azure felügyelt lemezeket használja a DBMS-telepítések és az SAP-telepítések általában.
>

A nem felügyelt lemezekről a következő témakörökben szeretné átváltani:

- [Windows-virtuális gép konvertálása nem felügyelt lemezekből felügyelt lemezekké.](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Linuxos virtuális gép konvertálása nem felügyelt lemezekről felügyelt lemezekké.](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="caching-for-vms-and-data-disks"></a><a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Virtuális gépek és adatlemezek gyorsítótárazása
Lemezek csatlakoztatásakor a virtuális gépekhez, kiválaszthatja, hogy a virtuális gép és az Azure storage-ban található lemezek közötti I/O-forgalom gyorsítótárazott-e. A normál és a prémium szintű tárolás két különböző technológiát használ az ilyen típusú gyorsítótárhoz.

A következő ajánlások a szabványos DBMS-hez következő I/O-jellemzőket feltételezik:

- Ez többnyire egy olvasási számítási feladat egy adatbázis adatfájljai ellen. Ezek az olvasások a DBMS rendszer teljesítményszempontjából kritikus teljesítményszempontjából kritikusak.
- Az adatfájlokra való írás ellenőrzőpontok vagy állandó adatfolyam alapján adatlöketekben történik. Egy nap alatt átlagban kevesebb írás van, mint az olvasás. Az adatfájlokból beolvasással ellentétben ezek az írások aszinkronak, és nem tartanak fel semmilyen felhasználói tranzakciót.
- Alig van olvasás a tranzakciós naplóból vagy a fájlok ismétléséből. A kivételek a nagy I/O-k a tranzakciónapló biztonsági mentésekor.
- A tranzakciós vagy ismétlődő naplófájlok fő terhelése írás. A munkaterhelés jellegétől függően az I/O-k akár 4 KB-os is lehetnek, vagy más esetekben 1 MB vagy annál nagyobb I/O-mérettel rendelkezhet.
- Minden írási kell megpersistni a lemezen megbízható módon.

A szabványos tárolás esetében a lehetséges gyorsítótártípusok a következők:

* None
* Olvasás
* Olvasás/írás

A konzisztens és determinisztikus teljesítmény érdekében állítsa be a gyorsítótárazást a szabványos tárolón az összes olyan lemezre, amely DBMS-sel kapcsolatos adatfájlokat, napló- és újrafájlokat, valamint asztalterületet tartalmaz **a NONE**elemre. Az alap virtuális merevlemez gyorsítótárazása az alapértelmezett marad.

A prémium szintű tárolás hoz a következő gyorsítótárazási lehetőségek:

* None
* Olvasás
* Olvasás/írás
* Nincs + Írásgyorsító, amely csak az Azure M sorozatú virtuális gépekhez érhető el
* Olvasási + Írásgyorsító, amely csak az Azure M sorozatú virtuális gépekhez érhető el

Prémium szintű tárolás esetén azt javasoljuk, hogy az SAP-adatbázis adatfájljaihoz használja a **Gyorsítótárazás olvasása** parancsot, és válassza **a Nincs gyorsítótárazás lehetőséget a naplófájl(ok) lemezeihez.**

M sorozatú telepítések esetén azt javasoljuk, hogy az Azure Write Accelerator-t használja a DBMS-telepítéshez. Az Azure Write Accelerator további, korlátozásaiés üzembe helyezése az [Írásgyorsító engedélyezése témakörben](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator)talál.


### <a name="azure-nonpersistent-disks"></a>Nem állandó Azure-lemezek
Az Azure virtuális gépek nem állandó lemezeket kínálnak a virtuális gép üzembe helyezése után. Virtuális gép újraindítása esetén a meghajtókon lévő összes tartalom törlődik. Ez egy adott, hogy az adatfájlok és a napló-és ismétlésfájlok adatbázisok semmilyen körülmények között nem található az ilyen nem megőrzött meghajtókon. Egyes adatbázisok esetében előfordulhatnak kivételek, ahol ezek a nem megőrzött meghajtók tempdb és temp tablespaces esetén is használhatók. Kerülje az a-sorozatú virtuális gépek meghajtóinak használatát, mert ezek a nem megőrzött meghajtók átviteli sebességűek az adott virtuálisgép-családnál. 

További [információ: Az Azure-beli Windows-virtuális gépek ideiglenes meghajtójának ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)című témakörben talál.

---
> ![Windows][Logo_Windows] Windows
>
> D-meghajtó egy Azure virtuális gép egy nem megőrzött meghajtó, amely az Azure számítási csomópont néhány helyi lemez által támogatott. Mivel nem tartós, a D meghajtón lévő tartalom módosításai elvesznek a virtuális gép újraindításakor. A módosítások közé tartoznak a tárolt fájlok, a létrehozott könyvtárak és a telepített alkalmazások.
>
> ![Linux][Logo_Linux] Linux
>
> A Linux Azure virtuális gépek automatikusan csatlakoztatnak egy meghajtót a /mnt/erőforrás, amely egy nem megőrzött meghajtó által támogatott helyi lemezek az Azure számítási csomóponton. Mivel nem tartjuk meg, a /mnt/resource tartalommódosításai elvesznek a virtuális gép újraindításakor. A módosítások közé tartoznak a tárolt fájlok, a létrehozott könyvtárak és a telepített alkalmazások.
>
>

---



### <a name="microsoft-azure-storage-resiliency"></a><a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>A Microsoft Azure Storage rugalmassága
A Microsoft Azure Storage tárolja az alap Virtuális merevlemez, operációs rendszer rel és csatlakoztatott lemezek vagy blobok, legalább három külön tárolási csomópontokon tárolja. Az ilyen típusú tárolót helyileg redundáns tárolásnak (LRS) nevezik. Az LRS az alapértelmezett az Azure-beli összes típusú tárhely esetében.

Vannak más redundancia módszerek. További információ: [Azure Storage replication](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>A prémium szintű tárhely az adatbázis-, valamint a napló- és fájlokat tároló lemezek ajánlott tárolótípusa a DBMS-virtuális gépek és lemezek számára. A prémium szintű tárolás egyetlen rendelkezésre álló redundanciamódszere az LRS. Ennek eredményeképpen konfigurálnia kell az adatbázis-metódusok egy másik Azure-régióba vagy rendelkezésre állási zónába történő replikációjának engedélyezéséhez. Az adatbázis-kezelési módszerek közé tartozik az SQL Server Always On, az Oracle Data Guard és a HANA system replication.


> [!NOTE]
> A DBMS-telepítések esetében a georedundáns tárolás (GRS) használata nem ajánlott a szabványos tároláshoz. A GRS súlyosan befolyásolja a teljesítményt, és nem tartja tiszteletben az írási sorrendet a virtuális gépekhez csatlakoztatott különböző virtuális gépek között. A különböző virtuális gépek írási sorrendjének figyelmen kívül állítása potenciálisan inkonzisztens adatbázisokhoz vezet a replikációs céloldalon. Ez a helyzet akkor fordul elő, ha az adatbázis- és napló- és újrafájlok több virtuális gép között vannak elosztva, mint általában a forrás virtuális gép oldalán.



## <a name="vm-node-resiliency"></a>Virtuálisgép-csomópont rugalmassága
Az Azure számos különböző SL-t kínál a virtuális gépekhez. További információt a [virtuális gépeksla-sla](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/)legutóbbi kiadásában talál. Mivel a DBMS-réteg általában kritikus fontosságú az SAP-rendszerben való rendelkezésre álláshoz, meg kell értenie a rendelkezésre állási csoportokat, a rendelkezésre állási zónákat és a karbantartási eseményeket. Ezekről a fogalmakról további információt [a Windows virtuális gépek Azure-beli elérhetőségének kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és a [Linux-alapú virtuális gépek Azure-beli elérhetőségének kezelése című témakörben talál.](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability)

Az SAP-munkaterheléssel rendelkező éles ADATBÁZIS-forgatókönyvek minimális javaslata a következő:

- Üzembe helyezhet két virtuális gépet egy külön rendelkezésre állási csoportban ugyanabban az Azure-régióban.
- Futtassa ezt a két virtuális gépet ugyanabban az Azure-beli virtuális hálózatban, és a hálózati adapterek ugyanazon alhálózatokból vannak csatolva.
- Adatbázis-módszerek használatával tartsa a készenléti állapot a második virtuális gép. A metódusok lehetnek SQL Server Always On, Oracle Data Guard vagy HANA system replication.

Egy harmadik virtuális gép is üzembe helyezhető egy másik Azure-régióban, és ugyanazokat az adatbázis-módszereket használhatja egy másik Azure-régióban egy aszinkron replika biztosításához.

Az Azure rendelkezésre állási csoportjainak beállításáról [az oktatóanyagban](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets)talál további információt.



## <a name="azure-network-considerations"></a>Az Azure hálózati szempontjai
A nagy méretű SAP-telepítések, használja az [Azure Virtual Datacenter](https://docs.microsoft.com/azure/architecture/vdc/networking-virtual-datacenter)tervrajzát. Használja a virtuális hálózati konfigurációhoz, valamint a szervezet különböző részeihez rendelt engedélyekhez és szerepkör-hozzárendelésekhez.

Ezek az ajánlott eljárások több száz ügyfél-telepítés eredménye:

- A virtuális hálózatok az SAP-alkalmazás telepítve nem férnek hozzá az internethez.
- Az adatbázis virtuális gépei ugyanabban a virtuális hálózatban futnak, mint az alkalmazásréteg.
- A virtuális hálózaton belüli virtuális gépek a privát IP-cím statikus lefoglalásával rendelkeznek. További információt az [IP-címtípusok és a foglalási módszerek az Azure-ban című témakörben talál.](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
- Az ADATBÁZIS-RENDSZER virtuális gépekre érkező és onnan érkező útválasztási korlátozások *nincsenek* beállítva a helyi ADATBÁZIS-rendszerű virtuális gépeken telepített tűzfalakkal. Ehelyett a forgalomútválasztás [hálózati biztonsági csoportokkal (NSG- k)](https://docs.microsoft.com/azure/virtual-network/security-overview)van definiálva.
- A forgalom elkülönítése és elkülönítése a DBMS virtuális gép, rendeljen különböző hálózati adapterek a virtuális gép. Minden hálózati adapter kap egy másik IP-címet, és minden hálózati adapter van rendelve egy másik virtuális hálózati alhálózathoz. Minden alhálózat különböző NSG-szabályokkal rendelkezik. A hálózati forgalom elkülönítése vagy elkülönítése az útválasztás mértéke. Nem használják kvóták beállítására a hálózati átviteli.

> [!NOTE]
> Statikus IP-címek hozzárendelése az Azure-on keresztül azt jelenti, hogy az okat az egyes virtuális hálózati adapterek. Ne rendeljen statikus IP-címeket a vendég operációs rendszeren belül egy virtuális hálózati adapterhez. Egyes Azure-szolgáltatások, például az Azure Backup arra a tényre támaszkodnak, hogy legalább az elsődleges virtuális hálózati adapter DHCP-re van állítva, nem pedig statikus IP-címekre. További információt az [Azure virtuálisgépek biztonsági mentésének hibaelhárítása című](https://docs.microsoft.com/azure/backup/backup-azure-vms-troubleshoot#networking)témakörben talál. Ha több statikus IP-címet szeretne hozzárendelni egy virtuális géphez, rendeljen több virtuális hálózati adaptert egy virtuális géphez.
>


> [!IMPORTANT]
> Az SAP-alkalmazás és az SAP NetWeaver-, Hybris-vagy S/4HANA-alapú SAP-rendszer DBMS-rétege közötti kommunikációs útvonalon lévő [hálózati virtuális készülékek](https://azure.microsoft.com/solutions/network-appliances/) konfigurálása nem támogatott. Ez a korlátozás a funkcionalitás és a teljesítmény miatt. Az SAP alkalmazásréteg és a DBMS-réteg közötti kommunikációs útvonalnak közvetlennek kell lennie. A korlátozás nem tartalmazza [az alkalmazásbiztonsági csoport (ASG) és az NSG-szabályokat,](https://docs.microsoft.com/azure/virtual-network/security-overview) ha ezek az ASG- és NSG-szabályok közvetlen kommunikációs útvonalat engedélyeznek. 
>
> Egyéb forgatókönyvek, ahol a hálózati virtuális készülékek nem támogatottak vannak:
>
> * A Linux-pacemaker fürtcsomópontokat és Az SBD-eszközöket képviselő Azure-virtuális gépek közötti kommunikációs útvonalak az [SAP NetWeaver magas rendelkezésre állása az Azure-beli sap-alkalmazásokhoz szolgáltatásban található Sap-virtuális gépeken](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/high-availability-guide-suse)leírtak szerint.
> * Az Azure virtuális gépek és a Windows Server scale-out file server (SOFS) közötti kommunikációs útvonalak, amelyek et a Fürtben leírt módon állítottak [be, egy SAP ASCS/SCS-példány windowsos feladatátvételi fürtön az Azure-beli fájlmegosztás használatával.](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/sap-high-availability-guide-wsfc-file-share) 
>
> A hálózati virtuális készülékek a kommunikációs útvonalakon könnyen megduplázhatják a hálózati késést két kommunikációs partner között. Emellett korlátozhatják az átviteli szintet a kritikus útvonalakon az SAP-alkalmazásréteg és a DBMS-réteg között. Egyes ügyfél-forgatókönyvek, hálózati virtuális készülékek okozhat Pacemaker Linux-fürtök sikertelen. Ezek azok az esetek, amikor a Linux Pacemaker fürtcsomópontok közötti kommunikáció kommunikál az SBD-eszköz egy hálózati virtuális berendezésen keresztül.
>

> [!IMPORTANT]
> Egy másik terv, amely *nem* támogatott az SAP-alkalmazásréteg és a DBMS-réteg elkülönítése különböző Azure virtuális hálózatokra, amelyek nem [egyenrangúak](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) egymással. Azt javasoljuk, hogy az SAP-alkalmazásréteg és a DBMS-réteg elkülönítése az Azure virtuális hálózaton belüli alhálózatok használatával, nem pedig különböző Azure virtuális hálózatok használatával. 
>
> Ha úgy dönt, hogy nem követi az ajánlást, és ehelyett a két réteget különböző virtuális hálózatokra különíti el, a két virtuális [hálózatot társviszonyba](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview)kell venni. 
>
> Ne feledje, hogy a hálózati forgalom két [társviszony-létesítési](https://docs.microsoft.com/azure/virtual-network/virtual-network-peering-overview) Azure virtuális hálózatok közötti átviteli költségek. A számos terabájtból álló hatalmas adatkötet az SAP alkalmazásréteg és a DBMS-réteg között kerül kicserére. Jelentős költségeket halmozhat fel, ha az SAP-alkalmazásréteg és a DBMS-réteg két társviszony-létesítő Azure virtuális hálózat között van elkülönítve.

Két virtuális gép használata az azure-beli rendelkezésre állási csoporton belüli éles ADATBÁZIS-üzembe helyezéshez. Is használjon külön útválasztás az SAP-alkalmazásréteg és a felügyeleti és üzemeltetési forgalmat a két DBMS virtuális gépek. Lásd a következő képet:

![Két virtuális gép diagramja két alhálózatban](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="use-azure-load-balancer-to-redirect-traffic"></a>Forgalom átirányítása az Azure Load Balancer segítségével
A magánhálózati IP-címek használata olyan funkciókban, mint az SQL Server Always On vagy a HANA system replication, egy Azure load balancer konfigurálásához szükséges. A terheléselosztó mintavételi portok segítségével határozza meg az aktív DBMS-csomópontot, és a forgalmat kizárólag az adott aktív adatbázis-csomópontra irányítsa. 

Ha van egy feladatátvétel az adatbázis-csomópont, nincs szükség az SAP-alkalmazás újrakonfigurálása. Ehelyett a leggyakoribb SAP-alkalmazásarchitektúrák újra csatlakoznak a privát virtuális IP-címhez. Eközben a terheléselosztó reagál a csomópont feladatátvételre a forgalom átirányításával a privát virtuális IP-cím a második csomópontra.

Az Azure két különböző [terheléselosztó termékváltozatot](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview)kínál: egy alaptermékváltozatot és egy szabványos termékváltozatot. Hacsak nem szeretné üzembe helyezni az Azure rendelkezésre állási zónáiközött, az alapvető terheléselosztó Termékváltozat nem felel meg.

A DBMS virtuális gépek és az SAP-alkalmazásréteg közötti forgalom mindig a terheléselosztón keresztül van irányítva? A válasz attól függ, hogyan kell beállítani a terheléselosztót. 

Ebben az időben a bejövő forgalmat a DBMS virtuális gép mindig a terheléselosztón keresztül. A kimenő forgalmi útvonal a DBMS virtuális gép az alkalmazásréteg virtuális gép a ttól függ, hogy a konfiguráció a terheléselosztó. 

A terheléselosztó a DirectServerReturn lehetőséget kínálja. Ha ez a beállítás konfigurálva van, a DBMS virtuális gépről az SAP-alkalmazásrétegre irányuló forgalom *nem* lesz a terheléselosztón keresztül. Ehelyett közvetlenül az alkalmazásrétegre kerül. Ha a DirectServerReturn nincs konfigurálva, a visszatérési forgalom az SAP alkalmazásrétega a terheléselosztón keresztül lesz irányítva.

Azt javasoljuk, hogy konfigurálja a DirectServerReturn-et az SAP alkalmazásréteg és a DBMS-réteg között elhelyezett terheléselosztókkal együtt. Ez a konfiguráció csökkenti a hálózati késést a két réteg között.

A konfiguráció beállítása az SQL Server Always On szolgáltatással című témakörben található: [ILB-figyelő konfigurálása az Azure-ban az Always On rendelkezésre állási csoportokhoz című témakörben.](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener)

Ha közzétett GitHub JSON-sablonokat használ referenciaként az SAP-infrastruktúra-telepítésekhez az Azure-ban, tanulmányozza ezt [a sablont egy SAP 3 tier rendszerhez.](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md) Ebben a sablonban a terheléselosztó megfelelő beállításaiis láthatók.

### <a name="azure-accelerated-networking"></a>Azure gyorsított hálózatkezelés
Az Azure virtuális gépek közötti hálózati késés további csökkentése érdekében azt javasoljuk, hogy az [Azure accelerated networking](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/)lehetőséget válassza. Használja, ha üzembe helyezi az Azure-beli virtuális gépek et egy SAP-számítási feladatokhoz, különösen az SAP-alkalmazásréteghez és az SAP DBMS-réteghez.

> [!NOTE]
> Nem minden virtuálisgép-típus támogatja az accelerated networking-et. Az előző cikk felsorolja a virtuális gép típusait, amelyek támogatják a gyorsított hálózatkezelést.
>

---
> ![Windows][Logo_Windows] Windows
>
> A Windows gyorsított hálózattal rendelkező virtuális gépek telepítéséről a [Windows rendszerű virtuális gép létrehozása gyorsított hálózattal](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell)című témakörben olvashat.
>
> ![Linux][Logo_Linux] Linux
>
> A Linux-disztribúcióról további információt a [Linux os virtuális gép létrehozása gyorsított hálózattal](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli)című témakörben talál.
>
>

---

> [!NOTE]
> A SUSE, a Red Hat és az Oracle Linux esetében az Accelerated Networking a legutóbbi kiadásokban támogatott. A régebbi kiadások, például az SLES 12 SP2 vagy az RHEL 7.2 nem támogatják az Azure Accelerated Networking szolgáltatást.
>


## <a name="deployment-of-host-monitoring"></a>A gazdagép figyelésének telepítése
Az SAP-alkalmazások azure-beli virtuális gépeken való éles használatához az SAP-nak lehetővé kell tennie, hogy az Azure virtuális gépeket futtató fizikai gazdagépektől állomásfigyelési adatokat szerezzen be. Egy adott SAP-ügynök patch szint szükséges, amely lehetővé teszi ezt a képességet a SAPOSCOL és az SAP host agent. A pontos javítási szint az SAP [Note 1409604]feljegyzésben van dokumentálva.

A gazdagépadatokat a SAPOSCOL-nak és az SAP host Agentnek kézbesítő összetevők telepítéséről, valamint az összetevők életciklus-kezeléséről a [Telepítési útmutatóban][deployment-guide]talál további információt.


## <a name="next-steps"></a>További lépések
Egy adott adatbázis-kezelővel kapcsolatos további információkért lásd:

- [SQL Server Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_sqlserver.md)
- [Oracle Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_oracle.md)
- [IBM DB2 Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_ibm.md)
- [SAP ASE Azure-beli virtuális gépek DBMS üzembe helyezése SAP számítási feladatokhoz](dbms_guide_sapase.md)
- [SAP maxDB, Live Cache és Content Server üzembe helyezése az Azure-ban](dbms_guide_maxdb.md)
- [SAP HANA az Azure-műveletek útmutatójában](hana-vm-operations.md)
- [SAP HANA magas rendelkezésre állás az Azure virtuális gépekhez](sap-hana-availability-overview.md)
- [Biztonsági mentési útmutató az SAP HANA-hoz az Azure virtuális gépeken](sap-hana-backup-guide.md)

