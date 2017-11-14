---
title: Linux Cassandra futtassa az Azure-on |} Microsoft Docs
description: "Hogyan Linux Azure Virtual Machines Cassandra fürt futtassa a Node.js-alkalmazás"
services: virtual-machines-linux
documentationcenter: nodejs
author: craigshoemaker
manager: routlaw
editor: 
tags: azure-service-management
ms.assetid: 30de1f29-e97d-492f-ae34-41ec83488de0
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: cshoe
ms.openlocfilehash: 28eb281d8d301fa5478afb0925c74349de92ca58
ms.sourcegitcommit: e38120a5575ed35ebe7dccd4daf8d5673534626c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/13/2017
---
# <a name="running-cassandra-with-linux-on-azure-and-accessing-it-from-nodejs"></a>A linuxos Cassandra futtatása az Azure-ban és az alkalmazás Node.js-ből való elérése
> [!IMPORTANT] 
> Azure az erőforrások létrehozására és kezelésére két különböző üzembe helyezési modellel rendelkezik: [Resource Manager és klasszikus](../../../resource-manager-deployment-model.md). Ez a cikk a klasszikus telepítési modell használatát bemutatja. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Tekintse meg a Resource Manager-sablonok [alapszintű Datastax vállalati](https://azure.microsoft.com/documentation/templates/datastax) és [Spark-fürt és Cassandra a CentOS](https://azure.microsoft.com/documentation/templates/spark-and-cassandra-on-centos/).

## <a name="overview"></a>Áttekintés
Microsoft Azure a egy megnyitott felhőalapú platform, amely a Microsoft fut, és a nem Microsoft szoftvert, amely operációs rendszerek, alkalmazás-kiszolgálókat, üzenetküldési köztes, valamint SQL és a NoSQL-adatbázisok mindkét kereskedelmi és nyissa meg a forrás-modellek. A nyilvános felhők, beleértve az Azure rugalmas szolgáltatások igényel gondos tervezéssel és szándékos architektúra mindkét alkalmazáskiszolgálók jól tárolási rétegek. Cassandra által elosztott tároló-architektúra természetes segítségével magas rendelkezésre állású rendszerek, amelyek a fürt hibák hibatűrő fejlesztése során. Cassandra egy felhőhöz méretezett NoSQL-adatbázis által Apache szoftver Foundation költséget cassandra.apache.org; Cassandra Java nyelven van megírva, és ezért mind a Windows, Linux futtatja platformokon.

Ez a cikk célja Cassandra telepítési megjelenítése Ubuntu a Microsoft Azure virtuális gépek és virtuális hálózatok egyetlen és több data center fürtként. A fürtöt tartalmazó környezetben optimalizált termelési számítási feladatokhoz nem ez a cikk érhető el, mivel az több lemez a csomópont-konfiguráció, megfelelő körgyűrűs topológia tervezési és a szükséges replikációs, a adatkonzisztenciát, az átviteli sebesség és a magas támogatásához modellezési adatok rendelkezésre állási követelményeinek.

A cikk vesz részt vesz egy alapvető megközelítés jeleníthető meg, mi a Cassandra fürt építése Docker, Chef vagy Puppet, amely megkönnyítheti az infrastruktúra telepítése sokkal képest.  

## <a name="the-deployment-models"></a>Az üzembe helyezési modellek
A Microsoft Azure hálózatkezelés lehetővé teszi, hogy a telepítése elkülönített titkos fürtök esetén, amelyek a hozzáférés korlátozható részletes hálózati biztonság eléréséhez.  Mivel ez a cikk a Cassandra telepítési alapvető szinten megjelenítésével kapcsolatos, nem tárgyaljuk a konzisztenciaszint és átviteli sebesség eléréséhez optimális tároló-kialakításában. A hálózati követelményei a elméleti fürt listája itt található:

* Külső rendszerek Cassandra adatbázis belül vagy kívül Azure nem tud hozzáférni.
* Cassandra fürt-nak kell lennie a thrift-forgalmat egy terheléselosztó mögött
* Minden adatközpontot továbbfejlesztett fürt rendelkezésre állási csoportok két csomópontja Cassandra telepítése
* Zárolását, a fürt úgy, hogy csak az alkalmazás kiszolgálófarm közvetlenül rendelkezik hozzáféréssel az adatbázishoz
* Eltérő SSH nyilvános hálózati végpontok
* Minden egyes Cassandra csomópont kell egy rögzített belső IP-cím

Cassandra egyetlen Azure-régió, vagy a munkaterhelés elosztott jellege alapján több régióba telepíthető. Több régióban üzembe helyezési modellel is javítható kiszolgálni a végfelhasználók számára közelebb, hogy egy adott földrajzi Cassandra ugyanazon az infrastruktúrán keresztül. Cassandra meg beépített csomópont replikációs vesz igénybe a szinkronizálás többszörös főkiszolgáló több különböző adatközponthoz származó ír és megadja az adatok az alkalmazások konzisztens nézetét. Több területi központi telepítés is segíthetnek a a kockázatcsökkentés a szélesebb körű Azure szolgáltatás-kimaradások számát. Cassandra tartozó hangolható konzisztencia és a replikációs topológia segít az alkalmazások különböző Helyreállítási igényeket.

### <a name="single-region-deployment"></a>Egyetlen régión központi telepítés
Rendszer egy régió telepítés elindításához és nem kér be adatokat az learnings több területi modell létrehozásához. Az Azure virtuális hálózat használandó elkülönített alhálózatokat létre úgy, hogy a fent említett hálózatbiztonsági követelményei érheti el.  A régió egyetlen központi telepítés létrehozása ismertetett folyamatot használ Ubuntu 14.04 LTS és Cassandra 2.08; azonban a folyamat is könnyen elfogadni Linux Variant adattípusban. Az alábbiakban a egyetlen régión telepítési rendszeres jellemzőit.  

**Magas rendelkezésre állás:** az 1. ábráján látható Cassandra csomópont két rendelkezésre állási csoportokra vannak telepítve, úgy, hogy a csomópont a magas rendelkezésre állású több tartalék tartományok között van elosztva. Virtuális gépek minden egyes rendelkezésre állási csoport attribútummal 2 tartalék tartományok van leképezve.  A Microsoft Azure által használt tartalék tartomány le nem tervezett idő (pl. a hardver- vagy hibák) közben (pl. gazdagép vagy vendég operációs rendszer javítás vagy frissítés, alkalmazásfrissítések) frissítési tartomány fogalma kezelésére fogalma állásidő ütemezett kezelésére használt. Ellenőrizze a [vész-helyreállítási és magas rendelkezésre állás, az Azure-alkalmazások](http://msdn.microsoft.com/library/dn251004.aspx) hiba és a frissítési tartományok magas rendelkezésre állás elérése a szerepkörhöz.

![Egyetlen régión központi telepítés](./media/cassandra-nodejs/cassandra-linux1.png)

1. ábra: Egy régiót központi telepítés

Vegye figyelembe, hogy a cikk írásának időpontjában Azure nem engedélyezi egy csoportján az adott tartalék tartomány; explicit leképezése ezért még a üzembe helyezési modellel, 1. ábrán látható, a valószínű statisztikailag, hogy a virtuális gépek két tartalék tartományok helyett négy lehet rendelni.

**Terheléselosztás Thrift forgalmi betöltése:** Thrift ügyfél függvénytárainak belül a webalkalmazás-kiszolgáló csatlakozzon a fürthöz, a belső terheléselosztók használatával. Ehhez szükséges, hogy a belső terheléselosztó hozzáadása a "data" alhálózathoz (lásd az 1. ábra) keretén belül a felhőalapú szolgáltatás, a Cassandra fürtöt. A belső terheléselosztó van definiálva, miután minden csomópont szükséges az elosztott terhelésű végpont a Megjegyzések a korábban meghatározott terheléselosztó neve az elosztott terhelésű készlet lehet hozzáadni. Lásd: [Azure belső terheléselosztás ](../../../load-balancer/load-balancer-internal-overview.md)további részleteket.

**Fürt magok:** fontos, hogy válassza ki a legtöbb magas rendelkezésre állású csomópontok beállítása mag, mert a kezdőérték csomópontok feltérképezi a fürt kommunikál az új csomópontok. Egyes rendelkezésre állási csoport egy csomópont kezdőérték csomópontként kijelölt kerülje a hibaérzékeny pontok kialakulását.

**Replikációs tényező és Konzisztenciaszint:** Cassandra a beépített magas rendelkezésre állású és az adatok tartóssága jellemzőek a replikációs tényező (RF - példányszámot az egyes sorok, a fürtön tárolt), és a Konzisztenciaszint (replikák száma kell olvasása/írása az eredmény a hívó való visszatérés előtt). Replikációs tényező kulcstérértesítések használatával (hasonlóan egy relációs adatbázisban) létrehozása közben megadott, mivel a konzisztenciaszint van megadva a CRUD-lekérdezés elküldése során. Lásd a Cassandra dokumentációját a [konfigurálásával konzisztenciájának](http://www.datastax.com/documentation/cassandra/2.0/cassandra/dml/dml_config_consistency_c.html) konzisztencia részletek és a képlet kvórum számításhoz.

Cassandra kétféle adatok integritásának modellek – konzisztencia és a végleges konzisztencia; a replikációs tényező és Konzisztenciaszint együtt meghatározzák, ha az adatok lesz konzisztens, amint egy írási művelet befejeződik, vagy idővel konzisztenssé lesz. Például KVÓRUM megadását, mint a Konzisztenciaszint fognak mindig biztosítja az adatok konzisztenciájának során bármely konzisztenciaszint, eléréséhez szükség szerint írandó replikák száma alatt (pl. egy) KVÓRUM eredményezi, hogy idővel konzisztenssé adatok.

A fentiek, egy replikációs tényező 3-KVÓRUM 8 csomópontos fürt (2 csomópontok vannak olvasása vagy írása konzisztencia) olvasási/írási konzisztenciaszint, az elméleti elvész az egyes replikációs csoportok legfeljebb 1 csomópont hibatűrését előtt az alkalmazás indítása előtt tartva a Hiba történt. A parancs feltételezi, hogy a kulcs szóközöket jól kiegyensúlyozott rendelkezik az összes olvasási/írási kérések.  A telepített fürt használjuk a paraméterek a következők:

Egyetlen régión Cassandra fürtkonfiguráció:

| Fürt paraméter | Érték | Megjegyzések |
| --- | --- | --- |
| (N) csomópontok száma |8 |A fürtben található csomópontok száma |
| Replikációs tényező (RF) |3 |Adott sor replikák száma |
| Konzisztenciaszint (írás) |QUORUM[(RF/2) +1) = 2] az eredmény a képlet lefelé kerekíti |Legfeljebb 2 replikával ír a hívónak; a válasz elküldése előtt 3. a replika idővel konzisztenssé módon írása. |
| Konzisztenciaszint (olvasás) |KVÓRUM [(RF/2) + 1 = 2] kerekíti a képlet eredménye |2 replikával beolvassa a hívónak elküldése előtt. |
| Replikációs stratégia |NetworkTopologyStrategy lásd [adatreplikáció](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) Cassandra dokumentációjában talál további információt a |A telepítési topológia megértette és replikák csomóponton helyezi el, hogy minden replika az azonos állványra a nem végződhet |
| Snitch |Lásd a GossipingPropertyFileSnitch [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) Cassandra dokumentációjában talál további információt a |NetworkTopologyStrategy snitch egy fogalom megértéséhez a topológia használja. GossipingPropertyFileSnitch az egyes csomópontok leképezése a adatközpont és állvány nagyobb ellenőrzést biztosít. A fürt pletykákat használja fel ezeket az információkat terjesztése. Ez az jóval egyszerűbb, a dinamikus IP-beállítása PropertyFileSnitch viszonyítva |

**Cassandra fürt Azure szempontjai:** Microsoft Azure virtuális gépek funkció Azure Blob Storage tárolót használja a lemez megőrzéséhez; Az Azure Storage minden lemez a magas tartósság 3 replikák menti. Ez azt jelenti, hogy minden egyes soraiban levő adatok, a Cassandra táblába 3 replikák már tárolja, és ezért adatkonzisztencia van már hozott megvagyunk akkor is, ha a replikációs tényező (RF) 1. A fő replikációs tényező alatt 1 probléma, hogy az az alkalmazás leállás következik, még akkor is, ha egy önálló Cassandra csomópont meghibásodik. Azonban ha egy csomópont le a Azure Fabric Controller által felismert problémákat (például a hardver, a szoftver rendszerhibák), akkor kiépíti a azonos tárolási-meghajtókat használ helyette egy új csomópont. Lecseréli a régi egy olyan új csomópont kiépítés néhány percet is igénybe vehet.  Tervezett karbantartás tevékenységek, például a vendég operációs rendszer módosításokat, hasonlóképpen Cassandra frissíti, és a alkalmazás Azure Fabric Controller hajtja végre a működés közbeni frissítés a csomópontok a fürtben.  Működés közbeni frissítés is is igénybe vehet néhány csomópont le egyszerre, és ezért a fürt rövid idejű leállást néhány partíciók problémákat tapasztalhat. Azonban az adatok nem elvesznek a beépített Azure adattároló redundanciája, amely miatt.  

A telepített Azure-ba, amelyek nem igényelnek magas rendelkezésre állású rendszerek (pl. körülbelül 99,9 amely egyenértékű 8.76 óra/év; lásd: [magas rendelkezésre állású](http://en.wikipedia.org/wiki/High_availability) részletes) esetleg RF futtatásához = 1 és Konzisztenciaszint = egyik.  A magas rendelkezésre állású KÖVETELMÉNYŰ RF alkalmazások = 3 és Konzisztenciaszint = KVÓRUM egyik csomópontján egy a replikák lefelé idején fog működését. RF = 1, a hagyományos telepítések (pl. a helyszíni) a problémák, mint például a lemezek meghibásodása eredő esetleges adatvesztés miatt nem használható.   

## <a name="multi-region-deployment"></a>Több területi központi telepítés
Cassandra tartozó adatok-center-kompatibilis replikációs és konzisztencia modell a fent leírt segít a több területi telepítés kívül a lista bármely külső tooling szükségessége nélkül. Ez eltér elég a hagyományos, relációs adatbázisok ahol adatbázis-tükrözés a több főkiszolgálós írások telepítőprogramja elég bonyolult lehet. Állítson be több régióban Cassandra is segítséget nyújt a használati forgatókönyvei többek között a következőket:

**Közelségi kapcsolat alapú központi telepítés:** több-bérlős alkalmazásokhoz, és törölje a jelet a bérlő felhasználók-az-régió, is származott, a több területi fürt alacsony késleltetésű által. Például egy tanulási felügyeleti rendszerek oktatási intézményeknél a megfelelő kampuszok kiszolgálására USA keleti régiója, és az USA nyugati régiója régiókban a elosztott fürtök telepítése tranzakciós, továbbá az elemzés. Az adatok lehetnek helyileg egységes idő olvasási és írási műveletek és idővel konzisztenssé mindkét a régiók között. További példák mint az adathordozó terjesztési, elektronikus kereskedelmi és semmit, és koncentrált földrajzi felhasználói alap látja, hogy egy megfelelő használati eset a központi telepítési modell.

**Magas rendelkezésre állás:** redundancia szoftverek és hardverek magas rendelkezésre állás elérése kulcsfontosságú tényező; a részleteket lásd a Microsoft Azure épület megbízható a felhőalapú rendszerek. A Microsoft Azure-ban a csak megbízható igaz redundancia elérésének módja több területi fürt üzembe helyezésével. Alkalmazások is telepíthető egy aktív-aktív vagy aktív-passzív módban, és ha a régiók egyikéhez sem működik, Azure Traffic Manager forgalom irányíthatja át az aktív terület.  Az egyetlen régión üzemelő példányhoz, ha a rendelkezésre állás 99,9, a két-régió központi telepítés el tud érni a képlettel kiszámított 99.9999 a rendelkezésre állási: (1-(1-0.999) * (1-0.999)) * 100); olvassa el a fenti részleteiről.

**Vész-helyreállítási:** több területi Cassandra fürt, ha megfelelően, is képes elviselni katasztrofális data center kimaradások esetén. Ha egy régió nem működik, más régiókban központi telepítésű alkalmazás indításához szolgál a végfelhasználók számára. Bármely más üzleti folytonossági megvalósításokhoz, például az alkalmazás nem lehet az adatokat a aszinkron feldolgozási eredő adatvesztést a hibatűrő. Cassandra azonban a helyreállítási sokkal zavartalanabbá, mint a hagyományos adatbázis helyreállítási folyamatok által igénybe vett idő. A 2. ábrán minden régióban nyolc csomópontokkal tipikus több területi telepítési modelljét mutatja. Mindkét régiók a következők tükör képek minden más ugyanazon szimmetriasíkjához; valós tervek munkaterhelésének típusát (pl. tranzakciós vagy analitikai), a helyreállítási Időkorlát, RTO, adatkonzisztenciát és rendelkezésre állási követelmények függ.

![Több régióban központi telepítés](./media/cassandra-nodejs/cassandra-linux2.png)

2. ábra: Több területi Cassandra központi telepítés

### <a name="network-integration"></a>Hálózati integráció
Készlet két régiókban található magánhálózatokhoz telepített virtuális gépek egymástól a VPN-alagút segítségével kommunikál. A VPN-alagút két szoftver átjárók a hálózati telepítési folyamat során létesített kapcsolatot. Mindkét memóriaterületnél hasonló hálózati architektúra szempontjából "web" és "data" alhálózat; Az Azure hálózatkezelés lehetővé teszi, hogy a lehető legtöbb alhálózatok igény szerint létrehozása, és alkalmazni a hozzáférés-vezérlési listákat, igényei szerint hálózati biztonság. A fürt topológia tervezése során többek data center kommunikációs késés és a hálózati forgalmat kell figyelembe kell venni a gazdasági hatását.

### <a name="data-consistency-for-multi-data-center-deployment"></a>Adatok konzisztenciájának több adatközpont központi telepítéshez
Az elosztott központi telepítések kell figyelembe vennie átviteli sebesség és a magas rendelkezésre állású fürt topológia hatását. A RF és Konzisztenciaszint kell megadni, hogy a kvórum sem függ a adatközpontok rendelkezésre állását módon.
A rendszer, amelyet a magas konzisztencia egy LOCAL_QUORUM konzisztencia szint (az olvasási és írási) fog győződjön meg arról, hogy a helyi olvasási és írási teljesülnek a helyi csomópontjáról adatok aszinkron módon replikálja a távoli adatközpontokban.  2. táblázat összefoglalja a több területi fürt be az írás leírt konfigurációs részleteit.

**A két-régió Cassandra fürtkonfiguráció**

| Fürt paraméter | Érték | Megjegyzések |
| --- | --- | --- |
| (N) csomópontok száma |8 + 8 |A fürtben található csomópontok száma |
| Replikációs tényező (RF) |3 |Adott sor replikák száma |
| Konzisztenciaszint (írás) |LOCAL_QUORUM [(sum(RF)/2) +1) = 4] kerekíti a képlet eredménye |2 csomópontok rendszer úgy tünteti fel az első adatközpont szinkron módon történik; a kvórum szükséges további 2 csomópontok aszinkron módon történik a 2. az Adatközpont fog szerepelni. |
| Konzisztenciaszint (olvasás) |LOCAL_QUORUM ((RF/2) + 1) a képlet eredménye lefelé kerekíti 2 = |Olvasási kérések teljesülnek a csak egy régió tartozik; 2 csomópontok olvasható az ügyfélnek a válasz elküldése előtt. |
| Replikációs stratégia |NetworkTopologyStrategy lásd [adatreplikáció](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureDataDistributeReplication_c.html) Cassandra dokumentációjában talál további információt a |A telepítési topológia megértette és replikák csomóponton helyezi el, hogy minden replika az azonos állványra a nem végződhet |
| Snitch |Lásd a GossipingPropertyFileSnitch [Snitches](http://www.datastax.com/documentation/cassandra/2.0/cassandra/architecture/architectureSnitchesAbout_c.html) Cassandra dokumentációjában talál további információt a |NetworkTopologyStrategy snitch egy fogalom megértéséhez a topológia használja. GossipingPropertyFileSnitch az egyes csomópontok leképezése a adatközpont és állvány nagyobb ellenőrzést biztosít. A fürt pletykákat használja fel ezeket az információkat terjesztése. Ez az jóval egyszerűbb, a dinamikus IP-beállítása PropertyFileSnitch viszonyítva |

## <a name="the-software-configuration"></a>A SZOFTVERKONFIGURÁCIÓJÁNAK ÖSSZEÁLLÍTÁSA
Az alábbi szoftververziók a telepítés során használt:

<table>
<tr><th>Szoftver</th><th>Forrás</th><th>Verzió</th></tr>
<tr><td>JRE    </td><td>[8 JRE](http://www.oracle.com/technetwork/java/javase/downloads/server-jre8-downloads-2133154.html) </td><td>8U5</td></tr>
<tr><td>JNA    </td><td>[JNA](https://github.com/twall/jna) </td><td> 3.2.7</td></tr>
<tr><td>Cassandra</td><td>[Apache Cassandra 2.0.8](http://www.apache.org/dist/cassandra/2.0.8/apache-cassandra-2.0.8-bin.tar.gz)</td><td> 2.0.8</td></tr>
<tr><td>Ubuntu    </td><td>[A Microsoft Azure](https://azure.microsoft.com/) </td><td>14.04 LTS</td></tr>
</table>

Az Oracle licencfeltételeinek elfogadását manuális JRE letöltése szükséges, egyszerűbbé teheti a központi telepítést, töltse le a szükséges szoftverek később feltöltése a Ubuntu sablon lemezképpel azt létrehozni a fürtöt tartalmazó környezetben való előanyagát, az asztalon.

A fenti szoftverek letöltési könyvtárba, amely egy jól ismert letöltési (pl. Windows %TEMP%/downloads vagy ~/Downloads legtöbb Linux terjesztésekről vagy Mac) a helyi számítógépen.

### <a name="create-ubuntu-vm"></a>UBUNTU VIRTUÁLIS GÉP LÉTREHOZÁSA
Ebben a lépésben a folyamat létrehozunk Ubuntu kép az előfeltételt jelentő szoftvereket az, hogy a kép felhasználhatók számos Cassandra csomópont történő üzembe helyezéséhez.  

#### <a name="step-1-generate-ssh-key-pair"></a>1. lépés: Az SSH-kulcspárral létrehozása
Azure egy nyilvános kulcsot, amely PEM vagy DER kódolású létesítési időpontjában X509 kér. A el az SSH Linux Azure-on található utasításokat követve nyilvános/titkos kulcspárt létre. Ha tervezi putty.exe Windows vagy Linux SSH-ügyfélként, hogy alakítsa át a PEM-kódolású RSA titkos kulcs PPK formátum használatával puttygen.exe; Ehhez útmutatást a fenti weblapon találhatók.

#### <a name="step-2-create-ubuntu-template-vm"></a>2. lépés: Sablon Ubuntu virtuális gép
A Virtuálisgép-sablon létrehozásához jelentkezzen be a klasszikus Azure portálra, és kövesse az alábbi eljárást: új, számítási, virtuális gép, FROM GYŰJTEMÉNYE, UBUNTU, Ubuntu Server 14.04 LTS kattintson, majd a jobbra mutató nyílra. Ez az oktatóanyag ismerteti, hogyan hozzon létre egy Linux virtuális Gépet hozzon létre egy virtuális gép futó Linux talál.

Adja meg a következő információkat a "virtuálisgép-konfiguráció" képernyőn #1:

<table>
<tr><th>MEZŐ NEVE              </td><td>       MEZŐÉRTÉK               </td><td>         MEGJEGYZÉSEK                </td><tr>
<tr><td>VERZIÓ KIADÁSI DÁTUM    </td><td> Egy dátumot a legördülő menüből válassza le</td><td></td><tr>
<tr><td>VIRTUÁLIS GÉP NEVE    </td><td> esetén-sablon                   </td><td> Ez az a virtuális gép állomásnevét </td><tr>
<tr><td>RÉTEG                     </td><td> STANDARD                           </td><td> Hagyja meg az alapértelmezett              </td><tr>
<tr><td>MÉRET                     </td><td> A1                              </td><td>Válassza ki a virtuális Gépet a IO igényeinek megfelelően; erre a célra hagyja meg az alapértelmezett </td><tr>
<tr><td> ÚJ FELHASZNÁLÓ NEVE             </td><td> localadmin                       </td><td> "rendszergazda" az egyetlen foglalt felhasználónévvel Ubuntu 12. xx és után</td><tr>
<tr><td> HITELESÍTÉS         </td><td> Jelölje be jelölőnégyzetet                 </td><td>Ellenőrizze, hogy szeretné-e az SSH-kulcs biztonságos </td><tr>
<tr><td> TANÚSÍTVÁNY             </td><td> a nyilvános kulcsú tanúsítvány fájlneve </td><td> A korábban létrehozott nyilvános kulcs</td><tr>
<tr><td> Új jelszó    </td><td> erős jelszó </td><td> </td><tr>
<tr><td> Jelszó megerősítése    </td><td> erős jelszó </td><td></td><tr>
</table>

Adja meg a következő információkat a "virtuálisgép-konfiguráció" képernyőn #2:

<table>
<tr><th>MEZŐ NEVE             </th><th> MEZŐÉRTÉK                       </th><th> MEGJEGYZÉSEK                                 </th></tr>
<tr><td> A FELHŐALAPÚ SZOLGÁLTATÁS    </td><td> Új felhőalapú szolgáltatás létrehozása    </td><td>Felhőszolgáltatás egy tároló számítási erőforrásokhoz, mint a virtuális gépek</td></tr>
<tr><td> FELHŐALAPÚ SZOLGÁLTATÁS DNS-NÉV    </td><td>ubuntu-template.cloudapp.net    </td><td>Adjon meg egy gép független terheléselosztó neve</td></tr>
<tr><td> RÉGIÓ/AFFINITÁSCSOPORT/VIRTUÁLIS HÁLÓZAT </td><td>    USA nyugati régiója    </td><td> Válasszon ki egy régiót, ahol a webalkalmazások érje el a Cassandra</td></tr>
<tr><td>TÁRFIÓK </td><td>    Alapértelmezett használata    </td><td>Az alapértelmezett tárfiók vagy egy korábban létrehozott tárfiókot használja az adott</td></tr>
<tr><td>A RENDELKEZÉSRE ÁLLÁSI CSOPORT </td><td>    None </td><td>    Hagyja üresen</td></tr>
<tr><td>VÉGPONTOK    </td><td>Alapértelmezett használata </td><td>    Az alapértelmezett SSH-konfigurációt használja. </td></tr>
</table>

Kattintson a jobbra mutató nyílra, a #3 képernyőn hagyja meg az alapértelmezett beállításokat, és kattintson az "ellenőrzés" gombra a Virtuálisgép-létrehozásnál folyamat befejezéséhez. Néhány perc múlva a virtuális Géphez a neve "ubuntu-template" a "fut" állapotú kell lennie.

### <a name="install-the-necessary-software"></a>A SZÜKSÉGES SZOFTVER TELEPÍTÉSÉHEZ
#### <a name="step-1-upload-tarballs"></a>1. lépés: Feltöltés tarballs
A következő parancs formátumban ~/downloads directory scp vagy pscp használ, másolja a korábban letöltött szoftverfrissítések:

##### <a name="pscp-server-jre-8u5-linux-x64targz-localadminhk-cas-templatecloudappnethomelocaladmindownloadsserver-jre-8u5-linux-x64targz"></a>pscp kiszolgáló-jre-8u5-linux-x64.tar.gzlocaladmin@hk-cas-template.cloudapp.net:/home/localadmin/downloads/server-jre-8u5-linux-x64.tar.gz
Ismételje meg a fenti parancs JRE, valamint a Cassandra bits esetében.

#### <a name="step-2-prepare-the-directory-structure-and-extract-the-archives"></a>2. lépés: Készítse elő a könyvtárstruktúra, és bontsa ki az archívumban
Jelentkezzen be a virtuális gép és a könyvtárstruktúra létrehozása, és bontsa ki a szoftver felügyelőként a bash az alábbi parancsfájlt:

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


Ha vim ablakba be ezt a parancsfájlt, ügyeljen arra, hogy távolítsa el a kocsivissza ("\r") a következő parancsot:

    tr -d '\r' <infile.sh >outfile.sh

#### <a name="step-3-edit-etcprofile"></a>3. lépés: Stb/profil szerkesztése
A következő végén hozzáfűzése:

    JAVA_HOME=/opt/java/jdk1.8.0_05
    CASS_HOME= /opt/cassandra/apache-cassandra-2.0.8
    PATH=$PATH:$HOME/bin:$JAVA_HOME/bin:$CASS_HOME/bin
    export JAVA_HOME
    export CASS_HOME
    export PATH

#### <a name="step-4-install-jna-for-production-systems"></a>4. lépés: Telepítse JNA éles rendszerek esetén
A következő parancssort használja: A következő parancsot a jna-3.2.7.jar és jna-platform-3.2.7.jar /usr/share.java könyvtárba sudo apt-get libjna-java

Hozza létre a szimbolikus csatolást $CASS_HOME/lib könyvtárban, hogy Cassandra indítási parancsfájl megtalálhassa a JAR-fájlok kivételével:

    ln -s /usr/share/java/jna-3.2.7.jar $CASS_HOME/lib/jna.jar

    ln -s /usr/share/java/jna-platform-3.2.7.jar $CASS_HOME/lib/jna-platform.jar

#### <a name="step-5-configure-cassandrayaml"></a>5. lépés: Cassandra.yaml konfigurálása
Az egyes virtuális gépek megfelelően [azt fogja tudjon végezni ezzel a tényleges kiépítése során] összes virtuális gép által igényelt konfigurációs cassandra.yaml szerkesztése:

<table>
<tr><th>Mező neve   </th><th> Érték  </th><th>    Megjegyzések </th></tr>
<tr><td>fürtnév </td><td>    "CustomerService"    </td><td> A nevet válasszon, amely tükrözi a központi telepítés</td></tr>
<tr><td>listen_address    </td><td>[hagyja üresen a mezőt]    </td><td> Törölje a "localhost" </td></tr>
<tr><td>rpc_addres   </td><td>[hagyja üresen a mezőt]    </td><td> Törölje a "localhost" </td></tr>
<tr><td>magok    </td><td>"10.1.2.4, 10.1.2.6, 10.1.2.8"    </td><td>Minden a magok jelöli a rendszer IP-címek listáját.</td></tr>
<tr><td>endpoint_snitch </td><td> org.apache.cassandra.locator.GossipingPropertyFileSnitch </td><td> A NetworkTopologyStrateg által szolgál az adatközpontban és a virtuális gép állvány behívásakor</td></tr>
</table>

#### <a name="step-6-capture-the-vm-image"></a>6. lépés: A virtuális gép lemezképének rögzítése
Jelentkezzen be a virtuális gépet az állomásnév (hk-cas-template.cloudapp.net) és a korábban létrehozott titkos SSH-kulcs használatával. Tekintse meg az SSH használata az Azure-ral való bejelentkezéshez használja a parancsot ssh vagy putty.exe Linux hogyan.

A következő feladatütemezési lépéssel a lemezkép rögzítése hajtható végre:

##### <a name="1-deprovision"></a>1. Deprovision
A paranccsal "sudo waagent-deprovision + felhasználói" virtuálisgép-példányt adott adatok eltávolítása. Tekintse át a vonatkozó [egy Linux virtuális gép rögzítése](capture-image.md) sablonként használatára további részleteket a lemezkép rögzítését.

##### <a name="2-shutdown-the-vm"></a>2: a virtuális gép leállítása
Győződjön meg arról, hogy a virtuális gép ki van jelölve, és kattintson a LEÁLLÍTÁS hivatkozásra az alsó parancs segítségével.

##### <a name="3-capture-the-image"></a>3: a lemezképének rögzítése
Győződjön meg arról, hogy a virtuális gép ki van jelölve, és az utolsó parancs segítségével a rögzítési hivatkozásra. A következő képernyőn nevezze el egy kép (pl. hk-cas-2-08-ub-14-04-2014071), a megfelelő RENDSZERKÉP leírása, és kattintson az "ellenőrzés" jelre a rögzítési folyamat befejezéséhez.

Ez eltarthat néhány másodpercig, és a lemezkép elérhetőnek kell lennie a kép tár a saját LEMEZKÉPEK szakaszban. A forrás virtuális gép automatikusan után törlődni fognak a lemezkép rögzítése sikerült. 

## <a name="single-region-deployment-process"></a>Egyetlen régión telepítési folyamata
**1. lépés: A virtuális hálózat létrehozása** jelentkezzen be az Azure portálon, és hozzon létre egy virtuális hálózat (klasszikus) az alábbi táblázatban szereplő attribútumait. Lásd: [virtuális hálózat létrehozása (klasszikus) Azure-portálon](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a részletes lépéseket a folyamat.      

<table>
<tr><th>VM-attribútum neve</th><th>Érték</th><th>Megjegyzések</th></tr>
<tr><td>Név</td><td>vnet-esetén-nyugati-us</td><td></td></tr>
<tr><td>Régió</td><td>USA nyugati régiója</td><td></td></tr>
<tr><td>DNS-kiszolgálók</td><td>None</td><td>Figyelmen kívül hagyja ezt a DNS-kiszolgáló nem használjuk</td></tr>
<tr><td>Címterület</td><td>10.1.0.0/16</td><td></td></tr>    
<tr><td>Kezdő IP-Címét</td><td>10.1.0.0</td><td></td></tr>    
<tr><td>CIDR </td><td>/16 (65531)</td><td></td></tr>
</table>

Adja hozzá a következő alhálózatok:

<table>
<tr><th>Név</th><th>Kezdő IP-Címét</th><th>CIDR</th><th>Megjegyzések</th></tr>
<tr><td>webalkalmazás</td><td>10.1.1.0</td><td>/24 (251)</td><td>A webfarm-alhálózatot</td></tr>
<tr><td>Adatok</td><td>10.1.2.0</td><td>/24 (251)</td><td>Az adatbázis-csomópont-alhálózatot</td></tr>
</table>

Adatok és a webes alhálózatok hálózati biztonsági csoportokkal, ez a cikk a hatókörén kívül esik a körét, amelyek védhetők.  

**2. lépés: Kiépítése virtuális gépek** a korábban létrehozott rendszerkép használatával, rendszer a következő virtuális gépek létrehozása a felhő kiszolgálón "hk-c-svc-nyugati" és a megfelelő alhálózatokban alább látható módon köthető:

<table>
<tr><th>Gépnév    </th><th>Alhálózat    </th><th>IP-cím    </th><th>Rendelkezésre állási csoport</th><th>DC/Rack</th><th>Kezdőérték?</th></tr>
<tr><td>HK-c1-nyugati-us    </td><td>Adatok    </td><td>10.1.2.4    </td><td>HK-c-aset-1    </td><td>DC = WESTUS állvány = rack1 </td><td>Igen</td></tr>
<tr><td>HK-c2-nyugati-us    </td><td>Adatok    </td><td>10.1.2.5    </td><td>HK-c-aset-1    </td><td>DC = WESTUS állvány = rack1    </td><td>Nem </td></tr>
<tr><td>HK-c3-nyugati-us    </td><td>Adatok    </td><td>10.1.2.6    </td><td>HK-c-aset-1    </td><td>DC = WESTUS állvány = rack2    </td><td>Igen</td></tr>
<tr><td>HK-c4-nyugati-us    </td><td>Adatok    </td><td>10.1.2.7    </td><td>HK-c-aset-1    </td><td>DC = WESTUS állvány = rack2    </td><td>Nem </td></tr>
<tr><td>HK-c5-nyugati-us    </td><td>Adatok    </td><td>10.1.2.8    </td><td>HK-c-aset-2    </td><td>DC = WESTUS állvány = rack3    </td><td>Igen</td></tr>
<tr><td>HK-c6-nyugati-us    </td><td>Adatok    </td><td>10.1.2.9    </td><td>HK-c-aset-2    </td><td>DC = WESTUS állvány = rack3    </td><td>Nem </td></tr>
<tr><td>HK-c7-nyugati-us    </td><td>Adatok    </td><td>10.1.2.10    </td><td>HK-c-aset-2    </td><td>DC = WESTUS állvány = rack4    </td><td>Igen</td></tr>
<tr><td>HK-c8-nyugati-us    </td><td>Adatok    </td><td>10.1.2.11    </td><td>HK-c-aset-2    </td><td>DC = WESTUS állvány = rack4    </td><td>Nem </td></tr>
<tr><td>HK-F1-nyugati-us    </td><td>webalkalmazás    </td><td>10.1.1.4    </td><td>HK-w-aset-1    </td><td>                       </td><td>N/A</td></tr>
<tr><td>HK-w2-nyugati-us    </td><td>webalkalmazás    </td><td>10.1.1.5    </td><td>HK-w-aset-1    </td><td>                       </td><td>N/A</td></tr>
</table>

A fenti listában található virtuális gépek létrehozásához a következő folyamat van szükség:

1. Az adott üres felhőalapú szolgáltatás létrehozása
2. Hozzon létre egy virtuális Gépet a korábban rögzített lemezképből, és csatlakoztassa a virtuális hálózat létrehozása korábban; Ismételje meg ezt a virtuális gépek
3. A belső terheléselosztók hozzáadása a felhőalapú szolgáltatás, és a "data" alhálózati csatlakoztatása
4. A korábban létrehozott virtuális gépek hozzáadása egy elosztott terhelésű végpont keresztül csatlakozik a korábban létrehozott belső terheléselosztóhoz elosztott terhelésű készlet thrift-forgalom

A fenti folyamatot használja a klasszikus Azure portálon; hajtható végre egy Windows-számítógép (használja a virtuális gép Azure, ha nem rendelkezik hozzáféréssel a Windows-számítógép), használja a következő PowerShell-parancsfájl segítségével minden 8 virtuális gépek automatikus kiépítéséhez.

**Lista 1: Virtuális gépek rendszerbe állításához PowerShell-parancsfájl**

        #Tested with Azure Powershell - November 2014
        #This powershell script deployes a number of VMs from an existing image inside an Azure region
        #Import your Azure subscription into the current Powershell session before proceeding
        #The process: 1. create Azure Storage account, 2. create virtual network, 3.create the VM template, 2. crate a list of VMs from the template

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
        #Add add the thrift endpoint to the internal load balancer for all the VMs
        foreach($vmName in $vmNames)
        {
            Get-AzureVM -ServiceName $serviceName -Name $vmName |
                Add-AzureEndpoint -Name $thriftEndPoint -LBSetName "ThriftLBSet" -Protocol tcp -LocalPort 9160 -PublicPort 9160 -ProbePort 9160 -ProbeProtocol tcp -ProbeIntervalInSeconds 10 -InternalLoadBalancerName $ilbName |
                Update-AzureVM

            Write-Host "created $vmName"     
        }

**3. lépés: Cassandra konfigurálása az egyes virtuális gépek**

Jelentkezzen be a virtuális Gépet, és hajtsa végre a következő:

* A data center és állvány tulajdonságainak $CASS_HOME/conf/cassandra-rackdc.properties szerkesztése:
  
       dc =EASTUS, rack =rack1
* Kezdőérték csomópontok beállítása az alábbi cassandra.yaml szerkesztése:
  
       Seeds: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10"

**4. lépés: Indítsa el a virtuális gépek és a fürt tesztelése**

Jelentkezzen be az egyik csomóponton (pl. hk-c1-nyugati-us), és a fürt állapotának megtekintéséhez a következő parancsot:

       nodetool –h 10.1.2.4 –p 7199 status

A képernyőt a hasonló alatt a 8 csomópontos fürtök kell megjelennie:

<table>
<tr><th>status</th><th>Cím    </th><th>Betöltés    </th><th>Tokenek    </th><th>Tulajdonos </th><th>Állomás azonosítója    </th><th>Állvány</th></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.4     </td><td>87.81 KB    </td><td>256    </td><td>38.0%    </td><td>GUID (eltávolítani)</td><td>rack1</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.5     </td><td>41.08 KB    </td><td>256    </td><td>68.9%    </td><td>GUID (eltávolítani)</td><td>rack1</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.6     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (eltávolítani)</td><td>rack2</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.7     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (eltávolítani)</td><td>rack2</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.8     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (eltávolítani)</td><td>rack3</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.9     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (eltávolítani)</td><td>rack3</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.10     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (eltávolítani)</td><td>rack4</td></tr>
<tr><th>VISSZAVONÁSA    </td><td>10.1.2.11     </td><td>55.29 KB    </td><td>256    </td><td>68.8%    </td><td>GUID (eltávolítani)</td><td>rack4</td></tr>
</table>

## <a name="test-the-single-region-cluster"></a>Az egyetlen régión fürt tesztelése
A fürt ellenőrzéséhez tegye a következőket:

1. (Pl. a Powershell-parancs Get-AzureInternalLoadbalancer parancsmag segítségével használja, szerezze be a belső terheléselosztó IP-címe  10.1.2.101). A parancs szintaxisa alább látható: Get-AzureLoadbalancer – szolgáltatásnév "hk-c-svc-nyugati-us" [megjeleníti a részletek a belső terheléselosztó mellett az IP-címe]
2. Jelentkezzen be a virtuális gép (pl. hk-F1-nyugati-us) webfarm a Putty használatával vagy ssh
3. Végrehajtás $CASS_HOME/bin/cqlsh 10.1.2.101 9160
4. A következő CQL parancsokkal ellenőrizheti, hogy működik-e a fürt:
   
     Kulcstérértesítések használatával hozzon létre customers_ks rendelkező replikációs = {"class": "SimpleStrategy", "replication_factor": 3};   HASZNÁLJA a customers_ks;   Hozzon létre a tábla Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Helyezze be a Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Helyezze be a Customers(customer_id, firstname, lastname) értékek (2, "Jane", "Doe").
   
     Válassza ki * ügyfelek;

Például az alábbi megjelenítésre kell megjelennie:

<table>
  <tr><th> customer_id </th><th> Utónév </th><th> Vezetéknév </th></tr>
  <tr><td> 1 </td><td> Jakab </td><td> Gipsz </td></tr>
  <tr><td> 2 </td><td> Jane </td><td> Gipsz </td></tr>
</table>

Vegye figyelembe, hogy a 4. lépésében létrehozott kulcstérértesítések használatával egy replication_factor 3 SimpleStrategy használ. SimpleStrategy ajánlott egyetlen data center központi telepítések mivel több adatok NetworkTopologyStrategy center központi telepítések. A / 3 replication_factor csomópont hibák tolerancia rendszerében.

## <a id="tworegion"></a>Több területi telepítési folyamata
A rendszer használja ki a egyetlen régión telepítése befejeződött, és ugyanezt az eljárást ismételje meg a második régió telepítése. Az egyetlen és több régió telepítési közötti fő különbség a VPN-alagút beállítást régió közti kommunikációhoz; rendszer a hálózati telepítéssel, a virtuális gépek telepítéséhez és konfigurálásához Cassandra.

### <a name="step-1-create-the-virtual-network-at-the-2nd-region"></a>1. lépés: A virtuális hálózat létrehozása a 2. terület:
Jelentkezzen be a klasszikus Azure portálra, és hozzon létre egy virtuális hálózatot az attribútumok megjelenítése a táblában. Lásd: [Cloud-Only virtuális hálózat konfigurálása a klasszikus Azure portálon](../../../virtual-network/virtual-networks-create-vnet-classic-pportal.md) a részletes lépéseket a folyamat.      

<table>
<tr><th>Attribútum neve    </th><th>Érték    </th><th>Megjegyzések</th></tr>
<tr><td>Név    </td><td>vnet-esetén-keleti-us</td><td></td></tr>
<tr><td>Régió    </td><td>USA keleti régiója</td><td></td></tr>
<tr><td>DNS-kiszolgálók        </td><td></td><td>Figyelmen kívül hagyja ezt a DNS-kiszolgáló nem használjuk</td></tr>
<tr><td>A pont-pont VPN konfigurálása</td><td></td><td>        Figyelmen kívül hagyja ezt</td></tr>
<tr><td>Webhelyek közötti virtuális magánhálózat konfigurálása</td><td></td><td>        Figyelmen kívül hagyja ezt</td></tr>
<tr><td>Címterület    </td><td>10.2.0.0/16</td><td></td></tr>
<tr><td>Kezdő IP-Címét    </td><td>10.2.0.0    </td><td></td></tr>
<tr><td>CIDR    </td><td>/16 (65531)</td><td></td></tr>
</table>

Adja hozzá a következő alhálózatok:

<table>
<tr><th>Név    </th><th>Kezdő IP-Címét    </th><th>CIDR    </th><th>Megjegyzések</th></tr>
<tr><td>webalkalmazás    </td><td>10.2.1.0    </td><td>/24 (251)    </td><td>A webfarm-alhálózatot</td></tr>
<tr><td>Adatok    </td><td>10.2.2.0    </td><td>/24 (251)    </td><td>Az adatbázis-csomópont-alhálózatot</td></tr>
</table>


### <a name="step-2-create-local-networks"></a>2. lépés: A helyi hálózatok létrehozása
Az Azure virtuális hálózatban egy helyi hálózaton, egy proxy címtartományt, amely egy magánfelhőbe vagy egy másik Azure-régió, beleértve egy távoli helyre van leképezve. A proxy címtartomány a megfelelő hálózati helyre egy távoli átjáró útválasztási hálózathoz van kötve. Lásd: [konfigurálása egy VNet és Kapcsolatcsoporttal](../../../vpn-gateway/virtual-networks-configure-vnet-to-vnet-connection.md) kapcsolatos utasításokat a VNET – VNET-kapcsolatot létesít.

Hozzon létre két helyi hálózatok száma a következő adatokat:

| Hálózati név | VPN-átjáró címét | Címterület | Megjegyzések |
| --- | --- | --- | --- |
| HK-lnet-Map-to-East-us |23.1.1.1 |10.2.0.0/16 |A helyi hálózat létrehozásakor adjon címet az átjáró egy helyőrző. Az átjáró létrehozása után a tényleges átjárócím ki van töltve. Győződjön meg arról, hogy a címtartomány pontosan egyezik a megfelelő távoli virtuális hálózat; Ebben az esetben a virtuális hálózat létrehozása az USA keleti régiójában. |
| HK-lnet-Map-to-West-us |23.2.2.2 |10.1.0.0/16 |A helyi hálózat létrehozásakor adjon címet az átjáró egy helyőrző. Az átjáró létrehozása után a tényleges átjárócím ki van töltve. Győződjön meg arról, hogy a címtartomány pontosan egyezik a megfelelő távoli virtuális hálózat; Ebben az esetben a virtuális hálózat az USA nyugati régiója régióban létrehozott. |

### <a name="step-3-map-local-network-to-the-respective-vnets"></a>3. lépés: Térkép "Helyi" hálózat a megfelelő Vnetek
A klasszikus Azure portálon minden egyes virtuális hálózat kiválasztása, kattintson a "Beállítása", ellenőrizze a "Csatlakozás a helyi hálózatra", majd a következő adatok egy helyi hálózatok kiválasztása:

| Virtual Network | Helyi hálózati |
| --- | --- |
| HK-vnet-nyugati-us |HK-lnet-Map-to-East-us |
| HK-vnet-keleti-us |HK-lnet-Map-to-West-us |

### <a name="step-4-create-gateways-on-vnet1-and-vnet2"></a>4. lépés: A VNET1 és VNET2 átjárók létrehozása
A virtuális hálózatok az irányítópultról kattintson a létrehozása ÁTJÁRÓN, amely akkor indul el, a VPN-átjáró létesítésének folyamatát kell használnia. Néhány perc elteltével az irányítópult az összes virtuális hálózat megjelenjen-e a tényleges átjárócímet.

### <a name="step-5-update-local-networks-with-the-respective-gateway-addresses"></a>5. lépés: Frissítés "Helyi" hálózatok a megfelelő "Gateway" címek
Mindkét a helyi hálózat a helyőrző átjáró IP-címet lecseréli az imént telepített átjárók valós IP-címének szerkesztése. Az alábbi megfeleltetést a használja:

<table>
<tr><th>Helyi hálózati    </th><th>Virtuális hálózati átjáró</th></tr>
<tr><td>HK-lnet-Map-to-East-us </td><td>A hk-vnet-nyugati-us átjáró</td></tr>
<tr><td>HK-lnet-Map-to-West-us </td><td>A hk-vnet-keleti-us átjáró</td></tr>
</table>

### <a name="step-6-update-the-shared-key"></a>6. lépés: A megosztott kulcs frissítése
A következő Powershell-parancsfájl segítségével frissíteni az IPSec-kulcsot minden egyes VPN-átjáró [használatát mindkét átjáró szakét kulcsa]: Set-AzureVNetGatewayKey - VNetName hk-vnet-keleti-us - LocalNetworkSiteName hk-lnet-map-to-west-us - SharedKey D9E76BKK Set-AzureVNetGatewayKey - VNetName hk-vnet-nyugati-us - LocalNetworkSiteName hk-lnet-map-to-east-us - SharedKey D9E76BKK

### <a name="step-7-establish-the-vnet-to-vnet-connection"></a>7. lépés: A VNET – VNET-kapcsolatot létrehozni.
A klasszikus Azure portálon az "IRÁNYÍTÓPULT" menü, mind a virtuális hálózatok használatával átjárók közötti kapcsolat létrehozásához. Az alsó eszköztár használja a "Csatlakozás" menü menüpontjai. Néhány perc elteltével az irányítópult megjelenjen-e a kapcsolat adatai grafikusan.

### <a name="step-8-create-the-virtual-machines-in-region-2"></a>8. lépés: A virtuális gépek létrehozása régióban #2
Ubuntu lemezkép létrehozásához a következő ugyanazokat a lépéseket vagy másolása a kép VHD-fájlt az Azure storage-fiók a #2 régióban található #1 régió telepítési leírtak szerint, és a lemezkép létrehozásához. Ezzel a lemezképpel, és hozzon létre az alábbi listán szereplő virtuális gépeket az új felhőalapú szolgáltatás hk-c-svc-keleti-us:

| Gépnév | Alhálózat | IP-cím | Rendelkezésre állási csoport | DC/Rack | Kezdőérték? |
| --- | --- | --- | --- | --- | --- |
| HK-c1-keleti-us |Adatok |10.2.2.4 |HK-c-aset-1 |DC = EASTUS állvány = rack1 |Igen |
| HK-c2-keleti-us |Adatok |10.2.2.5 |HK-c-aset-1 |DC = EASTUS állvány = rack1 |Nem |
| HK-c3-keleti-us |Adatok |10.2.2.6 |HK-c-aset-1 |DC = EASTUS állvány = rack2 |Igen |
| HK-c5-keleti-us |Adatok |10.2.2.8 |HK-c-aset-2 |DC = EASTUS állvány = rack3 |Igen |
| HK-c6-keleti-us |Adatok |10.2.2.9 |HK-c-aset-2 |DC = EASTUS állvány = rack3 |Nem |
| HK-c7-keleti-us |Adatok |10.2.2.10 |HK-c-aset-2 |DC = EASTUS állvány = rack4 |Igen |
| HK-c8-keleti-us |Adatok |10.2.2.11 |HK-c-aset-2 |DC = EASTUS állvány = rack4 |Nem |
| HK-F1-keleti-us |webalkalmazás |10.2.1.4 |HK-w-aset-1 |N/A |N/A |
| HK-w2-keleti-us |webalkalmazás |10.2.1.5 |HK-w-aset-1 |N/A |N/A |

Ugyanezeket az utasításokat, mint #1 régió, de 10.2.xxx.xxx címterület használata.

### <a name="step-9-configure-cassandra-on-each-vm"></a>9. lépés: Cassandra konfigurálása az egyes virtuális gépek
Jelentkezzen be a virtuális Gépet, és hajtsa végre a következő:

1. A formátumban adja meg a data center és állvány tulajdonságok $CASS_HOME/conf/cassandra-rackdc.properties szerkesztése: dc = EASTUS állvány = rack1
2. Cassandra.yaml konfigurálása kezdőérték csomópontok szerkesztése: magok: "10.1.2.4,10.1.2.6,10.1.2.8,10.1.2.10,10.2.2.4,10.2.2.6,10.2.2.8,10.2.2.10"

### <a name="step-10-start-cassandra"></a>10. lépés: Indítsa el a Cassandra
Jelentkezzen be minden virtuális gép, majd indítsa el a Cassandra a háttérben a következő parancs futtatásával: $CASS_HOME/bin/cassandra

## <a name="test-the-multi-region-cluster"></a>A több területi fürt tesztelése
Mostanra Cassandra már alkalmazva van minden Azure régióban 8 csomópontokkal 16 csomóponttal. Ezek a csomópontok ugyanabban a fürtben, a közös fürt neve és a kezdőérték csomópont-konfiguráció szerepelnek. A fürt ellenőrzéséhez tegye a következőket:

### <a name="step-1-get-the-internal-load-balancer-ip-for-both-the-regions-using-powershell"></a>1. lépés: A belső terheléselosztó IP lekérése is a PowerShell használatával régiók
* "Hk-c-svc-nyugati-us" Get-AzureInternalLoadbalancer - szolgáltatásnév
* "Hk-c-svc-keleti-us" Get-AzureInternalLoadbalancer - szolgáltatásnév  
  
    Vegye figyelembe az IP-címek (pl. - 10.1.2.101, kelet - nyugati 10.2.2.101) jelenik meg.

### <a name="step-2-execute-the-following-in-the-west-region-after-logging-into-hk-w1-west-us"></a>2. lépés: Hajtsa végre az alábbiakat eladásait hk-F1-nyugati-us való bejelentkezés után
1. Végrehajtás $CASS_HOME/bin/cqlsh 10.1.2.101 9160
2. A következő CQL parancsokat hajthat végre:
   
     Kulcstérértesítések használatával hozzon létre customers_ks rendelkező replikációs = {"class": "NetworkToplogyStrategy", "WESTUS": 3, "EASTUS": 3};   HASZNÁLJA a customers_ks;   Hozzon létre a tábla Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Helyezze be a Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Helyezze be a Customers(customer_id, firstname, lastname) értékek (2, "Jane", "Doe").   Válassza ki * ügyfelek;

Például az alábbi megjelenítésre kell megjelennie:

| customer_id | Utónév | Vezetéknév |
| --- | --- | --- |
| 1 |Jakab |Gipsz |
| 2 |Jane |Gipsz |

### <a name="step-3-execute-the-following-in-the-east-region-after-logging-into-hk-w1-east-us"></a>3. lépés: A következő végrehajtani a keleti terület hk-F1-keleti-us való bejelentkezés után:
1. Végrehajtás $CASS_HOME/bin/cqlsh 10.2.2.101 9160
2. A következő CQL parancsokat hajthat végre:
   
     HASZNÁLJA a customers_ks;   Hozzon létre a tábla Customers(customer_id int PRIMARY KEY, firstname text, lastname text);   Helyezze be a Customers(customer_id, firstname, lastname) VALUES(1, 'John', 'Doe');   Helyezze be a Customers(customer_id, firstname, lastname) értékek (2, "Jane", "Doe").   Válassza ki * ügyfelek;

A nyugati régiójában alapegységét az azonos megjelenítési kell megjelennie:

| customer_id | Utónév | Vezetéknév |
| --- | --- | --- |
| 1 |Jakab |Gipsz |
| 2 |Jane |Gipsz |

Néhány további Beszúrások hajtható végre, és tekintse meg, hogy azok replikálja nyugati-nekünk a fürt része.

## <a name="test-cassandra-cluster-from-nodejs"></a>Cassandra Tesztfürthöz Node.js-ből
Egyikének használatával jön létre a "web" a Linux virtuális gépek réteg korábban, azt fogja végrehajtani egy egyszerű Node.js parancsfájl a korábban beszúrt adatokat olvasni.

**1. lépés: A Node.js és Cassandra ügyfél telepítése**

1. Telepítse a Node.js és npm
2. Telepítse a csomag "cassandra-ügyfél csomópont" npm segítségével
3. Hajtsa végre a következő parancsfájlt a rendszerhéj-parancssorba, mely megjeleníti a json-karakterláncban a beolvasott adatok:
   
        var pooledCon = require('cassandra-client').PooledConnection;
        var ksName = "custsupport_ks";
        var cfName = "customers_cf";
        var hostList = ['internal_loadbalancer_ip:9160'];
        var ksConOptions = { hosts: hostList,
                             keyspace: ksName, use_bigints: false };
   
        function createKeyspace(callback){
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
   
        function createColumnFamily(ksConOptions, callback){
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
   
        //update will also insert the record if none exists
        function updateCustomer(ksConOptions,params)
        {
          var cql = 'UPDATE customers_cf SET custname=?,custaddress=? where custid=?';
          var con = new pooledCon(ksConOptions);
          con.execute(cql,params,function(err) {
              if (err) console.log(err);
              else console.log("Inserted customer : " + params[0]);
          });
          con.shutdown();
        }
   
        //read the two rows inserted above
        function readCustomer(ksConOptions)
        {
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
   
        //exectue the code
        createKeyspace(createColumnFamily);
        readCustomer(ksConOptions)

## <a name="conclusion"></a>Összegzés
Microsoft Azure a rugalmas platform, amely lehetővé teszi a Microsoft, valamint nyílt forráskódú szoftverek futtatását, amint azt a ebben a gyakorlatban. Magas rendelkezésre állású Cassandra fürtök telepíthetők egyetlen adatközpontba keresztül a fürt csomópontjai terjednek több tartalék tartományokban. Cassandra fürtök különféle régiókban földrajzilag távoli Azure katasztrófa igazoló rendszerekhez is telepíthető. Azure és Cassandra együtt lehetővé teszi, hogy jól skálázható, magas rendelkezésre állású létrehozása és a vészhelyreállítás helyreállítható felhőszolgáltatások szükséges mai internet által méret szolgáltatások.  

## <a name="references"></a>Referencia
* [http://cassandra.Apache.org](http://cassandra.apache.org)
* [http://www.datastax.com](http://www.datastax.com)
* [http://www.nodejs.org](http://www.nodejs.org)

