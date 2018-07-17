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
ms.date: 07/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: fdf5685ad8072175bdabf8938ef293bed6f5cc13
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39076150"
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

Ez az útmutató a végrehajtási és a Microsoft Azure az SAP-szoftverek üzembe helyezésére dokumentáció részét képezi. Ez az útmutató elolvasása, előtt olvassa el a [tervezési és megvalósítási útmutató][planning-guide]. Ez a dokumentum ismerteti az általános üzembe helyezési szempontok kapcsolatos SAP DBMS-rendszerek a Microsoft Azure-beli virtuális gépeken (VM) használatával az Azure infrastruktúra-szolgáltatás (IaaS) képességeket.

A tanulmány az SAP-telepítési dokumentációt és SAP-megjegyzések, amelyek tartalmazzák a telepítések és SAP-szoftverek központi telepítései az elsődleges erőforrásokat a megadott platformok egészíti ki.

Ebben a dokumentumban megfontolásokkal kapcsolatos SAP DBMS-rendszerek Azure-beli virtuális gépeken futó jelennek meg. Ez a fejezet az adott DBMS-rendszerekre való néhány hivatkozások találhatók. Ehelyett az adott DBMS-rendszerek kezelése a tanulmány után ez a dokumentum.

## <a name="definitions-upfront"></a>Előre definíciók
A dokumentumban a következő kifejezéseket használjuk:

* IaaS: Infrastruktúra-szolgáltatás.
* PaaS: Platformszolgáltatás.
* SaaS: Szolgáltatott szoftver.
* Az SAP-összetevőt: Egyedi SAP alkalmazások például az ECC, a BW, a megoldás Manager vagy a EP  Az SAP-összetevők hagyományos ABAP és Java-technológiák vagy egy nem NetWeaver-alapú alkalmazás, például az üzleti objektumok alapulhat.
* Az SAP-környezet: egy vagy több SAP összetevő logikusan például fejlesztési, QAS, képzés, DR vagy éles üzleti függvény végrehajtásához.
* SAP-rendszeren: A kifejezés a teljes SAP-eszközök az ügyfél informatikai környezetét. Az SAP-rendszeren, tartalmazza az összes éles környezetben, és nem éles környezetekben.
* SAP-rendszerhez: Adatbázis-kezelő réteg és kombinációja alkalmazásréteg, például egy fejlesztőrendszerrel SAP ERP, SAP BW tesztgépen, SAP CRM-előállítási rendszerek stb. Az Azure-környezetek nem támogatott ezen két réteg között a helyszíni és az Azure osztani. Ez azt jelenti, hogy az SAP-rendszer vagy az üzembe helyezett helyszíni, vagy azt az Azure-ban üzemel. Azonban telepíthet egy Azure-ban vagy a helyszíni SAP-rendszeren, a különböző rendszerek. Például sikerült telepíteni az SAP CRM fejlesztési és rendszerek tesztelése az Azure azonban az SAP CRM rendszert a helyi környezetben.
* Létesítmények közötti: Egy forgatókönyvet, ahol a virtuális gépek Azure-előfizetéssel, amely rendelkezik a site-to-site, többhelyes vagy az ExpressRoute-kapcsolat a helyszíni kommunikálhassanak és az Azure közötti üzembe ismerteti. A gyakori Azure dokumentációjában, az ilyen típusú központi telepítések is rendelkezésre állnak, létesítmények közötti forgatókönyvek leírtak szerint. A kapcsolat az az oka, hogy kiterjesztése a helyszíni tartományokra, a helyszíni Active Directory és a helyszíni DNS az Azure-bA. Az Azure-előfizetés objektumok kiterjed a helyszíni környezet változásaihoz. Ez a bővítmény kapcsolatban, a virtuális gépek a helyszíni tartomány része lehet. Tartományi felhasználókat a helyszíni tartomány férhet hozzá a kiszolgálókat, és futtathatja a szolgáltatások a virtuális gépeken (például adatbázis-kezelő szolgáltatások). Virtuális gépek közötti kommunikációt és a névfeloldás üzembe helyezte a helyszínen és az Azure-ban üzembe helyezett virtuális gépek lehetséges. Ebben a forgatókönyvben az a leggyakoribb forgatókönyv üzembe helyezéséhez SAP-eszközök az Azure-ban. További információkért lásd: [tervezéssel és kialakítással VPN-átjáró](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-plan-design).

> [!NOTE]
> Létesítmények közötti SAP-rendszereit, amelyeknél a helyszíni tartomány tagjai az Azure Virtual machines gépeken futó SAP-rendszerek központi telepítései éles SAP-rendszerek támogatottak. Létesítmények közötti konfigurációkat üzembe helyezésének részek támogatottak, vagy hajtsa végre az SAP-környezetünk az Azure-bA. Akár az Azure-ban futó a teljes SAP-rendszeren, ezeket a virtuális gépek is tagja a helyszíni tartomány és az AD/LDAP igényel. A-dokumentáció korábbi verziói a hibrid-informatikai alkalmazási helyzetek is említettük, ahol az előfizetési időszak *hibrid* rootolva van, az a tény, hogy nincs-e a létesítmények közötti kapcsolatok a helyszíni és az Azure között. Ebben az esetben *hibrid* azt is jelenti, hogy a virtuális gépek az Azure-ban a helyszíni Active Directory részét képezik.
> 
> 

Bizonyos Microsoft-dokumentációt létesítmények közötti forgatókönyv különösen az adatbázis-kezelő magas rendelkezésre ÁLLÁS konfigurációk esetén egy kicsit másképp ismerteti. Az SAP-kapcsolatos dokumentumok esetében a létesítmények közötti forgatókönyv forrni kezd, hogy a magán- vagy site-to-site [ExpressRoute](https://azure.microsoft.com/services/expressroute/) kapcsolat, és azt a tényt, hogy az SAP-rendszeren oszlik el a helyszíni és az Azure között.

## <a name="resources"></a>További források
Az SAP számítási feladatok Azure-ban található különböző cikkek nyilvánosan is.  Javasoljuk, hogy start [SAP számítási feladatok Azure-ban – első lépések a](https://docs.microsoft.com/azure/virtual-machines/workloads/sap/get-started) majd válasszon a terület az érdeklődési kör

Az alábbi SAP-megjegyzések kapcsolódó Azure-beli SAP kapcsolatban a dokumentumban leírt terület:

| Megjegyzés száma | Beosztás |
| --- | --- |
| [1928533] |SAP-alkalmazások az Azure-on: támogatott termékek és Azure-beli Virtuálisgép-típusok |
| [2015553] |A Microsoft Azure-beli SAP: támogatás előfeltételei |
| [1999351] |Továbbfejlesztett Azure Monitoring for SAP hibaelhárítási |
| [2178632] |Metrikák figyelése a Microsoft Azure-beli SAP-kulcs |
| [1409604] |A Windows virtualizálási: Enhanced Monitoring |
| [2191498] |SAP használata Linux az Azure-ral: Enhanced Monitoring |
| [2039619] |A Microsoft Azure-ban, az Oracle-adatbázishoz az SAP-alkalmazások: támogatott termékek és termékverziók |
| [2233094] |DB6: Azure-ban, IBM DB2-höz használatával Linux, UNIX és a Windows - további információ az SAP-alkalmazások |
| [2243692] |A Microsoft Azure (IaaS) virtuális gép Linux: SAP-licenccel kapcsolatos problémák |
| [1984787] |SUSE LINUX Enterprise Server 12: Telepítési jegyzetek |
| [2002167] |Red Hat Enterprise Linux 7.x: telepítés és frissítés |
| [2069760] |Oracle Linux 7.x SAP telepítése és frissítése |
| [1597355] |Linux-lapozóterület javaslat |
| [2171857] |Oracle Database 12c - fájlrendszer Linux-támogatás |
| [1114181] |Oracle Database 11g - fájlrendszer Linux-támogatás |


Emellett olvassa el a [Állapotváltozás Wiki](https://wiki.scn.sap.com/wiki/display/HOME/SAPonLinuxNotes) minden SAP-megjegyzések, amely tartalmazza a Linuxhoz készült.

Rendelkeznie kell a Microsoft Azure-architektúra és a Microsoft Azure Virtual Machines telepített és üzemeltetett hogyan ismeretét. További információt talál [Azure-dokumentáció](https://docs.microsoft.com/azure/).

Témák megvitatásához IaaS, bár általában a Windows, Linux- és adatbázis-kezelő rendszer telepítési és konfigurációs lényegében ugyanaz, mint a virtuális gépekhez vagy telepítenie a helyi operációs rendszer nélküli gépen. Azonban néhány architektúra és a rendszer felügyeleti megvalósítási döntést kell meghoznia, amelyek különböző Azure IaaS használatakor. A jelen dokumentum célja, hogy a specifikus architekturális és a rendszer felügyeleti különbségeket, hogy fel kell készülnie az Azure IaaS használata esetén.


## <a name="65fa79d6-a85f-47ee-890b-22e794f51a64"></a>A virtuális gépek RDBMS központi telepítésekhez tárolószerkezet
Annak érdekében, hogy kövesse az ebben a fejezetben, érdekében fontos megérteni, milyen a bemutatott [ez] [ deployment-guide-3] fejezete az [üzembe helyezési útmutató][deployment-guide]. A különböző Virtuálisgép-sorozat, és azok és különbségek az Azure standard kapcsolatos Tudásbázis és [prémium szintű Storage](https://docs.microsoft.com/azure/virtual-machines/windows/premium-storage) kell érteni, és ez a fejezet elolvasása előtt ismert.

Azure Storage az Azure virtuális gépek tekintetében, ismerkedjen meg a cikkek:

- [Az Azure Windows virtuális gépek disks storage-ról](https://docs.microsoft.com/azure/virtual-machines/windows/about-disks-and-vhds)
- [Az Azure Linux virtuális gépek disks storage-ról](https://docs.microsoft.com/azure/virtual-machines/linux/about-disks-and-vhds) 

Alapszintű konfiguráció esetén általában javasoljuk, hogy a struktúra a központi telepítés, ahol az operációs rendszer, az adatbázis-kezelő és végleges SAP bináris fájlokat nem azonosak az adatbázisfájlokat. Ezért azt javasoljuk, az Azure Virtual Machines telepítve az operációs rendszer, adatbázis-kezelési rendszer futtatható fájljainak és SAP végrehajtható fájlok alap virtuális merevlemez (vagy lemez) futó SAP-rendszereinket. Az adatbázis-kezelő adathoz és naplófájlhoz a különálló lemezek (Standard vagy prémium szintű tároló) Azure Storage-ban tárolt és az eredeti Azure operációs rendszer lemezképének virtuális gép logikai lemezként csatolt. Linux-környezetek, különösen a dokumentált különböző javaslatokat is lehet. Különös tekintettel az SAP HANA.  

A lemez elrendezése megtervezésekor a következő elemek között egyensúlyba kell:

* Az adatfájlok számát.
* A lemezek, amelyek tartalmazzák a fájlok számát.
* Az egyetlen lemez iops-érték kvóták.
* Lemezenként fájlmegosztásra.
* A lehetséges Virtuálisgép-méret szerint további adatlemezek száma.
* A teljes tárterületek átviteli sebességének egy virtuális Gépet biztosít.
* A késést tud biztosítani a különféle Azure Storage.
* Virtuális gép SLA-k

Azure-adatlemez IOPS kvóta kényszeríti. Az Azure Standard Storage és a Premium Storage üzemeltetett lemezeket kvóta eltérőek. I/o-késés is eltér a két tárolási típusok között.  Tényezők biztosítása a Premium Storage a jobb i/o várakozási ideje. A virtuális gépek különböző típusairól mindegyike rendelkezik egy korlátozott számú adatlemezeket, amelynek a csatolni. Egy másik korlátozás, hogy csak bizonyos virtuális gépek típusai kihasználhatják az Azure Premium Storage. Ennek eredményeképpen a döntést Virtuálisgép bizonyos típusú előfordulhat, hogy nem csak alapján vezeti a Processzor- és követelményeket, hanem által az IOPS, késés és a lemez, amely általában vannak méretezve, a lemezek számát vagy a prémium szintű tárolólemezeket típusát az átviteli sebességet megkövetelő. Kifejezetten a Premium Storage a lemez mérete is valószínűleg szabja IOPS és átviteli sebesség érhető el, hogy az egyes lemezek szükséges számát.

> [!NOTE]
> A Premium Storage használatát az minden olyan adatok, a tranzakciós napló és a ismétlés fájlokat a DBMS üzembe helyezési erősen ajánlott. Így nem számít, hogy éles vagy nem éles rendszerek üzembe szeretné.

> [!NOTE]
> Annak érdekében, hogy az Azure előnyeit a egyedi [egyetlen virtuális gép SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) csatolt összes lemezt kell lennie az Azure Premium Storage, beleértve az alap virtuális merevlemez típusú.
>

Az elhelyezési az adatbázisfájlokat és a napló vagy visszaállíthatja a fájlokat és a használt, az Azure Storage IOPS, késés és a teljesítménybeli követelmények lehet definiálni. Annak érdekében, hogy van elég iops-t, előfordulhat, hogy kényszerített több lemez használhatja, vagy használjon nagyobb prémium szintű Storage-lemez. Esetén több lemezt használ, a lemezeken, amelyeket az adatfájlokat tartalmaz, vagy a napló vagy visszaállíthatja a fájlokat szoftver teríti volna létre. Ezekben az esetekben az IOPS és az adatátviteli sebességet SLA-k az alapul szolgáló Premium Storage-lemez vagy a maximális elérhető iops-t az Azure standard szintű Storage-lemezeket is halmozódnak az eredményül kapott stripe-készlet. 

Mint már említettük Ha az IOPS követelmény nagyobb, mint ami egy virtuális Merevlemezzel is szerepel, akkor kell egy VHD-k száma közötti az adatbázisfájlok számára szükséges IOPS számát egyensúlyba hozni. Az IOPS osszák szét a lemezeket a legegyszerűbb módja, hogy különböző lemezek a szoftver paritásos build. Ezután elhelyezheti egy számot, az SAP DBMS-adatfájlok ki a szoftver stripe faragottnak LUN-okra. a stripe a lemezek számát a növekvő igények szerint határozzák meg az IOPs a növekvő igények szerint, a lemez átviteli igényeknek és a kötet. 


- - -
> ![Windows][Logo_Windows] Windows
> 
> Azt javasoljuk, hogy a Windows tárolóhelyek használatával hozhat létre ilyen stripe között több Azure virtuális merevlemezeket. Javasoljuk, hogy legalább Windows Server 2012 R2 vagy Windows Server 2016-ban.
> 
> ![Linux][Logo_Linux] Linux
> 
> Csak a MDADM és az LVM (a Logical Volume Manager) hozhat létre egy szoftveres RAID Linux rendszeren támogatott. További információkért olvassa el a következő cikkeket:
> 
> - [A linuxon futó szoftver RAID konfigurálása](https://docs.microsoft.com/azure/virtual-machines/linux/configure-raid) MDADM használatával
> - [LVM konfigurálása az Azure-beli Linuxos virtuális gépre](https://docs.microsoft.com/azure/virtual-machines/linux/configure-lvm) LVM használatával
> 
> 

- - -
 
> [!NOTE]
> Mivel az Azure Storage három rendszerkép virtuális merevlemezhez viselkedéssel, azt nem értelmezhető szétosztott konfigurálása a redundancia. Csak akkor kell konfigurálni szétosztott, tehát, hogy az i/o vannak első elosztva a különböző virtuális merevlemezeket.
>

### <a name="managed-or-non-managed-disks"></a>Felügyelt vagy nem felügyelt lemezek
Egy Azure Storage-fiók nem, nem csak egy felügyeleti szerkezet, de korlátozások tárgya is. A korlátozások az Azure standard szintű Storage Accounst és az Azure Premium Storage-fiókok különböznek. A cikkben felsorolt pontos lehetőségeit és korlátait [Azure Storage méretezhetőségi és Teljesítménycéljai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)

Az Azure standard szintű Storage esetében fontos, hogy nincs-e korlátozni az iops-értékkel tárfiókonként visszahívása (sor **teljes kérelmek gyakorisága** cikkben [Azure Storage méretezhetőségi és Teljesítménycéljai](https://docs.microsoft.com/azure/storage/common/storage-scalability-targets)). Emellett van egy kezdeti maximális számát, a Tárfiókok és Azure-előfizetésenként. Ezért kell VHD-k egyenleg nagyobb SAP-rendszeren, ezeket a tárfiókok korlátairól elkerülje a különböző tárfiókok között. Egy fárasztó munka, ha néhány száz virtuális gépekről több ezer virtuális merevlemezzel rendelkező kiindulásként. 

Mivel az Azure standard szintű tárolást olyan adatbázis-kezelő telepítésekhez SAP számítási feladatok együtt nem ajánlott, referenciák és az Azure Standard tárolási javaslatok korlátozva, ebben a rövid [cikk](https://blogs.msdn.com/b/mast/archive/2014/10/14/configuring-azure-virtual-machines-for-optimal-storage-performance.aspx)

Annak érdekében, hogy a tervezési és üzembe helyezése a VHD-k különböző Azure-tárfiókok között az adminisztratív munka, a Microsoft bevezette úgynevezett [Managed Disks](https://azure.microsoft.com/services/managed-disks/) 2017-ben. A felügyelt lemezek az Azure standard szintű Storage, valamint az Azure Premium Storage érhetők el. Nem felügyelt lemezek listája például a főbb előnyei a Managed Disks képest:

- Az Azure Managed Disks esetében elosztja a különböző virtuális merevlemezeket különböző storage-fiókok automatikus üzembe helyezéskor, és ezáltal elkerülhető az adatmennyiség, i/o-teljesítményt és IOPS tekintetében az Azure Storage-fiók korlátairól lenyomásával.
- Felügyelt lemezek használatával Azure Storage van teljesítésére az Azure rendelkezésre állási csoportok a fogalmait, és az adott helyez üzembe a virtuális alaplemez és a egy virtuális gép csatlakoztatott lemez különböző hibatűrési és frissítési tartományokban, ha a virtuális gép Azure rendelkezésre állási csoport része.


> [!IMPORTANT]
> Adja meg az Azure Managed Disks előnyeit, erősen ajánlott az Azure Managed Disks használata az adatbázis-kezelő központi telepítések és SAP üzemelő példányok az általános.
>

Konvertálás nem felügyeltről felügyelt lemezzé, tekintse meg a cikkeket:

- [Windows virtuális gép átalakítása nem felügyeltről felügyelt a managed Disks szolgáltatásba](https://docs.microsoft.com/azure/virtual-machines/windows/convert-unmanaged-to-managed-disks)
- [Linux rendszerű virtuális gép átalakítása nem felügyeltről felügyelt a managed Disks szolgáltatásba](https://docs.microsoft.com/azure/virtual-machines/linux/convert-unmanaged-to-managed-disks)


### <a name="c7abf1f0-c927-4a7c-9c1d-c7b5b3b7212f"></a>Virtuális gépek és az adatlemezek gyorsítótárazás
Ha csatlakoztatja a virtuális lemezek, kiválaszthatja a i/o-forgalom a virtuális gép és az Azure storage-ban található lemezek közötti gyorsítótárba kerüljenek-e. Az Azure Standard és prémium szintű Storage két különböző technológiák használata az ilyen típusú gyorsítótár. 

Az alábbi javaslatok a standard szintű adatbázis-kezelő feltételezzük következő i/o-jellemzőkkel:

- Többnyire az adatfájlokat az adatbázis elleni munkaterhelés írásvédett. Ezek olvasási teljesítmény kritikus fontosságú az adatbázis-kezelő rendszer
- Az ellenőrzőpontok vagy állandó adatfolyam alapú adatlöketekkel bekövetkezik szemben az adatfájlok írása. Mindazonáltal átlagolva a nap, az írási műveletek olyan kisebb, mint az olvasások. Olvasott adatok fájlokból leváló ezek írási műveletek aszinkron, és be minden felhasználói tranzakció nem tárolnak.
- A tranzakcióból alig minden olvasási naplózás vagy a visszaállítási fájlok vannak. Kivételek nagyméretű i/o akkor, ha a tranzakciós napló biztonsági mentések végrehajtásához. 
- A tranzakció vagy ismétlés naplófájlok fő terhelés írások. Számítási feladat jellegétől függ rendelkezhet i 4 KB-os vagy más esetben i/o-méret 1 MB vagy annál kisebb.
- A rendszer megőrzi a lemezen egy megbízható módon kell az összes írási művelet

Az Azure standard szintű Storage a lehetséges gyorsítótár-típusok a következők:

* None
* Olvasás
* Olvasás/Írás

Annak érdekében, hogy egységes és determinisztikus teljesítmény eléréséhez, állítsa be a gyorsítótárazás az Azure standard szintű tárolást tartalmazó összes lemezek **DBMS kapcsolódó adatok fájlokat napló vagy visszaállíthatja a fájlokat, és hely a tábla "NONE"**. A gyorsítótárazás az alap VHD-t az alapértelmezett maradhat.

Az Azure Premium Storage esetében a következő gyorsítótárazási lehetőségek érhetők el:

* None
* Olvasás 
* Olvasási/írási 
* Nincs + Írásgyorsító (csak az Azure M sorozatú virtuális gépek esetén)
* Olvasás + Írásgyorsító (csak az Azure M sorozatú virtuális gépek esetén)

Az Azure Premium Storage esetében javasoljuk, hogy kihasználhatja **olvasási gyorsítótárazás az adatfájlok** az SAP adatbázis és a kiválasztott **naplófájlokhoz, a lemezek gyorsítótárazása**.

M-sorozat üzemelő példánya esetében javasoljuk a DBMS üzembe helyezési Azure Write Accelerator használandó. Részletekért, korlátozások és központi telepítését az Azure Írásgyorsító tekintse meg a dokumentum [Írásgyorsító](https://docs.microsoft.com/azure/virtual-machines/windows/how-to-enable-write-accelerator). 


### <a name="azure-non-persistent-disks"></a>Az Azure nem állandó lemezek
Azure virtuális gépek nem állandó lemezt is kínálnak, egy virtuális gép üzembe helyezése után. Esetén a virtuális gép újraindítását az adott meghajtókat minden tartalom törlődik. Ennélfogva a tekintve, hogy az adatfájlok és az adatbázisok napló vagy visszaállíthatja a fájlokat semmilyen körülmények között találhatók, ezek nem megőrzött meghajtókon. Van néhány adatbázist, a kivételek, ezek a meghajtók nem megőrzött tempdb és ideiglenes táblahelyeket alkalmas lehet. Azonban ne az adott meghajtókat használja A-sorozatú virtuális gépek, mivel ezek nem megőrzött meghajtók korlátozott, hogy a Virtuálisgép-család átviteli. További részletekért olvassa el a cikket [a Windows Azure virtuális gépeken futó ideiglenes meghajtó ismertetése](https://blogs.msdn.microsoft.com/mast/2013/12/06/understanding-the-temporary-drive-on-windows-azure-virtual-machines/)

- - -
> ![Windows][Logo_Windows] Windows
> 
> Egy Azure virtuális Gépen a D:\ meghajtón egy nem megőrzött meghajtót, az Azure-beli számítási csomópont által az egyes helyi lemezek biztonsági mentése. Mivel a szolgáltatás nem megőrzött, akkor ez azt jelenti, hogy a tartalom a D:\ meghajtóra végzett módosítások elvész, amikor a virtuális gép újraindul. "Végrehajtott módosítások", például a tárolt fájlok, hozta létre, könyvtárak, telepített alkalmazások stb.
> 
> ![Linux][Logo_Linux] Linux
> 
> Linux rendszerű Azure virtuális gépek automatikusan, amely az Azure számítási csomópont helyi lemezek által támogatott nem megőrzött meghajtó /mnt/resource meghajtót csatlakoztatni. Mivel a szolgáltatás nem megőrzött, akkor ez azt jelenti, hogy /mnt/resource tartalom végzett módosítások elvesznek, ha a virtuális gép újraindul. Végrehajtott módosítások, például a tárolt fájlok, hozta létre, könyvtárak, telepített alkalmazások stb.
> 
> 

- - -



### <a name="10b041ef-c177-498a-93ed-44b3441ab152"></a>A Microsoft Azure Storage rugalmasságot
A Microsoft Azure Storage tárolja az alap virtuális merevlemez (az operációs rendszer) és a csatlakoztatott lemezek vagy a Blobok legalább három külön tárolócsomópontok. Ez a tény helyi redundáns tárolást (LRS) nevezzük. LRS a alapértelmezett minden típusú tároló az Azure-ban. 

Többféleképpen is további redundancia, olyan minden cikk [Azure Storage replikáció](https://docs.microsoft.com/azure/storage/common/storage-redundancy?toc=%2fazure%2fstorage%2fqueues%2ftoc.json).

> [!NOTE]
>Kezdődően az Azure Premium Storage, amely az adatbázis-kezelő virtuális gépek és lemezek, adatbázis és a napló vagy visszaállíthatja a fájlokat tároló tárolási ajánlott típus, az egyetlen elérhető módszer esetén az LRS. Ennek eredményeképpen kell konfigurálása az adatbázis módszerek, például az SQL Server Always On, Oracle Data Guard vagy HANA Rendszerreplikáció adatbázis replikálása egy másik Azure-régióban, vagy egy másik Azure rendelkezésre állási zónában engedélyezéséhez.


> [!NOTE]
> A DBMS üzembe helyezési elérhető az Azure standard szintű Storage, Georedundáns tárolás használata nem ajánlott, mert teljesítményt, és nem fogadja el az írási sorrend között különböző virtuális merevlemezek, virtuális géphez csatolt. A tény nem teljesítésére az írási sorrend között különböző virtuális merevlemezek, a magas lehetséges, ha az adatbázis és a napló vagy visszaállíthatja a fájlokat a forrás virtuális gép oldalán (adott esetben többnyire) több virtuális merevlemezek vannak elosztva a replikációs cél oldalon inkonzisztens adatbázisok végül viseli.

 

## <a name="vm-node-resiliency"></a>A virtuális gép csomópont rugalmasságát
Az Azure Platform számos különböző SLA-kat kínál a virtuális gépek. A pontos részleteit a legújabb kiadásával található [virtuális gépekre vonatkozó SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/). Mivel az adatbázis-kezelő réteg része általában egy rendelkezésre állási kritikus fontosságú SAP-rendszerhez, hogy saját maga az rendelkezésre állási csoportok, a rendelkezésre állási zónák és a karbantartási események ismernie kell. Ezek a fogalmak a cikkek van [Azure-beli Windows virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability) és [az Azure-ban Linux rendszerű virtuális gépek rendelkezésre állásának kezelése](https://docs.microsoft.com/azure/virtual-machines/linux/manage-availability).  

Éles adatbázis-kezelő forgatókönyvek esetén az SAP számítási feladatok minimális javasoljuk, hogy:

- Egy külön rendelkezésre állási csoportot az azonos Azure-régióban található virtuális gépek üzembe helyezése.
- E két virtuális gépet az azonos Azure virtuális hálózat futtatná, és lenne hálózati adapterek kapcsolt kívül ugyanannyi alhálózatot.
- Adatbázis módszerekkel tartsa a készenléti a második virtuális géppel. Módszerek az SQL Server Always On, a Oracle Data Guard vagy a HANA-Rendszerreplikálást lehet.

Emellett egy harmadik egy másik Azure-régióban található virtuális gép üzembe helyezése és az azonos adatbázis módszerek használatával adjon meg egy aszinkron replika egy másik Azure-régióban.

Azure rendelkezésre állási csoportok beállításához módon mutatják be, ez [oktatóanyag](https://docs.microsoft.com/azure/virtual-machines/windows/tutorial-availability-sets).



## <a name="azure-network-considerations"></a>Az Azure a hálózatokkal kapcsolatos szempontok 
A nagyméretű SAP üzemelő példánya esetében javasoljuk, hogy használja a tervezet-, [Azure Virtual Datacenter](https://docs.microsoft.com/azure/networking/networking-virtual-datacenter) virtuális hálózatok közötti konfigurációs és az engedélyek és a szerepkör a hozzárendelések a szervezet különböző részeit.

Nincsenek kívül rendszereit több száz szabványosította többféle ajánlott:

- Az SAP-alkalmazás (ok) hoz van üzembe helyezve, nem rendelkezik hozzáféréssel az internethez.
- Az adatbázis, virtuális gépek futnak az alkalmazásrétegre megegyező virtuális hálózatba.
- A virtuális gépek a virtuális hálózaton belül egy privát IP-cím statikus foglalási rendelkezik. Tekintse meg a cikket [IP-címtípusokat és foglalási módszereket az Azure-ban](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm) hivatkozásként van listázva.
- Az adatbázis-kezelő virtuális gépek útválasztási korlátozások **nem** rendelkező tűzfal van telepítve a helyi adatbázis-kezelő virtuális gépeken. Ehelyett forgalom-útválasztást van definiálva a [Azure hálózati biztonsági csoportok (NSG)](https://docs.microsoft.com/azure/virtual-network/security-overview)
- Megadhat, és az adatbázis-kezelő virtuális gép forgalom elkülönítése, a virtuális gép rendeljen hozzá különböző hálózati adapterein. Ha minden hálózati adapter egy másik IP-címmel rendelkezik, és minden hálózati adapter egy hozzárendelt egy másik VNet-alhálózathoz, amely különböző NSG-szabályok újra rendelkezik. Ne feledje, hogy az elkülönítés vagy a hálózati forgalom-útválasztás csak egy mérték, és nem teszi lehetővé a hálózati átviteli sebességet a kvóták beállítását.

Két virtuális gépet az éles üzemben futó DBMS üzembe helyezési belüli Azure rendelkezésre állási csoport és a egy külön útválasztási az SAP-alkalmazási rétegre és a felügyeleti és a műveletek forgalmat a két adatbázis-kezelő virtuális gépekhez használja, a nyers diagram jelenne meg:

![Két virtuális gépet, a két alhálózat ábrája](./media/virtual-machines-shared-sap-deployment-guide/general_two_dbms_two_subnets.PNG)


### <a name="azure-load-balancer-for-redirecting-traffic"></a>Forgalom átirányítása az Azure load balancerben
A Funkciók, például SQL Server Always On vagy a HANA System replikációs használt privát virtuális IP-címek használatát egy Azure Load Balancer konfigurálása szükséges. Az Azure Load Balancer is képes meghatározni az adatbázis-kezelő aktív csomópontra, és átirányítja a forgalmat, kizárólag az aktív adatbázisba csomópont a mintavételi portokon keresztül. Az adatbázis-csomópont feladatátvétel esetén nincs szükség a SAP alkalmazás újrakonfigurálása. Ehelyett a leggyakrabban használt SAP-alkalmazások architektúrák újracsatlakozik a privát virtuális IP-cím alapján. Az Azure load balancer csapattagok pedig nyugodtabban aludhatnak a csomópont feladatátvételi reagált, a második csomópont a magánhálózati virtuális IP-cím alapján a forgalom átirányításával.

Az Azure két különböző kínál [betölteni a terheléselosztó termékváltozatok](https://docs.microsoft.com/azure/load-balancer/load-balancer-overview). Egy alapszintű vagy standard Termékvátozatú. Hacsak nem szeretné üzembe helyezés több Azure-beli rendelkezésre állási zónák, részletes hajtja végre az alapszintű load balancer Termékváltozat. 

Az adatbázis-kezelő virtuális gépek és az SAP alkalmazásrétegre mindig keresztül irányítja át az Azure load balancer mindig közötti adatforgalom? A válasz attól függ, hogyan konfigurálja a terheléselosztó. Ezen a ponton az időben a bejövő forgalom az adatbázis-kezelő virtuális gép mindig keresztül lesznek irányítva az Azure load balancert. A kimenő forgalmat útvonal az adatbázis-kezelő virtuális gép virtuális gép az Azure load Balancer konfigurációjától függ az alkalmazásrétegre. A terheléselosztó lehetővé teszi egy, a DirectServerReturn. Ha ez a lehetőség van konfigurálva, az adatbázis-kezelő virtuális gépről az SAP alkalmazásrétegre irányított forgalom lesz **nem** átirányíthatók az Azure load balanceren keresztül. Ehelyett azt közvetlenül halad át az alkalmazásrétegre. Ha DirectServerReturn nincs konfigurálva, a visszatérő forgalom, a SAP alkalmazás réteghez áthalad az Azure load balancer

Ajánlott, hogy az SAP-alkalmazási rétegre és a két réteg között a hálózati késés csökkentése érdekében az adatbázis-kezelő réteg között elhelyezett Azure load Balancer terheléselosztók együtt DirectServerReturn konfigurálása

Ilyen konfiguráció beállítása például közzé van téve az SQL server mindig a körül [Ez a cikk](https://docs.microsoft.com/azure/virtual-machines/windows/sqlclassic/virtual-machines-windows-classic-ps-sql-int-listener).

Ha az SAP-infrastruktúra környezetekhez, az Azure-ban közzétett github JSON-sablonok használni kíván, kell-e tanulmánya ez [sablon egy SAP 3 szintű rendszer](https://github.com/Azure/azure-quickstart-templates/tree/4099ad9bee183ed39b88c62cd33f517ae4e25669/sap-3-tier-marketplace-image-converged-md). Ez a sablon akkor is is vizsgálja a megfelelő beállításokat az Azure load Balancer.

### <a name="azure-accelerated-networking"></a>Az Azure gyorsított hálózatkezelés
Annak érdekében, hogy további csökkentése érdekében az Azure virtuális gépek közötti hálózati késés, erősen javasoljuk, hogy válassza ki a lehetőséget a [Azure gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) SAP számítási feladatok Azure virtuális gépek telepítésekor. Különösen a az SAP alkalmazásrétegre és az SAP DBMS réteg. 

> [!NOTE]
> Nem minden Virtuálisgép-típusokat támogatja a gyorsított hálózatkezelés. A hivatkozott cikk a virtuális gépek típusai, amely támogatja a gyorsított hálózatkezelés van listázása. 
>  

- - -
> ![Windows][Logo_Windows] Windows
> 
> A Windows tekintse meg [hozzon létre egy Windows virtuális gép gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-powershell) fogalmakat és a módot a gyorsított hálózatkezelésű virtuális gépek üzembe helyezése
> 
> ![Linux][Logo_Linux] Linux
> 
> A Linux, olvassa el a cikket [hozzon létre egy Linux rendszerű virtuális gép gyorsított hálózatkezelésű](https://docs.microsoft.com/azure/virtual-network/create-vm-accelerated-networking-cli) annak érdekében, hogy a Linux-disztribúció részletekért. 
> 
> 

- - -

> [!NOTE]
> SUSE, a Red Hat és az Oracle Linux esetében a gyorsított hálózatkezelés az újabb verziókban is támogatja. Korábbi kiadások, mint az SLES 12 SP2 vagy RHEL 7.2 nem támogatnak az Azure gyorsított hálózatkezelés 
>  


## <a name="deployment-of-host-monitoring"></a>Host Monitoring telepítése
Az éles használathoz az SAP alkalmazások az Azure Virtual machines szolgáltatásban az SAP képes monitorozási adatai az Azure Virtual machines szolgáltatásban futó fizikai gazdagép-gazda lekérése használatához. Egy adott gazdagép-ügynök SAP javítási szintje szükség, amely lehetővé teszi, hogy ez a funkció a SAPOSCOL és SAP-gazdagép-ügynök. A pontos javítási szintet leírása itt található SAP-Jegyzetnek [1409604].

Összetevő-gazdagép adatok SAPOSCOL és SAP-gazdagép-ügynök telepítési és összetevők életciklusának kezelését a részletekért tekintse meg a [üzembe helyezési útmutató][deployment-guide]


## <a name="next-steps"></a>További lépések
Az adott DBMS-dokumentáció tekintse meg a következő cikkeket:

- [Az SQL Server Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz](dbms_guide_sqlserver.md)
- [Oracle az Azure Virtual Machines DBMS üzembe helyezési SAP számítási feladatokhoz](dbms_guide_oracle.md)
- [IBM DB2 Azure virtuális gépek DBMS üzembe helyezési SAP számítási feladatokhoz](dbms_guide_ibm.md)
- [SAP ASE az Azure Virtual Machines DBMS üzembe helyezési SAP számítási feladatokhoz](dbms_guide_sapase.md)
- [SAP maxDB, a gyorsítótár Live és a webtartalom-kiszolgáló üzembe helyezés az Azure-ban](dbms_guide_maxdb.md)
- [SAP HANA az Azure-műveletek útmutatójában](hana-vm-operations.md)
- [SAP HANA magas rendelkezésre állás az Azure-beli virtuális gépek](sap-hana-availability-overview.md)
- [Biztonsági mentési útmutató az SAP Hana az Azure Virtual machines szolgáltatásban](sap-hana-backup-guide.md)

