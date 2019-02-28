---
title: Az Azure Service Fabric-vészhelyreállítás |} A Microsoft Docs
description: Az Azure Service Fabric által biztosított lehetőségek a bármilyen típusú katasztrófák kezelése érdekében. Ez a cikk bemutatja, milyen katasztrófák esetén fordulhat elő, és azok kezelése.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: ab49c4b9-74a8-4907-b75b-8d2ee84c6d90
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: b8824ba7bd3042f447df19fe20ca3ab255dfb165
ms.sourcegitcommit: fdd6a2927976f99137bb0fcd571975ff42b2cac0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2019
ms.locfileid: "56961516"
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Vészhelyreállítás az Azure Service Fabricben
Magas rendelkezésre állás biztosítása kritikus része annak ellenőrzése, hogy szolgáltatásokat az összes különböző típusú hibák hibatűrését. Ez különösen fontos a nem tervezett hibák és a hatáskörén kívül esik. Ez a cikk ismerteti az egyes közös hibaállapotra katasztrófák lehet, ha nem modellezése, és megfelelően felügyelt. Megoldások és a teendő, ha vészhelyzet történt ennek ellenére is ismertetjük. A célja, hogy korlátozni vagy kiküszöbölése az állásidőt és az adatvesztés kockázatát, hibák, tervezett felmerülő, vagy ellenkező esetben fordulhat elő.

## <a name="avoiding-disaster"></a>Vészhelyreállítási elkerülése
A Service Fabric elsődleges célja, hogy a környezet és a a szolgáltatás oly módon, hogy gyakori hiba típust nem állnak-e katasztrófák modell segítségével. 

Az általános vészhelyreállítási/hibaesetet két típusa van:

1. Hardvert vagy szoftvert hibák
2. Operatív hibák

### <a name="hardware-and-software-faults"></a>Hardver- és hibák
Hardver- és a hibák olyan előre nem látható. A legegyszerűbben úgy stabilitást biztosít a hibák több példányát, a hardver- vagy tartalék határokon átnyúló szolgáltatás fut. Például ha a szolgáltatás csak egy adott számítógépen fut, majd, hogy egy gép hibája katasztrófa, hogy a szolgáltatás. Az egyszerű elkerülése érdekében a vészhelyreállítási módja győződjön meg arról, hogy a szolgáltatás ténylegesen fut több gépen. Vizsgálat nem is annak érdekében, hogy a hiba egy gép nem zavarja a futó szolgáltatás szükséges. Kapacitástervezés biztosítja, hogy egy helyettesítő példányt lehet létrehozni máshol, illetve, hogy a kapacitás csökkenése nem túlterhelni a fennmaradó szolgáltatásokat. Ugyanezt a mintát működik, függetlenül attól, milyen próbál, a bukás elkerülése érdekében. Például. Ha aggódik a hiba a egy San-alapú, több San hálózatok között futtatnia. Ha aggódik egy kiszolgálóállvány kiszolgálók elvesztését, több állványok közötti futtatnia. Ha aggódik az adatközpontok elvesztését, a szolgáltatás a több Azure-régiók vagy adatközpontok fusson. 

Az ilyen típusú átnyúló módban fut, amikor Ön továbbra is egyidejű meghibásodása, de egy bizonyos típusú, és még több sikertelen adott (például: egyetlen virtuális gép vagy a hálózati kapcsolat sikertelen) kezeli automatikusan (és így már "katasztrófa"). A Service Fabric bővítésére, a fürt számos mechanizmusokat biztosít, és történő visszaállítását végzi a csomópontok és a szolgáltatások vissza nem sikerült. A Service Fabric lehetővé teszi a szolgáltatás számos példánya fut az ilyen típusú valódi katasztrófa, forduljon a nem tervezett hibák elkerülése érdekében.

Előfordulhat, hogy miért fut, elég nagy legyen foglalhat magában a sikertelen központi telepítés nem található okok miatt. További hardveres erőforrások hajlandó viszonyított hiba esélyét kell fizetnie, mint például igénybe vehet. Esetén az elosztott alkalmazások, annak oka az lehet, hogy a további kommunikációt ugrások vagy állapotú replikáció költségei különböző földrajzi távokat okok elfogadhatatlan mértékű késést. Ahol ezt a sort megjelenítése abban különbözik az egyes alkalmazásokhoz. Szoftver hibák kifejezetten, a tartalék lehet skálázni szeretne, a szolgáltatásban. Ebben az esetben több példányt nem akadályozzák meg a vészhelyreállítás, mivel a hibától visszamenőleges korrelációban állnak a példányok között.

### <a name="operational-faults"></a>Operatív hibák
Akkor is, ha a szolgáltatás világszerte sok redundanciát rendelkező van átnyúló, továbbra is tapasztalhatnak katasztrofális események. Ha például valaki véletlenül újrakonfigurálja a szolgáltatás dns-nevét, vagy törlése végleges. Például tegyük fel, egy állapotalapú Service Fabric-szolgáltatás volt, és valaki az adott szolgáltatás véletlen törlését. Kivéve, ha van egy másik megoldás, szolgáltatás és az összes kellett az állapot akkor most már lefutott. Az ilyen típusú működési katasztrófák ("hoppá") szükséges különböző megoldások és a lépések, mint a rendszeres nem tervezett hibákat a helyreállításhoz. 

A rendszer a legjobb módszer az ilyen típusú operatív hibák elkerülése érdekében
1. a környezetet üzemeltetési hozzáférés korlátozása
2. szigorúan a veszélyes műveletek naplózása
3. írja elő az automation, manuális vagy a tartományon kívül a sávon kívüli módosítások megakadályozása és a tényleges környezetre adott módosítások érvényesítése őket életbe léptetése előtt
4. Győződjön meg arról, hogy felülíró művelet "Soft szót". Helyreállítható műveleteket nem azonnal érvénybe lépnek, illetve visszavonható néhány időtartományon belül

A Service Fabric biztosítja, hogy az operatív hibák, így például bizonyos mechanizmusok [szerepköralapú](service-fabric-cluster-security-roles.md) hozzáférés-vezérlés a fürt működését. Ezek a műveleti hibák többségét azonban szervezeti erőfeszítések és más rendszerekhez szükséges. A Service Fabric nyújtanak néhány újraindulnak a még működő incidensről biztonsági mentési működési hibákat, és állítsa vissza az állapotalapú szolgáltatások esetében.

## <a name="managing-failures"></a>Hibák kezelése
A Service Fabric célja szinte mindig automatikus felügyelete hibák. Annak érdekében, hogy bizonyos típusú hibák kezelésére, azonban szolgáltatások kell további programkódokat kellene megtervezni. Más típusú hibák kell _nem_ biztonságának és az üzleti folytonossági okok miatt automatikusan kibocsátásokban megtörténik. 

### <a name="handling-single-failures"></a>Egyetlen hibák
Egyetlen gépeket nem sikerül, a legkülönbözőbb célokra. Ezek némelyike hardver okait, például a tápegységek és a hálózati hardveres hibák esetén. Más hibák vannak a szoftver. Ezek közé tartozik a hibák számát a tényleges operációs rendszer és a szolgáltatás. A Service Fabric automatikusan észleli az ilyen típusú hibák, beleértve azokat az eseteket, ahol a gép válik különítve a többi gép a hálózati problémák miatt.

Függetlenül attól, milyen típusú szolgáltatás fut egyetlen példányt eredmények állásidőt jelent, hogy a szolgáltatás Ha valamilyen okból meghiúsul, hogy a kód egyetlen példányát. 

Annak érdekében, hogy bármilyen egyetlen hibakezeléshez olyan, a legegyszerűbb dolog, amit az győződjön meg arról, hogy a szolgáltatások alapértelmezés szerint ki futtassa egynél több csomóponton. Az állapotmentes szolgáltatások esetében ez valósítható létesíteni egy `InstanceCount` 1-nél nagyobb. Az állapotalapú szolgáltatások esetében a minimális javaslat mindig van egy `TargetReplicaSetSize` és `MinReplicaSetSize` legalább 3. Több példányát, a szolgáltatás kódot futtató biztosítja, hogy a szolgáltatás képes automatikusan kezelni minden egyetlen hiba. 

### <a name="handling-coordinated-failures"></a>Hibák összehangolt kezelése
Koordinált hibák miatt, vagy tervezett fürt vagy nem tervezett infrastruktúra hibák és, és tervezett szoftver változásainak fordulhat elő. A Service Fabric-infrastruktúra zónák, hogy a tartalék tartományok koordinált hibák modellekhez. Frissítési tartományok tapasztalható koordinált szoftvermódosítások területeket van modellezve. További információ a tartalék és frissítési tartományokba van [ebben a dokumentumban](service-fabric-cluster-resource-manager-cluster-description.md) , amely ismerteti a fürt topológia és a definíció.

Alapértelmezés szerint a Service Fabric hibatűrési és frissítési tartományokba tekinti, ahol a szolgáltatásainak futniuk tervezésekor. Alapértelmezés szerint a Service Fabric próbál győződjön meg arról, hogy a szolgáltatások futtatása több hibatűrési és frissítési tartományok között, ha a tervezett vagy nem tervezett változások történhetnek a szolgáltatások elérhetők maradnak. 

Például tegyük fel, hogy power forrás hiba okozza-e sikertelen egyidejűleg gépek állvány. A tartalék tartomány nem sok gépek elvesztését fut a szolgáltatás több példányát bekapcsolja az egyetlen hiba egy adott szolgáltatáshoz csak egy másik példát. Éppen ezért a tartalék tartományok kezelése, kritikus fontosságú a magas rendelkezésre állás, a szolgáltatások biztosítása. Ha az Azure-ban futó Service Fabric, tartalék tartományok kezelése automatikusan történik. Más környezetekben nem lehetnek. A saját helyszíni fürtök létrehozásakor, leképezéséhez, és a tartalék tartomány elrendezésének tervezése megfelelően mindenképpen.

Frissítési tartományok hol szoftvert frissíthető egyszerre fog területek modellezési hasznosak. Emiatt a frissítési tartományok is gyakran a határokat, ahol szoftvereket csökken tervezett frissítések során határozza meg. Frissítés a Service Fabric és a szolgáltatások kövesse ugyanannak a modellnek. A működés közbeni frissítés frissítési tartományok és a Service Fabric health modell, amely segítségével megakadályozhatja, hogy a nem szándékos módosítások hatással lenne a fürt és a szolgáltatás további információkért lásd: ezeket a dokumentumokat:

 - [Alkalmazás frissítése](service-fabric-application-upgrade.md)
 - [Frissítési vonatkozó oktatóanyag](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric Állapotközpontú modellről](service-fabric-health-introduction.md)

Jelenítheti meg a fürt a fürt leképezéssel megadott elrendezését [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![A Service Fabric Explorerben tartalék tartomány között elosztva csomópontok][sfx-cluster-map]
</center>

> [!NOTE]
> Elhelyezési szabályok biztosítják a szolgáltatásokat futtatása sikertelen, a működés közbeni frissítés, a szolgáltatást kóddal és állapottal, számos példánya fut területeit modellezési hibatűrési és frissítési tartományok között, és beépített szolgáltatásállapot-figyelést csak **néhány** , a az funkciók, amelyek a Service Fabric biztosít annak érdekében, hogy a rendes működési problémák és hibák megtartása bekapcsolása folyamatban, a vészhelyzetek esetére. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Egyidejű hardveres vagy szoftveres hibák
Újabb egyetlen hibák Olvashatott. Amint láthatja, is egyszerűen csak a több példányát tárolja különböző hibatűrési és frissítési tartományokba futó kódot (és állapot) is állapot nélküli és állapotalapú szolgáltatások kezelésére. Több egyidejű véletlen hibákat is fordulhat elő. Ezek olyan nagyobb valószínűséggel bekövetkezik a tényleges katasztrófa vezethet.


### <a name="random-failures-leading-to-service-failures"></a>Szolgáltatáshibák vezető véletlenszerűen hibák
Tegyük fel, hogy a szolgáltatás egy `InstanceCount` 5, és több csomópont futtatása ezekhez a példányokhoz összes sikertelen volt egy időben. A Service Fabric válaszol, a többi csomóponton helyettesítő-példányok automatikus létrehozásával. Csere létrehozása mindaddig, amíg a szolgáltatás nem a kívánt példányszámot vissza a továbbra is. Másik példaként tegyük fel, hiba történt az állapotmentes szolgáltatás, amely egy `InstanceCount`a-1, ami azt jelenti, a fürt összes csomópontja érvényes futtatott. Tegyük fel, hogy ezekhez a példányokhoz némelyike a sikertelen volt. Ebben az esetben a Service Fabric észleli, hogy a szolgáltatás nem a kívánt állapotban van, és próbálja meg a példányok létrehozásához a csomópontokon, hogy hol megadva. 

A helyzet az állapotalapú szolgáltatások esetében függ, hogy a szolgáltatás rendelkezik megőrzött állapot vagy sem. Attól is függ, hogyan lehet a szolgáltatás korábban számos replikákat és a hány sikertelen volt. E katasztrófa történt egy állapotalapú szolgáltatás és a kezelése, a következő három lépésben hajtaná meghatározásakor:

1. Ha történt kvórum elvesztése vagy nem meghatározása
 - A kvórum elvesztése a replikákat, az állapotalapú szolgáltatások többsége nem működik egy időben, beleértve az elsődleges bármikor.
2. Ha a kvórum elvesztése állandó van-e vagy sem
 - A legtöbb esetben a hibák átmenetiek. Folyamatok újra lesz indítva, csomópont újra lesz indítva, s vannak a virtuális gépek, a javítandó hálózati partíciókat. Egyes esetekben azonban a hibák olyan állandó. 
    - A szolgáltatások nélkül megőrzött állapot, legalább egy kvórum replikák eredmények hiba _azonnal_ állandó kvórumveszteségben. Amikor a Service Fabric kvórum elvesztése nem állandó az állapotalapú szolgáltatások észlel, azonnal halad a 3. lépés (esetleges) adatvesztés deklarálásával. Adatok továbblépés adatveszteség értelme, mert a Service Fabric tudja, hogy nincs-e a Várakozás a replikák tér vissza, mert még akkor is, ha helyre lett állítva, akkor lehet üres pont.
    - A állapotalapú állandó szolgáltatások esetében replikák legalább egy kvórum hiba hatására a Service Fabric a replikák tér vissza, és állítsa vissza a kvórum Várakozás indítása. Bármely az eredmény egy szolgáltatás-kimaradás _ír_ az érintett partícióhoz (vagy "replika") a szolgáltatás. Előfordulhat azonban, olvasási továbbra is lehetséges a csökkentett konzisztenciagaranciákat. Az alapértelmezett, hogy mennyi ideig kvórum vissza kell állítani a Service Fabric várakozik azért végtelen, mert a Folytatás (esetleges) adatvesztési esemény sorozatéhoz és más fenyegetésekkel szemben. Felülbírálja az alapértelmezett `QuorumLossWaitDuration` érték lehet, de nem ajánlott. Ehelyett jelenleg minden erőfeszítést kell tenni a lefelé replikák visszaállítására. Ehhez történő visszaállítását a csomópontokat, amelyek biztonsági mentése le, és annak biztosítása, hogy azok is csatlakoztassa újra a meghajtókat, és tárolhatja is azokat a helyi állandó állapotot. A kvórum elvesztése folyamat hibája okozza, ha a Service Fabric automatikusan megpróbálja hozza létre újra a folyamatokat, és indítsa újra a bennük lévő replikákat. Ha ez meghiúsul, a Service Fabric jelentések állapotával kapcsolatos hibák. Ha ezek orvosolni tudja majd a replikák általában térjen vissza. Egyes esetekben azonban a replikák nem hozható vissza. Például a meghajtók előfordulhat, hogy az összes sikertelen volt, vagy a gépek fizikailag megsemmisül valamilyen módon. Ezekben az esetekben ezzel kapunk egy állandó kvórum adatvesztési eseményhez. Állapítható meg, hogy a Service Fabric nem vár tovább a lefelé replikák tér vissza, akkor a fürt rendszergazdája meg kell határoznia, amelyek szolgáltatásokat érinti, és hívja partíciók a `Repair-ServiceFabricPartition -PartitionId` vagy ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API-t.  Az API lehetővé teszi, hogy a partíció QuorumLoss és az esetleges dataloss áthelyezése ID megadása.

  > [!NOTE]
  > Ez _soha nem_ az API nem használhatja az adott partíciók elleni célzott módon. 
  >

3. Meghatározhatja, hogy a tényleges adatvesztés lett, és a biztonsági másolatokból való visszaállítással
  - Ha a Service Fabric meghívja a `OnDataLossAsync` metódus, a rendszer mindig miatt _gyanús_ adatvesztés. A Service Fabric biztosítja, hogy a rendszer továbbítja a hívást a _ajánlott_ fennmaradó replika. Ez a bármelyik replika által végrehajtott a legtöbb folyamatot. Az OK azt mindig azt _gyanús_ adatvesztés, lehetséges, hogy a fennmaradó replika ténylegesen rendelkezik összes ugyanabban az állapotban, mint az elsődleges ha csökkent. Az összehasonlító állapotban, nélkül van azonban nem jó módszer a Service Fabric vagy az operátorok biztosan tudni. Ezen a ponton a Service Fabric is tudja, hogy a többi replikák nem érkeznek vissza. A döntést, hogy várakozik a kvórum elvesztése a magától megoldódik, amikor volt. A legjobb megoldás érdekében a szolgáltatás általában rögzítése, és várjon, amíg az adott rendszergazdai beavatkozás a. Tehát egy tipikus megvalósításában a célja a `OnDataLossAsync` metódus tegye?
  - Első lépésként jelentkezzen, amely `OnDataLossAsync` aktiválódott-e, és a szükséges felügyeleti riasztásokat.
   - Általában ezen a ponton szüneteltetéséről, és várja meg, további döntések és manuális műveletek végrehajtását. Ennek oka az, akkor is, ha a biztonsági másolatok szükségük lehet, hogy elő kell készíteni. Például ha két különböző szolgáltatásokat koordinálja az adatokat, ezeket a biztonsági mentéseket lehet, hogy kell biztosítása után a visszaállítás történik, amely az adatokat e két szolgáltatás ellátás kapcsolatos konzisztens módosítható. 
  - Gyakran nincs néhány egyéb telemetriai és a szolgáltatás származó. A metaadatok tartalmazhatja, más szolgáltatások vagy a naplókat. Ez az információ szükséges meghatározni, hogy voltak-e bármilyen hívások fogadása és feldolgozása az elsődleges, amelyek nem voltak jelen a biztonságimásolat- vagy az adott replika replikálja is használható. Előfordulhat, hogy ezek kell játssza vissza vagy a biztonsági mentés visszaállítási megvalósítható hozzá.  
   - A fennmaradó replika állapotra, amely minden biztonsági, rendelkezésre álló összehasonlítását. Ha a Service Fabric megbízható gyűjtemények használatával, akkor vannak az eszközökkel és dolgozza fel a rendelkezésre álló végrehajtására, ismertetett [Ez a cikk](service-fabric-reliable-services-backup-restore.md). A cél, hogy e belül a replika állapota megfelelő, vagy milyen a biztonsági mentés is esetleg hiányzik.
  - Az összehasonlítás befejezése után, és a szükséges a visszaállítás befejeződött, a webszolgáltatás kódjához igaz, ha az állapot módosítások kell visszaadnia. Ha a replika határozza meg, hogy a legjobb elérhető másolatot az állapot volt, és nem módosította, majd vissza false (hamis). Igaz értéket jelzi, hogy bármely _más_ fennmaradó replikák most már valószínűleg inkonzisztens erre. Lesz azok dobva, és az újonnan létrehozott e replikáról. Hamis jelzi, hogy, hogy nem állapot módosítás is, így a többi replikák tartani tudja a véleményét. 

Különösen fontos, hogy szolgáltatás szerzők lehetséges adat elvesztése és sikertelen eseteinek gyakorlat az szolgáltatások minden eddiginél éles környezetben üzembe helyezése előtt. Az adatvesztés lehetősége ellen védelmet biztosító, fontos, hogy rendszeres időközönként [biztonsági mentése az állapot](service-fabric-reliable-services-backup-restore.md) bármely, az állapotalapú szolgáltatások egy georedundáns tárolóban. Emellett győződjön meg arról, hogy a számítógép erre. Mivel számos különböző szolgáltatások biztonsági másolatokat készít különböző időpontokban, győződjön meg arról, hogy a visszaállítás után a szolgáltatások rendelkezik egy egységes nézetben minden más kell. Vegyük példaként egy olyan helyzetet, ahol egy szolgáltatás állít elő, egy számot, és tárolja, majd elküldi egy másik szolgáltatás, amely tárolja is. A visszaállítás után előfordulhat, hogy a második szolgáltatás rendelkezik azzal a számot, de a első nem, mert a biztonsági mentés nem tartalmazza a művelet felderítése.

Ha talál, hogy a fennmaradó replikák nem elegendőek továbbra is az adatok elvesztése esetén, és a telemetria- vagy kipufogógáz szolgáltatás állapota, nem hozza, a biztonsági mentések gyakoriságát határozza meg, a legjobb lehetséges helyreállításipont-célkitűzés (RPO). A Service Fabric számos eszközt kínál a különféle hiba eseteket, beleértve az állandó kvórum és a visszaállítást egy biztonsági másolatból kellene adatvesztés teszteléséhez. Ezeket a forgatókönyveket a Service Fabric, testability Fault Analysis Service által kezelt eszközök részeként is. További információ ezen eszközök és a minták [Itt](service-fabric-testability-overview.md). 

> [!NOTE]
> Rendszerszolgáltatások is esetén csökkenhet a kvórum elvesztése, az hatással van a szolgáltatással az adott folyamatban. Például kvórum elvesztése a elnevezési szolgáltatásban hatással van a névfeloldás, mivel a kvórum elvesztése a Feladatátvevőfürt-kezelő szolgáltatás a letiltja az új szolgáltatás létrehozása és a feladatátvételeket. A Service Fabric-rendszerszolgáltatások, az állapot-felügyeleti szolgáltatások ugyanazt a mintát követi, amíg nem ajánlott, hogy meg kell próbálni áthelyezi őket a kvórum elvesztése és az esetleges adatvesztés. Az ajánlás inkább, hogy [támogatási hledání](service-fabric-support.md) meghatározni olyan megoldás, amely az Ön konkrét helyzetére vonatkozik.  Általában célszerű, egyszerűen várja meg, amíg a lefelé replikák adja vissza.
>

## <a name="availability-of-the-service-fabric-cluster"></a>A Service Fabric-fürt rendelkezésre állása
Általánosan fogalmazva a Service Fabric-fürthöz egy elosztott környezetben a nem a hibaérzékeny pontokat. Egyetlen csomópont hibája miatt nem fog rendelkezésre állás vagy a fürt megbízhatósági problémák elsősorban, mert a Service Fabric-rendszerszolgáltatások kövesse a korábban megadott vezérlőelemnél ismertetett irányelvek: ezek mindig futnia a replika alapértelmezés szerint, és ezeket a rendszer állapotmentes szolgáltatások futtatása az összes csomópontra. Az alapul szolgáló Service Fabric hálózatkezelés és a hiba észlelése rétegek teljes oszlanak meg. A legtöbb helyrendszeri szolgáltatások a fürtben metaadataiból újraépíthető, vagy tudja, hogyan más helyeiről állapotuk újraszinkronizálása. A fürt rendelkezésre állását is válnak biztonsága sérül, ha a helyrendszeri szolgáltatások kaphat a kvórum elvesztése helyzetekben leírtakhoz hasonlóan a fenti. Ezekben az esetekben, előfordulhat, hogy nem kell tudni elvégezni bizonyos műveleteket, mint a frissítés megkezdése, vagy új szolgáltatások üzembe helyezése a fürtön, de magának a fürtnek még fel. A már futó szolgáltatások ezeket a feltételeket a futó marad, kivéve, ha az írási műveletek továbbra is működik-e a rendszerszolgáltatások van szükségük. Például ha a Feladatátvevőfürt-kezelő kvórumveszteségben van az összes szolgáltatás továbbra is futni fog, de sikertelen függő szolgáltatások nem lesz automatikusan újra, mivel ehhez a Feladatátvevőfürt-kezelő bevonása. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>Hibák egy adatközpontban vagy az Azure-régió
Bizonyos ritkán előforduló esetekben átmenetileg nem érhető el power vagy a hálózati kapcsolat megszakadása miatt válhat a fizikai adatközpontban. Ezekben az esetekben a Service Fabric-fürtök és a szolgáltatások az adott adatközpont vagy az Azure-régióban lesz elérhető. Azonban _az adatok megmaradjanak_. Az Azure-ban futó fürtök esetén valamilyen okból kimaradás lép a frissítések a megtekintheti a [Azure állapotlapján][azure-status-dashboard]. A valószínűtlen esemény, hogy a fizikai adatközpont részlegesen vagy teljesen megsemmisülésekor, bármely Service Fabric-fürtök üzemeltetett van, vagy a bennük lévő szolgáltatások elveszhetnek. Ez magában foglalja a munkalehetőségek nem készült biztonsági másolat kívül, hogy adatközpontban vagy régióban.

Két különböző stratégiák újraindulnak a még működő a állandó és tartós hiba egyazon adatközpontban vagy régióban van. 

1. Önálló Service Fabric-fürtök futtatása több régióban, és a feladatátvétel és az ezekben a környezetekben közötti visszavenni néhány mechanizmus használatához. Az ilyen több aktív – aktív vagy aktív – passzív fürtmodell kiegészítő felügyeleti és műveletek kód szükséges. Ez is szükséges, a szolgáltatások egy adatközpontban vagy régióban lévő biztonsági mentések koordinálása, hogy elérhetők más adatközpontokban vagy régiókban, ha egy sikertelen lesz. 
2. Futtatása a önálló Service Fabric-fürt több adatközpontban vagy régióban is. Ez a minimális konfigurációs három adatközpontokban vagy régiókban. Az ajánlott érték régiók vagy az adatközpontok öt. Ehhez egy összetettebb fürtjének topológiája. Azonban ez a modell előnye, hogy egy adatközpontban vagy régióban sikertelen lesz konvertálva a katasztrófa utáni normál hiba. Ezek a hibák, amelyek működnek a mechanizmusok fürtök egy adott régión belül tudja kezelni. Tartalék tartományok, a frissítési tartományok és a Service Fabric elhelyezési szabályok győződjön meg arról, számítási feladatok oszlanak meg, hogy azok működését a szokásos hibákat. További információ a házirendek, amelyek segítségével az ilyen típusú fürt szolgáltatások működtetéséhez olvassa a [elhelyezési házirendek](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Véletlenszerűen hibák fürtön hibákhoz vezet.
A Service Fabric Magcsomópontok fogalma rendelkezik. Ezek a csomópontok, melyek fenntartják a mögöttes fürt rendelkezésre állását. Ezek a csomópontok biztosítania kell további csomópontokkal bérleteket létrehozó és bizonyos típusú hálózati hibák során tiebreakers szolgáló mentése marad a fürt segítségével. Ha véletlenszerűen hibák eltávolítása a magcsomópontok többsége a fürtben, és azok nem kerülnek vissza, majd a fürt összevonási kör összecsukása, elveszítette a kezdőérték csomópont kvórumot, és a fürt nem. Az Azure-ban Service Fabric erőforrás-szolgáltató kezeli a Service Fabric-fürt konfigurációkat, és alapértelmezés szerint elosztja Magcsomópontok különböző hibatűrési és frissítési tartományokba elsődleges csomóponttípus; Ha az elsődleges NodeType csomóponttípus van megjelölve Silver vagy Gold tartósság érdekében, ha eltávolít egy kezdőérték-csomópontot, az elsődleges NodeType csomóponttípus méretezés, vagy manuálisan eltávolítja a kezdőérték csomópontot, a fürt megpróbálja a rendelkezésre álló elsődleges NodeType csomóponttípus nem kezdőérték egy másik csomópontra előléptetése kapacitás- és sikertelen lesz, ha kevesebb rendelkezésre álló kapacitást, mint a fürt megbízhatósági szint van szükség az adott elsődleges csomóponttípus van.

Önálló Service Fabric-fürtök és az Azure az "elsődleges csomópont" típus azt, amelyik futtatja a magok. Egy elsődleges csomóponttípus meghatározásakor a Service Fabric lesz automatikusan előnyeit legfeljebb 9 magcsomópontok és az egyes rendszerszolgáltatások 7 replikák létrehozásával megadott csomópontok számát. Ha egy véletlenszerűen hibák készletét egyidejűleg ezen szolgáltatás rendszerreplikák többsége ki tart, a helyrendszeri szolgáltatások lép kvórum elvesztése, azt a fent leírtak szerint. A magcsomópontok többsége elvesznek, ha a fürt le fog állni után minél hamarabb.

## <a name="next-steps"></a>További lépések
- Ismerje meg, hogyan különféle hibák szimulálása a [testability keretrendszer](service-fabric-testability-overview.md)
- Olvassa el a vészhelyreállítás és magas rendelkezésre állású erőforrását. A Microsoft az alábbi témakörök útmutatást nagy mennyiségű tett közzé. Míg néhány ezeket a dokumentumokat más termékekkel való használathoz adott technikák hivatkozunk, számos általánosan ajánlott eljárást a Service Fabric környezetben is alkalmazhat tartalmaznak:
  - [Rendelkezésre állási ellenőrzőlista](../best-practices-availability-checklist.md)
  - [Vészhelyreállítási próba végrehajtása](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Vészhelyreállítás és magas rendelkezésre állás az Azure-alkalmazások][dr-ha-guide]
- A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése


<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
