---
title: A Microsoft Azure-ban Oracle-megoldások |} A Microsoft Docs
description: További információ a támogatott konfigurációk és korlátozások, a Microsoft Azure-ban Oracle-megoldásokról.
services: virtual-machines-linux
documentationcenter: ''
author: romitgirdhar
manager: jeconnoc
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 08/02/2018
ms.author: rogirdh
ms.custom: seodec18
ms.openlocfilehash: 945ba9b2ba4dbc22941ca6b105417f591f2dd837
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58012749"
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Oracle-megoldásokról és azok üzembe helyezését, a Microsoft Azure
Ez a cikk ismerteti a különféle, Microsoft Azure-on Oracle-megoldásokról sikeres telepítéséhez szükséges információkat. Ezek a megoldások az Azure piactéren elérhető Oracle által közzétett virtuálisgép-lemezképek alapul. Jelenleg az elérhető rendszerképek listájának lekéréséhez futtassa a következő parancsot:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
2017. 6-16-rendszerképek listája a következők:
```bash
Offer                   Publisher    Sku                     Urn                                                          Version
----------------------  -----------  ----------------------  -----------------------------------------------------------  -------------
Oracle-Database-Ee      Oracle       12.1.0.2                Oracle:Oracle-Database-Ee:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Database-Se      Oracle       12.1.0.2                Oracle:Oracle-Database-Se:12.1.0.2:12.1.20170202             12.1.20170202
Oracle-Linux            Oracle       6.4                     Oracle:Oracle-Linux:6.4:6.4.20141206                         6.4.20141206
Oracle-Linux            Oracle       6.7                     Oracle:Oracle-Linux:6.7:6.7.20161007                         6.7.20161007
Oracle-Linux            Oracle       6.8                     Oracle:Oracle-Linux:6.8:6.8.20161020                         6.8.20161020
Oracle-Linux            Oracle       6.9                     Oracle:Oracle-Linux:6.9:6.9.20170406                         6.9.20170406
Oracle-Linux            Oracle       7.0                     Oracle:Oracle-Linux:7.0:7.0.20141217                         7.0.20141217
Oracle-Linux            Oracle       7.2                     Oracle:Oracle-Linux:7.2:7.2.20161020                         7.2.20161020
Oracle-Linux            Oracle       7.3                     Oracle:Oracle-Linux:7.3:7.3.20170320                         7.3.20170320
Oracle-WebLogic-Server  Oracle       Oracle-WebLogic-Server  Oracle:Oracle-WebLogic-Server:Oracle-WebLogic-Server:12.1.2  12.1.2
```

Ezek a lemezképek tekinti a "Saját licenc", és mint ilyen, csak kell fizetni számítási, tárolási és hálózatkezelési költségei a futó virtuális gép.  Feltételezzük, hogy meg vannak megfelelően-licenccel rendelkező Oracle-szoftverek és, hogy már működik a az Oracle egy jelenlegi támogatási szerződés. Oracle garantálta licenchordozhatósági program a helyszínről az Azure-bA. Tekintse meg a közzétett [Oracle és a Microsoft](https://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Megjegyzés licenchordozhatósági részleteiért. 

Egyéni felhasználók számára is beállíthatja megoldásaikat alapuljon egyéni rendszerkép létrehozása az Azure-ban, vagy a saját a helyszíni környezetből egyéni kép feltöltéséhez.

## <a name="support-for-jd-edwards"></a>JD Edwards támogatása
Oracle-támogatási Megjegyzés [Doc azonosító 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , 9.2 JD Edwards EnterpriseOne verziók és az újabb támogatott **bármely nyilvánosfelhő-ajánlat** , amely megfelel a saját konkrét `Minimum Technical Requirements` (MTR).  Azok az operációs rendszer és szoftverek Alkalmazáskompatibilitás MTR előírásokat teljesítő egyéni rendszerképek létrehozásához szükséges. 

## <a name="oracle-database-virtual-machine-images"></a>Oracle Database virtuálisgép-lemezképek
Oracle az Azure-ban futó Oracle DB 12.1 Standard és Enterprise kiadás támogatja az Oracle Linux-alapú virtuálisgép-lemezképek.  A legjobb teljesítmény érdekében az éles számítási feladatokhoz, Oracle DB, az Azure-ban mindenképpen a Virtuálisgép-lemezkép mérete és a Premium Storage élvezik Managed Disks használatához. A való gyorsan megkezdheti az Oracle DB az Azure-ban az Oracle Virtuálisgép-lemezkép közzétett [próbálja meg az Oracle DB-Quickstart forgatókönyv](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Csatlakoztatott lemez konfigurációs beállítások

Csatlakoztatott lemezek az Azure Blob storage szolgáltatás támaszkodnak. Minden standard szintű lemezes elméleti legfeljebb körülbelül 500 bemeneti/kimeneti műveletek másodpercenként (IOPS) alkalmas állapotban. Prémium szintű lemez ajánlat részesíti előnyben, kiemelkedő teljesítményű adatbázisokat számítási feladatokhoz, és érhet el akár 5000 iops-érték lemezenként. Egyetlen használhatja, ha a teljesítmény igényeinek, amely – a hatékony IOPS-teljesítmény növelheti több csatlakoztatott lemezek használatakor, amíg adathalmazainak elosztva, és Oracle automatikus Storage szolgáltatásfelügyelet (ASM) használja. Lásd: [Oracle automatikus Storage áttekintése](https://www.oracle.com/technetwork/database/index-100339.html) Oracle ASM adott bővebben. Megpróbálhatja telepítéséről és a egy Linux rendszerű Azure virtuális gépen – Oracle ASM konfigurálása egy példát a [telepítése és az Oracle automatikus Tároláskezelés konfigurálása](configure-oracle-asm.md) oktatóanyag.

## <a name="oracle-real-application-cluster-oracle-rac"></a>Oracle alkalmazás valós fürt (Oracle RAC)
Oracle jogosultságifiók-tanúsítványok célja a több csomópontos fürt helyi konfigurációkban egyetlen csomópont a hiba elhárításához. Két helyszíni technológia, amely nem natív, rendkívül nagy kapacitású nyilvános felhő környezeteiben támaszkodik: hálózati csoportos küldéses és a megosztott lemez. Ha az adatbázis-megoldástól igényel Oracle jogosultságifiók-tanúsítványok az Azure-ban, 3. fél szoftverrel, hogy ezek a technológiák kell. Oracle jogosultságifiók-tanúsítványok további információkért tekintse meg a [FlashGrid megoldást lapon](https://www.flashgrid.io/oracle-rac-in-azure/).

## <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állás és vészhelyreállítás kapcsolatos szempontok
Az Azure-ban Oracle-adatbázisok használatakor Ön felelős végrehajtási egy magas rendelkezésre állási és vészhelyreállítási megoldás állásidő elkerülése érdekében. 

Magas rendelkezésre állás és vészhelyreállítás helyreállítási Oracle Database Enterprise Edition (anélkül, hogy az Oracle RAC) elérhető Azure-beli [Data Guard, aktív Data Guard](https://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), vagy [Oracle Golden kapu](https://www.oracle.com/technetwork/middleware/goldengate), a két adatbázis két különálló virtuális gépeken. Virtuális gépeket is lehet ugyanazon [virtuális hálózat](https://azure.microsoft.com/documentation/services/virtual-network/) annak érdekében, hogy azok képesek elérni egymást az állandó magánhálózati IP-címen keresztül.  Emellett javasoljuk, hogy a virtuális gépek elhelyezése az azonos rendelkezésre állási beállításai lehetővé teszik az Azure-bA helyezze azokat különálló tartalék tartományokban és frissítési tartományokba.  Meg szeretné engedélyezni, georedundanciával – a két adatbázis két különböző régiók közötti replikálására, és a két példány együttműködnek a VPN-átjáró is rendelkezhet.

Rendelkezünk egy oktatóanyag "[megvalósítása Oracle DataGuard az Azure-ban](configure-oracle-dataguard.md)", amely végigvezeti az alapszintű beállítás eljárás próbaverzió, ez az Azure-ban.  

Az Oracle Data Guard, magas rendelkezésre állás érhető el egy virtuális gép, egy másik virtuális gépen, egy másodlagos (készenléti) adatbázis elsődleges adatbázis és a közöttük beállítása egyirányú replikálás. Ez az adatbázis másolatát olvasási hozzáférést. Az Oracle GoldenGate konfigurálhatja úgy a kétirányú replikációt a két adatbázis között. Állítsa be a magas rendelkezésre állású megoldás az adatbázisok, ezek az eszközök kezelésével kapcsolatos információkért lásd: [aktív Data Guard](https://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) és [GoldenGate](https://docs.oracle.com/goldengate/1212/gg-winux/index.html) az Oracle-webhelyen található dokumentáció. Ha meg kell olvasási és írási hozzáférés, a másolatot készít az adatbázisról, [Oracle aktív Data Guard](https://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Rendelkezünk egy oktatóanyag "[megvalósítása Oracle GoldenGate az Azure-ban](configure-oracle-golden-gate.md)", amely végigvezeti az alapszintű beállítás eljárás próbaverzió, ez az Azure-ban.

Annak ellenére, hogy fel egy magas rendelkezésre ÁLLÁS és Vészhelyreállítás megoldás az Azure-ban lett tervezve, érdemes ellenőrizze, hogy egy biztonsági mentési stratégia helyen állíthatja vissza az adatbázist.  Rendelkezünk egy oktatóanyag [biztonsági mentése és helyreállítása az Oracle-adatbázis](oracle-backup-recovery.md) amely végigvezet a biztonsági másolat alkalmazáskonzisztens létesítése alapszintű eljárását.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuálisgép-lemezképek
* **Fürtszolgáltatás támogatott Enterprise kiadásában csak.** WebLogic fürtözés csak az Enterprise Edition a WebLogic Server használatakor használandó rendelkezik licenccel. Ne használja a WebLogic Server Standard Edition fürtszolgáltatás.
* **A csoportos UDP nem támogatott.** Az Azure támogatja az UDP Államokban, de nem csoportos vagy szórásos küldéshez. WebLogic Server számíthat a Azure UDP egyedi képességeit. A legjobb eredmények támaszkodva az UDP egyedi küldést, azt javasoljuk, hogy a WebLogic fürtméret tárolni statikus, és tartalmazza a fürt legfeljebb 10 felügyelt kiszolgálókkal kell tartani.
* **WebLogic Server (például vállalati JavaBeans használatakor) T3 hozzáférés azonosnak kell lennie a nyilvános és magánhálózati portokra vár.** Egy többrétegű forgatókönyvet, a szolgáltatási réteg (EJB) alkalmazás futtató két vagy több virtuális gépet, nevű virtuális hálózat álló WebLogic Server fürtön **SLWLS**. Az ügyfél szinten van egy másik alhálózatot ugyanabban a vnetben, egyszerű Java programokat EJB hívása a szolgáltatási rétegben. Terheléselosztás a szolgáltatási réteg szükség, mert egy nyilvános kiegyenlített terhelésű végpontot kell létrehozni a virtuális gépek, a WebLogic Server-fürt. Ha az Ön által megadott magánhálózati port különbözik a nyilvános portot (például 7006:7008), például a következő hiba jelenik meg:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Ennek az oka T3 távelérési WebLogic Server vár a terheléselosztó portja és a felügyelt WebLogic server port azonosnak kell lennie. Az előző esetben az ügyfél port 7006 (a terheléselosztó portja) fér hozzá, és a felügyelt kiszolgáló figyel 7008 (a magánhálózati port). Ez a korlátozás akkor alkalmazható, csak a T3 eléréséhez, nem HTTP.

   A probléma elkerülése érdekében a következő kerülő megoldások valamelyikével:

  * Elosztott terhelésű végpontok T3 hozzáférés dedikált azonos számú privát és nyilvános portot használni.
  * Az alábbi JVM-paraméter WebLogic Server indítása során a következők:

         -Dweblogic.rjvm.enableprotocolswitch=true

Kapcsolódó tudnivalókért lásd a Tudásbázis **860340.1** , <https://support.oracle.com>.

* **A dinamikus fürtözés és a terheléselosztási korlátozások.** Tegyük fel, a WebLogic Server dinamikus-fürtöt használ, és közzéteheti egyetlen, nyilvános kiegyenlített terhelésű végponton keresztül az Azure-ban. Ehhez használja egy rögzített portszámot az egyes felügyelt kiszolgálókhoz (egy tartomány nem dinamikusan kiosztott), és ne indítsa el a több felügyelt kiszolgálók, mint a virtuális gépek a rendszergazda nyomon követéséhez (vagyis legfeljebb egy felügyelt kiszolgálónként és virtuális m achine). Ha a konfigurációt eredményez további WebLogic-kiszolgálókat, mint a virtuális gép indítása folyamatban (azaz ahol több WebLogic Server-példány osztozik ugyanahhoz a virtuális géphez), akkor már nem lehetséges több ezekhez a példányokhoz, a WebLogic-kiszolgálók egy adott port száma – kötést létrehozni a többi virtuális gép által sikertelen.

   Ha konfigurál a felügyeleti kiszolgáló automatikusan hozzárendelni a felügyelt kiszolgálókra egyedi portszámot, majd terheléselosztás nem alkalmas, mert az Azure nem támogatja több magánhálózati port, egy nyilvános portot történő hozzárendelésével lenne szükséges a konfiguráció.
* **A virtuális gépen a Weblogic Server több példánya.** Az üzemelő példány követelményeitől függően érdemes WebLogic Server több példánya futtatása ugyanarra a virtuális gépre, ha a virtuális gép elég nagy. Például egy közepes méretű virtuális gépen, két magot tartalmazó dönthet WebLogic Server két példánya futtatásához. Ne feledje azonban, hogy mégis azt javasoljuk, hogy elkerülheti a hibaérzékeny pontokat kíván bevezetni a architektúra, amely vonatkozik arra az esetre, ha csak egy virtuális gép, amelyen WebLogic Server több példánya fut. Legalább két virtuális gép használatával jobb módszer lehet, és minden egyes virtuális gépek futtatására WebLogic Server több példánya. Mindegyik WebLogic Server példány továbbra is lehet ugyanazon fürt része. Vegye figyelembe, hogy azonban azt jelenleg nem lehetséges az Azure-terheléselosztás végpontok belül ugyanahhoz a virtuális géphez, ilyen WebLogic Server üzemelő példányok által feltárt, mert az Azure load balancer az elosztott terhelésű kiszolgálókat szétosztására egyedi igényel virtuális gépek.

## <a name="oracle-jdk-virtual-machine-images"></a>JDK-Oracle virtuálisgép-lemezképek
* **JDK 6 és 7 legújabb frissítéseit.** Javasoljuk, hogy a legújabb nyilvános, támogatott Java (aktuálisan Java 8) verzióját használja, amíg az Azure is elérhetővé teszi JDK 6 és 7 lemezképek. Ennek célja, hogy még nem áll készen, frissíteni kell a JDK 8 örökölt alkalmazások számára. Bár előfordulhat, hogy a frissítések előző JDK-lemezképek már nem érhető el nyilvános, az Oracle, a Microsoft partneri adott a JDK 6 és 7, Azure által biztosított rendszerképek célja egy újabb nem nyilvános frissítés általában az Oracle által nyújtott tartalmaz csak bizonyos csoportjaira Oracle ügyfél a támogatott. A JDK-lemezképek újabb verzióit lesz elérhető a JDK 6. és 7 frissített kiadásokban idővel.

   A JDK a JDK 6 és 7 képeket, és a virtuális gépek és képek származik, csak akkor használható, Azure-ban.
* **64-bit JDK.** Az Oracle WebLogic Server virtuálisgép-lemezképeket és az Azure által biztosított Oracle-JDK virtuálisgép-lemezképek tartalmazzák a Windows Server és a JDK 64 bites verzióit.

## <a name="next-steps"></a>További lépések
Most már aktuális Oracle megoldások áttekintése a Microsoft Azure-ban. A következő lépés, hogy az első Oracle Database, az Azure-ban telepíti.
- Próbálja ki a [Oracle-adatbázis létrehozása az Azure-ban](oracle-database-quick-create.md) oktatóanyag segítséget nyújt.
