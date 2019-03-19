---
title: IBM DB2-höz pureScale az Azure-ban
description: Ebben a cikkben bemutatjuk az architektúra az IBM DB2-höz pureScale környezet Azure-ban futtatja.
services: virtual-machines-linux
documentationcenter: ''
author: njray
manager: edprice
editor: edprice
tags: ''
ms.assetid: ''
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/09/2018
ms.author: njray
ms.openlocfilehash: 1622de0cccdbc8fee0681e209e756b30da292d3c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58003506"
---
# <a name="ibm-db2-purescale-on-azure"></a>IBM DB2-höz pureScale az Azure-ban

Az IBM DB2-höz pureScale környezet egy adatbázis-fürtöt az Azure-ban magas rendelkezésre állást és méretezhetőséget, a Linux operációs rendszer biztosít. Ez a cikk bemutatja az architektúra az Azure-on futó DB2 pureScale.

## <a name="overview"></a>Áttekintés

Vállalatok hosszú használt relációs adatbázis-felügyeleti rendszerének (RDBMS) platformok azok online tranzakciófeldolgozási (OLTP) igényeinek megfelelően. Napjainkban számos végzi az áttelepítést a nagygépes-alapú adatbázis-környezetek Azure arra, hogy a kapacitás bővítése, csökkentheti a költségeket és a egy folyamatos üzemeltetési költségek struktúra karbantartásához.

Az áttelepítés akkor gyakran az első lépés egy régebbi platform újratervezésén. Például egy nagyvállalati ügyfél nemrég rehosted z/OS IBM DB2-höz pureScale az Azure-ban futó IBM DB2-környezete. Bár ez nem azonos az eredeti környezetbe, IBM DB2-höz pureScale linuxon biztosít hasonló magas rendelkezésre állás és méretezhetőség szolgáltatások, IBM DB2-höz z/os-t, a nagyszámítógépes párhuzamos Sysplex konfigurációban futó.

Ez a cikk ismerteti az architektúra az Azure-migrálás használt. Az ügyfél használja a Red Hat Linux 7.4 teszteli a konfigurációt. Ebben a verzióban az Azure piactéren érhető el. Mielőtt úgy dönt, hogy a Linux-disztribúció, ügyeljen arra, hogy a jelenleg támogatott verziók ellenőrzéséhez. További információkért lásd: a dokumentációban [IBM DB2-höz pureScale](https://www.ibm.com/support/knowledgecenter/SSEPGG) és [GlusterFS](https://docs.gluster.org/en/latest/).

Ez a cikk a DB2-megvalósítási terv kiindulópontként szolgál. Az üzleti követelmények eltérőek lehetnek, de az alapszintű minta vonatkozik. Architekturális mintáról online elemzésfeldolgozási (OLAP) alkalmazásokhoz az Azure-ban is használhatja.

Ez a cikk nem tárgyalja, különböző módszereit és az IBM DB2-höz z/OS-adatbázis áthelyezése a linuxon futó IBM DB2-höz pureScale lehetséges áttelepítési feladatok. És azt nem biztosít méretezési becsléseket és számítási feladatok elemzéseket a DB2 pureScale DB2 z/OS áthelyezését. 

Eldöntheti, hogy a környezet a legjobb DB2 pureScale architektúrával érdekében azt javasoljuk, hogy teljes mértékben méretezési megbecsülheti, és győződjön meg arról, egy változat. A forrás rendszeren, ügyeljen arra, hogy fontolja meg a DB2 z/operációs rendszer párhuzamos Sysplex adatmegosztás architektúra, kapcsolási létesítmény konfigurációs és elosztott adatok létesítmény (DDF) kihasználtságának statisztikai adatai.

> [!NOTE]
> Ez a cikk ismerteti a DB2-áttelepítés egyik módja, de más felhasználók is. DB2-pureScale például is futtathatja a helyszíni virtualizált környezetekben. IBM a Microsoft Hyper-V támogatja DB2 különböző konfigurációkban. További információkért lásd: [DB2 pureScale virtualizálási architektúra](https://www.ibm.com/support/knowledgecenter/en/SSEPGG_11.1.0/com.ibm.db2.luw.qb.server.doc/doc/r0061462.html) az IBM Tudásbázis központban.

## <a name="architecture"></a>Architektúra

Támogatja a magas rendelkezésre állás és méretezhetőség az Azure-ban, DB2 pureScale egy horizontális felskálázás, a megosztott adatok architektúra is használhatja. Az ügyfél áttelepítése a következő példa architektúrát használja.

![Megjeleníti a tárolási és hálózatkezelési Azure virtuális gépeken futó DB2 pureScale](media/db2-purescale-on-azure/pureScaleArchitecture.png "DB2 pureScale megjeleníti a tárolási és hálózatkezelési Azure virtuális gépeken")


Az ábrán a logikai rétegekre DB2 pureScale fürt szükséges. Ezek közé tartozik a virtuális gépek ügyfél, a felügyeleti, gyorsítótárazás, az adatbázismotor és a megosztott tároló. 

A diagram mellett a database engine csomópontok magában foglalja a gyorsítótárazás létesítményekben (CFs) fürt két csomópont. Legalább két csomóponttal szolgálnak az adatbázismotor magát. Egy DB2-kiszolgálót, amely a pureScale fürtjeihez tag neve. 

A fürt egy három csomópontos GlusterFS megosztott tárolási fürt kibővített tárolási és magas rendelkezésre állás biztosítása érdekében iSCSI-n keresztül csatlakozik. DB2-pureScale telepítve van a Linux operációs rendszert futtató Azure virtuális gépeken.

Ez a megközelítés akkor a sablont, amely módosíthatja a méretét és a méretezési csoport a szervezet számára. Alapul a következők:

-   CF-hez legalább két csomóponttal rendelkező mostantól két vagy több adatbázis tagjai. A csomópontok egy globális pufferkészletben (GBP) kezelheti a megosztott memória és a globális lock (GLM) manager szolgáltatásainak vezérlésére megosztott hozzáférés és a zárolási versengését a több aktív tagjainak. CF-hez egy csomópont az elsődleges és a másik a másodlagos, CF Feladatátvevőfürt-csomópontként működik. A környezetben a hibaérzékeny pont elkerülése érdekében egy DB2-pureScale fürt legalább négy csomópont szükséges.

-   Nagy teljesítményű megosztott tárolót (P30 méretű az ábrán látható). Ezt a tárolót használja a Gluster FS csomópontok.

-   Nagy teljesítményű hálózatkezelés az adatokat a tagok és a megosztott tárolót.

### <a name="compute-considerations"></a>Számítási szempontok

Ez az architektúra az alkalmazást, tárolás és adatok csomagok Azure-beli virtuális gépeken futtatja. A [központi telepítési telepítő szkriptek](https://aka.ms/db2onazure) hozza létre a következő:

-   Egy DB2-pureScale fürtöt. Az Azure-ban van szüksége a számítási erőforrások típusától függ, a telepítő. Általában két módszer használható:

    -   Használja a több csomópontos, nagy teljesítményű feldolgozási (HPC)-stílus hálózati, kis és közepes méretű példányok hozzáférhetnek a megosztott tárolóhoz. Az ilyen konfigurációs HPC típusú Azure memóriaoptimalizált E sorozatú vagy az L sorozat tárolásra optimalizált [virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/windows/sizes) adja meg a szükséges számítási kapacitást.

    -   Kevesebb nagyméretű virtuálisgép-példányt használja a motorok adatait. Nagyméretű példányok a memóriára optimalizált [M-sorozat](https://azure.microsoft.com/pricing/details/virtual-machines/series/) virtuális gépek ideálisak a nagy memóriabeli számítási feladatok. A dedikált példányt, a DB2 futtatásához használt logikai partíció (LPAR) méretétől függően szükség lehet.

-   A DB2 CF memóriára optimalizált virtuális gépek, például az E sorozatú vagy az L sorozat használ.

-   GlusterFS storage használ Standard\_DS4\_v2 virtuális gépeket futtató Szolgáltatásrétegekben.

-   Egy GlusterFS jumpbox egy Standard\_DS2\_linuxos v2 virtuális gép.

-   Az ügyfél nem szabványos\_DS3\_v2 virtuális gép fut (a teszteléshez használt) Windows.

-   A tanúsító kiszolgáló egy szabványos\_DS3\_v2 virtuális gép linuxos (DB2 pureScale használatos).

> [!NOTE]
> Egy DB2-pureScale fürt legalább két DB2-példányt igényel. A cache-példány és a egy zárolás manager példányt is szükséges.

### <a name="storage-considerations"></a>A tárterülettel kapcsolatos szempontok

Oracle jogosultságifiók-tanúsítványok, például a DB2 pureScale egy nagy teljesítményű blokk i/o, horizontális felskálázás adatbázis. Javasoljuk, hogy használja a legnagyobb [Azure prémium szintű SSD](disks-types.md) igényeinek megfelelő beállítást. Kisebb méretű tárolási lehetőség lehet megfelelő, fejlesztési-tesztelési környezetet, míg az éles környezetben gyakran további tárolási kapacitásra van szüksége. A példa architektúrát használ [P30](https://azure.microsoft.com/pricing/details/managed-disks/) miatt az IOPS és a méretét és az ár aránya. Méret, függetlenül a Premium Storage használata a legjobb teljesítmény érdekében.

DB2-pureScale használ egy megosztott-mindent architektúra, ahol az összes adat érhető el a fürt összes csomópontján. A Premium storage-példányán, meg kell osztani, igény szerinti vagy dedikált példányon.

Egy nagy DB2 pureScale fürtöt is igénybe 200 terabájt (TB), vagy több prémium szintű storage, megosztva, 100 000 iops-t. DB2 pureScale támogatja az iSCSI blokk felületet is használhatja az Azure-ban. Az iSCSI-illesztő van szükség a megosztott tárolófürt GlusterFS, S2D-t vagy egy másik eszköz valósíthatók meg. Az ilyen típusú megoldás létrehoz egy virtuális tárolási területen (vSAN) hálózati eszköz az Azure-ban. DB2-pureScale használja a vsan-hoz használt virtuális gép között adatokat megosztani fürtözött fájlrendszer telepítéséhez.

A példa architektúra használja, GlusterFS, egy ingyenes, skálázható, nyílt forráskódú elosztott fájlrendszer, amely a felhőalapú tárolás van optimalizálva.

### <a name="networking-considerations"></a>Hálózati megfontolások

IBM DB2 pureScale fürt szereplő összes tag InfiniBand hálózatok használatát javasolja. DB2-pureScale is használ távoli közvetlen memória-hozzáféréses (RDMA), ha rendelkezésre áll, a CFs.

A telepítés során az Azure-beli létrehozása [erőforráscsoport](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-overview) összes virtuális gépet tartalmazhat. Általánosságban véve csoportosítja az erőforrásokat az élettartamuk és fogják kezelni őket. Ebben az architektúrában a virtuális gépeknél szükség van a [gyorsított hálózatkezelés](https://azure.microsoft.com/blog/maximize-your-vm-s-performance-with-accelerated-networking-now-generally-available-for-both-windows-and-linux/). Az Azure szolgáltatásai által biztosított ultramagas alacsony késést keresztül egygyökerű i/o-virtualizálás (SR-IOV) egy virtuális géphez.

Minden Azure virtuális gépen van üzembe helyezve egy virtuális hálózatot, amely rendelkezik alhálózatokkal: main, Gluster FS előtér (gfsfe), Gluster FS háttérrendszerének (bfsbe), DB2 pureScale (db2be) és DB2 pureScale front end (db2fe). A telepítési parancsfájlt is létrehoz az elsődleges [hálózati adapterek](https://docs.microsoft.com/azure/virtual-machines/linux/multiple-nics) fő alhálózaton lévő virtuális gépeken.

Használat [hálózati biztonsági csoportok](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg) különíteni az alhálózatokat és hálózati forgalom korlátozására a virtuális hálózaton belül.

Az Azure-ban DB2 pureScale kell TCP/IP használata a hálózati kapcsolat, tároláshoz.

## <a name="next-steps"></a>További lépések

-   [Ez az architektúra az Azure-ban üzembe helyezése](deploy-ibm-db2-purescale-azure.md)
