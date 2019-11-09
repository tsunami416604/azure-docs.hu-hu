---
title: Azure Service Fabric vész-helyreállítás | Microsoft Docs
description: Az Azure Service Fabric az összes típusú katasztrófa kezeléséhez szükséges képességeket biztosítja. Ez a cikk az esetlegesen felmerülő katasztrófák típusait és azok kezelését ismerteti.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 1be891d38eb918d65cd8efda86e9a81fa629cf38
ms.sourcegitcommit: cf36df8406d94c7b7b78a3aabc8c0b163226e1bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/09/2019
ms.locfileid: "73883995"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Vész-helyreállítás az Azure Service Fabric
A magas rendelkezésre állás megvalósításának kritikus része annak biztosítása, hogy a szolgáltatások képesek legyenek túlélni az összes különböző típusú hibát. Ez különösen fontos a nem tervezett és a vezérlőn kívüli hibák esetén. Ez a cikk néhány olyan gyakori meghibásodási módot ismertet, amely katasztrófák esetén is lehetséges, ha nem modellezhető és nem megfelelően kezeli őket. Azt is tárgyalja, hogy a rendszer miért történt meg a vészhelyzetek esetén. A cél az állásidő vagy az adatvesztés kockázatának korlátozása vagy megszüntetése, ha hiba történik, tervezett vagy egyéb módon történik.

## <a name="avoiding-disaster"></a>Katasztrófák elkerülése
Service Fabric elsődleges célja, hogy segítse a környezet és a szolgáltatások modellezését oly módon, hogy a gyakori meghibásodási típusok ne legyenek katasztrófák. 

A katasztrófa-/meghibásodási forgatókönyvek esetében általában két típus létezik:

1. Hardver-vagy szoftver-hibák
2. Működési hibák

### <a name="hardware-and-software-faults"></a>Hardver-és szoftver-hibák
A hardver-és szoftver-hibák kiszámíthatatlanok. A hibák túlélésének legegyszerűbb módja, ha a szolgáltatás több példányát futtatja a hardveres és a szoftveres hibák határain keresztül. Ha például a szolgáltatás csak egy adott gépen fut, akkor az adott gép meghibásodása az adott szolgáltatás számára katasztrófa. A katasztrófa elkerülésének egyszerű módja annak biztosítása, hogy a szolgáltatás valójában több gépen fusson. A tesztelés is szükséges ahhoz, hogy az egyik gép meghibásodása ne zavarja meg a futó szolgáltatást. A kapacitás megtervezése biztosítja, hogy a helyettesítő példányok máshol is létrehozhatók, és a kapacitás csökkenése ne terhelje túl a fennmaradó szolgáltatásokat. Ugyanez a minta attól függetlenül működik, hogy milyen hibát próbál elkerülni. Például:. Ha aggódik a SAN meghibásodása miatt, több SANs-t futtat. Ha aggódik a kiszolgálók rack elvesztése miatt, több állványon is futtathatja őket. Ha aggódik az adatközpontok elvesztése miatt, a szolgáltatásnak több Azure-régióban vagy adatközpontban kell futnia. 

Ha az ilyen típusú átnyúló módban fut, továbbra is bizonyos egyidejű hibákra van szükség, de az egyes típusú hibák (például egyetlen virtuális gép vagy hálózati kapcsolat meghiúsulása) automatikusan kezelhetők (és így már nem "vész"). Service Fabric számos mechanizmust biztosít a fürt bővítéséhez és a sikertelen csomópontok és szolgáltatások visszahívásához. A Service Fabric a szolgáltatások számos példányának futtatását is lehetővé teszi, hogy elkerülje az ilyen típusú nem tervezett hibák valós katasztrófákkal való bekapcsolását.

Előfordulhat, hogy a központi telepítés elég nagy méretűre állítása miatt nem valósítható meg a hibák elterjedése. Előfordulhat például, hogy több hardveres erőforrást is igénybe vehet, mint amennyit a meghibásodás valószínűsége miatt szeretne fizetni. Az elosztott alkalmazások kezelése során előfordulhat, hogy a földrajzi távolságok közötti további kommunikációs ugrások vagy az állapot replikálásának költségei elfogadhatatlan késést okoznak. Ha ez a sor eltér az egyes alkalmazásokhoz. A szoftveres hibák esetében a hiba lehet a méretezni kívánt szolgáltatásban. Ebben az esetben több példány nem akadályozza meg a katasztrófát, mivel a meghibásodási feltétel összefügg az összes példány között.

### <a name="operational-faults"></a>Működési hibák
Még akkor is, ha a szolgáltatás számos redundanciával rendelkezik a világ különböző részeit illetően, továbbra is katasztrofális eseményekkel találkozhat. Ha például valaki véletlenül újrakonfigurálja a szolgáltatás DNS-nevét, vagy törli azt. Tegyük fel például, hogy van egy állapot-nyilvántartó Service Fabric szolgáltatás, és valaki törölte a szolgáltatást véletlenül. Hacsak nincs valamilyen más megoldás, akkor ez a szolgáltatás és az összes olyan állam, amely már elfogyott. Az ilyen típusú működési katasztrófák ("Hoppá") különböző kockázatcsökkentő és helyreállítási lépéseket igényelnek, mint a normál, nem tervezett hibák. 

Az ilyen típusú működési hibák elkerülésének legjobb módjai
1. a környezethez való operatív hozzáférés korlátozása
2. veszélyes műveletek szigorú ellenőrzése
3. automatizálhatja az automatizálást, megelőzheti a manuális vagy a sávon kívüli változásokat, és érvényesítheti a tényleges környezet adott módosításait, mielőtt azokat meghozza
4. Győződjön meg arról, hogy a roncsolásos műveletek "Soft". A lágy műveletek nem lépnek azonnal érvénybe, vagy egy kis idő alatt visszavonhatók

Service Fabric biztosít bizonyos mechanizmusokat az üzemeltetési hibák megelőzésére, például szerepköralapú hozzáférés [-](service-fabric-cluster-security-roles.md) vezérlés biztosítására a fürt műveleteihez. Azonban a legtöbb működési hiba a szervezeti erőfeszítésekhez és más rendszerekhez szükséges. Service Fabric biztosít valamilyen mechanizmust az operatív hibák túléléséhez, ami a legtöbb esetben biztonsági mentést és visszaállítást tesz lehetővé az állapot-nyilvántartó szolgáltatások számára.

## <a name="managing-failures"></a>Hibák kezelése
A Service Fabric célja szinte mindig automatikusan történik a hibák kezelése. Bizonyos típusú hibák kezeléséhez azonban a szolgáltatásoknak további kóddal kell rendelkezniük. A biztonság és az üzletmenet folytonosságának okai miatt _nem_ lehet automatikusan foglalkozni más típusú hibákkal. 

### <a name="handling-single-failures"></a>Egyszeri hibák kezelése
Az egyes gépek bármilyen okból sikertelenek lehetnek. Ezek némelyike hardverhiba, például energiaellátási és hálózati hardveres hibák. Más hibák a szoftverben vannak. Ilyenek például a tényleges operációs rendszer és a szolgáltatás hibái. Service Fabric automatikusan észleli az ilyen típusú hibákat, beleértve azokat az eseteket is, amikor a gép hálózati problémák miatt el lesz különítve más gépektől.

A szolgáltatás típusától függetlenül az adott példány futtatása az adott szolgáltatáshoz tartozó állásidőt eredményezi, ha a kód egyetlen példánya valamilyen okból meghiúsul. 

Ha egyetlen hibát szeretne kezelni, a legegyszerűbben azt is megteheti, hogy a szolgáltatások alapértelmezés szerint egynél több csomóponton futnak. Az állapot nélküli szolgáltatások esetében ez úgy valósítható meg, hogy egy `InstanceCount` nagyobb, mint 1. Az állapot-nyilvántartó szolgáltatások esetében a minimális javaslat mindig `TargetReplicaSetSize`, és `MinReplicaSetSize` legalább 3. A szolgáltatási kód több példányának futtatásával biztosítható, hogy a szolgáltatás automatikusan kezeljen egyetlen hibát. 

### <a name="handling-coordinated-failures"></a>Koordinált hibák kezelése
Az egyeztetett hibák a fürtben a tervezett vagy nem tervezett infrastruktúra-meghibásodások és-módosítások, illetve a tervezett szoftverfrissítések miatt történhetnek. Az olyan infrastruktúra-zónák Service Fabric, amelyek az összehangolt hibákat a tartalék tartományokban tapasztalják. Az összehangolt szoftveres változásokat tapasztaló területek frissítési tartományként lesznek modellezve. A hiba-és frissítési tartományokkal kapcsolatos további információkat ebben a [dokumentumban](service-fabric-cluster-resource-manager-cluster-description.md) ismertetjük, amely leírja a fürt topológiáját és definícióját.

Alapértelmezés szerint Service Fabric a szolgáltatások futtatásának megtervezése során a hiba-és frissítési tartományokat tekinti át. Alapértelmezés szerint a Service Fabric megkísérli biztosítani, hogy a szolgáltatások több hibatűrési és frissítési tartományon keresztül fussanak, így ha tervezett vagy nem tervezett módosítások történnek, a szolgáltatások továbbra is elérhetők maradnak. 

Tegyük fel például, hogy az áramforrás meghibásodása miatt a gépek rack-je egyszerre meghiúsul. Ha a szolgáltatás több példánya is fut, a tartalék tartomány meghibásodása sok gép elvesztését eredményezi, egy adott szolgáltatáshoz tartozó egyetlen hiba egy másik példája. Ezért fontos a tartalék tartományok kezelése a szolgáltatások magas rendelkezésre állásának biztosításához. Service Fabric az Azure-ban való futtatásakor a tartalék tartományok kezelése automatikusan történik. Más környezetekben előfordulhat, hogy nem. Ha saját fürtöket készít a helyszínen, ügyeljen arra, hogy megfelelően képezze le és tervezze meg a tartalék tartomány elrendezését.

A frissítési tartományok olyan modellezési területek esetében hasznosak, ahol a szoftverek frissítése egyszerre történik. Emiatt a frissítési tartományok gyakran meghatározzák azokat a határokat, ahol a szoftverek a tervezett frissítések során le vannak véve. A Service Fabric és a szolgáltatásainak frissítései ugyanazt a modellt követik. További információ a működés közbeni frissítésekről, a frissítési tartományokról és a Service Fabric Health modellről, amely segít megakadályozni, hogy a fürt és a szolgáltatás nem kívánt módosításai befolyásolják a fürtöt és a szolgáltatást. Ezek a dokumentumok:

 - [Alkalmazás frissítése](service-fabric-application-upgrade.md)
 - [Alkalmazás-verziófrissítési oktatóanyag](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric Health Model](service-fabric-health-introduction.md)

A fürt elrendezését a [Service Fabric Explorerban](service-fabric-visualizing-your-cluster.md)megadott fürtös Térkép használatával jelenítheti meg:

<center>

![csomópontok Service Fabric Explorer][sfx-cluster-map]
ban lévő tartalék tartományok között vannak elosztva </center>

> [!NOTE]
> A hibák modellezése, a működés közbeni frissítés, a szolgáltatási kód és az állapot számos példányának futtatása, elhelyezési szabályok, amelyek biztosítják, hogy a szolgáltatások a hibák és a frissítési tartományok között fussanak, és a beépített állapot-figyelés csupán **néhány** olyan funkció, amelyet a Service Fabric biztosít a normál működési problémák és hibák a katasztrófák miatti megtartása érdekében. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Egyidejű hardveres vagy szoftveres hibák kezelése
A fentiekben egyetlen hibáról beszéltünk. Amint láthatja, egyszerűen kezelhető az állapot nélküli és az állapot-nyilvántartó szolgáltatások esetében is, mivel a kód (és az állapot) több példányát is megtartja a hibák és a frissítési tartományok között. Több egyidejű véletlenszerű hiba is előfordulhat. Ezek nagyobb valószínűséggel eredményezik a tényleges katasztrófát.


### <a name="random-failures-leading-to-service-failures"></a>A szolgáltatás hibáihoz vezető véletlenszerű hibák
Tegyük fel, hogy a szolgáltatásnak 5 `InstanceCount` volt, és a példányokat futtató számos csomópont egyszerre meghiúsult. A Service Fabric úgy válaszol, hogy automatikusan létrehozza a helyettesítő példányokat a többi csomóponton. Ha a szolgáltatás vissza nem kerül a kívánt példányszámba, a rendszer továbbra is létrehozza a kihelyezéseket. Egy másik Példaként tegyük fel, hogy egy állapot nélküli szolgáltatás volt a-1 `InstanceCount`, ami azt jelenti, hogy a fürt összes érvényes csomópontján fut. Tegyük fel, hogy egyes példányok sikertelenek voltak. Ebben az esetben Service Fabric észleli, hogy a szolgáltatás nem a kívánt állapotban van, és megpróbálja létrehozni a példányokat azokon a csomópontokon, amelyeken hiányoznak. 

Az állapot-nyilvántartó szolgáltatások esetében a helyzet attól függ, hogy a szolgáltatás állapota megmaradt-e. Attól is függ, hogy hány replikát tartalmaz a szolgáltatás, és hány sikertelen volt. Annak megállapítása, hogy vészhelyzet történt-e egy állapot-nyilvántartó szolgáltatás esetében, és a kezelése három szakaszt követ:

1. Annak megállapítása, hogy van-e kvórum elvesztése vagy sem
   - A kvórum elvesztése az állapot-nyilvántartó szolgáltatás replikáinak többsége egy időben, beleértve az Elsődlegeset is.
2. Annak megállapítása, hogy a kvórum elvesztése állandó-e
   - Az idő nagy részében a hibák átmenetiek. A folyamatok újraindulnak, a csomópontok újraindulnak, a virtuális gépek újraindulnak, és a hálózati partíciók meggyógyulnak. Néha azonban a hibák állandóak. 
     - A megőrzött állapot nélküli szolgáltatások esetében a kvórum vagy a több replika meghibásodása _azonnal_ visszavezeti az állandó kvórum elvesztését. Ha a Service Fabric állapot-nyilvántartó nem állandó szolgáltatásban észleli a kvórum elvesztését, a (lehetséges) adatvesztés bejelentésével azonnal továbblép a 3. lépésre. Az adatvesztés elkerülése érdekében a Service Fabric tudja, hogy a replikák visszaszerzése nem áll fenn, mert még akkor is, ha azok helyre lettek állítva, üresek lesznek.
     - Az állapot-nyilvántartó állandó szolgáltatások esetében a kvórum vagy több replika meghibásodása miatt Service Fabric a rendszer megkezdi a replikák visszahívását és a kvórum visszaállítását. Ennek eredményeképpen a szolgáltatás leállást eredményez a szolgáltatás érintett partíciójának (vagy a "replikakészlet-készletnek") _írásakor_ . Az olvasás azonban továbbra is lehetséges a korlátozott konzisztencia-garanciákkal. Az az időtartam, ameddig Service Fabric a kvórum visszaállítására vár a végtelen, mivel a folytatás egy (lehetséges) adatvesztési esemény, és egyéb kockázatokat hordoz. Az alapértelmezett `QuorumLossWaitDuration` érték felülbírálása lehetséges, de nem ajánlott. Ebben az esetben az összes erőfeszítést el kell végezni a leállított replikák visszaállításához. Ehhez be kell állítania a biztonsági mentés alatt álló csomópontokat, és meg kell győződnie arról, hogy képes-e újracsatlakoztatni azokat a meghajtókat, ahol a helyi állandó állapotot tárolják. Ha a kvórum elvesztését a folyamat meghibásodása okozta, Service Fabric automatikusan megkísérli újból létrehozni a folyamatokat, majd újraindítani a bennük lévő replikákat. Ha ez nem sikerül, Service Fabric a jelentések állapotával kapcsolatos hibákat. Ha ezek megoldhatók, akkor a replikák általában visszatérnek. Néha azonban a replikák nem állíthatók vissza. Előfordulhat például, hogy a meghajtók mindegyike meghiúsult, vagy a gépek fizikailag megsemmisültek. Ezekben az esetekben már van egy állandó kvórum-elvesztési esemény. Annak eldöntéséhez, hogy Service Fabric, hogy a leállított replikák ne álljanak vissza, a fürt rendszergazdájának meg kell határoznia, hogy mely partíciókat érinti a szolgáltatás, és hívja meg a `Repair-ServiceFabricPartition -PartitionId` vagy a `System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API-t.  Ez az API lehetővé teszi a partíció AZONOSÍTÓjának megadását a QuorumLoss való kilépéshez és a lehetséges dataloss.

   > [!NOTE]
   > Ez az API _nem_ használható a célzott módon meghatározott partíciók ellen. 
   >

3. Annak megállapítása, hogy van-e tényleges adatvesztés, és hogyan lehet visszaállítani a biztonsági másolatokból
   - Ha Service Fabric meghívja a `OnDataLossAsync` metódust, az mindig a _feltételezett_ adatvesztés miatt következik be. Service Fabric biztosítja, hogy a rendszer a hívást a _legjobb_ fennmaradó replikára továbbítsa. Ez az a replika, amely a legtöbb folyamatot elvégezte. A _feltételezett_ adatvesztés oka az, hogy lehetséges, hogy a fennmaradó replika valójában ugyanolyan állapotú, mint az elsődleges, ha leállt. Azonban anélkül, hogy ez az állapot össze legyen hasonlítva a szolgáltatással, nincs jó módszer arra, hogy a Service Fabric vagy az operátorok biztosan tudják. Ezen a ponton Service Fabric is tudja, hogy a többi replika nem jön vissza. Ez a döntés akkor történt, amikor a rendszer nem várta, hogy a kvórum elvesztését megszüntették. A szolgáltatásra vonatkozó legjobb művelet általában lefagy, és megvárja az adott rendszergazdai beavatkozást. Mit jelent a `OnDataLossAsync` módszer tipikus megvalósítása?
   - Először is jelentkezzen be, hogy a `OnDataLossAsync` aktiválva lett, és indítsa el a szükséges rendszergazdai riasztásokat.
   - Általában ezen a ponton szüneteltetheti és megvárhatja a további döntéseket és a manuális teendőket. Ennek az az oka, hogy akkor is elő kell készíteni a biztonsági mentéseket, ha vannak ilyenek. Ha például két különböző szolgáltatás koordinálja az adatokat, előfordulhat, hogy ezeket a biztonsági másolatokat módosítani kell annak érdekében, hogy a visszaállítást követően a két szolgáltatás által törődő információk konzisztensek legyenek. 
   - Gyakran van más telemetria vagy kipufogó is a szolgáltatástól. Ez a metaadatok más szolgáltatásokban vagy naplókban is szerepelhetnek. Ezen információk alapján megállapítható, hogy van-e olyan hívás, amely az elsődleges biztonsági mentésben nem szerepelt, vagy az adott replikára replikált. Előfordulhat, hogy a helyreállítás megkezdése előtt újra kell játszani, vagy hozzá kell adni őket a biztonsági mentéshez.  
   - A fennmaradó replika állapotának összehasonlítása a rendelkezésre álló biztonsági másolatokban szereplővel. Ha a Service Fabric megbízható gyűjteményeket használja, akkor a [jelen cikkben](service-fabric-reliable-services-backup-restore.md)ismertetett eszközök és folyamatok érhetők el. A cél az, hogy a replika állapota elegendő-e, vagy hogy a biztonsági mentés milyen esetben hiányzik.
   - Ha az összehasonlítás elkészült, és ha szükséges, a visszaállítás befejeződött, a szolgáltatási kódnak igaz értéket kell visszaadnia, ha bármilyen változás történt. Ha a replika azt állapította meg, hogy az állapot legjobb példánya volt, és nem történt módosítás, akkor a hamis értéket adja vissza. Az igaz érték azt jelzi, hogy a _többi_ fennmaradó replikák mostantól nem konzisztensek. A rendszer elveti és újraépíti ezt a replikát. A False (hamis) érték azt jelzi, hogy az állapot nem változik, így a többi replika is megtarthatja a kívánt értéket. 

Rendkívül fontos, hogy a szolgáltatás szerzője az adatvesztést és a meghibásodást is alkalmazza a szolgáltatások éles környezetben való üzembe helyezése előtt. Az adatvesztés elleni védelem érdekében fontos, hogy rendszeres időközönként [biztonsági mentést](service-fabric-reliable-services-backup-restore.md) végezzenek az állapot-nyilvántartó szolgáltatások állapotáról egy földrajzi redundáns tárolóba. Győződjön meg arról is, hogy van lehetőség a visszaállításra. Mivel a különböző szolgáltatásokból származó biztonsági másolatok különböző időpontokban készülnek, gondoskodnia kell arról, hogy a szolgáltatások visszaállítása után konzisztensen tekintse át egymást. Vegyünk például egy olyan helyzetet, amelyben az egyik szolgáltatás létrehoz egy számot, és tárolja azt, majd elküldi azt egy másik szolgáltatásnak, amely szintén tárolja azt. A visszaállítást követően felfedezheti, hogy a második szolgáltatás rendelkezik a számmal, de az első nem, mert a biztonsági másolat nem tartalmazza ezt a műveletet.

Ha azt állapítja meg, hogy a fennmaradó replikák nem elégségesek az adatvesztési forgatókönyvek folytatásához, és a szolgáltatás állapotát nem lehet telemetria vagy kipufogóból rekonstruálni, a biztonsági másolatok gyakorisága határozza meg a lehető legjobb helyreállítási időkorlátot (RPO). Service Fabric számos eszközt biztosít a különböző meghibásodási forgatókönyvek teszteléséhez, beleértve az állandó kvórumot és az adatvesztést, amely a biztonsági mentésből való visszaállítást igényel Ezek a forgatókönyvek a hiba-elemzési szolgáltatás által felügyelt Service Fabric tesztelési eszközeinek részét képezik. Ezekről az eszközökről és mintákról további információk [itt](service-fabric-testability-overview.md)érhetők el. 

> [!NOTE]
> A rendszerszolgáltatások a kvórum elvesztését is elérhetik, és a szóban forgó szolgáltatásra jellemző hatással vannak. Például az elnevezési szolgáltatás Kvórumának elvesztése befolyásolja a névfeloldást, míg a Feladatátvevőfürt-kezelő szolgáltatásban a kvórum elvesztése blokkolja az új szolgáltatások létrehozását és feladatátvételét. Habár a Service Fabric rendszerszolgáltatások ugyanazt a mintát követik, mint az állami felügyelettel kapcsolatos szolgáltatások, nem ajánlott a kvórum elvesztésével és a lehetséges adatvesztéssel való áthelyezést végrehajtani. A javaslat Ehelyett az adott helyzetre célzott megoldás meghatározásához nyújt [támogatást](service-fabric-support.md) .  Általában érdemes egyszerűen megvárni, amíg a lefelé irányuló replikák vissza nem állnak.
>

## <a name="availability-of-the-service-fabric-cluster"></a>A Service Fabric-fürt rendelkezésre állása
Általánosságban elmondható, hogy a Service Fabric-fürt maga is egy nagyon elosztott környezet, egyetlen meghibásodási pont hiányában. Egy csomópont meghibásodása nem eredményezi a fürt rendelkezésre állási vagy megbízhatósági problémáit, elsősorban azért, mert a Service Fabric rendszerszolgáltatások a korábban megadott irányelveket követik: mindig három vagy több replikával futnak alapértelmezés szerint, és ezek a rendszerek azok a szolgáltatások, amelyek állapot nélküliek futnak az összes csomóponton. Az alapul szolgáló Service Fabric hálózatkezelési és meghibásodási észlelési rétegek teljes mértékben el vannak osztva. A legtöbb rendszerszolgáltatás újraépíthető a fürt metaadataiból, vagy megtudhatja, hogyan szinkronizálja az állapotát más helyekről. A fürt rendelkezésre állása veszélybe kerülhet, ha a rendszerszolgáltatások a fent leírtak szerint a kvórum elvesztése miatt válnak elérhetővé. Ezekben az esetekben előfordulhat, hogy nem tudja végrehajtani bizonyos műveleteket a fürtön, például a frissítés megkezdését vagy új szolgáltatások telepítését, de maga a fürt is. A már futó szolgáltatások továbbra is futnak ezekben a feltételekben, hacsak nem igénylik a rendszerszolgáltatásoknak a működés folytatását. Ha például a Feladatátvételi felügyelő a kvórum elvesztésével jár, az összes szolgáltatás továbbra is futni fog, de az esetlegesen meghiúsuló szolgáltatások nem fognak automatikusan újraindulni, mivel ez megköveteli a Feladatátvételi felügyelő bevonását. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Adatközpont vagy Azure-régió meghibásodása
Ritka esetekben előfordulhat, hogy egy fizikai adatközpont átmenetileg elérhetetlenné válik az áramellátás vagy a hálózati kapcsolat elvesztése miatt. Ezekben az esetekben az adott adatközpontban vagy az Azure-régióban található Service Fabric-fürtök és-szolgáltatások elérhetetlenné válnak. _Az adatai azonban megmaradnak_. Az Azure-ban futó fürtök esetében az [Azure állapot lapján][azure-status-dashboard]megtekintheti az kimaradások frissítéseit. Abban a nagyon valószínűtlen esetben, ha a fizikai adatközpont részben vagy teljesen megsemmisül, az ott üzemeltetett Service Fabric-fürtök vagy a bennük található szolgáltatások elvesznek. Ebbe beletartozik minden olyan állapot, amely nem biztonsági mentést készít az adott adatközponton vagy régión kívül.

Két különböző stratégia létezik egy adott adatközpont vagy régió állandó vagy tartós meghibásodásának megtúlélésére. 

1. Több ilyen régióban is futtathat külön Service Fabric-fürtöket, és a feladatátvételhez és a feladatok elvégzéséhez szükséges mechanizmust is használhat a környezetek között. Ez a több fürt aktív-aktív vagy aktív-passzív modellje további felügyeleti és műveleti kódokat igényel. Ehhez az egyik adatközpontban vagy régióban lévő szolgáltatásokból származó biztonsági másolatok koordinálására is szükség van, hogy azok más adatközpontokban vagy régiókban is elérhetők legyenek, ha az egyik meghibásodik. 
2. Futtasson egyetlen Service Fabric-fürtöt, amely több adatközpontra vagy régióra terjed ki. A minimálisan támogatott konfiguráció három adatközpont vagy régió. A régiók vagy adatközpontok ajánlott száma öt. Ehhez összetettebb fürt-topológia szükséges. A modell előnye azonban az, hogy az egyik adatközpont vagy régió meghibásodása egy katasztrófából normális hiba miatt alakul. Ezeket a hibákat olyan mechanizmusok tudják kezelni, amelyek egy adott régióban lévő fürtökhöz működnek. A tartalék tartományok, a frissítési tartományok és a Service Fabric elhelyezési szabályai biztosítják a számítási feladatok terjesztését, hogy azok a normál hibáknak legyenek kitéve. További információ azokról a szabályzatokról, amelyek segíthetnek az ilyen típusú fürtökön belüli szolgáltatások működtetésében, az [elhelyezési szabályzatok](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) beolvasása

### <a name="random-failures-leading-to-cluster-failures"></a>A fürtök meghibásodásához vezető véletlenszerű hibák
Service Fabric a vetőmag-csomópontok fogalmával rendelkezik. Ezek olyan csomópontok, amelyek megőrzik a mögöttes fürt rendelkezésre állását. Ezek a csomópontok biztosítják, hogy a fürt a többi csomóponttal való bérletek létrehozásával, valamint bizonyos hálózati meghibásodások során tiebreakers szolgáljon. Ha a véletlenszerű hibák miatt a rendszer eltávolítja a fürtben található vetőmag-csomópontok többségét, és ezek nem állnak vissza, akkor a fürt összevonási gyűrűje összeomlik, mivel elveszítette a mag-csomópont kvórumát, és a fürt meghibásodik. Az Azure-ban Service Fabric erőforrás-szolgáltató kezeli Service Fabric fürt konfigurációját, és alapértelmezés szerint kiosztja a magok csomópontjait az elsődleges csomópont típusú hibák és a frissítési tartományok között. Ha az elsődleges NodeType Silver vagy Gold tartósságként van megjelölve, akkor ha eltávolít egy mag csomópontot, vagy az elsődleges NodeType skálázásával vagy a magok csomópontjának manuális eltávolításával, a fürt megpróbál előléptetni egy másik nem vetőmag-csomópontot az elérhető elsődleges NodeType. a kapacitás, és sikertelen lesz, ha kevesebb rendelkezésre álló kapacitása van, mint a fürt megbízhatósági szintjének az elsődleges csomópont típusához.

Az önálló Service Fabric-fürtökben és az Azure-ban az "elsődleges csomópont típusa" a magok futtatásának egyike. Az elsődleges csomópont típusának meghatározásakor a Service Fabric automatikusan kihasználja a csomópontok számát, amelyet akár 9 mag csomópont létrehozásával, mind a rendszerszolgáltatások 7 replikájának létrehozásával biztosít. Ha a rendszerszolgáltatás-replikák egy csoportját egyszerre hajtják végre, a rendszerszolgáltatások a fent ismertetett kvórum elvesztését fogják megadni. Ha a vetőmag-csomópontok többsége elveszik, akkor a fürt hamarosan leáll.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan szimulálhat különböző hibákat a [tesztelési keretrendszer](service-fabric-testability-overview.md) használatával
- További katasztrófa-helyreállítási és magas rendelkezésre állású erőforrások olvasása. A Microsoft nagy mennyiségű útmutatót tett közzé ezekkel a témakörökkel kapcsolatban. A dokumentumok némelyike a más termékekben való használatra adott technikákra vonatkozik, és számos általános ajánlott gyakorlatot tartalmaz, amelyeket a Service Fabric kontextusban is alkalmazhat:
  - [Rendelkezésre állási ellenőrzőlista](/azure/architecture/checklist/resiliency-per-service)
  - [Vész-helyreállítási részletezés végrehajtása](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Vészhelyreállítás és magas szintű rendelkezésre állás az Azure-alkalmazásokhoz][dr-ha-guide]
- A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
