---
title: "Oracle-megoldások a Microsoft Azure |} Microsoft Docs"
description: "További tudnivalók a támogatott konfigurációk és korlátozások vonatkoznak a Microsoft Azure Oracle-megoldások."
services: virtual-machines-linux
documentationcenter: 
manager: timlt
author: rickstercdn
tags: azure-resource-management
ms.assetid: 5d71886b-463a-43ae-b61f-35c6fc9bae25
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 06/15/2017
ms.author: rclaus
ms.openlocfilehash: 9174f7c8d16ff311312980fbe4d35996ec7ac832
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="oracle-solutions-and-their-deployment-on-microsoft-azure"></a>Oracle-megoldások és a központi telepítést, a Microsoft Azure
Sikeresen szükséges, ez a cikk magában foglalja az adatokat a Microsoft Azure különböző Oracle megoldások telepítése. Ezek a megoldások az Azure piactéren Oracle által közzétett virtuálisgép-rendszerképek alapulnak. Jelenleg elérhető rendszerkép listájának lekéréséhez futtassa a következő parancsot:
```azurecli-interactive
az vm image list --publisher oracle -o table --all
```
6 – 16-2017 képek listájának frissítésétől a következők:
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

Ezek a lemezképek minősülnek "A saját licenc", és így csak kell fizetnie a számítási, tárolási és hálózati költségeit egy virtuális Gépet futtató.  Feltételezzük, hogy megfelelően jogosult Oracle szoftverek és, hogy rendelkezik-e egy jelenlegi támogatási megállapodás helyen Oracle-adatbázissal. Oracle garanciát licenchordozhatósági a helyszíni Azure-bA. Tekintse meg a közzétett [Oracle és a Microsoft](http://www.oracle.com/technetwork/topics/cloud/faq-1963009.html) Megjegyzés licenchordozhatósági leírását. 

Egyéni felhasználók számára is választhatja, hogy a megoldás alapjául egyéni képek létrehozása az Azure-ban, vagy egy egyéni képek azok a helyszíni környezetből feltöltése.

## <a name="support-for-jd-edwards"></a>JD Edwards támogatása
Oracle támogatási Megjegyzés [Doc azonosító 2178595.1](https://support.oracle.com/epmos/faces/DocumentDisplay?_afrLoop=573435677515785&id=2178595.1&_afrWindowMode=0&_adf.ctrl-state=o852dw7d_4) , JD Edwards EnterpriseOne verions 9.2 és újabb verziók támogatottak **bármely nyilvánosfelhő-ajánlat** , amely megfelel saját konkrét `Minimum Technical Requirements` (MTR).  Szüksége lesz, amelyek megfelelnek az operációs rendszer és a szoftver alkalmazás kompatibilitási MTR specifikációk egyéni lemezképek létrehozásához. 

## <a name="oracle-database-virtual-machine-images"></a>Oracle Database virtuálisgép-rendszerképek
Oracle az Azure-ban futó Oracle DB 12.1 Standard és Enterprise kiadás támogatja az Oracle Linux-alapú virtuálisgép-lemezképeket.  A legjobb teljesítmény érdekében a termelési számítási feladatokhoz Oracle-adatbázis az Azure-on ügyeljen arra, hogy megfelelően a VM-lemezkép mérete és a prémium szintű Storage üzemelnek felügyelt lemezeket használni. A gyors az Oracle-DB, amelyekből megismerheti az Azure-ban az Oracle útmutatást közzétett Virtuálisgép-lemezkép [próbálja az Oracle-adatbázis gyors üzembe helyezési forgatókönyv](oracle-database-quick-create.md).

### <a name="attached-disk-configuration-options"></a>Csatlakoztatott lemez konfigurációs beállítások

Csatlakoztatott lemezek támaszkodnak az Azure Blob storage szolgáltatásban. Minden egyes szabványos lemez képes elméleti legfeljebb körülbelül 500 bemeneti/kimeneti műveletek száma másodpercenként (IOPS). A prémium szintű lemez ajánlat részesíti előnyben a nagy teljesítményű adatbázis számítási feladatait, és legfeljebb 5000 iops-érték lemezenként érhető el. Használhat egyetlen lemezre, amennyiben, amely megfelel a teljesítményigény - javítható a hatékony IOPS teljesítménye több csatlakoztatott lemezek használatakor, amíg adatbázis adatok elosztva, és Oracle automatikus Storage kezelési (ASM) használja. Lásd: [Oracle automatikus tárolás – áttekintés](http://www.oracle.com/technetwork/database/index-100339.html) Oracle ASM adott olvashat. A példa bemutatja, hogyan telepítése és konfigurálása a címterület-kezelés Oracle Linux Azure virtuálisgép - próbálja meg a [telepítése és konfigurálása Oracle automatikus tárhely-kezelés](configure-oracle-asm.md) oktatóanyag.

### <a name="oracle-realtime-application-cluster-rac"></a>Oracle valós idejű alkalmazás fürt (RAC)
Oracle jogosultságifiók-tanúsítványok célja, hogy egy helyszíni több csomópontos fürt konfigurációjába egyetlen csomópont hibája mérsékelni.  Két helyszíni technológiákat, amelyek nem natív módon kapacitású nyilvános felhő környezeteiben támaszkodnak: hálózati csoportos küldésre és a megosztott lemez. Nincsenek a vállalatok által létrehozott külső megoldások [FlashGrid például](https://www.flashgrid.io/oracle-rac-in-azure/) , hogy ezek a technológiák emulációjához, ha kell telepítenie az Oracle RAC az Azure-ban. 

### <a name="high-availability-and-disaster-recovery-considerations"></a>Magas rendelkezésre állási és vészhelyreállítási kapcsolatos szempontok
Oracle-adatbázis használata az Azure-ban, telepítésért felelős, magas rendelkezésre állási és vészhelyreállítási helyreállítási megoldások megvalósításának állásidő elkerülése érdekében. 

Oracle Database Enterprise Edition (RAC) nélkül Azure magas rendelkezésre állási és vészhelyreállítási helyreállítási használatával megvalósítható [Data Guard, aktív Data Guard](http://www.oracle.com/technetwork/articles/oem/dataguardoverview-083155.html), vagy [Oracle Golden kapu](http://www.oracle.com/technetwork/middleware/goldengate), két adatbázisok két különálló virtuális gép. Mindkét virtuális gép kell lennie ugyanazon [virtuális hálózati](https://azure.microsoft.com/documentation/services/virtual-network/) annak érdekében, hogy azok képesek elérni egymást keresztül a magánhálózati állandó IP-címet.  Ajánlott továbbá helyezi el a virtuális gépek ugyanabban a rendelkezésre állási Azure elhelyezése különálló tartalék tartományok és frissítési tartományt engedélyezi.  Azt szeretné, hogy szeretné, hogy georedundancia - akkor is a két adatbázis között két különböző régiókban replikálja, és csatlakozzon a két példányt VPN-átjáró.

Oktatóanyag tudunk "[megvalósítása Oracle DataGuard Azure](configure-oracle-dataguard.md)" amely bemutatja, hogyan az alapvető telepítési eljárás vizsgálattal Ez az Azure-on.  

Az Oracle Data Guard, magas rendelkezésre állású érhető el egy virtuális gép, egy másik virtuális gép, egy másodlagos (készenléti) adatbázis elsődleges adatbázis és a közöttük beállítása egyirányú replikációs. Olvasási hozzáférés az adatbázis másolata jön létre. Az Oracle GoldenGate beállíthatja a két adatbázis között kétirányú replikációt. Az ezekkel az eszközökkel adatbázisok esetében a magas rendelkezésre állású megoldás beállításával kapcsolatban a [aktív Data Guard](http://www.oracle.com/technetwork/database/features/availability/data-guard-documentation-152848.html) és [GoldenGate](http://docs.oracle.com/goldengate/1212/gg-winux/index.html) dokumentációban, az Oracle-webhelyet. Ha meg kell olvasási és írási hozzáférés az adatbázis másolatához való, [Oracle aktív Data Guard](http://www.oracle.com/uk/products/database/options/active-data-guard/overview/index.html).

Oktatóanyag tudunk "[megvalósítása Oracle GoldenGate Azure](configure-oracle-golden-gate.md)" amely bemutatja, hogyan az alapvető seup eljárás vizsgálattal Ez az Azure-on.

Annak ellenére, hogy egy magas rendelkezésre ÁLLÁSÚ és vész-Helyreállítási megoldás, az Azure-ban tervezett, érdemes ellenőrizze, hogy egy biztonsági mentési stratégia, hogy az adatbázis visszaállításához.  Oktatóanyag tudunk [biztonsági mentés és helyreállítás, Oracle-adatbázishoz](oracle-backup-recovery.md) amely bemutatja, hogyan az alapvető eljárás consistant biztonsági másolat létrehozásához.

## <a name="oracle-weblogic-server-virtual-machine-images"></a>Oracle WebLogic Server virtuálisgép-rendszerképek
* **Fürtszolgáltatás támogatott Enterprise Edition csak.** WebLogic fürtszolgáltatás csak az Enterprise Edition az WebLogic Server használata esetén használandó licencét. Ne használjon WebLogic Server Standard Edition fürtszolgáltatásra.
* **UDP csoportos küldés nem támogatott.** Azure támogatja az UDP történő, de nem csoportos vagy szórásos küldéshez. WebLogic-kiszolgáló nem hivatkozhatnak Azure UDP egyedi képességeit. A legjobb eredmények támaszkodva az UDP egyedi küldést, azt javasoljuk, hogy a WebLogic fürtméret statikus, tárolni vagy tartalmazza a fürt legfeljebb 10 felügyelt kiszolgálókkal kell tartani.
* **WebLogic Server vár nyilvános vagy privát portokkal T3 access (például vállalati JavaBeans használatakor) azonosnak kell lennie.** Fontolja meg egy többrétegű forgatókönyv egy szolgáltatási réteg (EJB) alkalmazás futtató két vagy több virtuális gép, egy nevű Vnetet álló WebLogic kiszolgálófürtre **SLWLS**. Az ügyfél réteg van egy másik alhálózat ugyanazon virtuális, egyszerű Java programot EJB hívása a szolgáltatási rétegben. Mivel terheléselosztásához a szolgáltatási rétegben, egy nyilvános elosztott terhelésű végpont kell létrehozni a virtuális gépek WebLogic kiszolgálófürt. Ha a magánhálózati port megadott eltér a nyilvános port (például 7006:7008), például a következő hiba akkor fordul elő:

       [java] javax.naming.CommunicationException [Root exception is java.net.ConnectException: t3://example.cloudapp.net:7006:

       Bootstrap to: example.cloudapp.net/138.91.142.178:7006' over: 't3' got an error or timed out]

   Ennek az az oka T3 távelérési WebLogic Server vár a load balancer port és a felügyelt WebLogic server port azonosnak kell lennie. A fenti esetben az ügyfél port (a betöltési terheléselosztó port) 7006 fér hozzá, és a felügyelt kiszolgáló figyel a következőn 7008 (a magánhálózati port). Ez a korlátozás esetén csak T3 hozzáférés, HTTP-nem alkalmazható.

   A probléma elkerülése érdekében a következő kerülő megoldások valamelyikével:

  * Azonos számú privát és nyilvános portot használja a dedikált T3 hozzáférés elosztott terhelésű végpont.
  * Adja meg a következő JVM értékét WebLogic Server indításakor:

         -Dweblogic.rjvm.enableprotocolswitch=true

Kapcsolódó tudnivalókért lásd a Tudásbázis következő cikkét **860340.1** : <http://support.oracle.com>.

* **Dinamikus fürtszolgáltatás és terheléselosztás korlátozások.** Tegyük fel, hogy WebLogic Server dinamikus-fürtöt használ, és azt egy egyetlen, nyilvános elosztott terhelésű végpont az Azure-ban elérhetővé. Ehhez mindaddig, amíg egy rögzített portszámot használni a felügyelt kiszolgálókra (egy tartomány nem dinamikusan hozzárendelt), és nem indulnak el, mint a rendszergazda nyomon követéséhez gép több kezelt kiszolgálók (Ez azt jelenti, hogy legfeljebb egy felügyelt virtuális m-kiszolgálókhoz achine). Ha a konfiguráció további WebLogic kiszolgálók indítását, mint a virtuális gép (Ez azt jelenti, amelyben több WebLogic Server-példány osztozik az azonos virtuális gép), akkor nincs lehetőség több WebLogic Server logikailemez nem lehet kötést létrehozni a megadott port száma – a többi virtuális gép által a kiszolgálók.

   Másrészről Ha konfigurál a felügyeleti kiszolgáló egyedi portszámot automatikus hozzárendelése a felügyelt kiszolgálókon, majd terheléselosztás nincs lehetőség Azure nem támogatja a több magánhálózati portokra, egyetlen nyilvános port-leképezés lenne szükség Ehhez a konfigurációhoz.
* **A virtuális gépen Weblogic Server több példánya.** Attól függően, hogy a telepítési követelményei érdemes lehet WebLogic Server több példánya futtassák az azonos virtuális gépen, ha a virtuális gép elég nagy. Például egy közepes méretű virtuális gépen, két magok tartalmazó sikerült választja WebLogic Server két példánya. Ne feledje azonban, hogy mégis azt javasoljuk, hogy elkerülheti a hibaérzékeny pontokat kíván bevezetni a architektúra, amely vonatkozik arra az esetre, ha csak egy virtuális gép, amelyen WebLogic Server több példánya fut.. Legalább két virtuális gépeknek jobb megközelítés lehet, és azon virtuális gépek mindegyikének futtatására WebLogic Server több példánya. Minden esetben WebLogic kiszolgáló továbbra is lehet ugyanazon fürt része. Vegye figyelembe, azonban jelenleg nem lehetséges a Azure által ilyen WebLogic Server környezetekben belül ugyanaz a virtuális gép terheléselosztásához végpontok, mert az Azure terheléselosztó szükséges az elosztott terhelésű kiszolgálókat elosztható egyedi virtuális gépek.

## <a name="oracle-jdk-virtual-machine-images"></a>Oracle JDK virtuálisgép-rendszerképek
* **JDK 6 és 7 legújabb frissítéseit.** Azt javasoljuk, hogy a legújabb nyilvános, támogatott Java (jelenleg a 8 Java) verzióját használja, amíg Azure is elérhetővé teszi JDK 6 és 7 képek. Ennek célja, amelyek nem frissíthető a JDK 8 készen örökölt alkalmazások számára. Amíg az előző JDK képek frissítéseit nem lehet elérhető a nagyközönség, Oracle, a Microsoft számos megadott a JDK 6 és 7 képek Azure által biztosított célja, hogy az Oracle általában ajánlott későbbi nem nyilvános frissítés tartalmazhat csak egy csoportjának Oracle által támogatott ügyfelek. A JDK-lemezképeket az új verzióit lesz elérhető az JDK 6 és 7 frissített kiadásainak idővel.

   A JDK 6 és 7 lemezképeket, és a virtuális gépek és származik, képek JDK csak Azure-ban használható.
* **64 bites JDK.** Oracle WebLogic Server virtuálisgép-lemezképeket és az Azure által biztosított Oracle JDK virtuálisgép-lemezképeket tartalmaz a Windows Server és a JDK 64 bites verziói.

## <a name="next-steps"></a>Következő lépések
Most már rendelkezik aktuális Oracle megoldások áttekintése a Microsoft Azure-on. A következő lépés, és nyissa meg az első Oracle-adatbázishoz az Azure-on.
- Próbálja meg a [az Oracle-adatbázis létrehozása az Azure](oracle-database-quick-create.md) oktatóanyag első lépéseiben.