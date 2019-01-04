---
title: Cassandra-fürt futtatásához a linuxon futó Azure-ban node.js-sel
description: Cassandra-fürtjére futtatása az Azure Virtual machines gépeken Linux rendszeren a Node.js-alkalmazásból
services: virtual-machines-linux
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: ''
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: b38db71e624d32e7a4a532181a374edb13f13fbf
ms.sourcegitcommit: 25936232821e1e5a88843136044eb71e28911928
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/04/2019
ms.locfileid: "54021934"
---
# <a name="run-a-cassandra-cluster-on-linux-in-azure-with-nodejs"></a>Cassandra-fürt futtatásához az Azure-ban Node.js linuxon

> [!IMPORTANT]
> Az Azure az erőforrások létrehozásához és használatához két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk ismerteti a klasszikus üzemi modell használatával. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Tekintse meg a Resource Manager-sablonokkal [Datastax Enterprise](https://azure.microsoft.com/documentation/templates/datastax) és [a Spark-fürt és a Cassandra futtatása CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Áttekintés
Microsoft Azure egy nyílt felhőplatform, amely a Microsoft és nem Microsoft szoftvert is fut a. A szoftver tartalmazza az operációs rendszerek, alkalmazáskiszolgálók, üzenetkezelési közbenső szoftverek, valamint az SQL és a NoSQL-adatbázisok mindkét kereskedelmi és nyílt forráskódú modellből. A nyilvános felhők – köztük az Azure rugalmas szolgáltatások gondos tervezést és igényel szándékos architektúra mindkét alkalmazáskiszolgálók jól tárolási rétegek szerint. A Cassandra elosztott tároló-architektúra természetes módon segít a magas rendelkezésre állású, hibatűrő fürt hibák rendszereket. Cassandra a felhőbeli skálázással cassandra.apache.org, az Apache Software Foundation által kezelt NoSQL-adatbázis. Cassandra Java nyelven van megírva. Így futtatja a Windows és Linux platformokon is.

Ez a cikk célja, hogy megjelenítése a Cassandra üzembe helyezése az ubuntu rendszeren, amely használja az Azure Virtual Machines és a virtuális hálózatok egy vagy több data center fürtként. A fürt üzembe helyezésének optimalizált éles számítási feladatokra az ebben a cikkben hatókörén kívül esik, mert több lemez csomópont-konfiguráció megfelelő körgyűrűs topológia tervezési és támogatja a szükséges replikációs, az adatkonzisztencia, az átviteli sebesség, adatmodellezést igényel és magas rendelkezésre állással.

Ez a cikk egy alapvető megközelítés megjelenítéséhez a Cassandra-fürt, mint a korábban megszokott Docker, a Chef vagy Puppet épületben vesz részt vesz igénybe. Ez a megközelítés megkönnyítheti az infrastruktúra telepítése sokkal.

## <a name="the-deployment-models"></a>Az üzembe helyezési modellek
A Microsoft Azure-hálózatkezelés lehetővé teszi, hogy a központi telepítését, amelyek a hozzáférés korlátozható részletes hálózati biztonság megvalósítása érdekében elkülönített privát fürtök. Mivel ez a cikk a Cassandra üzembe helyezése alkalmazáskészítés megjelenítésével kapcsolatos, a konzisztencia szintjét, és az átviteli sebességet optimális tároló-kialakításában, nem koncentrálhat. A következő hálózati követelményei a elméleti fürt listáját:

* Külső rendszerek nem tudnak hozzáférni a Cassandra-adatbázis belül vagy kívül Azure
* Cassandra-fürtjére azt thrift-forgalom egy terheléselosztó mögött kell
* Két, minden adatközpontban, a továbbfejlesztett fürt rendelkezésre állási csoport Cassandra csomópontok üzembe helyezése
* Zárolását, így a fürt úgy, hogy csak az alkalmazás kiszolgálófarm hozzáféréssel rendelkezik az adatbázishoz közvetlenül
* Eltérő SSH nyilvános hálózati végpontok
* Cassandra-csomópontokon kell egy rögzített belső IP-cím

Cassandra telepíthető egy adott Azure-régióban, vagy több régióban a számítási feladatok elosztott jellege alapján. Többrégiós üzembe helyezési modell használatával szolgálja ki a végfelhasználók számára közelebb, hogy egy adott térségen Cassandra ugyanazon az infrastruktúrán keresztül. Cassandra a beépített csomópont replikációs veszi fel kell foglalkozni a szinkronizálást az több főkiszolgálós ír, több adatközpontot származó, és megadja az adatok az alkalmazások konzisztens nézetét. Többrégiós üzembe helyezés a kockázatcsökkentés a szélesebb körű támogatásban részesülnek az Azure szolgáltatás-kimaradások, az segíthet is. A Cassandra hangolható konzisztencia és a replikációs topológia segítségével az alkalmazások különböző RPO igényeinek kielégítésében.

### <a name="single-region-deployment"></a>Egyetlen régióban történő üzembe helyezés
Most egy egyetlen régióban történő üzembe helyezés kezdődhet, és gyűjtsön a tapasztalatainkat többrégiós modell létrehozásához. Az Azure virtuális hálózatok segítségével elkülönített alhálózatok létrehozására, hogy a fent említett hálózati biztonsági követelmények is kielégíthetők. Az egyetlen régióban történő üzembe helyezés létrehozása ismertetett folyamatot Ubuntu 14.04 LTS és a Cassandra 2.08 használja. Azonban a folyamat is könnyen elfogadni Linux variantní hodnoty. Az alábbiakban néhány szisztematikus módszert, az egyetlen régióban történő üzembe helyezés.

**Magas rendelkezésre állás:** A Cassandra-csomópontokat az 1. ábráján látható két rendelkezésre állási csoportot telepített, hogy a csomópontokon futó magas rendelkezésre állás érdekében több tartalék tartomány között. Az egyes rendelkezésre állási feliratozva virtuális gépek 2 hibatűrési tartományt van leképezve. Az Azure a tartalék tartomány fogalmát kezelheti a nem tervezett leállás (például a hardver- vagy hibák) használja. A frissítési tartomány (például gazdagép vagy vendég operációs rendszer javítási/frissítése, alkalmazásfrissítések) fogalmát ütemezett üzemszünet kezelésére szolgál. Lásd: [vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások](https://msdn.microsoft.com/library/dn251004.aspx) a szerepkör a hibatűrési és frissítési tartományok magas rendelkezésre állás eléréséhez.

![Egyetlen régióban történő üzembe helyezés](./media/cassandra-nodejs/cassandra-linux1.png)

1. ábra: Egyetlen régióban történő üzembe helyezés

Vegye figyelembe, hogy a cikk írásának időpontjában Azure nem engedélyezi egy adott tartalék tartomány; a virtuális gépek csoportjai explicit leképezése Ennek következtében akár az üzemi modellel 1. ábráján látható, valószínű statisztikailag, hogy a virtuális gépek négy helyett két tartalék tartomány lehet rendelni.

**Terheléselosztás Thrift-forgalmat:** A webkiszolgáló belül Thrift-ügyfélkódtárak csatlakozzon a fürthöz egy belső terheléselosztón keresztül. Ehhez szükséges, hogy a belső terheléselosztó hozzáadása az "adatok" alhálózathoz (lásd az 1. ábra) a Cassandra-fürt üzemeltető felhőszolgáltatás keretében. Miután a belső terheléselosztó van definiálva, minden csomópont szükséges az elosztott terhelésű végpontot hozzá kell a megjegyzések, elosztott terhelésű készlet a korábban meghatározott terheléselosztó neve. Lásd: [Azure Internal Load Balancing ](../../../load-balancer/load-balancer-internal-overview.md)további részletekért.

**Fürt magok:** Fontos, az új csomópontok kommunikálni magcsomópontok feltérképezi a fürt magok, a magas rendelkezésre állású legtöbb csomópont jelöli. Egyes rendelkezésre állási csoport egy csomópont magcsomópontok kijelölt rendszerkritikus meghibásodási pontot elkerülése érdekében.

**Replikációs tényező és a Konzisztenciaszint:** A replikációs tényező (RF – minden sorban a fürtön tárolt adatmásolatok száma) az egyes Cassandra a beépített magas rendelkezésre állású és az adatok tartóssága és a konzisztencia szintjét (kell az írt vagy olvasott előtt a hívónak az eredményt visszaadó replikák száma). Replikációs tényező van megadva KULCSTÉR (hasonlóan egy relációs adatbázishoz) létrehozása közben, mivel a konzisztenciaszint van megadva a CRUD-lekérdezés elküldése során. Cassandra-dokumentációban, megtekintéséhez [konzisztencia konfigurálása](https://docs.datastax.com/en/cassandra/3.0/cassandra/dml/dmlConfigConsistency.html) konzisztencia részleteket és a képlet kvórum törölje a számításhoz.

Cassandra integritás adatmodellek – konzisztencia és a végleges konzisztencia; két típusát támogatja. a replikációs tényező és a Konzisztenciaszint együtt, hogy az adatok egységes, amint kész vagy a végül konzisztens egy írási művelet. Például adja meg a KVÓRUMA, a Konzisztenciaszint mindig biztosítja, hogy az adatok konzisztenciájának során bármilyen konzisztenciaszint alább a KVÓRUM (például egy) eléréséhez szükség szerint írandó replikák száma eredményez folyamatban van a végül konzisztens adatokat.

A 8 csomópontos fürt látható a fenti 3 és KVÓRUM replikációs tényezőt (2 csomópont vannak olvasása vagy írása a konzisztencia) olvasási/írási konzisztencia szintjét, a replikáció csoportonként legfeljebb 1 csomópontos elméleti elvesztését hibatűrését okainak alkalmazás indítása előtt a Hiba történt. A parancs feltételezi, hogy a kulcs tárolóhelyek jól kiegyensúlyozott rendelkezik az összes olvasási/írási kérelmek. Az üzembe helyezett fürt használt paraméterek a következők:

Egyetlen régió Cassandra fürtkonfiguráció:

| Fürt paraméter | Érték | Megjegyzések |
| --- | --- | --- |
| Csomópontok (N) |8 |A fürtben található csomópontok száma összesen |
| Replikációs tényező (RF) |3 |Adott sor replikák száma |
| Konzisztenciaszint (írás) |QUORUM[(RF/2) +1) = 2] a képlet eredménye kerekíti a rendszer |Legfeljebb 2 replika ír, hogy a hívó; a válasz elküldése előtt 3 replika egy végül konzisztens módon nyelven van megírva. |
| Konzisztenciaszint (olvasás) |KVÓRUM [(RF/2) + 1 = 2] a képlet eredménye kerekíti a rendszer |2 replika beolvasása a hívónak elküldése előtt. |
| Replikációs stratégia |Lásd a NetworkTopologyStrategy [adatreplikáció](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) Cassandra dokumentációjában talál további információt a |Tisztában van azzal az üzembe helyezési topológiát és a replikák helyezi a csomópontokon, hogy minden replika végül nem a azonos állvány a |
| Snitch |Lásd a GossipingPropertyFileSnitch [kapcsolók](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) Cassandra dokumentációjában talál további információt a |NetworkTopologyStrategy snitch egy fogalom megértéséhez a topológia használ. GossipingPropertyFileSnitch az egyes csomópontok leképezése adatközpont- és állványalapú nagyobb ellenőrzést biztosít. A fürt pletykákat használja fel ezeket az információkat propagálása. Ez a jóval egyszerűbb viszonyított PropertyFileSnitch dinamikus IP-beállításai |

**Cassandra-fürt Azure szempontjai:** A Microsoft Azure Virtual Machines képesség használja az Azure Blob storage lemezek adatmegőrzési állapota; Az Azure Storage menti a nagyfokú tartósság az egyes lemezek három replikával. Ez azt jelenti, hogy minden egyes sorához egy Cassandra táblába beszúrt adatokat már tárolták a három replika készül. Ezért az adatkonzisztencia már elvégzi, akkor is, ha a replikációs tényező (RF) 1. 1 folyamatban replikációs tényező fő problémája, hogy az alkalmazás állásidő észlel, akkor is, ha egy Cassandra egyetlen csomópont meghibásodik. Azonban ha egy csomópont nem működik a ismeri fel az Azure Fabric Controller problémákat (például hardverek, szoftverek rendszerhibák), helyezi üzembe a segítségével az ugyanazon tárolóeszközöket helyére egy új csomópont. A régi helyett egy új csomópont kiépítése néhány percig is eltarthat. Hasonlóképpen tervezett karbantartási tevékenységek például a vendég operációs rendszer módosításokat, Cassandra frissíti, és alkalmazások módosítására az Azure Fabric Controller végrehajtja a működés közbeni frissítés a csomópontok a fürtben. A működés közbeni frissítés is is igénybe vehet néhány csomópont le egy időben, és ezért a fürt tapasztalhat néhány partíciók rövid idejű leállást. Azonban az adatok nem vesznek el, a beépített Azure-tárhely-redundancia miatt.

Üzembe helyezni az Azure magas rendelkezésre állást nem igénylő rendszerek (például körülbelül 99,9 ami egyenértékű 8.76 óra/év; lásd: [magas rendelkezésre állású](http://en.wikipedia.org/wiki/High_availability) részletekért) lehet futtatni, hogy RF = 1 és a Konzisztenciaszint = egyet. Az alkalmazások magas rendelkezésre állással, RF = 3, és a Konzisztenciaszint = KVÓRUM eltűr egyik a replikákat az egyik csomópont lefelé idején. RF = 1, a hagyományos központi telepítések (például a helyszínen) a problémák, mint például a lemezek meghibásodása eredő esetleges adatvesztés miatt nem használható.

## <a name="multi-region-deployment"></a>Többrégiós fejlesztés
A több régióból álló üzemelő bármilyen külső eszköz nélkül segít a Cassandra a data-center-kompatibilis replikáció és a konzisztencia modell a fent leírt. Ez különbözik a hagyományos relációs adatbázisoktól, a telepítő az adatbázis-tükrözés több főkiszolgálós írási műveletek számára bonyolult lehet. A használati forgatókönyvek, többek között a forgatókönyvek a több régióban a telepítő a Cassandra segítséget:

**Közelségi alapú telepítés:** Több-bérlős alkalmazások, a bérlő felhasználói egyértelmű leképezés-a-régió, is származott, által a többrégiós fürt alacsony késés érdekében. Ha például egy learning oktatási intézmények számára a felügyeleti rendszerek kiszolgálása a megfelelő most az USA keleti RÉGIÓJA és USA nyugati RÉGIÓJA régiókban elosztott fürtöt is telepíthet tranzakciós és analitikai. Az adatok az idő írások és olvasások: lehet helyileg egységes és végül konzisztens mindkét a régiók között elosztva. További példák, például a telepítési adathordozó, e-kereskedelmi és bármit és mindent, ami a koncentrált földrajzi felhasználói alap szolgálja ki az e-alapú üzemi modell hasznosnak találta az esetben.

**Magas rendelkezésre állás:** A redundancia egy kulcsfontosságú tényező szoftverek és hardverek; magas rendelkezésre állás elérése információt talál a Microsoft Azure épület megbízható felhőalapú rendszerek. A Microsoft Azure-ban a csak megbízható igaz redundancia megvalósításának módja egy többrégiós fürt üzembe helyezésével. Alkalmazások telepíthetők egy aktív – aktív vagy aktív – passzív módban, és a egy régiót nem működik, ha az Azure Traffic Manager forgalom átirányíthatja az aktív terület. Az egyetlen régióban üzemelő Ha a rendelkezésre állás 99,9, a két-régióban történő üzembe helyezés el tud érni a képlettel kiszámított 99.9999 a rendelkezésre állási: (1-(1-0.999) * (1 – 0.999)) * 100); olvassa el a fenti részleteiről.

**Vész-helyreállítási:** Többrégiós Cassandra-fürt megfelelően ki tudja szolgálni, ha képes elviselni katasztrofális data center valamilyen okból kimaradás lép. Ha egy adott régióban nem működik, a más régióban üzembe helyezett alkalmazás megkezdheti a végfelhasználók kiszolgálása. Mint bármely más üzleti folytonossági megvalósításokhoz az alkalmazás nem lehet az adatokat a aszinkron folyamat eredő adatvesztést a hibatűrő. Azonban Cassandra lehetővé teszi a helyreállítást, mint a hagyományos adatbázis helyreállítási folyamatok által igénybe vett idő sokkal zavartalanabbá. 2. ábra nyolc csomóponttal rendelkező tipikus többrégiós üzembe helyezési modelljét mutatja az egyes régiókban. Mindkét régióban ugyanahhoz a szimmetrikus; minden más tükrözött képek valós tervek attól függ, a számításifeladat-típust (például tranzakciós vagy elemző), a helyreállítási Időkorlát, a RTO, a adatkonzisztencia és a rendelkezésre állási követelmények vonatkoznak.

![Több régióban történő üzembe helyezés](./media/cassandra-nodejs/cassandra-linux2.png)

2. ábra: Többrégiós a Cassandra üzembe helyezése

### <a name="network-integration"></a>Hálózat-integráció
Csoportok magánhálózatokon található két régióban üzembe helyezett virtuális gépek egymáshoz VPN-alagút használatával kommunikál. A VPN-alagút a hálózat üzembe helyezési folyamat során létesített két szoftverfrissítési átjáró csatlakozik. Mindkét régióban van a hasonló hálózati architektúra szempontjából a "webes" és "adatok" alhálózatok; Az Azure-hálózatok lehetővé teszi, hogy igény szerint annyi alhálózatok létrehozásának és ACL-ek alkalmazása a hálózati biztonság igény szerint. A fürt topológia tervezésekor többek data center kommunikációs késés és a hálózati forgalmat kell figyelembe venni a gazdasági hatásáról.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Adatkonzisztencia több adatközpontban üzembe helyezéshez
Az elosztott központi telepítések kell figyelembe venni átviteli sebesség és a magas rendelkezésre állású fürt topológia hatását. A RF és a Konzisztenciaszint kell, hogy a kvórum nem függ az adatközpontok rendelkezésre állását módon ki kell jelölni.
Magas konzisztenciát igénylő rendszer a konzisztencia szintjét (az olvasási és írási) egy LOCAL_QUORUM gondoskodik arról, hogy elégedett a a helyi írások és olvasások a helyi csomópont, miközben az adatok replikálása aszinkron módon történik a távoli adatközpontban. 2. táblázat foglalja össze a többrégiós fürt másolatot az írható leírt konfigurációs adatait.

**Cassandra-fürtkonfiguráció két-régió**

| Fürt paraméter | Érték | Megjegyzések |
| --- | --- | --- |
| Csomópontok (N) |8 + 8 |A fürtben található csomópontok száma összesen |
| Replikációs tényező (RF) |3 |Adott sor replikák száma |
| Konzisztenciaszint (írás) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] a képlet eredménye kerekíti a rendszer |2 csomópont szinkron módon; az első data center írt a kvóruma számára szükséges további 2 csomópont aszinkron módon történik a 2. adatközpont íródik. |
| Konzisztenciaszint (olvasás) |LOCAL_QUORUM ((RF/2) + 1) a képlet eredménye lefelé kerekíti a rendszer 2 = |Olvasási kérelmek csak egy régió; teljesülnek 2 csomópont olvasható az ügyfélnek a válasz elküldése előtt. |
| Replikációs stratégia |Lásd a NetworkTopologyStrategy [adatreplikáció](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archDataDistributeAbout.html) Cassandra dokumentációjában talál további információt a |Tisztában van azzal az üzembe helyezési topológiát és a replikák helyezi a csomópontokon, hogy minden replika végül nem a azonos állvány a |
| Snitch |Lásd a GossipingPropertyFileSnitch [Snitches](https://docs.datastax.com/en/cassandra/3.0/cassandra/architecture/archSnitchesAbout.html) Cassandra dokumentációjában talál további információt a |NetworkTopologyStrategy snitch egy fogalom megértéséhez a topológia használ. GossipingPropertyFileSnitch az egyes csomópontok leképezése adatközpont- és állványalapú nagyobb ellenőrzést biztosít. A fürt pletykákat használja fel ezeket az információkat propagálása. Ez a jóval egyszerűbb viszonyított PropertyFileSnitch dinamikus IP-beállításai |

## <a name="the-software-configuration"></a>A SZOFTVERFRISSÍTÉSI KONFIGURÁCIÓ
Az alábbi szoftververziók a telepítése során van szükség:

<table>
<tr><th>Szoftver</th><th>Forrás</th><th>Verzió</th></tr>
<tr><td>JRE    </td><td>[JRE 8](https://aka.ms/azure-jdks) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Az Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Az üzembe helyezés egyszerűsítéséhez, töltse le a szükséges szoftvereknek az asztalon. Töltse fel azt az Ubuntu sablon rendszerképet, hozzon létre egy előzetes, hogy a fürt üzembe helyezése.

A fenti szoftverek letöltési be egy jól ismert letöltési könyvtár (például a Windows %TEMP%/downloads vagy ~/Downloads a legtöbb Linux-disztribúciók vagy Mac) a helyi számítógépen.

### <a name="create-ubuntu-vm"></a>UBUNTU RENDSZERŰ VIRTUÁLIS GÉP LÉTREHOZÁSA
Ebben a lépésben a folyamat hoz létre Ubuntu-képre az előfeltételként szükséges szoftverek az, hogy a kép több Cassandra-csomópontok kiépítéséhez használható fel újra.

#### <a name="step-1-generate-ssh-key-pair"></a>1. LÉPÉS: SSH-kulcspár létrehozása
Az Azure-PEM vagy DER nyilvános kulcsot, az üzembe helyezés ideje kódolású X509 kér. Hozzon létre egy nyilvános/titkos kulcspárt, hogyan lehet az SSH használata Linuxon az Azure-ban található utasításokat követve. Ha azt tervezi, putty.exe használja, mint egy SSH-ügyféllel, vagy a Windows vagy Linux rendszeren, a PEM-kódolású konvertálni kell RSA titkos kulcs használatával puttygen.exe PPK formátumba. A következő útmutatót: Ez a fenti weblapon található.

#### <a name="step-2-create-ubuntu-template-vm"></a>2. LÉPÉS: Ubuntu sablon virtuális gép létrehozása
A Virtuálisgép-sablon létrehozásához jelentkezzen be az Azure Portalra, és kövesse az alábbi eljárást: Kattintson az új, számítás, a virtuális gépek, FROM GALLERY, UBUNTU, Ubuntu Server 14.04 LTS, és kattintson a jobbra mutató nyílra. Ez az oktatóanyag azt ismerteti, hogyan hozhat létre Linux rendszerű virtuális gép hozzon létre egy virtuális gépen futó Linux talál.

A #1 "virtuálisgép-konfiguráció" képernyőn adja meg a következőket:

<table>
<tr><th>MEZŐ NEVE              </td><td>       A MEZŐ ÉRTÉKE               </td><td>         MEGJEGYZÉS                </td><tr>
<tr><td>VERZIÓ KIADÁSI DÁTUMA    </td><td> Egy dátumot a legördülő listából válassza a lefelé</td><td></td><tr>
<tr><td>A VIRTUÁLIS GÉP NEVE    </td><td> CAS kiszolgálókat is – sablon                   </td><td> Ez az a virtuális gép állomásnevét </td><tr>
<tr><td>RÉTEG                     </td><td> STANDARD                           </td><td> Hagyja meg az alapértelmezett              </td><tr>
<tr><td>MÉRET                     </td><td> A1                              </td><td>Válassza ki a virtuális Gépet, az i/o-megfelelően; erre a célra hagyja meg az alapértelmezett </td><tr>
<tr><td> ÚJ FELHASZNÁLÓNÉV             </td><td> localadmin                       </td><td> "rendszergazda" egy fenntartott felhasználónév, az Ubuntu 12. xx és után</td><tr>
<tr><td> HITELESÍTÉS         </td><td> Jelölje be jelölőnégyzetet                 </td><td>Ellenőrizze, hogy szeretné-e az SSH-kulcs védelme </td><tr>
<tr><td> TANÚSÍTVÁNY             </td><td> a nyilvános kulcsú tanúsítvány fájlneve </td><td> Korábban létrehozott nyilvános kulcs használata</td><tr>
<tr><td> Új jelszó    </td><td> erős jelszó </td><td> </td><tr>
<tr><td> Jelszó megerősítése    </td><td> erős jelszó </td><td></td><tr>
</table>

A #2 "virtuálisgép-konfiguráció" képernyőn adja meg a következőket:

<table>
<tr><th>MEZŐ NEVE             </th><th> A MEZŐ ÉRTÉKE                       </th><th> MEGJEGYZÉS                                 </th></tr>
<tr><td> FELHŐSZOLGÁLTATÁS    </td><td> Új felhőszolgáltatás hozható létre    </td><td>Felhőszolgáltatások, a tároló számítási erőforrások, például a virtuális gépek</td></tr>
<tr><td> CLOUD SERVICE DNS-NÉV    </td><td>ubuntu-template.cloudapp.net    </td><td>Adjon meg egy gép független terheléselosztó neve</td></tr>
<tr><td> RÉGIÓ/AFFINITÁSCSOPORT/VIRTUÁLIS HÁLÓZAT </td><td>    USA nyugati régiója    </td><td> Válassza ki a régiót, amelyből a webes alkalmazások férhetnek hozzá a Cassandra-fürt</td></tr>
<tr><td>TÁRFIÓK </td><td>    Alapértelmezett használata    </td><td>Az alapértelmezett tárfiókot, vagy egy előre létrehozott tárfiókot használja egy adott régióban</td></tr>
<tr><td>RENDELKEZÉSRE ÁLLÁSI CSOPORT </td><td>    None </td><td>    Hagyja üresen a mezőt</td></tr>
<tr><td>VÉGPONTOK    </td><td>Alapértelmezett használata </td><td>    Az alapértelmezett SSH-konfiguráció </td></tr>
</table>

Kattintson a jobbra mutató nyílra, majd meghagyhatja az alapértelmezett beállításokat a #3 képernyőn. Kattintson az "ellenőrzés" gombra a virtuális gép üzembe helyezési folyamat befejezéséhez. Néhány perc elteltével a virtuális Gépet a neve "ubuntu-template" a "fut" állapotban kell lennie.

### <a name="install-the-necessary-software"></a>A SZÜKSÉGES SZOFTVEREK TELEPÍTÉSE
#### <a name="step-1-upload-tarballs"></a>1. LÉPÉS: Tarballs feltöltése
A szolgáltatáskapcsolódási pont vagy pscp, másolja a korábban letöltött szoftverügyfélre ~/downloads directory formátuma a következő parancs használatával:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp kiszolgáló-jre-8u5 – linux-x64.tar.gz localadmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Ismételje meg a fenti parancs JRE, valamint a Cassandra bits lehet.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>2. LÉPÉS: Készítse elő a könyvtárstruktúra, és bontsa ki az archívumba
Jelentkezzen be a virtuális Gépre, és a directory-struktúra létrehozása, és bontsa ki a szoftver az alábbi bash-szkript használatával felügyelőként:

```bash
#!/bin/bash
CASS_INSTALL_DIR="/opt/cassandra"
JRE_INSTALL_DIR="/opt/java"
CASS_DATA_DIR="/var/lib/cassandra"
CASS_LOG_DIR="/var/log/cassandra"
DOWNLOADS_DIR="~/downloads"
JRE_TARBALL="server-jre-8u5-linux-x64.tar.gz"
CASS_TARBALL="apache-cassandra-2.0.8-bin.tar.gz"
SVC_USER="localadmin"

RESET_ERROR=1
MKDIR_ERROR=2

reset_installation ()
{
  rm -rf $CASS_INSTALL_DIR 2> /dev/null
  rm -rf $JRE_INSTALL_DIR 2> /dev/null
  rm -rf $CASS_DATA_DIR 2> /dev/null
  rm -rf $CASS_LOG_DIR 2> /dev/null
}
make_dir ()
{
  if [ -z "$1" ]
  then
    echo "make_dir: invalid directory name"
    exit $MKDIR_ERROR
  fi

  if [ -d "$1" ]
  then
    echo "make_dir: directory already exists"
    exit $MKDIR_ERROR
  fi

  mkdir $1 2>/dev/null
  if [ $? != 0 ]
  then
    echo "directory creation failed"
    exit $MKDIR_ERROR
  fi
}

unzip()
{
  if [ $# == 2 ]
  then
    tar xzf $1 -C $2
  else
    echo "archive error"
  fi

}

if [ -n "$1" ]
then
  SVC_USER=$1
fi

reset_installation
make_dir $CASS_INSTALL_DIR
make_dir $JRE_INSTALL_DIR
make_dir $CASS_DATA_DIR
make_dir $CASS_LOG_DIR

#unzip JRE and Cassandra
unzip $HOME/downloads/$JRE_TARBALL $JRE_INSTALL_DIR
unzip $HOME/downloads/$CASS_TARBALL $CASS_INSTALL_DIR

#Change the ownership to the service credentials

chown -R $SVC_USER:$GROUP $CASS_DATA_DIR
chown -R $SVC_USER:$GROUP $CASS_LOG_DIR
echo "edit /etc/profile to add JRE to the PATH"
echo "installation is complete"
```

Ha ez a szkript illessze vim ablak, el kell távolítania a kocsivissza ("\r") a következő paranccsal:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>3. lépés: Stb-profil szerkesztése
Fűzze hozzá a végén a következőket:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>4. lépés: JNA telepítése éles rendszerek esetén.
Használja a következő parancssort: Az alábbi parancs telepíti a jna-3.2.7.jar és jna-platform-3.2.7.jar /usr/share.java directory sudo apt-get paranccsal való telepítése libjna – java

Hozzon létre szimbolikus hivatkozások $CASS_HOME/lib könyvtár, Cassandra indítási parancsfájl megkereshesse a JAR-fájlok kivételével:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>5. lépés: Cassandra.yaml konfigurálása
Az egyes virtuális Gépeken, hogy [akkor tényleges kiépítése során ez a konfiguráció Teljesítménybeállítások] összes virtuális gép által igényelt konfigurációját tükrözzék cassandra.yaml szerkesztése:

<table>
<tr><th>Mezőnév   </th><th> Érték  </th><th>    Megjegyzések </th></tr>
<tr><td>fürtnév </td><td>    "CustomerService"    </td><td> Használja a nevét, amely tükrözi a központi telepítés</td></tr>
<tr><td>listen_address    </td><td>[hagyja üresen a mezőt]    </td><td> Törölje a "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[hagyja üresen a mezőt]    </td><td> Törölje a "localhost" </td></tr>
<tr><td>magok    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Az összes mag van megjelölve, amelyek IP-címek listája.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> Ezt a NetworkTopologyStrateg az adatcsatornához az adatközpont és az állványra szerelt, a virtuális gép használja</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>6. lépés: A virtuális gép lemezképének rögzítése
Jelentkezzen be a virtuális gépet az állomásnevet (hk-cas-template.cloudapp.net) és a korábban létrehozott titkos SSH-kulcs használatával. Tekintse meg az SSH használata Linuxon az Azure-ban való bejelentkezéshez használja a parancsot ssh vagy putty.exe kapcsolatos útmutató.

Hajtsa végre a következő lépéseket, a lemezkép rögzítését célzó műveletek:

##### <a name="1-deprovision"></a>1. Megszüntetési
A parancs használata "sudo waagent – megszüntetési + felhasználó" virtuálisgép-példány adott információk eltávolításához. Tekintse meg a [Linux rendszerű virtuális gép rögzítése](capture-image-classic.md) használandó sablont további részleteket a lemezkép rögzítését.

##### <a name="2-shut-down-the-vm"></a>2: A virtuális gép leállítása
Győződjön meg arról, hogy a virtuális gép ki van-e jelölve, és kattintson a LEÁLLÍTÁS hivatkozásra a alsó parancssávon.

##### <a name="3-capture-the-image"></a>3: A lemezkép rögzítése
Győződjön meg arról, hogy a virtuális gép ki van-e jelölve, és a rögzítés hivatkozásra az alsó sáv. A következő képernyőn, adja meg a RENDSZERKÉP neve (például hk-cas-2-08-ub-14-04-2014071), megfelelő KÉPLEÍRÁS, és kattintson az "ellenőrzés" megjelölni a rögzítési folyamat befejezéséhez.

Ez a folyamat eltarthat néhány másodpercig, és a lemezkép elérhetőnek kell lennie, a lemezkép-katalógus MY IMAGES szakaszában. A forrásoldali virtuális gép automatikusan törlődik, miután a lemezkép rögzítése sikerült. 

## <a name="single-region-deployment-process"></a>Egy régióban üzembe helyezési folyamat
**1. lépés: A virtuális hálózat létrehozása** jelentkezzen be az Azure Portalon, és hozzon létre egy virtuális hálózat (klasszikus) a következő táblázatban szereplő attribútumokkal. Lásd: [hozzon létre egy virtuális hálózat (klasszikus), az Azure portal használatával](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a folyamat részletes leírását.

<table>
<tr><th>Virtuális gép attribútum neve</th><th>Érték</th><th>Megjegyzések</th></tr>
<tr><td>Name (Név)</td><td>vnet-CAS kiszolgálókat is – Nyugat-USA</td><td></td></tr>
<tr><td>Régió</td><td>USA nyugati régiója</td><td></td></tr>
<tr><td>DNS-kiszolgálók</td><td>None</td><td>Figyelmen kívül hagyja ezt, hogy nem használ a DNS-kiszolgáló</td></tr>
<tr><td>Címtartomány</td><td>10.1.0.0/16</td><td></td></tr>
<tr><td>Kezdő IP-cím</td><td>10.1.0.0</td><td></td></tr>
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Adja hozzá a következő alhálózatok:

<table>
<tr><th>Name (Név)</th><th>Kezdő IP-cím</th><th>CIDR</th><th>Megjegyzések</th></tr>
<tr><td>web</td><td>10.1.1.0</td><td>/24 (251)</td><td>A webfarm-alhálózatot</td></tr>
<tr><td>adat</td><td>10.1.2.0</td><td>/24 (251)</td><td>Az adatbázis-csomópont alhálózatot</td></tr>
</table>

Adatok és a webes alhálózatok – Ez a cikk az hatókörén kívül esik a lefedettségét a hálózati biztonsági csoportok védelme biztosítható.

**2. lépés: Virtuális gépek üzembe helyezése** a korábban létrehozott rendszerkép használatával, akkor a következő virtuális gépek létrehozása a cloud Server "hk-c-svc-nyugati", és kösse őket a megfelelő alhálózatokat alább látható módon:

<table>
<tr><th>Gépnév    </th><th>Alhálózat    </th><th>IP-cím    </th><th>Rendelkezésre állási csoport</th><th>DC/állvány</th><th>Kezdőérték?</th></tr>
<tr><td>HK-c1 – Nyugat-USA    </td><td>adat    </td><td>10.1.2.4    </td><td>hk-c-aset-1    </td><td>DC = WESTUS állvány = rack1 </td><td>Igen</td></tr>
<tr><td>hk-c2-west-us    </td><td>adat    </td><td>10.1.2.5    </td><td>hk-c-aset-1    </td><td>DC = WESTUS állvány = rack1    </td><td>Nem </td></tr>
<tr><td>HK-C3 csomag – Nyugat-USA    </td><td>adat    </td><td>10.1.2.6    </td><td>hk-c-aset-1    </td><td>DC = WESTUS állvány = rack2    </td><td>Igen</td></tr>
<tr><td>hk-c4-west-us    </td><td>adat    </td><td>10.1.2.7    </td><td>hk-c-aset-1    </td><td>DC = WESTUS állvány = rack2    </td><td>Nem </td></tr>
<tr><td>HK-C5 csomag – Nyugat-USA    </td><td>adat    </td><td>10.1.2.8    </td><td>hk-c-aset-2    </td><td>DC = WESTUS állvány = rack3    </td><td>Igen</td></tr>
<tr><td>HK-C6 csomag – Nyugat-USA    </td><td>adat    </td><td>10.1.2.9    </td><td>hk-c-aset-2    </td><td>DC = WESTUS állvány = rack3    </td><td>Nem </td></tr>
<tr><td>HK-c7 – Nyugat-USA    </td><td>adat    </td><td>10.1.2.10    </td><td>hk-c-aset-2    </td><td>DC = WESTUS állvány = rack4    </td><td>Igen</td></tr>
<tr><td>hk-c8-west-us    </td><td>adat    </td><td>10.1.2.11    </td><td>hk-c-aset-2    </td><td>DC = WESTUS állvány = rack4    </td><td>Nem </td></tr>
<tr><td>HK-w1 – Nyugat-USA    </td><td>web    </td><td>10.1.1.4    </td><td>hk-w-aset-1    </td><td>                       </td><td>–</td></tr>
<tr><td>HK-w2 – Nyugat-USA    </td><td>web    </td><td>10.1.1.5    </td><td>hk-w-aset-1    </td><td>                       </td><td>–</td></tr>
</table>

A fenti lista a virtuális gépek létrehozása előtt a következőket:

1. Hozzon létre egy üres felhőszolgáltatás egy adott régióban
2. Virtuális gép létrehozása a korábban rögzített lemezképből, és mellékelje a korábban; létrehozott virtuális hálózat Ismételje meg ezt a virtuális gépek
3. Belső terheléselosztó hozzáadása a felhőalapú szolgáltatás, és csatolja az "adatok" alhálózathoz
4. Korábban létrehozott virtuális gépek vegyen fel egy elosztott terhelésű végpontot thrift-forgalmat a korábban létrehozott belső terheléselosztóhoz csatlakozó elosztott terhelésű készlet keresztül

A fenti folyamat hajtható végre az Azure Portalon; egy Windows-gép (Ha nincs hozzáférése egy Windows-gép az Azure-beli virtuális gép használata), használja a következő PowerShell-parancsfájl használatával minden 8 virtuális gépek automatikus kiépítéséhez.

**1. listája: PowerShell-szkript a virtuális gépek kiépítése**

```powershell
#Tested with Azure Powershell - November 2014
#This powershell script deployes a number of VMs from an existing image inside an Azure region
#Import your Azure subscription into the current Powershell session before proceeding
#The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. create a list of VMs from the template

#fundamental variables - change these to reflect your subscription
$country="us"; $region="west"; $vnetName = "your_vnet_name";$storageAccount="your_storage_account"
$numVMs=8;$prefix = "hk-cass";$ilbIP="your_ilb_ip"
$subscriptionName = "Azure_subscription_name";
$vmSize="ExtraSmall"; $imageName="your_linux_image_name"
$ilbName="ThriftInternalLB"; $thriftEndPoint="ThriftEndPoint"

#generated variables
$serviceName = "$prefix-svc-$region-$country"; $azureRegion = "$region $country"

$vmNames = @()
for ($i=0; $i -lt $numVMs; $i++)
{
    $vmNames+=("$prefix-vm"+($i+1) + "-$region-$country" );
}

#select an Azure subscription already imported into Powershell session
Select-AzureSubscription -SubscriptionName $subscriptionName -Current
Set-AzureSubscription -SubscriptionName $subscriptionName -CurrentStorageAccountName $storageAccount

#create an empty cloud service
New-AzureService -ServiceName $serviceName -Label "hkcass$region" -Location $azureRegion
Write-Host "Created $serviceName"

$VMList= @()   # stores the list of azure vm configuration objects
#create the list of VMs
foreach($vmName in $vmNames)
{
    $VMList += New-AzureVMConfig -Name $vmName -InstanceSize ExtraSmall -ImageName $imageName |
            Add-AzureProvisioningConfig -Linux -LinuxUser "localadmin" -Password "Local123" |
            Set-AzureSubnet "data"
}

New-AzureVM -ServiceName $serviceName -VNetName $vnetName -VMs $VMList

#Create internal load balancer
Add-AzureInternalLoadBalancer -ServiceName $serviceName -InternalLoadBalancerName $ilbName -SubnetName "data" -StaticVNetIPAddress "$ilbIP"
Write-Host "Created $ilbName"
#Add the thrift endpoint to the internal load balancer for all the VMs
foreach($vmName in $vmNames)
{
    Get-AzureVM -ServiceName $serviceName -Name $vmName |
            Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
            Update-AzureVM

    Write-Host "created $vmName"
}
```

**3. lépés: Cassandra konfigurálhatja az egyes virtuális Gépeken**

Jelentkezzen be a virtuális Gépre, és hajtsa végre a következő:

* Data center- és állványalapú tulajdonságainak megadásához $CASS_HOME/conf/cassandra-rackdc.properties szerkesztése:
  
       dc =EASTUS, rack =rack1
* Magcsomópontok konfigurálása az alábbi cassandra.yaml szerkesztése:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**4. lépés: Indítsa el a virtuális gépek és a fürt tesztelése**

Jelentkezzen be az egyik (például hk-c1 – Nyugat-us) csomópontot, és futtassa a következő parancsot a fürt állapotának megjelenítéséhez:

       nodetool –h 10.1.2.4 –p 7199 status

A 8 csomópontos fürt alábbihoz hasonló képernyő kell megjelennie:

<table>
<tr><th>status</th><th>Cím    </th><th>Betöltés    </th><th>Tokenek    </th><th>Tulajdonosa </th><th>Állomás azonosítója    </th><th>Kiszolgálószekrény</th></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>GUID (törli)</td><td>rack1</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>GUID (törli)</td><td>rack1</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (törli)</td><td>rack2</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (törli)</td><td>rack2</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (törli)</td><td>rack3</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (törli)</td><td>rack3</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (törli)</td><td>rack4</td></tr>
<tr><th>TÖRLÉSEKOR    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (törli)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Az egyetlen régióban fürt tesztelése
Használja az alábbi lépéseket a fürt teszteléséhez:

1. Powershell-parancsmaggal parancs Get-AzureInternalLoadbalancer, szerezze be a belső terheléselosztó (például 10.1.2.101) IP-címét. A parancs szintaxisa alább látható: Get-AzureLoadbalancer – szolgáltatásnév, "hk-c-svc-west-us" [jeleníti meg a részleteket a belső terheléselosztó IP-címének együtt]
2. Jelentkezzen be a webfarm (például hk-w1 – Nyugat-us) virtuális gép a Putty használatával vagy ssh
3. Hajtsa végre a $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. A következő CQL-parancsok segítségével győződjön meg arról, hogy a fürt működik:
   
     A REPLIKÁCIÓ létrehozása KULCSTÉR customers_ks = {"class": "SimpleStrategy',"replication_factor": 3};   Customers_ks; használata   Hozzon létre a tábla Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Helyezze be az Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, "Jane", "János").
   
     Válassza ki * ÜGYFELEKTŐL;

A következő eredményeket hasonlót kell megjelennie:

<table>
  <tr><th> customer_id </th><th> Keresztnév </th><th> Vezetéknév </th></tr>
  <tr><td> 1 </td><td> János </td><td> Például a DOE </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Például a DOE </td></tr>
</table>

A 4. lépésben létrehozott kulcstér SimpleStrategy használ egy replication_factor a 3-ból. SimpleStrategy ajánlott egyetlen data center központi telepítések mivel több adatok NetworkTopologyStrategy adatközpont üzemelő példányok. A 3-ból egy replication_factor csomóponthibák szintű biztosít.

## <a id="tworegion"> </a>Többrégiós üzembe helyezési folyamat
Kihasználhatja az egyetlen régióban üzembe helyezés befejeződött, és ugyanahhoz a folyamathoz ismételje meg a második régiót telepítése. Az egyetlen vagy több régióban történő üzembe helyezés közötti fő különbség a VPN-alagút beállítást a régiók közötti kommunikációhoz; Indítsa el a hálózati telepítést, a virtuális gépek kiépítése, és konfigurálja a Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>1. lépés: A virtuális hálózat létrehozása a 2. régió:
Jelentkezzen be az Azure Portalra, és hozzon létre egy virtuális hálózatot az attribútumok megjelenítése a táblában. Lásd: [Cloud-Only virtuális hálózat konfigurálása az Azure Portalon](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a folyamat részletes leírását.

<table>
<tr><th>Attribútum neve    </th><th>Érték    </th><th>Megjegyzések</th></tr>
<tr><td>Name (Név)    </td><td>vnet-CAS kiszolgálókat is – kelet-USA</td><td></td></tr>
<tr><td>Régió    </td><td>USA keleti régiója</td><td></td></tr>
<tr><td>DNS-kiszolgálók        </td><td></td><td>Figyelmen kívül hagyja ezt, hogy nem használ a DNS-kiszolgáló</td></tr>
<tr><td>Pont – hely VPN konfigurálása</td><td></td><td>        Figyelmen kívül hagyja ezt</td></tr>
<tr><td>Webhelyek közötti virtuális magánhálózat konfigurálása</td><td></td><td>        Figyelmen kívül hagyja ezt</td></tr>
<tr><td>Címtartomány    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Kezdő IP-cím    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Adja hozzá a következő alhálózatok:

<table>
<tr><th>Name (Név)    </th><th>Kezdő IP-cím    </th><th>CIDR    </th><th>Megjegyzések</th></tr>
<tr><td>web    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>A webfarm-alhálózatot</td></tr>
<tr><td>adat    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Az adatbázis-csomópont alhálózatot</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>2. lépés: Helyi hálózatok létrehozása
Az Azure virtuális hálózatok helyi hálózati proxy címtér képez le egy távoli helyen, beleértve a magánfelhő vagy egy másik Azure-régióban. A proxy címtartomány van kötve egy távoli átjáró útválasztási hálózat a megfelelő hálózati helyre. Lásd: [virtuális hálózat virtuális hálózatok közötti kapcsolat konfigurálása](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) VNET – VNET kapcsolat útmutatást.

Hozzon létre két helyi hálózatok száma a következő adatokat:

| Hálózatnév | VPN-átjáró címe | Címtartomány | Megjegyzések |
| --- | --- | --- | --- |
| hk-lnet-map-to-east-us |23.1.1.1 |10.2.0.0/16 |Létrehozásakor a helyi hálózati átjáró-címmel látja el egy helyőrző. A valódi átjáró címe meg van adva, az átjáró létrehozása után. Győződjön meg arról, hogy a címtér pontosan megegyezik a megfelelő távoli virtuális hálózat; Ebben az esetben a virtuális hálózat létrehozása az USA keleti régiójában. |
| hk-lnet-map-to-west-us |23.2.2.2 |10.1.0.0/16 |Létrehozásakor a helyi hálózati átjáró-címmel látja el egy helyőrző. A valódi átjáró címe meg van adva, az átjáró létrehozása után. Győződjön meg arról, hogy a címtér pontosan megegyezik a megfelelő távoli virtuális hálózat; Ebben az esetben a virtuális hálózat létrehozása az USA nyugati régiója. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>3. lépés: A megfelelő virtuális hálózat "Local" hálózat leképezése
Az Azure Portalon válassza ki a minden egyes virtuális hálózatok közötti, "Konfigurálás" gombra, ellenőrizze a "Kapcsolódás a helyi hálózatra", és válassza ki a helyi hálózatok száma a következő adatokat:

| Virtual Network | Helyi hálózat |
| --- | --- |
| hk-vnet-west-us |hk-lnet-map-to-east-us |
| hk-vnet-east-us |hk-lnet-map-to-west-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>4. lépés: A VNET1 és a VNET2 átjárók létrehozása
A virtuális hálózatok az irányítópultról kattintson az ÁTJÁRÓ létrehozása a VPN-átjáró kiépítési folyamat aktiválásához. Néhány perc elteltével az irányítópult minden egyes virtuális hálózat megjelenjen a tényleges átjárócímet.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>5. lépés: Frissítse a megfelelő "" átjárócímek "Local" hálózatok
Cserélje le a helyőrző IP-címét az imént kiépített átjárók valós IP-címét, mind a helyi hálózatok szerkesztése. A következő hozzárendelést használja:

<table>
<tr><th>Helyi hálózat    </th><th>Virtuális hálózati átjáró</th></tr>
<tr><td>hk-lnet-map-to-east-us </td><td>Átjáró, hk-vnet-Nyugat-USA</td></tr>
<tr><td>hk-lnet-map-to-west-us </td><td>Átjáró, hk – virtuális hálózat – kelet-USA</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>6. lépés: A megosztott kulcs frissítése
A következő Powershell-parancsfájlt használja az IPSec-kulcsot minden egyes VPN-átjáró [használja az átjárók szakét kulcsa] frissítése: Set-AzureVNetGatewayKey - VNetName hk – virtuális hálózat – kelet-USA - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-Nyugat-us - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>7. lépés: A VNET – VNET kapcsolat létrehozása
Az Azure Portalról az "IRÁNYÍTÓPULT" menü, mind a virtuális hálózatok használatával gateway-átjáró kapcsolatot létesíteni. Használja a "Csatlakozás" elemeket az alsó eszköztáron. Néhány perc elteltével az irányítópult megjelenjen-e a kapcsolat adatai grafikusan.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>8. lépés: #2 régióban található virtuális gépek létrehozása
Az Ubuntu-rendszerkép létrehozása a következő lépéseket vagy a #2 régióban található Azure storage-fiókhoz a kép VHD-fájl másolása #1 régióban történő üzembe helyezés leírtak szerint, és a rendszerkép létrehozása. Ennek a képnek és hozzon létre az alábbi listán szereplő virtuális gépek az új felhőalapú szolgáltatás hk-c-svc-kelet-USA:

| Gépnév | Alhálózat | IP-cím | Rendelkezésre állási csoport | DC/állvány | Kezdőérték? |
| --- | --- | --- | --- | --- | --- |
| HK-c1 – kelet-USA |adat |10.2.2.4 |hk-c-aset-1 |DC = USA keleti RÉGIÓJA rack = rack1 |Igen |
| hk-c2-east-us |adat |10.2.2.5 |hk-c-aset-1 |DC = USA keleti RÉGIÓJA rack = rack1 |Nem |
| HK-C3 csomag – kelet-USA |adat |10.2.2.6 |hk-c-aset-1 |DC = USA keleti RÉGIÓJA rack = rack2 |Igen |
| HK-C5 csomag – kelet-USA |adat |10.2.2.8 |hk-c-aset-2 |DC = USA keleti RÉGIÓJA rack = rack3 |Igen |
| hk-c6-east-us |adat |10.2.2.9 |hk-c-aset-2 |DC = USA keleti RÉGIÓJA rack = rack3 |Nem |
| hk-c7-east-us |adat |10.2.2.10 |hk-c-aset-2 |DC = USA keleti RÉGIÓJA rack = rack4 |Igen |
| HK-c8 – kelet-USA |adat |10.2.2.11 |hk-c-aset-2 |DC = USA keleti RÉGIÓJA rack = rack4 |Nem |
| HK-w1 – kelet-USA |web |10.2.1.4 |hk-w-aset-1 |– |– |
| HK-w2 – kelet-USA |web |10.2.1.5 |hk-w-aset-1 |– |– |

#1 régió mint ugyanezeket a lépéseket, de 10.2.xxx.xxx címteret használja.

### <a name="step-9-configure-cassandra-on-each-vm"></a>9. lépés: Cassandra konfigurálhatja az egyes virtuális Gépeken
Jelentkezzen be a virtuális Gépre, és hajtsa végre a következő:

1. A formátumban adja meg az erőforrás- és állványalapú tulajdonságait $CASS_HOME/conf/cassandra-rackdc.properties szerkesztése: dc = USA keleti RÉGIÓJA rack = rack1
2. Szerkesztés cassandra.yaml magcsomópontok konfigurálása:  Mag: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>10. lépés: Indítsa el a Cassandra
Jelentkezzen be minden egyes virtuális Gépre, és indítsa el a Cassandra a háttérben a következő parancs futtatásával: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>A többrégiós fürt tesztelése
Már Cassandra 16 csomóponton a 8 csomópont minden egyes Azure-régióban van telepítve. Ezek a csomópontok ugyanazon a fürtön a közös fürt neve és a kezdőérték csomópont-konfiguráció találhatók. A fürt teszteléséhez tegye a következőket:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>1. lépés: A belső terheléselosztó IP lekérése is a régiókat, PowerShell-lel
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-west-us"
* Get-AzureInternalLoadbalancer -ServiceName "hk-c-svc-east-us"
  
    Jegyezze fel az IP-címek (a példában Nyugat - 10.1.2.101, kelet - 10.2.2.101) jelenik meg.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>2. lépés: Miután bejelentkezett, hk-w1 – Nyugat-USA hajtsa végre az alábbiakat a nyugati régióban
1. Hajtsa végre a $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. Hajtsa végre a következő CQL-parancsokat:
   
     A REPLIKÁCIÓ létrehozása KULCSTÉR customers_ks = {"class": "NetworkToplogyStrategy', 'WESTUS': 3, "EASTUS": 3};   Customers_ks; használata   Hozzon létre a tábla Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Helyezze be az Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, "Jane", "János").   Válassza ki * ÜGYFELEKTŐL;

A képernyő az alábbihoz hasonlóan kell megjelennie:

| customer_id | Keresztnév | Vezetéknév |
| --- | --- | --- |
| 1 |János |Például a DOE |
| 2 |Jane |Például a DOE |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>3. lépés: Hajtsa végre a következő keleti hk-w1 – kelet-USA való bejelentkezés után:
1. Hajtsa végre a $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. Hajtsa végre a következő CQL-parancsokat:
   
     Customers_ks; használata   Hozzon létre a tábla Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Helyezze be az Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   INSERT INTO Customers(customer_id, firstname, lastname) VALUES (2, "Jane", "János").   Válassza ki * ÜGYFELEKTŐL;

Az azonos megjelenített a nyugati régió látható módon kell megjelennie:

| customer_id | Keresztnév | Vezetéknév |
| --- | --- | --- |
| 1 |János |Például a DOE |
| 2 |Jane |Például a DOE |

Néhány további Beszúrások hajtható végre, és tekintse meg, hogy azok replikálása az USA nyugati-velünk a kapcsolatot a fürt része.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cassandra-fürtjére teszt node.js-sel
A "webes" szint korábban létrehozott Linux virtuális gépek egyikével végrehajtása egy egyszerű Node.js-szkript a korábban beszúrt adatokat olvasni.

**1. lépés: Node.js és a Cassandra-ügyfél telepítése**

1. Telepítse a Node.js és npm
2. Csomag "cassandra-ügyfél csomópont" telepítése az npm segítségével
3. Hajtsa végre a következő szkriptet a shell parancssorában, mely megjeleníti a beolvasott adatok json-karakterlánc:
    
    ```
    var pooledCon = require('cassandra-client').PooledConnection;
    var ksName = "custsupport_ks";
    var cfName = "customers_cf";
    var hostList = ['internal_loadbalancer_ip:9160'];
    var ksConOptions = { hosts: hostList,
                         keyspace: ksName, use_bigints: false };

    function createKeyspace(callback) {
        var cql = 'CREATE KEYSPACE ' + ksName + ' WITH strategy_class=SimpleStrategy AND strategy_options:replication_factor=1';
        var sysConOptions = { hosts: hostList,
                              keyspace: 'system', use_bigints: false };
        var con = new pooledCon(sysConOptions);
        con.execute(cql,[],function(err) {
            if (err) {
                console.log("Failed to create Keyspace: " + ksName);
                console.log(err);
            }
            else {
                console.log("Created Keyspace: " + ksName);
                callback(ksConOptions, populateCustomerData);
            }
        });
        con.shutdown();
    }

    function createColumnFamily(ksConOptions, callback) {
        var params = ['customers_cf','custid','varint','custname',
                      'text','custaddress','text'];
        var cql = 'CREATE COLUMNFAMILY ? (? ? PRIMARY KEY,? ?, ? ?)';
        var con =  new pooledCon(ksConOptions);
        con.execute(cql,params,function(err) {
            if (err) {
                console.log("Failed to create column family: " + params[0]);
                console.log(err);
            }
            else {
                console.log("Created column family: " + params[0]);
                callback();
            }
        });
        con.shutdown();
    }

    //populate Data
    function populateCustomerData() {
        var params = ['John','Infinity Dr, TX', 1];
        updateCustomer(ksConOptions,params);

        params = ['Tom','Fermat Ln, WA', 2];
        updateCustomer(ksConOptions,params);
    }

    //update also inserts the record if none exists
    function updateCustomer(ksConOptions,params) {
        var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
        var con = new pooledCon(ksConOptions);
        con.execute(cql,params,function(err) {
            if (err) console.log(err);
            else console.log("Inserted customer : " + params[0]);
        });
        con.shutdown();
    }

    //read the two rows inserted above
    function readCustomer(ksConOptions) {
        var cql = 'SELECT * FROM customers_cf WHERE custid IN (1,2)';
        var con = new pooledCon(ksConOptions);
        con.execute(cql,[],function(err,rows) {
            if (err)
                console.log(err);
            else
                for (var i=0; i<rows.length; i++)
                    console.log(JSON.stringify(rows[i]));
            });
        con.shutdown();
    }

    //execute the code
    createKeyspace(createColumnFamily);
    readCustomer(ksConOptions)
    ```

## <a name="conclusion"></a>Összegzés
Microsoft Azure egy rugalmas platform, amely lehetővé teszi mind a Microsoft, valamint a nyílt forráskódú szoftverek futtatását, amint azt a ebben a gyakorlatban a. Magas rendelkezésre állású Cassandra-fürt egyetlen adatközpontján keresztül a fürt csomópontjai szét több tartalék tartomány között is telepíthetők. Cassandra-fürtök Azure-régióban több földrajzilag távoli vészhelyreállítási megvalósíthatósági példában rendszerekhez is telepíthető. Azure-ban és a Cassandra együtt teszi lehetővé felépítése rugalmasan méretezhető, magas rendelkezésre állású és vészhelyreállítási helyreállítható a cloud services szükséges mai interneten horizontális szolgáltatások.

## <a name="references"></a>Referencia
* [http://cassandra.apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)
