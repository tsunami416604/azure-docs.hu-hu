---
title: Oracle-megoldások Az Azure virtuális gépeken | Microsoft dokumentumok
description: Ismerje meg az Oracle virtuálisgép-lemezképek támogatott konfigurációit és korlátait a Microsoft Azure-ban.
services: virtual-machines-linux
documentationcenter: ''
author: BorisB2015
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: borisb
ms.openlocfilehash: 0cee7c25960d567c75a14d8ad9ef95b3e7221862
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/21/2020
ms.locfileid: "81683412"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle virtuálisgép-lemezképek és azok üzembe helyezése a Microsoft Azure-ban

Ez a cikk az Oracle által az Azure Piactéren közzétett virtuálisgép-lemezképeken alapuló Oracle-megoldásokra vonatkozó információkat ismerteti. Ha érdeklik a felhőközi alkalmazásmegoldások az Oracle Cloud Infrastructure segítségével, olvassa el a [Microsoft Azure-t és az Oracle Cloud Infrastructure-t integráló Oracle alkalmazásmegoldások című témakört.](oracle-oci-overview.md)

A jelenleg elérhető lemezképek listájának leéséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2019 májusátoktól a következő képek érhetők el:

```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Ee      Oracle       12.2.0.1                Oracle:Oracle-Database-Ee:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Ee      Oracle       18.3.0.0                Oracle:Oracle-Database-Ee:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170220             12.1.20170220
Oracle-Database-Se      Oracle       12.2.0.1                Oracle:Oracle-Database-Se:12.2.0.1:12.2.20180725             12.2.20180725
Oracle-Database-Se      Oracle       18.3.0.0                Oracle:Oracle-Database-Se:18.3.0.0:18.3.20181213             18.3.20181213
Oracle-Linux            Oracle       6.10                    Oracle:Oracle-Linux:6.10:6.10.20190506                       6.10.20190506
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20190506                         6.8.20190506
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20190506                         6.9.20190506
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20190506                         7.3.20190506
Oracle-Linux            Oracle       7.4                     Oracle:Oracle-Linux:7.4:7.4.20190506                         7.4.20190506
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20181207                         7.5.20181207
Oracle-Linux            Oracle       7.5                     Oracle:Oracle-Linux:7.5:7.5.20190506                         7.5.20190506
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20181207                         7.6.20181207
Oracle-Linux            Oracle       7.6                     Oracle:Oracle-Linux:7.6:7.6.20190506                         7.6.20190506
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Ezek a lemezképek "Hozd a saját licenc", és mint ilyen, csak akkor kell fizetnie a számítási, tárolási és hálózati költségek et a virtuális gép futtatása során.  Feltételezzük, hogy ön megfelelő licenccel rendelkezik az Oracle szoftver használatára, és hogy jelenleg támogatási megállapodás van érvényben az Oracle-lel. Az Oracle garantált licenchordozhatósággal rendelkezik a helyszíni azure-ból. A licenchordozhatóságról a közzétett [Oracle és Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) feljegyzésben talál részleteket. 

Az egyének dönthetnek úgy is, hogy a megoldásaikat egy olyan egyéni lemezképre alapozza, amelyet az Azure-ban hoznak létre, vagy egyéni lemezképet töltenek fel a helyszíni környezetükből.

## <a name="oracle-database-vm-images"></a>Oracle adatbázis Virtuálisgép-lemezképei
Az Oracle támogatja az Oracle Database 12.1 és a magasabb standard és nagyvállalati kiadások futtatását az Azure-ban Oracle Linux alapú virtuálisgép-lemezképeken.  Az Azure-beli Oracle Database éles számítási feladatok hoz a legjobb teljesítmény érdekében győződjön meg arról, hogy megfelelően méretezi a virtuális géplemezképét, és prémium szintű SSD- vagy Ultra SSD-felügyelt lemezeket használ. Ha tudni szeretné, hogyan lehet gyorsan üzembe hozni egy Oracle-adatbázist az Azure-ban az Oracle által közzétett virtuálisgép-lemezkép használatával, [próbálja meg az Oracle Database rövid útmutatóját.](oracle-database-quick-create.md)

### <a name="attached-disk-configuration-options"></a>Csatolt lemezkonfigurációs beállítások

A csatlakoztatott lemezek az Azure Blob storage szolgáltatásra támaszkodnak. Minden szabványos lemez elméletileg legfeljebb 500 bemeneti/kimeneti művelet/másodperc (IOPS) képes. Prémium szintű lemezajánlatunk a nagy teljesítményű adatbázis-számítási feladatokhoz előnyös, és lemezenként akár 5000 IOps-t is elérhet. Egyetlen lemezt is használhat, ha az megfelel a teljesítményigényeknek. Azonban javíthatja a hatékony IOPS-teljesítményt, ha több csatlakoztatott lemezt használ, adatbázisadatokat oszt el közöttük, majd az Oracle Automatic Storage Management (ASM) szolgáltatást használja. További Oracle ASM-specifikus információkért tekintse meg az [Oracle Automatic Storage áttekintését.](https://www.oracle.com/technetwork/database/index-100339.html) Az Oracle ASM Linux Azure virtuális gépeken történő telepítéséről és konfigurálásáról az [Oracle Automated Storage Management telepítése és konfigurálása](configure-oracle-asm.md) oktatóanyag című témakörben látható.

### <a name="shared-storage-configuration-options"></a>Megosztott tároló konfigurációs beállításai

Az Azure NetApp Files célja az volt, hogy megfeleljen a nagy teljesítményű számítási feladatok, például a felhőben lévő adatbázisok futtatásának alapvető követelményeinek, és biztosítja;
- Az Azure natív megosztott NFS-tárolási szolgáltatása az Oracle-számítási feladatok vm natív NFS-ügyfélen vagy Oracle dNFS-en keresztül történő futtatásához
- Skálázható teljesítményszintek, amelyek tükrözik az IOPS-igények valós tartományát
- Alacsony késleltetés
- Magas rendelkezésre állás, nagy tartósság és nagy fokú kezelhetőség, amelyet általában a kritikus fontosságú vállalati munkaterhelések (például az SAP és az Oracle) igényelnek
- Gyors és hatékony biztonsági mentés és helyreállítás, a legagresszívebb RTO és RPO SLA

Ezek a képességek azért lehetségesek, mert az Azure NetApp Files a NetApp® ONTAP® az Azure adatközponti környezetben futó összes flash rendszeren alapul – mint Azure Native szolgáltatás. Az eredmény egy ideális adatbázis-tárolási technológia, amely kiépíthető és felhasználható, mint más Azure-tárolási lehetőségek. Az Azure NetApp Files NFS-köteteinek üzembe helyezéséről és eléréséről az [Azure NetApp Files](https://docs.microsoft.com/azure/azure-netapp-files/) nfs-köteteinek üzembe helyezéséről és eléréséről további információt talál. Tekintse meg [az Oracle az Azure-telepítés gyakorlati útmutató ját az Azure NetApp-fájlok használatával](https://www.netapp.com/us/media/tr-4780.pdf) című témakörben az Oracle-adatbázisok Azure NetApp-fájlokon való üzemeltetésére vonatkozó gyakorlati tanácsokért.


## <a name="licensing-oracle-database--software-on-azure"></a>Az Oracle Database & szoftver licencelése az Azure-ban
A Microsoft Azure egy engedélyezett felhőalapú környezet az Oracle Database futtatásához. Az Oracle Core Factor tábla nem alkalmazható oracle adatbázisok felhőben történő licencelésekor. Ehelyett ha az Enterprise Edition-adatbázisokban engedélyezett Hyper-Threading technology technológiával rendelkező virtuális gépeket használ, két vCPU-t egy Oracle processzorlicencel egyenértékűnek tekint, ha a hyperthreading engedélyezve van (ahogy az a házirenddokumentumban is szerepel). A szabályzat részletei [itt](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)találhatók.
Az Oracle adatbázisok általában nagyobb memóriát és I/O-t igényelnek. Emiatt [memóriaoptimalizált virtuális gépek](https://docs.microsoft.com/azure/virtual-machines/linux/sizes-memory) ajánlott ezeket a számítási feladatokat. A számítási feladatok további optimalizálása [érdekében a korlátozott core vCPU-k](https://docs.microsoft.com/azure/virtual-machines/linux/constrained-vcpu) használata ajánlott a nagy memóriát, tárhelyet és I/O-sávszélességet igénylő Oracle Database-számítási feladatokhoz, de nem magas magszámot.

Az Oracle-szoftverek és -munkaterhelések helyszíni microsoft azure-ba történő áttelepítésekor az Oracle licencmobilitást biztosít az [Oracle az Azure-ban – gyakori kérdésekben](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) foglaltaknak aszerint.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application Cluster (Oracle RAC)
Az Oracle Real Application Cluster (Oracle RAC) célja, hogy csökkentse egyetlen csomópont meghibásodását egy helyszíni többcsomópontos fürtkonfigurációban. Két helyszíni technológiára támaszkodik, amelyek nem őshonosak a nagy méretű nyilvános felhőkörnyezetekben: a hálózati többküldésű és a megosztott lemez. Ha az adatbázis-megoldás hoz oracle RAC az Azure-ban, szüksége van a harmadik = féltől származó szoftver, hogy ezeket a technológiákat. Az Oracle RAC szolgáltatásról további információt a [FlashGrid SkyCluster oldalon](https://www.flashgrid.io/oracle-rac-in-azure/)talál.

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állási és vész-helyreállítási szempontok
Oracle-adatbázisok használata az Azure-ban, ön felelős a magas rendelkezésre állású és vész-helyreállítási megoldás megvalósítása érdekében az állásidő elkerülése érdekében. 

Az Oracle Database Enterprise Edition magas rendelkezésre állása és vészhelyreállítása (az Oracle RAC-ra való támaszkodás nélkül) az Azure-on a [Data Guard, az Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html)vagy az Oracle [GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)használatával érhető el, két különböző virtuális gépen lévő adatbázissal. Mindkét virtuális gépnek ugyanabban a [virtuális hálózatban](https://azure.microsoft.com/documentation/services/virtual-network/) kell lennie, hogy a privát állandó IP-címen keresztül hozzáférhessenek egymáshoz.  Emellett azt javasoljuk, hogy a virtuális gépek ugyanabban a rendelkezésre állási készletben, hogy az Azure-t, hogy azokat külön tartalék tartományokba és a tartományok frissítése. Ha georedundanciát szeretne, állítsa be a két adatbázist két különböző régió közötti replikálásra, és csatlakoztassa a két példányt egy VPN-átjáróval.

Az [oktatóanyag implementálja az Oracle Data Guard-ot az Azure-ban,](configure-oracle-dataguard.md) és végigvezeti az Azure alapvető beállítási eljárásán.  

Az Oracle Data Guard segítségével magas rendelkezésre állás érhető el egy virtuális gép elsődleges adatbázisával, egy másik virtuális gép másodlagos (készenléti) adatbázisával és a közöttük beállított egyirányú replikációval. Az eredmény az adatbázis másolatához való olvasási hozzáférés. Az Oracle GoldenGate segítségével konfigurálhatja a két adatbázis közötti kétirányú replikációt. Ha meg szeretné tudni, hogyan állíthat be magas rendelkezésre állású megoldást az adatbázisokhoz ezekkel az eszközökkel, olvassa el az [Active Data Guard](https://www.oracle.com/database/technologies/high-availability/dataguard.html) és a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentációját az Oracle webhelyén. Ha olvasási és írási hozzáférésre van szüksége az adatbázis másolatához, használhatja az [Oracle Active Data Guard programot.](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html)

Az [oktatóanyag implementálja az Oracle GoldenGate-et az Azure-on,](configure-oracle-golden-gate.md) és végigvezeti az Azure alapvető beállítási eljárásán.

Amellett, hogy az Azure-ban egy HA és DR megoldás van megtervezve, az adatbázis visszaállításához egy biztonsági mentési stratégiával is rendelkeznie kell. Az [oktatóanyag biztonsági mentése és helyreállítása az Oracle Database](oracle-backup-recovery.md) végigvezeti az alapvető eljárás létrehozásához egységes biztonsági mentés.


## <a name="support-for-jd-edwards"></a>JD Edwards támogatása
Az Oracle Support note [Doc ID 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4)szerint a JD Edwards EnterpriseOne 9.2-es `Minimum Technical Requirements` és újabb verziói minden olyan nyilvános **felhőajánlatban** támogatottak, amely megfelel az adott (MTR) szolgáltatásnak.  Létre kell hoznia olyan egyéni lemezképeket, amelyek megfelelnek az MTR-specifikációiknak az operációs rendszer és a szoftveralkalmazások kompatibilitásához. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuális gép lemezképei

* **A fürtözés csak az Enterprise Edition szolgáltatásban támogatott.** A WebLogic fürtözés csak az Oracle WebLogic Server Enterprise Edition használata esetén használható. Ne használja a fürtözést az Oracle WebLogic Server Standard Edition szolgáltatással.
* **Az UDP csoportos küldése nem támogatott.** Az Azure támogatja az UDP egyedi küldést, de nem csoportos küldést vagy műsorszórást. Az Oracle WebLogic Server az Azure UDP egyedi küldési képességeire támaszkodhat. Az UDP egyedi küldésre támaszkodó legjobb eredmény érdekében azt javasoljuk, hogy a WebLogic-fürt mérete statikus maradjon, vagy legfeljebb 10 felügyelt kiszolgálón maradjon.
* **Az Oracle WebLogic Server elvárja, hogy a nyilvános és a privát portok megegyeznek a T3-hozzáféréssel (például az Enterprise JavaBeans használatakor).** Vegyünk egy többrétegű forgatókönyvet, amelyben egy szolgáltatási réteg (EJB) alkalmazás fut egy Oracle WebLogic Server fürtön, amely két vagy több virtuális gépből áll, egy *SLWLS*nevű virtuális hálózatban. Az ügyfélszint ugyanabban a virtuális hálózatban található egy másik alhálózatban, és egy egyszerű Java programot futtat, amely megpróbálja felhívni az EJB-t a szolgáltatási rétegben. Mivel a szolgáltatási réteg terheléselosztása szükséges, nyilvános terheléselosztású végpontot kell létrehozni az Oracle WebLogic Server-fürt virtuális gépei számára. Ha a megadott privát port eltér a nyilvános porttól (például 7006:7008), a következőhöz hasonló hiba lép fel:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Ennek az az oka, hogy bármely távoli T3-hozzáférés esetén az Oracle WebLogic Server a terheléselosztó portját és a WebLogic felügyelt kiszolgálóportját is változatlannak várja. Az előző esetben az ügyfél a 7006-os porthoz (a terheléselosztó porthoz) fér hozzá, és a felügyelt kiszolgáló a 7008-as (a privát port) figyel. Ez a korlátozás csak a T3-hozzáférésre vonatkozik, a HTTP-re nem.

   A probléma elkerülése érdekében használja az alábbi kerülő megoldások egyikét:

  * Használja ugyanazokat a magán- és nyilvános portszámokat a T3-hozzáféréshez rendelt elosztási terhelésű végpontokhoz.
  * Az Oracle WebLogic Server indításakor adja meg a következő JVM paramétert:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

A cikkekkel kapcsolatos információkat a **KB 860340.1** cikkében <https://support.oracle.com>talál.

* **Dinamikus fürtözési és terheléselosztási korlátozások.** Tegyük fel, hogy egy dinamikus fürtöt szeretne használni az Oracle WebLogic Server ben, és egyetlen nyilvános terheléselosztásos végponton keresztül szeretné elérhetővé tenni az Azure-ban. Ez mindaddig elvégezhető, amíg rögzített portszámot használ minden egyes felügyelt kiszolgálóhoz (nem dinamikusan rendelve egy tartományból), és nem indít el több felügyelt kiszolgálót, mint amennyit a rendszergazda követ. Ez azt, hogy virtuális gépenként egynél több felügyelt kiszolgáló van). Ha a konfiguráció azt eredményezi, hogy több Oracle WebLogic kiszolgáló indul el, mint ahány virtuális gép van (azaz ha több Oracle WebLogic Server példány ugyanaz a virtuális gép), akkor az Oracle WebLogic kiszolgálók egynél több példánya nem köthető egy adott portszámhoz. A többi a virtuális gépen meghibásodik.

   Ha úgy konfigurálja a felügyeleti kiszolgálót, hogy automatikusan egyedi portszámokat rendeljen a felügyelt kiszolgálókhoz, akkor a terheléselosztás nem lehetséges, mert az Azure nem támogatja az egyetlen nyilvános portról több magánportra történő leképezést, ahogy az ehhez a konfigurációhoz szükséges lenne.
* **Az Oracle WebLogic Server több példánya egy virtuális gépen.** A központi telepítés követelményeitől függően érdemes lehet az Oracle WebLogic Server több példányát futtatni ugyanazon a virtuális gépen, ha a virtuális gép elég nagy. Egy közepes méretű virtuális gépen például, amely két magot tartalmaz, választhatja az Oracle WebLogic Server két példányának futtatását. Továbbra is javasoljuk azonban, hogy ne vezessen be egyetlen meghibásodási pontot az architektúrába, ami akkor lenne így, ha csak egy olyan virtuális gépet használna, amely az Oracle WebLogic Server több példányát futtatja. Legalább két virtuális gép használata jobb megközelítés lehet, és minden virtuális gép futtathatja az Oracle WebLogic Server több példányát. Az Oracle WebLogic Server minden példánya továbbra is ugyanannak a fürtnek a része lehet. Azonban jelenleg nem lehet használni az Azure-t az ilyen Oracle WebLogic Server-telepítések által ugyanazon a virtuális gépen belül elérhetővé tett végpontok terheléselosztására, mivel az Azure terheléselosztó megköveteli, hogy a terheléselosztással terhelt kiszolgálókat egyedi virtuális gépek között kell elosztani.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuális gép képek
* **JDK 6 és 7 legújabb frissítéseket.** Bár javasoljuk a Java legújabb, támogatott (jelenleg Java 8) verzióját, az Azure a JDK 6 és 7 lemezképeket is elérhetővé teszi. Ez olyan örökölt alkalmazásokhoz készült, amelyek még nem állnak készen a JDK 8-ra való frissítésre. Bár előfordulhat, hogy a korábbi JDK-lemezképek frissítései már nem érhetők el a nagyközönség számára, tekintettel a Microsoft és az Oracle közötti partnerségre, az Azure által biztosított JDK 6 és 7 lemezképek célja egy újabb, nem nyilvános frissítés, amelyet az Oracle általában csak az Oracle által támogatott ügyfelek egy kiválasztott csoportjának kínál. A JDK-képek új verziói idővel elérhetővé lesznek a JDK 6 és 7 frissített kiadásaival.

   A JDK 6-os és 7-es lemezképeken elérhető JDK-k, valamint az ezekből származó virtuális gépek és lemezképek csak az Azure-ban használhatók.
* **64 bites JDK.** Az Oracle WebLogic Server virtuálisgép-lemezképei és az Azure által biztosított Oracle JDK virtuálisgép-lemezképek a Windows Server és a JDK 64 bites verzióit tartalmazzák.

## <a name="next-steps"></a>További lépések
Most már áttekintést kap a Microsoft Azure virtuálisgép-lemezképein alapuló jelenlegi Oracle-megoldásokról. A következő lépés az első Oracle-adatbázis üzembe helyezése az Azure-ban.

> [!div class="nextstepaction"]
> [Oracle-adatbázis létrehozása az Azure-ban](oracle-database-quick-create.md)
