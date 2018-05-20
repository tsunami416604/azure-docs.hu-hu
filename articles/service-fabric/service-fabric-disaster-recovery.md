---
title: Az Azure Service Fabric vészhelyreállítás |} Microsoft Docs
description: Az Azure Service Fabric az összes típusú katasztrófák kezelése érdekében képességeket biztosít. Ez a cikk ismerteti a típusú katasztrófák, amik akkor léphetnek fel, és azokat kezelése.
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
ms.openlocfilehash: 295772b70529f79c7a4c135d8ea7c12a1c661fe6
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="disaster-recovery-in-azure-service-fabric"></a>Az Azure Service Fabric katasztrófa utáni helyreállítás
A magas rendelkezésre állású kézbesítéséhez kritikus része annak ellenőrzése, hogy a szolgáltatások hibatűrését összes különböző típusú hibák. Ez különösen fontos a nem tervezett hibák és a vezérlőn kívül. Ez a cikk ismerteti az egyes közös függően katasztrófák lehet, ha nem modellezve, és a megfelelő felügyelt. Megoldást és a teendő, ha egy olyan vészhelyzet esetén történt ennek ellenére is ismertetik. A cél, hogy korlátozható vagy kiküszöbölése állásidő vagy adatvesztés kockázatát, amikor jelentkeznek a hibák, a tervezett, vagy ellenkező esetben fordulhat elő.

## <a name="avoiding-disaster"></a>Vészhelyreállítás elkerülése
A Service Fabric elsődleges célja, hogy a modell, mind a környezettől, és a szolgáltatások úgy, hogy általános hiba típusok nincsenek katasztrófák nyújt segítséget. 

Általános vész és sikertelen forgatókönyvek két típusa van:

1. Hardver- vagy hibák
2. Működési hibák

### <a name="hardware-and-software-faults"></a>Hardver- és hibák
Hardver- és hibák előre nem látható. A legegyszerűbb módja a túlélést hibák további példányait a hardver- vagy tartalék határain túlnyúló ölel szolgáltatás fut. Például ha a szolgáltatás csak egy adott számítógép fut, majd, hogy egy géphez hibája, hogy a szolgáltatás egy olyan vészhelyzet esetén. Az egyszerű elkerülése érdekében a katasztrófa módja győződjön meg arról, hogy a szolgáltatás ténylegesen fut-e több gépen. Vizsgálat nem is egy gép hibája nem zavarja a futó szolgáltatás biztosításához szükséges. A kapacitástervezés biztosítja egy helyettesítő példányt is létrehozható máshol, és hogy kapacitásának csökkentése nem túlterhelés a fennmaradó szolgáltatásokat. Ugyanúgy működik, függetlenül attól, milyen kívánt hibája elkerülése érdekében. Például. Ha aggódik egy SAN hibája, több San-okkal keresztül futtatja. Ha aggódik a kiszolgálók állvány elvesztését, akkor több állványra futtatása. Ha aggódik az adatvesztés adatközpontok, a szolgáltatás a több Azure-régiók vagy adatközpontok futhat. 

Ha ilyen típusú átnyúló módban fut, Ön továbbra is egyidejű hibák, de egy bizonyos típusú, és akár több hibák számát egy adott típusának (például: egyetlen virtuális gép vagy a hálózati kapcsolat sikertelen) kezeli automatikusan (és már nem a "vész"). A Service Fabric a bővített a fürt számos mechanizmust biztosít, és kapcsolásának leírók nem sikerült a csomópontok és a szolgáltatások vissza. A Service Fabric is lehetővé teszi a sok szolgáltatáspéldányok típusaival kapcsolatban forduljon a valódi katasztrófa nem tervezett hibák elkerülése érdekében.

Előfordulhat, hogy miért fut elég nagy legyen keresztül hibák span központi telepítés esetén nem valósítható meg okok miatt. Például szükség lehet további hardver-erőforrások, mint Ön hajlandó hibát a legnagyobb viszonyítva kell fizetnie. Elosztott alkalmazások a meghatározásakor, hogy további kommunikációs ugrások vagy állapot replikációs költségek földrajzi távolság okok elfogadhatatlan késleltetés között lehet. Ha ezt a sort megrajzolása eltér az egyes alkalmazásokhoz. Szoftver hibák pontosabban, a tartalék lehet a méretezési próbált szolgáltatás. Ebben az esetben több példányt nem akadályozzák meg a katasztrófa, mivel a hibafeltétel minden példányára visszamenőleges korrelációban állnak.

### <a name="operational-faults"></a>Működési hibák
Akkor is, ha a szolgáltatás a sok létszámcsökkentések világszerte van ölel, továbbra is fedezheti katasztrofális esemény. Ha például valaki véletlenül újrakonfigurálja a szolgáltatás DNS-nevét, vagy törlése végleges. Tegyük fel tegyük fel, az állapotalapú Service Fabric-szolgáltatás volt, és valaki az adott szolgáltatás véletlenül törölt. Kivéve, ha egy másik megoldás, szolgáltatás és az összes kellett az állapot értéke most már meg. Működési katasztrófák ilyen típusú ("sajnos") különböző megoldást és lépéseket helyreállításához szükséges rendszeres nem tervezett leállások-nál. 

A legjobb módszer az ilyen típusú működési hibák elkerülése érdekében a rendszer
1. a környezet működési elérésének korlátozása
2. szigorúan naplózási veszélyes műveletek
3. automatizálási ugyanazok, megakadályozza a manuális és a módosítások sávon kívüli és a tényleges környezet ellen adott módosítások érvényesítése előtt életbe őket
4. Gondoskodjon arról, hogy ilyen beállítások mellett pusztító műveletek "Soft szót". Szoftveres műveletek nem azonnal lépnek érvénybe, vagy visszavonható néhány időkerete belül

A Service Fabric biztosít bizonyos működési hibák, például a biztosítása érdekében [szerepköralapú](service-fabric-cluster-security-roles.md) hozzáférés-vezérlés a fürt működését. A működési hibák többségét kérhetnek szervezeti erőfeszítéseket és más rendszerek. A Service Fabric egyes fennmaradó működési hibák, ilyen például a biztonsági mentési mechanizmus biztosítása, és állítsa vissza az állapotalapú szolgáltatások.

## <a name="managing-failures"></a>Hibák kezelése
A Service Fabric célja, szinte mindig automatikus hiba felügyeletét. Ahhoz, hogy az egyes hibák kezelésére, azonban szolgáltatások rendelkeznie kód. Más típusú hibák kell _nem_ automatikusan címezhető biztonsági és üzleti folytonossági okok miatt. 

### <a name="handling-single-failures"></a>Egyetlen hibák kezelése
Egyetlen gépek mindenféle okból sikertelen lehet. Ezek némelyike hardver oka van, például a áramforrások és a hálózati hardver meghibásodása. Szoftver más hibák vannak. Ezek közé tartozik a hibák számát az aktuális operációs rendszer és a szolgáltatást. A Service Fabric automatikusan észleli a hibákat, beleértve azokat az eseteket, ahol a gép válik különítve a többi gép hálózati problémák miatt az ilyen típusú.

Függetlenül attól, milyen típusú szolgáltatás fut egy példányban eredmények állásidőt, hogy a szolgáltatás Ha bármilyen okból sikertelen, hogy a kód egyetlen példányát. 

Ahhoz, hogy bármely egyetlen hibakezeléshez, a legegyszerűbb művelet, amelyet annak érdekében, hogy a szolgáltatások alapértelmezés szerint ki futtassa egynél több csomópont. Az állapotmentes szolgáltatások ehhez azzal, hogy egy `InstanceCount` 1-nél nagyobb. Állapotalapú szolgáltatások, a minimális ajánljuk, mindig egy `TargetReplicaSetSize` és `MinReplicaSetSize` legalább 3. A szolgáltatáskód hibáit további példányait futtató biztosítja, hogy a szolgáltatás képes automatikusan kezelni minden egyetlen hiba. 

### <a name="handling-coordinated-failures"></a>Kezelési koordinált hibák
Koordinált hiba akkor fordulhat elő, a fürt oka, hogy a tervezett vagy nem tervezett infrastruktúra és módosítások vagy a tervezett szoftver változásainak. A Service Fabric modellek infrastruktúra zónák, hogy a tartalék tartományok, koordinált hibák. Frissítési tartományok, amelyeknek a koordinált szoftvermódosítások területek van modellezve. További információ a tartalék és a frissítési tartományok van [Ez a dokumentum](service-fabric-cluster-resource-manager-cluster-description.md) , amely leírja a fürt topológia és definíciója.

Alapértelmezés szerint a Service Fabric hiba és a frissítési tartományok tekinti, ahol kell futtatni a szolgáltatások tervezése során. Alapértelmezés szerint a Service Fabric megpróbálja győződjön meg arról, hogy a szolgáltatások futtatása több hiba és a frissítési tartományok között, a tervezett vagy nem tervezett módosítások fordulhat elő, ha a szolgáltatások elérhetők maradnak. 

Tételezzük fel például, hogy az áramforrás hibája okozza-e a gépek is sikertelenül végződik, állványra. A tartalék tartomány nem a több gép elvesztését futó szolgáltatás több példányával kapcsolja be egy másik példa egy adott szolgáltatáshoz egyetlen hiba. Éppen ezért a tartalék tartományok kezelése fontos, hogy a szolgáltatások magas rendelkezésre állásának biztosítása. Az Azure Service Fabric futtatásakor tartalék tartományok automatikusan kezeli. Más környezetekben nem lehetnek. Ha a saját telephelyükön található fürtök most felépítése, ügyeljen arra, hogy az rendelve, és a tartalék tartomány elrendezését tervezik megfelelően.

Frissítési tartományok hasznosak modellezési területeken, ahol szoftver lesz frissíthető egyszerre. Ebből kifolyólag frissítési tartományok is gyakran határozza meg azt a határokat, ahol szoftvereket van leállítaná tervezett frissítéskor. A Service Fabric- és a szolgáltatások frissítéseket kövesse ugyanannak a modellnek. A működés közbeni frissítés frissítési tartományok és a Service Fabric állapotmodell, amelyek segítségével a nemkívánatos módosítások megakadályozása a fürt és a szolgáltatást érintő kapcsolatban bővebben lásd: a dokumentumok:

 - [Az alkalmazásfrissítés](service-fabric-application-upgrade.md)
 - [Frissítési vonatkozó oktatóanyag](service-fabric-application-upgrade-tutorial.md)
 - [Service Fabric Állapotmodell](service-fabric-health-introduction.md)

Azzal, hogy a fürt a fürt térképét használatával jelenítheti meg [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md):

<center>
![A Service Fabric Explorerben tartalék tartományok elosztva csomópontok][sfx-cluster-map]
</center>

> [!NOTE]
> Hiba: a működés közbeni frissítés, a szolgáltatáskód hibáit és a állapot több példányát futtató területéhez modellezési elhelyezési szabályokat, hogy biztosítsa a szolgáltatások futtatása hiba és a frissítési tartományok között, és beépített állapotfigyelés csak **néhány** , a a Service Fabric Forduljon katasztrófák a rendes működési problémák és hibák megőrzése érdekében biztosító funkciókat. 
>

### <a name="handling-simultaneous-hardware-or-software-failures"></a>Egyidejű hardver- vagy hibák kezelése
Újabb megtartásról egyetlen hibákról. Látható, a rendszer egyszerűen csak tartja a kód (és állapot) tartalék és verziófrissítési futtatásáért több példányt is állapotmentes és állapotalapú szolgáltatással kezelni. Több egyidejű véletlen hibákat is megtörténhet. Ezek a nagyobb eséllyel tartják be egy tényleges katasztrófa vezethet.


### <a name="random-failures-leading-to-service-failures"></a>Szolgáltatáshibák vezető véletlenszerűen hibák
Tegyük fel, hogy volt-e a szolgáltatás egy `InstanceCount` 5, és több csomópont futó összes példányt nem sikerült egy időben. A Service Fabric válaszol a többi csomóponton helyettesítő példányok automatikusan létrehozásával. Cserékhez létrehozása, amíg a szolgáltatás nem a kívánt példányok száma a továbbra is. Másik példaként, tegyük fel, az állapot nélküli szolgáltatás történt egy `InstanceCount`-1, ami azt jelenti, az a fürt összes érvényes csomópontján fut. Tegyük fel, hogy néhány logikailemez sikertelen volt. Ebben az esetben a Service Fabric észleli, hogy a szolgáltatás nem a megfelelő állapotban van, és megkísérli létrehozni a példányt a csomópontokon, ahol hiányoznak. 

A helyzet állapotalapú szolgáltatások függ, hogy a szolgáltatás rendelkezik megőrzött állapot vagy nem. Is függ, hogyan lehet a szolgáltatás számos replikákat, és hány sikertelen. Hogy egy olyan vészhelyzet esetén egy állapotalapú szolgáltatás történt, ezért a kezelése, a következő három szakaszban meghatározása:

1. Ha történt kvórum elvesztése vagy nem meghatározása
 - A kvórum elvesztése állapotalapú szolgáltatás a replikák többsége nem működik egy időben, ideértve a elsődleges bármikor.
2. Ha a kvórum elvesztése állandó van-e vagy sem
 - Az esetek többségében a hibák átmeneti jellegűek. Folyamatok újraindul, csomópont újraindul, virtuális gépek vannak s, hálózati partíciók javítandó. Egyes esetekben azonban hibák sem lesznek állandó. 
    - A szolgáltatások nélkül megőrzött állapot legalább egy kvórum-replikák eredmények hibát _azonnal_ állandó kvórumveszteségben. Service Fabric kvórum elvesztése állapot-nyilvántartó nem állandó szolgáltatás észleli, ha azonnal halad a 3. lépésre is deklarálni kell dataloss (lehetséges). Dataloss folytatása teljesen logikus, mert a Service Fabric tudja, hogy nincs-e a Várakozás a replikák térjen vissza, mert még akkor is, ha helyre lett állítva azok lenne üres pont.
    - Állapot-nyilvántartó állandó szolgáltatásokhoz replikák legalább egy kvórum hibát okoz a replikák visszatérhet, és állítsa vissza a kvórum vár el a Service Fabric. Az összes ennek eredményeként a szolgáltatáskimaradás _ír_ az érintett partícióhoz (vagy "replikakészlethez") a szolgáltatást. Előfordulhat azonban, olvasási továbbra is lehetséges a csökkentett konzisztencia biztosítja. Az alapértelmezett, hogy mennyi ideig Service Fabric megvárja-e a kvórum állítani végtelen, mivel eljárás az (esetleges) dataloss esemény, és más kockázatokat hordoz magában. Felülbírálja az alapértelmezett `QuorumLossWaitDuration` érték lehet, de nem ajánlott. Ehelyett jelenleg mindent kell tenni a lefelé replikák visszaállítására. Ehhez a mihamarabb elérhetővé tenni a csomópontokat, amelyek biztonsági mentése le, és győződjön meg arról, hogy azok is csatlakoztassa újra a meghajtók, a helyi állandó állapot tárolásához. A kvórum elvesztése folyamat hibája okozza, ha a Service Fabric automatikusan megpróbálja hozza létre újból a folyamatok, és indítsa újra a replikák tartalmaz. Ha ez nem sikerül, a Service Fabric állapot hibát jelez. Ha ezek orvosolni tudja majd a replikák általában térjen vissza. Egyes esetekben azonban a replikák nem térhetnek vissza. Például a meghajtók összes sikertelen volt, vagy a gépek fizikailag megsemmisül valamilyen módon. Ebben az esetben egy állandó kvórum adatvesztés most van. Várakozás a lefelé replikák térjen vissza a Service Fabric állapítható meg, a fürt rendszergazdája meg kell határoznia, partíciók, amelyek szolgáltatásokat érinti, és hívja meg a `Repair-ServiceFabricPartition -PartitionId` vagy ` System.Fabric.FabricClient.ClusterManagementClient.RecoverPartitionAsync(Guid partitionId)` API.  Ez az API lehetővé teszi, hogy QuorumLoss kívül, és lehetséges dataloss történő áthelyezése a partíció Azonosítójának megadása.

> [!NOTE]
> Az _soha nem_ biztonságos, ez az API nem adott partíciókra elleni célzott módon. 
>

3. Meghatározhatja, hogy a tényleges adatvesztés lett, és a biztonsági mentésekből visszaállítása
  - Ha a Service Fabric meghívja a `OnDataLossAsync` miatt a rendszer mindig metódus _gyanús_ dataloss. A Service Fabric biztosítja, hogy a hívás a rendszer a _legjobb_ fennmaradó replika. Ez a tetszőleges kiegészítendő replika tett a legtöbb folyamatban van. A ok azt mindig tegyük fel például _gyanús_ dataloss, lehetséges, hogy a többi replika ténylegesen rendelkezik minden olyan állapotban, ahogy az elsődleges amikor csökkent. Azonban nélkül vele összehasonlítandó állapotban, nincs megfelelő mód a Service Fabric vagy a kezelők biztosan tudni. Ezen a ponton a Service Fabric is tudja, hogy a többi replikához nem érkeznek vissza. A döntést, amikor azt várakozik a kvórum elvesztése a megoldódik, amely volt. A szolgáltatás a helyes intézkedéseket általában rögzítése és megadott rendszergazdai beavatkozás várakozás. Ezért egy tipikus végrehajtásának funkciója a `OnDataLossAsync` metódus tegye?
  - Első lépésként jelentkezzen, amely `OnDataLossAsync` lett elindítva, és minden szükséges felügyeleti riasztások megkezdéséhez.
   - Általában ezen a ponton a várjon további döntések és manuális műveletek végrehajtását. Ennek oka az, akkor is, ha a biztonsági másolatok szükségük lehet, hogy elő kell készíteni. Például ha két különböző szolgáltatások koordinálja az adatokat, azokat a biztonsági mentések esetleg érdekében gondoskodjon arról, hogy a visszaállítás történik, amely után a információt e két szolgáltatás ítélt információkat konzisztens módosítható. 
  - Gyakran van továbbá néhány egyéb telemetriai vagy a szolgáltatás származó. A metaadatok tartalmazhatja az egyéb szolgáltatások vagy a naplókban. Ezeket az információkat annak meghatározásához, hogy voltak-e a hívások fogadása és feldolgozása az elsődleges nem szerepel a biztonsági másolat vagy az adott replika replikált szükséges is használható. Előfordulhat, hogy ezek kell újból vagy hozzáadni a biztonsági mentés előtt visszaállítás esetén valósítható meg.  
   - A fennmaradó replika állapotban lévő összes rendelkezésre álló biztonsági másolatot az összehasonlítást. A Service Fabric megbízható gyűjtemények használatával, akkor vannak eszközökhöz, és feldolgozza a úgy érhető el, ha ismertetett [Ez a cikk](service-fabric-reliable-services-backup-restore.md). A cél, hogy belül a replika állapota megfelelő, vagy milyen a biztonsági mentés is esetleg hiányzik.
  - Ha az összehasonlítást végzett, és szükség esetén a visszaállítás befejeződött, a szolgáltatás kód értéke true, ha az állapot módosítások kell visszaadnia. Ha a replika határozza meg, hogy azt a legjobb elérhető másolatot állapotát, és nem módosítja, majd térjen vissza hamis. Igaz érték azt jelenti, hogy minden _más_ replikák fennmaradó most lehet a erre. Azok lesz dobva, újraépíti a replikából. Hamis azt jelzi, nem állapot változások, így a többi replikához biztosítható, azok rendelkeznek. 

Különösen fontos, hogy szolgáltatás szerzők gyakorlatban lehetséges dataloss és a hibát jelentő forgatókönyvek előtt szolgáltatás telepítve van a termelési. Elleni dataloss lehetőségét, fontos, hogy rendszeres időközönként [készítsen biztonsági másolatot az állapot](service-fabric-reliable-services-backup-restore.md) georedundáns tárolóval az állapotalapú szolgáltatások egyikét sem. Gondoskodnia kell arról is, hogy, hogy a számítógép állítsa vissza azt. Mivel számos különböző szolgáltatások biztonsági mentést készít a különböző időpontokban, győződjön meg arról, hogy a visszaállítást követően a szolgáltatások egymással egységes megjelenítése kell. Vegye figyelembe például olyan helyzet, ahol egy szolgáltatás több hoz létre és tárolja, majd elküldi egy másik szolgáltatás, amely is tárolja. A visszaállítást követően felfedezheti, hogy a második szolgáltatásnak van a számot, de a első nem, mert a biztonsági mentés nem tartalmazza a művelet.

Ha azt megtudhatja, hogy a többi replikák dataloss esetén a folytatáshoz elegendő, és telemetriai vagy kipufogógáz szolgáltatás állapota nem helyreállítására, a biztonsági mentések meghatározza, hogy a legjobb lehetséges helyreállítási időkorlát (RPO). A Service Fabric számos eszközt kínál a tesztelési különböző sikertelen forgatókönyvek, köztük a végleges kvórum és dataloss van szükség egy biztonsági másolatból. Ezek a forgatókönyvek szerepelnek a Service Fabric tesztelhetőségi eszközök, a tartalék elemzési szolgáltatás által kezelt részeként. További információ ezen eszközök és a minták érhető [Itt](service-fabric-testability-overview.md). 

> [!NOTE]
> Rendszerszolgáltatások is az adott szolgáltatásra vonatkozó terheli a kvórum elvesztése esetén csökkenhet. Például az elnevezési szolgáltatásban a kvórum elvesztése hatással van a névfeloldás, mivel a kvórum elvesztése a Feladatátvevőfürt-kezelő szolgáltatás az új szolgáltatás létrehozása és a feladatátvétel blokkolja. A Service Fabric rendszer szolgáltatások ugyanazt a sablont, az állapotkezelést a szolgáltatásként követi, amíg nem ajánlott, hogy meg kell próbálni kívül a kvórum elvesztése, valamint a potenciális dataloss helyezze azokat. A javasoljuk helyette, hogy [támogatási pozícionálni](service-fabric-support.md) olyan megoldás, amely az adott helyzetnek meghatározásához.  Általában célszerű egyszerűen Várjon, amíg a lefelé replikák adja vissza.
>

## <a name="availability-of-the-service-fabric-cluster"></a>A Service Fabric-fürt rendelkezésre állása
Általánosságban véve a egyrészt a Service Fabric-fürt egyetlen meghibásodási ponttal rendelkező magas elosztott környezetben. A hibát, hogy egy csomópont jelenléte nem okoz rendelkezésre állás vagy a fürt megbízhatósági problémák elsősorban, mert a Service Fabric rendszerszolgáltatások kövesse a korábban azonos útmutatásait: alapértelmezés szerint, és ezeket a rendszer három vagy több replikával mindig futnak állapotmentes szolgáltatások futtatása az összes olyan csomóponton. Az alapul szolgáló Service Fabric hálózat és a hiba észlelése rétegek teljesen terjesztése. A legtöbb rendszerszolgáltatások úgy is, a metaadatok a fürtben, vagy tudja, hogyan kell más helyeiről állapotukra újraszinkronizálása. A fürt rendelkezésre állását képes lesz biztonsága sérül, ha rendszerszolgáltatások feltölti a kvórum elvesztése helyzetek például szerepel a fenti. Ezekben az esetekben, előfordulhat, hogy nem tudják végrehajtani bizonyos műveletek, például a frissítés elindítása vagy új szolgáltatások telepítése a fürtön, de egyrészt a fürt működik-e továbbra is. Már fut a szolgáltatás fut, ezek a feltételek a marad, kivéve, ha szükségük van az írási műveleteket a rendszer szolgáltatásokhoz is működjenek. Például ha a Feladatátvevőfürt-kezelő kvórumveszteségben van az összes szolgáltatás továbbra is futtatható, de eleget nem tevő szolgáltatások nem tudnak automatikusan újraindul, mert ehhez a Feladatátvevőfürt-kezelő bevonása. 

### <a name="failures-of-a-datacenter-or-azure-region"></a>A datacenter vagy az Azure-régió hibák
Ritka esetekben a fizikai adatközpont átmenetileg nem érhető el, a teljesítmény vagy a hálózati kapcsolat megszakadása miatt válhat. Ezekben az esetekben a Service Fabric-fürtök és a szolgáltatások, az adott datacenter vagy az Azure-régió nem lesz elérhető. Azonban _az adatok megmaradjanak_. Az Azure-ban futó fürtök esetén megtekintheti kimaradások esetén a frissítések a [Azure állapotlapon][azure-status-dashboard]. A nagyon valószínű esemény, hogy a fizikai adatközpont részlegesen vagy teljesen megsemmisül, a Service Fabric-fürtök üzemeltetett van, vagy a szolgáltatások tartalmaz elveszhet. Ez magában foglalja a bármely kívül adatközpontját, illetve a régió nem biztonsági állapotát.

Két különböző stratégiák a még működő egyetlen datacenter vagy terület a végleges vagy tartós hiba van. 

1. Különálló Service Fabric-fürtök futtassa több régióba, és a feladatátvétel és visszavenni a feladatokat a különböző környezetek között valamilyen mód használatára. Az ilyen több fürt aktív-aktív vagy aktív-passzív modell felügyelete és műveletei kód szükséges. Ez is szükséges, a szolgáltatások egy adatközpontban, illetve a régió készített biztonsági másolat összehangolását, hogy elérhetők más adatközpontok vagy régiókban, ha egy sikertelen lesz. 
2. Futtassa egy egyetlen Service Fabric-fürt több adatközpontban vagy régiókból is. A támogatott konfigurációkra vonatkozó minimális ez három adatközpontban vagy régióban. A régiók vagy adatközpontok ajánlott száma öt. Ehhez egy összetettebb fürtjének topológiája. Azonban ez a modell előnye, hogy egy datacenter vagy régió sikertelen alakul át egy olyan vészhelyzet esetén a normál hiba. Ezek a hibák kezelhetik a mechanizmusok, amelyek működnek fürtök egyetlen régión belül. Tartalék tartományok, a frissítési tartományok és a Service Fabric elhelyezési szabályokat győződjön meg arról, munkaterhelések terjesztése, hogy azok működését normál hibák. Amely segít az ilyen típusú fürt szolgáltatások működtetéséhez házirendekkel kapcsolatos további információkért olvassa a [elhelyezési házirendeket](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md)

### <a name="random-failures-leading-to-cluster-failures"></a>Fürt hibák vezető véletlenszerűen hibák
A Service Fabric kezdőérték csomópontok fogalma rendelkezik. Ezek a csomópontokra, amelyeket az alapul szolgáló fürt rendelkezésre állását fenntartásához. Ezek a csomópontok így biztosítható, hogy a fürt továbbra is másolatot létrehozó további csomópontokkal bérleteket és bizonyos típusú hálózati hibák során tiebreakers szolgál. Ha a véletlenszerűen hibák távolítsa el a kezdőérték csomópontok többsége a fürtben, és azok nem teszik vissza, a fürt automatikusan leáll. Az Azure kezdőérték csomópontok automatikusan kezelt: keresztül a rendelkezésre álló tartalék és verziófrissítési továbbítja, és egyetlen kezdőérték csomópontot a fürtről eltávolításakor létrejön-e egy másik helyén. 

Különálló Service Fabric-fürtök és Azure az "elsődleges csomópont" típus azt, amelyik futtatja a magok. Egy elsődleges csomóponttípusok meghatározásakor a Service Fabric lesz automatikusan előnyeit legfeljebb 9 kezdőérték csomópontok és a rendszer szolgáltatások 9 replikáinak létrehozott csomópontok száma. Ha egy véletlenszerűen hibák készletét ki ezeket a rendszer szolgáltatás replikák többsége fogad adatokat egyidejűleg, a rendszerszolgáltatások lép kvórum elvesztése, azt a fent leírt módon. Ha a kezdőérték csomópontok többsége elvesznek, a fürt le fog állni hamarosan után.

## <a name="next-steps"></a>További lépések
- Megtudhatja, hogyan használatával különféle hibák szimulálása a [tesztelhetőségi keretrendszer](service-fabric-testability-overview.md)
- Olvassa el a vész-helyreállítási és magas rendelkezésre állású erőforrását. A Microsoft tett közzé útmutatást nagy mennyiségű ezekben a kérdésekben. Közben néhány ezeket a dokumentumokat adott technikák használható egyéb termékek, sok általános gyakorlati tanácsok a Service Fabric környezetben is alkalmazhat tartalmazzák:
  - [Rendelkezésre állási ellenőrzőlista](../best-practices-availability-checklist.md)
  - [A vész-helyreállítási részletezési végrehajtása](../sql-database/sql-database-disaster-recovery-drills.md)
  - [Vész-helyreállítási és magas rendelkezésre állás a Azure-alkalmazások][dr-ha-guide]
- A [Service Fabric támogatási lehetőségeinek](service-fabric-support.md) ismertetése

<!-- External links -->

[repair-partition-ps]: https://msdn.microsoft.com/library/mt163522.aspx
[azure-status-dashboard]:https://azure.microsoft.com/status/
[azure-regions]: https://azure.microsoft.com/regions/
[dr-ha-guide]: https://msdn.microsoft.com/library/azure/dn251004.aspx


<!-- Images -->

[sfx-cluster-map]: ./media/service-fabric-disaster-recovery/sfx-clustermap.png
