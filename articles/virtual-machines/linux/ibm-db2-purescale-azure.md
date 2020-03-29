---
title: IBM DB2 pureScale az Azure-ban
description: Ebben a cikkben egy IBM DB2 pureScale környezet azure-beli futtatásához egy architektúrát jelenítünk meg.
author: njray
manager: edprice
editor: edprice
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.workload: infrastructure-services
ms.topic: article
ms.date: 11/09/2018
ms.author: edprice
ms.openlocfilehash: d8309a69c9c38610fa7bea3fee202a60d836980c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945057"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2 pureScale az Azure-ban

Az IBM DB2 pureScale környezet magas rendelkezésre állású és méretezhető linuxos operációs rendszereken magas rendelkezésre állású és méretezhető adatbázis-fürtöt biztosít az Azure számára. Ez a cikk a DB2 pureScale azure-beli futtatásához egy architektúrát jelenít meg.

## <a name="overview"></a>Áttekintés

A vállalatok régóta használják a hagyományos relációs adatbázis-kezelő rendszer (RDBMS) platformokat, hogy kielégítsék online tranzakciófeldolgozási (OLTP) igényeiket. Ezekben a napokban sokan a nagyszámítógép-alapú adatbázis-környezetek az Azure-ba, mint egy módja annak, hogy bővítse a kapacitást, a költségek csökkentése, és fenntartani a folyamatos működési költségszerkezet. A migráció gyakran az első lépés az örökölt platform modernizálásában. 

A közelmúltban egy vállalati ügyfél újraüzemeltette a z/OS rendszeren futó IBM DB2 környezetét az IBM DB2 pureScale-nek az Azure-on. A Db2 pureScale adatbázis-fürt megoldás magas rendelkezésre állást és méretezhetőséget biztosít Linux operációs rendszereken. Az ügyfél a Db2-t sikeresen futtatta önálló, felskálázási példányként egyetlen virtuális gépen (VM) egy nagy méretű azure-beli hálózaton a Db2 pureScale telepítése előtt. 

Bár nem azonos az eredeti környezettel, az IBM DB2 pureScale Linuxon hasonló magas rendelkezésre állású és méretezhetőségi funkciókat kínál, mint az IBM DB2 for z/OS, amely párhuzamos Sysplex konfigurációban fut a nagyszámítógépen. Ebben az esetben a fürt iSCSI-n keresztül egy megosztott tárolófürthöz csatlakozik. A GlusterFS fájlrendszert használtuk, egy ingyenes, skálázható, nyílt forráskódú elosztott fájlrendszert, amely kifejezetten a felhőalapú tárolásra van optimalizálva. Az IBM azonban már nem támogatja ezt a megoldást. Az IBM támogatásának fenntartásához támogatott iSCSI-kompatibilis fájlrendszert kell használnia. A Microsoft a Közvetlen tárolóhelyeket (S2D) kínálja

Ez a cikk ismerteti az Azure-migrálás hoz használt architektúra. Az ügyfél a Red Hat Linux 7.4-et használta a konfiguráció teszteléséhez. Ez a verzió az Azure Piactérről érhető el. Mielőtt linuxos disztribúciót választana, ellenőrizze a jelenleg támogatott verziókat. További információt az [IBM DB2 pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) és [GlusterFS dokumentációjában talál.](https://docs.gluster.org/en/latest/)

Ez a cikk a DB2 végrehajtási terv kiindulópontja. Az üzleti követelmények eltérőek lesznek, de ugyanaz az alapvető minta vonatkozik. Ezt az architekturális mintát az Azure-beli online analitikus feldolgozási (OLAP) alkalmazásokhoz is használhatja.

Ez a cikk nem foglalkozik a különbségek és a lehetséges áttelepítési feladatok áthelyezéséhez ibm DB2 z/OS adatbázis IBM DB2 pureScale Linuxon futó. És nem biztosít méretezési becsléseket és munkaterhelés-elemzéseket a DB2 z/OS-ről a DB2 pureScale-re való áttéréshez. 

Annak érdekében, hogy segítsen eldönteni, hogy a legjobb DB2 pureScale architektúra a környezetben, azt javasoljuk, hogy teljes mértékben becsülje meg a méretezést, és egy hipotézist. A forrásrendszeren vegye figyelembe a DB2 z/OS párhuzamos Sysplex adatmegosztási architektúrával, a kapcsolóberendezés konfigurációjával és az elosztott adatlevelemekkel (DDF) kapcsolatos használati statisztikákat.

> [!NOTE]
> Ez a cikk a DB2-áttelepítés egyik megközelítését ismerteti, de vannak mások is. A DB2 pureScale például virtualizált helyszíni környezetekben is futtatható. Az IBM támogatja a DB2-t a Microsoft Hyper-V-n különböző konfigurációkban. További információ: [DB2 pureScale virtualization architecture](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) in the IBM Knowledge Center.

## <a name="architecture"></a>Architektúra

A magas rendelkezésre állás és a méretezhetőség az Azure-ban, használhatja a kibővített, megosztott adatarchitektúra DB2 pureScale. Az ügyfél áttelepítése a következő példaarchitektúrát használta.

![DB2 pureScale az Azure virtuális gépein, amelyek tárolót és hálózatot mutatnak](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale az Azure virtuális gépein, amelyek tárolót és hálózatot mutatnak")


Az ábra a DB2 pureScale fürthöz szükséges logikai rétegeket mutatja. Ezek közé tartoznak az ügyfél, a felügyelet, a gyorsítótárazás, az adatbázis-motor és a megosztott tárolás virtuális gépei. 

Az adatbázis-motor csomópontjain kívül az ábra két csomópontot is tartalmaz, amelyeket fürtgyorsítótárazási létesítményekhez (C-khez) használnak. Legalább két csomópontot használ magának az adatbázis-motornak. A pureScale fürthöz tartozó DB2-kiszolgálót tagnak nevezzük. 

A fürt iSCSI-n keresztül csatlakozik egy három csomópontos megosztott tárolófürthöz, hogy kibővített tárolót és magas rendelkezésre állást biztosítson. A DB2 pureScale linuxos Azure virtuális gépekre van telepítve.

Ez a megközelítés egy sablon, amely módosíthatja a mérete és mérete a szervezet. A következőkön alapul:

-   Két vagy több adatbázis-tag legalább két CF-csomóponton van kombinálva. A csomópontok globális pufferkészletet (GBP) kezelnek a megosztott memória és a globális zároláskezelő (GLM) szolgáltatások számára a megosztott hozzáférés szabályozásához és az aktív tagok versengésének zárolásához. Az egyik CF-csomópont elsődleges, a másik pedig másodlagos, feladatátvételi CF-csomópontként működik. A környezet egyetlen meghibásodási pontjának elkerülése érdekében a DB2 pureScale fürtlegalább négy csomópontot igényel.

-   Nagy teljesítményű megosztott tároló (a diagram p30-as méretével látható). Minden csomópont ezt a tárolót használja.

-   Nagy teljesítményű hálózatkezelés az adattagok és a megosztott tárolás számára.

### <a name="compute-considerations"></a>Számítási szempontok

Ez az architektúra az alkalmazás-, tárolási és adatrétegeket futtatja az Azure virtuális gépeken. A [központi telepítési telepítési parancsfájlok](https://aka.ms/db2onazure) a következőket hozzák létre:

-   DB2 pureScale fürt. Az Azure-ban szükséges számítási erőforrások típusa a beállítástól függ. Általában két módszert használhat:

    -   Használjon többcsomópontos, nagy teljesítményű (HPC) stílusú hálózatot, ahol a kis- és közepes méretű példányok közös tárolóhoz férnek hozzá. Ehhez a HPC-konfigurációtípushoz az Azure memóriaoptimalizált E-sorozatú vagy tárolási optimalizált L sorozatú [virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) biztosítják a szükséges számítási teljesítményt.

    -   Kevesebb nagy virtuálisgép-példányt használjon az adatmotorokhoz. Nagy példányokban a legnagyobb memóriaoptimalizált [M sorozatú](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuális gépek ideálisak a nagy memórián belüli munkaterhelésekhez. Előfordulhat, hogy a DB2 futtatásához használt logikai partíció (LPAR) méretétől függően egy dedikált példányra van szükség.

-   A DB2 CF memóriaoptimalizált virtuális gépeket használ, például E- vagy L-sorozatú gépeket.

-   A megosztott tárolófürt,\_amely\_szabványos DS4 v2 virtuális gépek Linux ot használ.

-   A felügyeleti jumpbox\_egy Linux\_ot futtató szabványos DS2 v2 virtuális gép.  Egy másik lehetőség az Azure Bastion, egy szolgáltatás, amely biztonságos RDP/SSH élményt biztosít a virtuális hálózat összes virtuális gépéhez.

-   Az ügyfél egy\_windows\_os szabványos DS3 v2 virtuális gép (tesztelésre szolgál).

-   *Nem kötelező*. Egy tanú szerver. Erre csak a Db2 pureScale bizonyos korábbi verzióinál van szükség. Ebben a példában egy linuxos (DB2 pureScale- hez használt) szabványos\_DS3 v2\_virtuális gépet használ.

> [!NOTE]
> A DB2 pureScale fürt legalább két DB2-példányt igényel. Gyorsítótár-példányt és zároláskezelői példányt is igényel.

### <a name="storage-considerations"></a>A tárterülettel kapcsolatos szempontok

Az Oracle RAC-hoz hasonlóan a DB2 pureScale is egy nagy teljesítményű i/o-blokk, kibővített adatbázis. Javasoljuk, hogy a legnagyobb [Azure prémium szintű SSD-beállítást](disks-types.md) használja, amely megfelel az igényeinek. Kisebb tárolási lehetőségek alkalmas lehet a fejlesztési és tesztelési környezetekben, míg az éles környezetekben gyakran több tárolási kapacitást igényel. A példa architektúra [p30-at](https://azure.microsoft.com/pricing/details/managed-disks/) használ az IOPS és a méret és az ár aránya miatt. Mérettől függetlenül használja a prémium szintű tárhelyet a legjobb teljesítmény érdekében.

A DB2 pureScale egy mindent megosztott architektúrát használ, ahol az összes adat elérhető az összes fürtcsomópontról. A prémium szintű tárhelyet több példányban is meg kell osztani, akár igény szerint, akár dedikált példányokon.

Egy nagy DB2 pureScale fürt 200 terabájt (TB) vagy több prémium szintű megosztott tárterületet igényelhet, 100 000 IOPS-értékkel. A DB2 pureScale támogatja az Azure-ban használható iSCSI blokkfelületet. Az iSCSI-csatolóhoz olyan megosztott tárolófürtre van szükség, amelyet S2D-vel vagy más eszközzel valósíthat meg. Ez a fajta megoldás létrehoz egy virtuális tárolóhálózati (vSAN) eszközt az Azure-ban. A DB2 pureScale a vSAN-t használja a fürtözött fájlrendszer telepítéséhez, amely az adatok virtuális gépek közötti megosztására szolgál.

### <a name="networking-considerations"></a>Hálózati megfontolások

Az IBM az InfiniBand hálózatkezelést ajánlja a DB2 pureScale fürt minden tagja számára. A DB2 pureScale távoli közvetlen memória-hozzáférést (RDMA) is használ, ahol elérhető, a cf-ekhez.

A telepítés során hozzon létre egy [Azure-erőforráscsoportot,](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) amely tartalmazza az összes virtuális gépet. Az erőforrásokat általában az élettartamuk és azok kezelése alapján csoportosíthatja. Az architektúra virtuális gépei [gyorsított hálózatkezelést igényelnek.](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/) Ez egy Azure-szolgáltatás, amely egységes, ultraalacsony hálózati késést biztosít egygyökeres I/O-virtualizáción (SR-IOV) keresztül egy virtuális gépre.

Minden Azure virtuális gép egy virtuális hálózatba van telepítve, amely alhálózatokkal rendelkezik: fő, Gluster FS előtér (gfsfe), Gluster FS háttérrendszer (bfsbe), DB2 pureScale (db2be) és DB2 pureScale előtér (db2fe). A telepítési parancsfájl is létrehozza az elsődleges [hálózati adapterek](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) a fő alhálózatban lévő virtuális gépeken.

A [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) segítségével korlátozhatja a hálózati forgalmat a virtuális hálózaton belül, és elkülönítheti az alhálózatokat.

Az Azure-ban a DB2 pureScale-nek a TCP/IP-t kell használnia a tárolási hálózati kapcsolatként.

## <a name="next-steps"></a>További lépések

-   [Az architektúra üzembe helyezése az Azure-ban](deploy-ibm-db2-purescale-azure.md)
