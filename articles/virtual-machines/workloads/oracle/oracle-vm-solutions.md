---
title: Oracle-megoldások az Azure Virtual Machines szolgáltatásban | Microsoft Docs
description: Ismerkedjen meg az Oracle virtuálisgép-rendszerképek támogatott konfigurációinak és korlátaival Microsoft Azureon.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 4480819a08ef9a7a4ad7257f75a94c5d10a3d312
ms.sourcegitcommit: 65131f6188a02efe1704d92f0fd473b21c760d08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2019
ms.locfileid: "70858574"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle VM-rendszerképek és azok üzembe helyezése Microsoft Azure

Ez a cikk az Oracle által az Azure piactéren közzétett virtuálisgép-rendszerképeken alapuló Oracle-megoldásokkal kapcsolatos információkat ismerteti. Ha az Oracle-alapú felhőalapú infrastruktúrával kapcsolatos felhőalapú alkalmazások megoldásait érdekli, tekintse meg az [Oracle Application solutions Microsoft Azure és az Oracle Cloud Infrastructure integrálását](oracle-oci-overview.md)ismertető témakört.

A jelenleg elérhető rendszerképek listájának lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

A május 2019-től kezdve az alábbi rendszerképek érhetők el:

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

Ezeket a lemezképeket "saját licenc használatára" számítjuk, ezért csak a virtuális gép futtatásával járó számítási, tárolási és hálózati költségekért kell fizetni.  Feltételezi, hogy Ön megfelelő licenccel rendelkezik az Oracle-szoftverek használatához, és az Oracle-vel meglévő támogatási szerződése van érvényben. Az Oracle garantálta a helyszíni Azure-ba történő licenc-mobilitást. A licencek mobilitásával kapcsolatos részletekért tekintse meg a közzétett [Oracle-és Microsoft-](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) megjegyzést. 

A felhasználók dönthetnek úgy is, hogy a megoldásaikat az Azure-ban létrehozott egyéni rendszerképre alapozzák, vagy egyéni rendszerképet töltenek fel a helyszíni környezetből.

## <a name="oracle-database-vm-images"></a>Oracle Database VM-rendszerképek
Az Oracle támogatja a Oracle DB 12,1-es és újabb standard és Enterprise kiadásait az Azure-ban Oracle Linux-alapú virtuálisgép-rendszerképeken.  Az Azure-beli Oracle DB éles számítási feladataihoz a legjobb teljesítmény érdekében ügyeljen arra, hogy a virtuális gép rendszerképét megfelelően méretezze, és használja prémium SSD vagy ultra SSD Managed Disks. Az Azure-beli közzétett virtuálisgép-rendszerkép használatával gyorsan lekérhet egy Oracle DBt az Azure-ban, és megtekintheti [Oracle db a](oracle-database-quick-create.md)gyors üzembe helyezési útmutatót.

### <a name="attached-disk-configuration-options"></a>Csatolt lemez konfigurációs beállításai

A csatolt lemezek az Azure Blob Storage szolgáltatásra támaszkodnak. Minden standard lemez másodpercenként körülbelül 500 bemeneti/kimeneti műveletből áll (IOPS). A prémium szintű lemezes ajánlat nagy teljesítményű adatbázis-számítási feladatokhoz ajánlott, és lemezenként akár 5000 IOps-t is elérhet. Ha megfelel a teljesítmény igényeinek, egyetlen lemezt is használhat. Ha azonban több csatlakoztatott lemezt is használ, akkor javíthatja a hatékony IOPS teljesítményt, az adatbázis-adathalmazokat, majd az Oracle automatikus tárterület-kezelést (ASM) használhatja. További Oracle ASM-specifikus információk: az [Oracle automatikus tárolásának áttekintése](https://www.oracle.com/technetwork/database/index-100339.html) . Az Oracle ASM Linux Azure-beli virtuális gépen történő telepítésével és konfigurálásával kapcsolatos példát a következő témakörben talál: [Oracle automatizált tároló-kezelési oktatóanyag telepítése és konfigurálása](configure-oracle-asm.md) .

### <a name="shared-storage-configuration-options"></a>Megosztott tároló konfigurációs beállításai

Azure NetApp Files úgy lett kialakítva, hogy megfeleljenek a nagy teljesítményű számítási feladatok, például az adatbázisok a felhőben való futtatásának alapvető követelményeinek, és biztosítson;
- Azure natív megosztott NFS Storage szolgáltatás Oracle-alapú számítási feladatok futtatásához virtuális gépek natív NFS-ügyfelén vagy Oracle dNFS
- Méretezhető teljesítményszint, amely a IOPS-igények valós tartományát tükrözi
- Közel valós idejű adatelérés
- Magas rendelkezésre állás, magas tartósság és kezelhetőség nagy méretekben, jellemzően a kritikus fontosságú vállalati munkaterhelések (például az SAP és az Oracle) iránt
- Gyors és hatékony biztonsági mentés és helyreállítás, a legagresszívebb RTO és RPO SLA-k elérése érdekében

Ezek a képességek azért lehetségesek, mert a Azure NetApp Files a NetApp® ONTAP® az Azure-adatközpont-környezetben futó összes Flash-rendszerre épül, Azure-beli natív szolgáltatásként. Az eredmény egy ideális adatbázis-tárolási technológia, amely ugyanúgy kiépíthető és felhasználható, mint a többi Azure Storage-lehetőség. Az Azure NetApp Files NFS-kötetek üzembe helyezésével és elérésével kapcsolatos további információkért tekintse meg [Azure NetApp Files dokumentációját](https://docs.microsoft.com/azure/azure-netapp-files/) . Tekintse meg az [Oracle az Azure-beli üzembe helyezéssel kapcsolatos ajánlott eljárásokat ismertető útmutatót](https://www.netapp.com/us/media/tr-4780.pdf) , amely az Oracle-adatbázisok Azure NetApp Files-on való üzemeltetésére vonatkozó ajánlott eljárások Azure NetApp Files


## <a name="licensing-oracle-database--software-on-azure"></a>Licencelés Oracle Database & szoftver az Azure-ban
Microsoft Azure a Oracle Database futtatására szolgáló, jóváhagyott felhőalapú környezet. Az Oracle Core Factor tábla nem alkalmazható, ha Oracle-adatbázisokat engedélyez a felhőben. Ehelyett ha olyan virtuális gépeket használ, amelyeken engedélyezve van a Hyper-Threading technológia a nagyvállalati kiadású adatbázisokhoz, a két vCPU egy Oracle Processor-licenccel egyenértékűnek számít, ha engedélyezve van a feleznie (a szabályzat dokumentuma szerint). [Itt](http://www.oracle.com/us/corporate/pricing/cloud-licensing-070579.pdf)megtalálja a szabályzat részleteit.
Az Oracle-adatbázisok általában nagyobb memóriát és IO-t igényelnek. Emiatt a memória- [optimalizált virtuális gépek](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/sizes-memory) ajánlottak ezekhez a számítási feladatokhoz. A számítási feladatok további optimalizálása érdekében [korlátozott alapszintű vCPU](https://docs.microsoft.com/en-us/azure/virtual-machines/linux/constrained-vcpu) ajánlott olyan Oracle db munkaterhelések esetén, amelyek nagy memóriát, tárterületet és I/O-sávszélességet igényelnek, de nem magas alapszám.

Az Oracle-szoftverek és-munkaterhelések helyi rendszerről Microsoft Azureba történő áttelepítésekor az Oracle a licencek mobilitását az Azure-beli [Oracle – gyakori kérdések](https://www.oracle.com/cloud/technologies/oracle-azure-faq.html) című szakaszban leírtak szerint


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle Real Application-fürt (Oracle RAC)
Az Oracle RAC úgy lett kialakítva, hogy csökkentse az egyetlen csomópont meghibásodását egy helyszíni, többcsomópontos fürt konfigurációjában. A szolgáltatás két helyszíni technológiára támaszkodik, amelyek nem natívak a Hyper-Scale Cloud Environment-környezetek számára: a hálózat több-Cast és a megosztott lemez. Ha az adatbázis-megoldáshoz Oracle RAC szükséges az Azure-ban, akkor ezeket a technológiákat harmadik = fél szoftverrel kell engedélyeznie. Az Oracle RAC-ról további információt a [FlashGrid SkyCluster oldalon](https://www.flashgrid.io/oracle-rac-in-azure/)talál.

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állás és vész-helyreállítási megfontolások
Ha Oracle-adatbázisokat használ az Azure-ban, az állásidő elkerülése érdekében a magas rendelkezésre állású és vész-helyreállítási megoldás végrehajtásáért felelős. 

A magas rendelkezésre állás és a vész-helyreállítás a Oracle Database Enterprise Edition számára (az Oracle RAC-ra való támaszkodás nélkül) az Azure-ban az [Adatvédelem, az aktív adatvédelem](https://www.oracle.com/database/technologies/high-availability/dataguard.html)vagy az [Oracle-GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate)használatával érhető el, két adatbázissal két külön virtuális gépen gépek. Mindkét virtuális gépnek ugyanabban a [virtuális hálózatban](https://azure.microsoft.com/documentation/services/virtual-network/) kell lennie annak biztosításához, hogy a privát állandó IP-címen keresztül hozzáférhessenek egymáshoz.  Javasoljuk továbbá, hogy a virtuális gépeket ugyanazon rendelkezésre állási csoportba helyezze, hogy az Azure külön tartalék tartományba és frissítési tartományba helyezze őket. Ha szeretné, hogy a Geo-redundancia legyen, állítsa be a két adatbázist két különböző régió között, és kapcsolódjon a két példányhoz egy VPN Gateway.

Az Azure-beli [Oracle-adatvédelmet](configure-oracle-dataguard.md) bemutató oktatóanyag végigvezeti az Azure-beli alapszintű telepítési eljáráson.  

Az Oracle-adatvédelemmel a magas rendelkezésre állást egyetlen virtuális gép elsődleges adatbázisa, egy másodlagos (készenléti) adatbázis egy másik virtuális gépen, valamint a közöttük zajló egyirányú replikáció is lehetővé teheti. Az eredmény olvasási hozzáférés az adatbázis másolatához. Az Oracle GoldenGate használatával kétirányú replikációt konfigurálhat a két adatbázis között. Ha meg szeretné tudni, hogyan állíthat be magas rendelkezésre állású megoldást az adatbázisaihoz ezekkel az eszközökkel, tekintse meg az Oracle webhelyén az [aktív adatvédelmet](https://www.oracle.com/database/technologies/high-availability/dataguard.html) és a [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentációját. Ha írási-olvasási hozzáféréssel kell rendelkeznie az adatbázis másolatához, használhatja az [Oracle aktív adatvédelmet](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Az Azure-beli [Oracle GoldenGate üzembe](configure-oracle-golden-gate.md) helyezése az Azure-beli alapszintű telepítési eljárás végigvezeti.

Az Azure-ban létrehozott HA-és DR-megoldás mellett egy biztonsági mentési stratégiával kell rendelkeznie az adatbázis visszaállításához. Az oktatóanyag [biztonsági mentése és helyreállítása egy Oracle Database](oracle-backup-recovery.md) végigvezeti a konzisztens biztonsági mentés létrehozásához szükséges alapvető eljáráson.


## <a name="support-for-jd-edwards"></a>A JD Edwards támogatása
Az Oracle támogatási megjegyzése szerint a 2178595,1-as [doc-azonosító](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), a JD Edwards EnterpriseOne 9,2-es és újabb verziói **minden olyan nyilvános Felhőbeli ajánlat** esetében támogatottak, amely megfelel az adott `Minimum Technical Requirements` (MTR) szolgáltatásnak.  Olyan egyéni rendszerképeket kell létrehoznia, amelyek megfelelnek az operációs rendszer és a szoftver alkalmazás kompatibilitásának MTR-specifikációinak. 


## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuálisgép-lemezképek

* **A fürtözés csak Enterprise Edition rendszeren támogatott.** A WebLogic-fürtözés csak akkor használható, ha a WebLogic Server Enterprise kiadását használja. Ne használjon fürtözést a WebLogic Server Standard Edition kiadásával.
* **Az UDP csoportos küldés nem támogatott.** Az Azure támogatja az UDP-címes küldést, de nem csoportos küldést vagy szórást. A WebLogic-kiszolgáló képes az Azure UDP egyedi küldési képességeinek kihasználására. Az UDP egyedi küldésre támaszkodó legjobb eredmények érdekében javasoljuk, hogy a WebLogic-fürt mérete statikus legyen, vagy ne legyen több, mint 10 felügyelt kiszolgáló.
* **A WebLogic-kiszolgáló nyilvános és magánhálózati portokat vár a T3-hozzáféréshez (például vállalati JavaBeans használatakor).** Vegyünk például egy többrétegű forgatókönyvet, amelyben a Service Layer (EJB) alkalmazás két vagy több virtuális gépet tartalmazó WebLogic Server-fürtön fut egy *SLWLS*nevű virtuális hálózatban. Az ügyfél szintje ugyanabban a virtuális hálózatban található egy másik alhálózatban, és egy egyszerű Java-program futtatásával próbálkozik a EJB meghívásával a szolgáltatási rétegben. Mivel a szolgáltatási réteg terheléselosztása szükséges, a WebLogic-kiszolgálófürt virtuális gépei számára létre kell hozni egy nyilvános terheléselosztási végpontot. Ha a megadott privát port eltér a nyilvános porttól (például 7006:7008), akkor a következő hiba történik:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Ennek az az oka, hogy bármely távoli T3-hozzáférés esetén a WebLogic-kiszolgáló a terheléselosztó portját és a WebLogic felügyelt kiszolgáló portját is megegyezőként várja. Az előző esetben az ügyfél eléri a 7006-as portot (a terheléselosztó portját), és a felügyelt kiszolgáló figyeli a 7008 (a magánhálózati portot). Ez a korlátozás csak a T3-hozzáférésre vonatkozik, HTTP-n keresztül.

   A probléma elkerüléséhez használja a következő megkerülő megoldások egyikét:

  * Ugyanazokat a magán-és nyilvános portszámokat használja a T3-hozzáféréshez dedikált elosztott terhelésű végpontokhoz.
  * Adja meg a következő JVM paramétert a WebLogic-kiszolgáló indításakor:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

A kapcsolódó információkról a TUDÁSBÁZISCIKK **860340,1** -es <https://support.oracle.com>cikkében olvashat.

* **Dinamikus fürtözési és terheléselosztási korlátozások.** Tegyük fel, hogy dinamikus fürtöt szeretne használni a WebLogic-kiszolgálón, és az Azure-ban egyetlen, nyilvános terheléselosztási végponton keresztül teszi közzé. Ezt akkor teheti meg, ha rögzített portszámot használ az egyes felügyelt kiszolgálókon (a tartományhoz nem dinamikusan van rendelve), és nem indít el több felügyelt kiszolgálót, mint a rendszergazda által nyomon követett gépek. A virtuális gépen nincs több felügyelt kiszolgáló. Ha a konfigurációban több WebLogic-kiszolgáló indul el, mint a virtuális gépek (azaz ha több WebLogic-kiszolgálópéldány ugyanazt a virtuális gépet használja), akkor a WebLogic-kiszolgálók több példánya nem lehetséges. kötés egy adott portszámhoz. Az adott virtuális gépen lévő többi nem sikerül.

   Ha úgy konfigurálja a felügyeleti kiszolgálót, hogy automatikusan rendeljen hozzá egyedi portszámokat a felügyelt kiszolgálókhoz, akkor a terheléselosztás nem lehetséges, mivel az Azure nem támogatja egyetlen nyilvános portról több privát portra való leképezést, mivel ehhez szükséges Configuration.
* **A WebLogic-kiszolgáló több példánya egy virtuális gépen.** A központi telepítés követelményeitől függően érdemes lehet több példányban futtatni a WebLogic-kiszolgálót ugyanazon a virtuális gépen, ha a virtuális gép elég nagy méretű. Például egy közepes méretű virtuális gépen, amely két magot tartalmaz, dönthet úgy, hogy a WebLogic-kiszolgáló két példányát futtatja. Azonban azt javasoljuk, hogy ne vezessen be egyetlen meghibásodási pontot az architektúrába, ami akkor fordulhat elő, ha csak egy olyan virtuális gépet használt, amely a WebLogic-kiszolgáló több példányát futtatja. Ha legalább két virtuális gépet használ, jobb megközelítés lehet, és minden egyes virtuális gép a WebLogic-kiszolgáló több példányát futtathatja. A WebLogic-kiszolgáló minden példánya továbbra is ugyanahhoz a fürthöz tartozik. Azonban jelenleg nem lehet az Azure-ban olyan terheléselosztási végpontokat használni, amelyeket az ilyen WebLogic-kiszolgálók ugyanazon a virtuális gépen tesznek elérhetővé, mert az Azure Load Balancer megköveteli, hogy a terheléselosztási kiszolgálók egyediek legyenek elosztva virtuális gépek.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuálisgép-rendszerképek
* **JDK 6 és 7 legújabb frissítések.** Habár javasoljuk, hogy a legújabb nyilvános, támogatott Java-verzió (jelenleg a Java 8) használatával az Azure a JDK 6 és 7 rendszerképeket is elérhetővé teszi. Ez olyan örökölt alkalmazások számára készült, amelyek még nem állnak készen a JDK 8 verzióra való frissítésre. Habár előfordulhat, hogy a korábbi JDK-lemezképek frissítései többé nem lesznek elérhetők az Oracle-hez készült Microsoft-partneri együttműködés miatt, az Azure által biztosított JDK 6 és 7 rendszerképeket úgy tervezték, hogy az Oracle által általában kínált újabb, nem nyilvános frissítést tartalmazza csak az Oracle támogatott ügyfeleinek kiválasztott csoportja. A JDK-lemezképek új verziói időben elérhetővé válnak a JDK 6 és 7-es verziójának frissített kiadásaival.

   A JDK 6 és 7 lemezképben elérhető JDK, valamint a belőlük származtatott virtuális gépek és képek csak az Azure-on belül használhatók.
* **64 bites JDK.** Az Oracle WebLogic Server virtuálisgép-lemezképek és az Azure által biztosított Oracle JDK virtuálisgép-lemezképek a Windows Server és a JDK 64 bites verzióit tartalmazzák.

## <a name="next-steps"></a>További lépések
Most áttekintheti az aktuális Oracle-megoldásokat a Microsoft Azure virtuális gépek rendszerképei alapján. A következő lépés az első Oracle-adatbázis üzembe helyezése az Azure-ban.

> [!div class="nextstepaction"]
> [Oracle-adatbázis létrehozása az Azure-ban](oracle-database-quick-create.md)
