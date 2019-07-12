---
title: Oracle-megoldások az Azure virtual machines |} A Microsoft Docs
description: További információ a támogatott konfigurációk és korlátozások, a Microsoft Azure-on Oracle virtuálisgép-lemezképek.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: gwallace
tags: azure-resource-management
ms.assetid: ''
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 05/23/2019
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 70e87a38373688c1b364a079cd07934309662e3e
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67707436"
---
# <a name="oracle-vm-images-and-their-deployment-on-microsoft-azure"></a>Oracle Virtuálisgép-rendszerképek és azok üzembe helyezését, a Microsoft Azure

Ez a cikk ismerteti az Oracle-megoldások az Azure piactéren elérhető Oracle által közzétett virtuálisgép-rendszerképek alapján kapcsolatos információkat. Ha érdekli a felhőbe irányuló kérelem Oracle Felhőbeli infrastruktúra-megoldások, [Oracle-alkalmazási megoldások integrálása a Microsoft Azure és az Oracle-felhő-infrastruktúra](oracle-oci-overview.md).

Jelenleg az elérhető rendszerképek listájának lekéréséhez futtassa a következő parancsot:

```azurecli-interactive
az vm image list --publisher oracle -o table --all
```

2019. május kezdődően a következő rendszerképek érhetők el:

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

Ezek a lemezképek tekinti a "Saját licenc", és mint ilyen, csak kell fizetni számítási, tárolási és hálózatkezelési költségei a futó virtuális gép.  Feltételezzük, hogy meg vannak megfelelően-licenccel rendelkező Oracle-szoftverek és, hogy már működik a az Oracle egy jelenlegi támogatási szerződés. Oracle garantálta licenchordozhatósági program a helyszínről az Azure-bA. Tekintse meg a közzétett [Oracle és a Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Megjegyzés licenchordozhatósági részleteiért. 

Egyéni felhasználók számára is beállíthatja megoldásaikat alapuljon egyéni rendszerkép létrehozása az Azure-ban, vagy a saját a helyszíni környezetből egyéni kép feltöltéséhez.

## <a name="support-for-jd-edwards"></a>JD Edwards támogatása
Oracle-támogatási Megjegyzés [Doc azonosító 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4), 9.2 JD Edwards EnterpriseOne verziók és az újabb támogatott **bármely nyilvánosfelhő-ajánlat** , amely megfelel a saját konkrét `Minimum Technical Requirements` (MTR).  Azok az operációs rendszer és szoftverek Alkalmazáskompatibilitás MTR előírásokat teljesítő egyéni rendszerképek létrehozásához szükséges. 

## <a name="oracle-database-vm-images"></a>Oracle database Virtuálisgép-rendszerképek
Oracle az Azure-ban futó Oracle DB 12.1 Standard és Enterprise kiadás támogatja az Oracle Linux-alapú virtuálisgép-lemezképek.  A legjobb teljesítmény érdekében az éles számítási feladatokhoz, Oracle DB, az Azure-ban mindenképpen a Virtuálisgép-lemezkép mérete és a Premium Storage élvezik Managed Disks használatához. A való gyorsan megkezdheti az Oracle DB az Azure-ban az Oracle Virtuálisgép-lemezkép közzétett [próbálja meg az Oracle DB-Quickstart forgatókönyv](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Csatlakoztatott lemez konfigurációs beállítások

Csatlakoztatott lemezek az Azure Blob storage szolgáltatás támaszkodnak. Minden standard szintű lemezes elméleti legfeljebb körülbelül 500 bemeneti/kimeneti műveletek másodpercenként (IOPS) alkalmas állapotban. Prémium szintű lemez ajánlat részesíti előnyben, kiemelkedő teljesítményű adatbázisokat számítási feladatokhoz, és érhet el akár 5000 iops-érték lemezenként. Egyetlen is használhatja, ha, amely megfelel a sorozatot. Azonban a hatékony IOPS-teljesítmény javításához, ha több csatlakoztatott lemezek használatát, adathalmazainak elosztva, és ezután használhatja az Oracle automatikus Storage szolgáltatásfelügyelet (ASM). Lásd: [Oracle automatikus Storage áttekintése](https://www.oracle.com/technetwork/database/index-100339.html) Oracle ASM adott bővebben. Telepítése és a egy Linux rendszerű Azure virtuális gépen Oracle ASM konfigurálása egy példa: a [telepítése és az Oracle automatikus Tároláskezelés konfigurálása](configure-oracle-asm.md) oktatóanyag.

### <a name="shared-storage-configuration-options"></a>Megosztott tároló konfigurációs beállítások

NetApp Azure Files úgy lett kialakítva, a core követelményeinek nagy teljesítményű számítási feladatokhoz, például adatbázisok a felhőben futó, és itt;
- Az Azure natív megosztott NFS tárolási szolgáltatás vagy Oracle-feladatok futtatásához a virtuális gép natív NFS-ügyfelet, vagy az Oracle dNFS
- A való életből vett IOPS tartomány igényeknek megfelelően méretezhető teljesítményszintek
- Közel valós idejű adatelérés
- Magas rendelkezésre állás, a nagyfokú tartósság és a nagy mennyiségű, az üzletmenet szempontjából kritikus fontosságú üzleti számítási feladatokhoz (például SAP és Oracle) által jellemzően igényelt kezelhetőség
- Gyors és hatékony biztonsági mentés és helyreállítás elérése érdekében a legtöbb agresszív RTO és RPO SLA

Ezeket a képességeket is lehetséges, mert NetApp fájlokat az Azure-alapú környezetben az Azure data center – natív Azure szolgáltatásként futtató NetApp® ONTAP® teljes mértékben flash rendszereken. Ez egy ideális adatbázis tárolási technológia, amely üzembe helyezhető és hasonlóan más az Azure tárolási lehetőségeinek felhasznált. Lásd: [Azure NetApp fájlok dokumentáció](https://docs.microsoft.com/azure/azure-netapp-files/) hogyan helyezheti üzembe és Azure NetApp fájlok NFS-kötetek eléréséhez további tájékoztatást. Lásd: [Oracle az Azure üzembe helyezési ajánlott eljárás az útmutató használatával Azure NetApp fájlokat](https://www.netapp.com/us/media/tr-4780.pdf) az Oracle-adatbázis, az Azure Files-NetApp működő kapcsolatos ajánlott eljárásokat mutatják.


## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle alkalmazás valós fürt (Oracle RAC)
Oracle jogosultságifiók-tanúsítványok célja a több csomópontos fürt helyi konfigurációkban egyetlen csomópont a hiba elhárításához. Két helyszíni technológia, amely nem natív, rendkívül nagy kapacitású nyilvános felhő környezeteiben támaszkodik: hálózati csoportos küldéses és a megosztott lemez. Ha az adatbázis-megoldástól igényel Oracle jogosultságifiók-tanúsítványok az Azure-ban, szüksége van-e külső gyártótól származó szoftverrel, hogy ezek a technológiák =. Az Oracle jogosultságifiók-tanúsítványok további információkért lásd: a [FlashGrid SkyCluster oldal](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állás és vészhelyreállítás kapcsolatos szempontok
Az Azure-ban Oracle-adatbázisok használatakor Ön felelős végrehajtási egy magas rendelkezésre állási és vészhelyreállítási megoldás állásidő elkerülése érdekében. 

Magas rendelkezésre állás és vészhelyreállítás helyreállítási Oracle Database Enterprise Edition (anélkül, hogy az Oracle RAC) elérhető Azure-beli [Data Guard, aktív Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), vagy [Oracle GoldenGate](https://www.oracle.com/technetwork/middleware/goldengate), a két adatbázis két különálló virtuális gépeken. Virtuális gépeket is lehet ugyanazon [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) annak érdekében, hogy azok képesek elérni egymást az állandó magánhálózati IP-címen keresztül.  Emellett javasoljuk, hogy a virtuális gépek elhelyezése az azonos rendelkezésre állási beállításai lehetővé teszik az Azure-bA helyezze azokat különálló tartalék tartományokban és frissítési tartományokba. Meg szeretné georedundancia rendelkezik, állítsa be a két adatbázis között két különböző régióban replikálja, és a két példányok összekapcsolása VPN-átjáró.

Az oktatóanyag [megvalósítása Oracle Data Guard az Azure-ban](configure-oracle-dataguard.md) végigvezeti azokon az alapvető beállítási eljárást az Azure-ban.  

Az Oracle Data Guard, magas rendelkezésre állás érhető el egy virtuális gép, egy másik virtuális gépen, egy másodlagos (készenléti) adatbázis elsődleges adatbázis és a közöttük beállítása egyirányú replikálás. Ez az adatbázis másolatát olvasási hozzáférést. Az Oracle GoldenGate konfigurálhatja úgy a kétirányú replikációt a két adatbázis között. Állítsa be a magas rendelkezésre állású megoldás az adatbázisok, ezek az eszközök kezelésével kapcsolatos információkért lásd: [aktív Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) és [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) az Oracle-webhelyen található dokumentáció. Ha meg kell olvasási és írási hozzáférés, a másolatot készít az adatbázisról, [Oracle aktív Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Az oktatóanyag [megvalósítása Oracle GoldenGate az Azure-ban](configure-oracle-golden-gate.md) végigvezeti azokon az alapvető beállítási eljárást az Azure-ban.

Mellett egy magas rendelkezésre ÁLLÁSÚ és Vészhelyreállítási megoldás lett tervezve, az Azure-ban, a biztonsági mentési stratégiájának helyen állíthatja vissza az adatbázist kell rendelkeznie. Az oktatóanyag [biztonsági mentése és helyreállítása az Oracle-adatbázis](oracle-backup-recovery.md) végigvezeti azokon az alapvető eljárás következetes biztonsági mentést.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuálisgép-lemezképek

* **Fürtszolgáltatás támogatott Enterprise kiadásában csak.** WebLogic fürtözés csak az Enterprise Edition a WebLogic Server használatakor használandó rendelkezik licenccel. Ne használja a WebLogic Server Standard Edition fürtszolgáltatás.
* **A csoportos UDP nem támogatott.** Az Azure támogatja az UDP Államokban, de nem csoportos vagy szórásos küldéshez. WebLogic Server számíthat a Azure UDP egyedi képességeit. A függő UDP egyedi küldést a legjobb eredmények érdekében azt javasoljuk, hogy a statikus vagy megtartott 10-nél több felügyelt kiszolgálókkal a WebLogic fürtméret tárolódik.
* **WebLogic Server (például vállalati JavaBeans használatakor) T3 hozzáférés azonosnak kell lennie a nyilvános és magánhálózati portokra vár.** Egy többrétegű forgatókönyvet, a szolgáltatási réteg (EJB) alkalmazás futtató két vagy több virtuális gépet, egy nevű virtuális hálózatot álló WebLogic Server fürtön *SLWLS*. Az ügyfél szintje az azonos virtuális hálózatba, egy egyszerű Java-program, amelyhez EJB hívja meg a szolgáltatási réteg fut egy másik alhálózat. Terheléselosztás a szolgáltatási réteg szükség, mert egy nyilvános kiegyenlített terhelésű végpontot kell létrehozni a virtuális gépek, a WebLogic Server-fürt. Ha az Ön által megadott magánhálózati port különbözik a nyilvános portot (például 7006:7008), például a következő hiba jelenik meg:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Ennek az oka T3 távelérési WebLogic Server vár a terheléselosztó portja és a felügyelt WebLogic server port azonosnak kell lennie. Az előző esetben az ügyfél port 7006 (a terheléselosztó portja) fér hozzá, és a felügyelt kiszolgáló figyel 7008 (a magánhálózati port). Ez a korlátozás akkor alkalmazható, csak a T3 eléréséhez, nem HTTP.

   A probléma elkerülése érdekében a következő kerülő megoldások valamelyikével:

  * Elosztott terhelésű végpontok T3 hozzáférés dedikált azonos számú privát és nyilvános portot használni.
  * Az alábbi JVM-paraméter WebLogic Server indítása során a következők:

    ```
    -Dweblogic.rjvm.enableprotocolswitch=true
    ```

Kapcsolódó tudnivalókért lásd a Tudásbázis **860340.1** , <https://support.oracle.com>.

* **A dinamikus fürtözés és a terheléselosztási korlátozások.** Tegyük fel, a WebLogic Server dinamikus-fürtöt használ, és közzéteheti egyetlen, nyilvános kiegyenlített terhelésű végponton keresztül az Azure-ban. Ezt megteheti, mindaddig, amíg használja egy rögzített portszámot az egyes felügyelt kiszolgálókhoz (egy tartomány nem dinamikusan kiosztott), és ne indítsa el a több felügyelt kiszolgálók, mint a virtuális gépek a rendszergazda nyomon követéséhez. Azt jelenti hogy nem virtuális gépenként legfeljebb egy felügyelt kiszolgáló). Ha a konfigurációt eredményez további WebLogic-kiszolgálókat, mint a virtuális gép indítása folyamatban (azaz ahol több WebLogic Server-példány osztozik ugyanahhoz a virtuális géphez), akkor már nem lehetséges több ezekhez a példányokhoz, a WebLogic-kiszolgálók kötést létrehozni egy adott port számát. A többi virtuális gép által sikertelen.

   Ha konfigurál a felügyeleti kiszolgáló automatikusan hozzárendelni a felügyelt kiszolgálókra egyedi portszámot, majd terheléselosztás nem alkalmas, mert az Azure nem támogatja több magánhálózati port, egy nyilvános portot történő hozzárendelésével lenne szükséges a konfiguráció.
* **A virtuális gépen a WebLogic Server több példánya.** Az üzemelő példány követelményeitől függően érdemes lehet WebLogic Server több példánya futó ugyanahhoz a virtuális géphez, ha a virtuális gép elég nagy. Például egy közepes méretű virtuális gépen, két magot tartalmazó dönthet WebLogic Server két példánya futtatásához. Azonban továbbra is ajánlott, kerülje a hibaérzékeny pontokat kíván bevezetni az architektúra, amely vonatkozik arra az esetre, ha csak egy virtuális gép, amelyen fut a WebLogic Server több példánya. Legalább két virtuális gép használatával jobb módszer lehet, és minden virtuális gép futtatására WebLogic Server több példánya. WebLogic Server mindegyik példányán is ugyanazon fürt része. Azonban ez jelenleg nem lehetséges az Azure-terheléselosztás végpontok belül ugyanahhoz a virtuális géphez, ilyen WebLogic Server üzemelő példányok által feltárt, mert az Azure load balancer az elosztott terhelésű kiszolgálókat szétosztására egyedi igényel virtuális gépek.

## <a name="oracle-jdk-virtual-machine-images"></a>JDK-Oracle virtuálisgép-lemezképek
* **JDK 6 és 7 legújabb frissítéseit.** Javasoljuk, hogy a legújabb nyilvános, támogatott Java (aktuálisan Java 8) verzióját használja, amíg az Azure is elérhetővé teszi JDK 6 és 7 lemezképek. Ennek célja, hogy még nem áll készen, frissíteni kell a JDK 8 örökölt alkalmazások számára. Bár előfordulhat, hogy a frissítések előző JDK-lemezképek már nem érhető el nyilvános, az Oracle, a Microsoft partneri adott a JDK 6 és 7, Azure által biztosított rendszerképek célja egy újabb nem nyilvános frissítés általában az Oracle által nyújtott tartalmaz csak bizonyos csoportjaira Oracle ügyfél a támogatott. A JDK-lemezképek újabb verzióit lesz elérhető a JDK 6. és 7 frissített kiadásokban idővel.

   A JDK a JDK 6 és 7 képeket, és a virtuális gépek és képek származik, csak akkor használható, Azure-ban.
* **64-bit JDK.** Az Oracle WebLogic Server virtuálisgép-lemezképeket és az Azure által biztosított Oracle-JDK virtuálisgép-lemezképek tartalmazzák a Windows Server és a JDK 64 bites verzióit.

## <a name="next-steps"></a>További lépések
Most már a Microsoft Azure virtuálisgép-rendszerképek alapján aktuális Oracle megoldások áttekintése. A következő lépés, hogy az első Oracle database, az Azure-ban telepíti.

> [!div class="nextstepaction"]
> [Oracle-adatbázis létrehozása az Azure-ban](oracle-database-quick-create.md)
