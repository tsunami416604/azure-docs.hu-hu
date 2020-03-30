---
title: A Service Fabric-szolgáltatások méretezhetősége
description: Ismerje meg az Azure Service Fabric méretezését és az alkalmazások méretezéséhez használt különböző technikákat.
author: masnider
ms.topic: conceptual
ms.date: 08/26/2019
ms.author: masnider
ms.openlocfilehash: 17827342b67d37d9fbeb56654824e004367823ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79282561"
---
# <a name="scaling-in-service-fabric"></a>Méretezés a szolgáltatáshálóban
Az Azure Service Fabric megkönnyíti a méretezhető alkalmazások létrehozása a szolgáltatások, partíciók és replikák kezelése a fürt csomópontjain. Számos számítási feladat futtatása ugyanazon a hardveren lehetővé teszi a maximális erőforrás-kihasználtságot, de rugalmasságot is biztosít a számítási feladatok méretezése tekintetében. Ez a Channel 9 videó bemutatja, hogyan hozhat létre méretezhető mikroszolgáltatási alkalmazásokat:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

Méretezés a Service Fabric számos különböző módon érhető el:

1. Méretezés állapotmentes szolgáltatáspéldányok létrehozásával vagy eltávolításával
2. Méretezés új elnevezett szolgáltatások létrehozásával vagy eltávolításával
3. Méretezés új elnevezett alkalmazáspéldányok létrehozásával vagy eltávolításával
4. Méretezés particionált szolgáltatások használatával
5. Méretezés csomópontok hozzáadásával és eltávolításával a fürtből 
6. Méretezés a Fürterőforrás-kezelő mérőszámai használatával

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Méretezés állapotmentes szolgáltatáspéldányok létrehozásával vagy eltávolításával
A Service Fabric-en belüli méretezés egyik legegyszerűbb módja állapotnélküli szolgáltatásokkal működik. Amikor állapotmentes szolgáltatást hoz létre, lehetősége van `InstanceCount`a . `InstanceCount`meghatározza, hogy a szolgáltatás indításakor hány futó példány jön létre a szolgáltatás kódjáról. Tegyük fel például, hogy 100 csomópont van a fürtben. Tegyük fel azt is, hogy `InstanceCount` egy szolgáltatás 10-es sel jön létre. Futásközben a kód 10 futó példánya idoben túl elfoglalt (vagy nem lehet elég elfoglalt). A számítási feladatok méretezésének egyik módja a példányok számának módosítása. Például néhány figyelési vagy felügyeleti kód módosíthatja a meglévő példányok száma 50, vagy 5, attól függően, hogy a számítási feladatok kell skálázni, vagy ki a terhelés alapján. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

Powershell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>A dinamikus példányok számának használata
Kifejezetten az állapotmentes szolgáltatások, Service Fabric automatikus módja a példányszám módosításának. Ez lehetővé teszi, hogy a szolgáltatás dinamikusan skálázható a rendelkezésre álló csomópontok számával. A viselkedés beállításának módja a példányszám = -1 beállítása. InstanceCount = -1 egy utasítás a Service Fabric, amely azt mondja: "Futtassa ezt az állapotmentes szolgáltatást minden csomóponton." Ha a csomópontok száma megváltozik, a Service Fabric automatikusan módosítja a példányok számát egyezésre, biztosítva, hogy a szolgáltatás fut az összes érvényes csomóponton. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

Powershell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Méretezés új elnevezett szolgáltatások létrehozásával vagy eltávolításával
Egy elnevezett szolgáltatáspéldány egy szolgáltatástípus egy adott példánya (lásd: [Service Fabric-alkalmazás életciklusa)](service-fabric-application-lifecycle.md)a fürt néhány elnevezett alkalmazáspéldányán belül. 

Új elnevezett szolgáltatáspéldányok hozhatók létre (vagy távolíthatók el), ahogy a szolgáltatások többé-kevésbé elfoglaltak lesznek. Ez lehetővé teszi, hogy a kérelmek több szolgáltatáspéldány között legyenek elosztva, ami általában lehetővé teszi a meglévő szolgáltatások terhelésének csökkentését. Szolgáltatások létrehozásakor a Service Fabric fürterőforrás-kezelő elosztott módon helyezi el a szolgáltatásokat a fürtben. A pontos döntéseket a fürt [metrikái](service-fabric-cluster-resource-manager-metrics.md) és más elhelyezési szabályok szabályozzák. A szolgáltatások többféleképpen is létrehozhatók, de a leggyakoribbak vagy a felügyeleti műveletek, például a hívás, [`New-ServiceFabricService`](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps)vagy a kódhívás. [`CreateServiceAsync`](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet) `CreateServiceAsync`a fürtben futó más szolgáltatásokból is meghívhatók.

A szolgáltatások dinamikus létrehozása mindenféle forgatókönyvben használható, és gyakori minta. Vegyünk például egy állapotalapú szolgáltatást, amely egy adott munkafolyamatot képvisel. A munkát képviselő hívások megjelennek a szolgáltatás számára, és ez a szolgáltatás végrehajtja a munkafolyamat lépéseit, és rögzíti a folyamatot. 

Hogyan tudná ezt a bizonyos szolgáltatási skálát? A szolgáltatás lehet több-bérlős valamilyen formában, és fogadja a hívásokat, és indítsa el a lépéseket számos különböző példányai ugyanazon munkafolyamat egyszerre. Ez azonban bonyolultabbá teheti a kódot, mivel most ugyanazon munkafolyamat számos különböző példánya miatt kell aggódnia, mindezt különböző szakaszokban és különböző ügyfelektől. Emellett több munkafolyamat egyidejű kezelése nem oldja meg a méretezési problémát. Ennek az az oka, hogy egy bizonyos ponton ez a szolgáltatás túl sok erőforrást fog felhasználni egy adott gépen való elféréshez. Sok szolgáltatás nem épített ez a minta az első helyen is nehézségekbe ütköznek miatt néhány rejlő szűk keresztmetszet vagy lassulás a kódot. Az ilyen típusú problémák miatt a szolgáltatás nem működik olyan jól, ha az általa nyomon követett párhuzamos munkafolyamatok száma nagyobb lesz.  

A megoldás az, hogy hozzon létre egy példányt a szolgáltatás minden különböző példányát a munkafolyamat követni kívánt. Ez egy nagyszerű minta, és működik, hogy a szolgáltatás állapot nélküli vagy állapotalapú. Ahhoz, hogy ez a minta működjön, általában van egy másik szolgáltatás, amely "Számítási feladatok kezelőszolgáltatásaként" működik. Ennek a szolgáltatásnak az a feladata, hogy kéréseket fogadjon, és ezeket más szolgáltatásokhoz irányítsa. A kezelő dinamikusan létrehozhatja a számítási feladat szolgáltatás egy példányát, amikor megkapja az üzenetet, majd továbbítja a kérelmeket ezeknek a szolgáltatásoknak. A kezelői szolgáltatás visszahívásokat is kaphat, ha egy adott munkafolyamat-szolgáltatás befejezi a feladatát. Amikor a vezető megkapja ezeket a visszahívásokat, törölheti a munkafolyamat-szolgáltatás adott példányát, vagy hagyhatja el, ha további hívásokra van szükség. 

Az ilyen típusú kezelő speciális verziói még az általa kezelt szolgáltatások készleteit is létrehozhatják. A készlet segít biztosítani, hogy amikor egy új kérelem érkezik, hogy nem kell várni a szolgáltatás felpörgetése. Ehelyett a vezető csak válasszon egy munkafolyamat-szolgáltatást, amely jelenleg nem foglalt a készletből, vagy véletlenszerűen irányíthatja. A szolgáltatások készletének rendelkezésre tartása gyorsabbá teszi az új kérelmek kezelését, mivel kevésbé valószínű, hogy a kérésnek meg kell várnia egy új szolgáltatás felpörgetését. Az új szolgáltatások létrehozása gyors, de nem ingyenes vagy azonnali. A készlet segít minimalizálni a kérelem kiszolgálása előtt megvárandó időt. Gyakran látni fogja ezt a kezelőt és a készletmintát, amikor a válaszidők a legfontosabbak. A kérelem sorban állása és a szolgáltatás létrehozása a háttérben, _majd_ továbbadása szintén népszerű kezelői minta, mivel a szolgáltatás jelenleg függőben lévő munka mennyiségének nyomon követése alapján szolgáltatások létrehozása és törlése. 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Méretezés új elnevezett alkalmazáspéldányok létrehozásával vagy eltávolításával
A teljes alkalmazáspéldányok létrehozása és törlése hasonló a szolgáltatások létrehozásának és törlésének mintájához. Ebben a mintában van néhány kezelői szolgáltatás, amely a döntést az általa látott kérelmek és a fürtön belüli egyéb szolgáltatásoktól kapott információk alapján hoz. 

Mikor kell létrehozni egy új elnevezett alkalmazáspéldányt ahelyett, hogy új névvel ellátott szolgáltatáspéldányokat hozna létre néhány már meglévő alkalmazásban? Van néhány eset:

  * Az új alkalmazáspéldány olyan ügyfélnek szól, akinek a kódját bizonyos identitás- vagy biztonsági beállítások alatt kell futtatni.
    * A Service Fabric lehetővé teszi a különböző kódcsomagok definiálását adott identitások alatt való futtatásra. Annak érdekében, hogy ugyanazt a kódcsomagot különböző identitások alatt indíthassa el, az aktiválásokat különböző alkalmazáspéldányokban kell benyújtani. Fontolja meg egy olyan esetet, amikor egy meglévő ügyfél munkaterhelései üzembe vannak helyezve. Előfordulhat, hogy ezek egy adott identitás alatt futnak, így figyelheti és szabályozhatja a más erőforrásokhoz, például távoli adatbázisokhoz vagy más rendszerekhez való hozzáférésüket. Ebben az esetben, amikor egy új ügyfél regisztrál, valószínűleg nem szeretné aktiválni a kódot ugyanabban a környezetben (feldolgozási terület). Bár lehetséges, ez megnehezíti a szolgáltatási kód egy adott identitás környezetében való cselekvést. Általában nagyobb biztonsággal, elkülönítési és identitáskezelési kóddal kell rendelkeznie. Ahelyett, hogy különböző elnevezett szolgáltatáspéldányokat használna ugyanazon az alkalmazáspéldányon belül, és így ugyanazt a folyamatteret, különböző nevű Service Fabric-alkalmazáspéldányokat használhat. Ez megkönnyíti a különböző identitáskörnyezetek definiálását.
  * Az új alkalmazáspéldány konfigurációs eszközként is szolgál
    * Alapértelmezés szerint egy adott szolgáltatástípus összes elnevezett szolgáltatáspéldánya egy alkalmazáspéldányon belül ugyanabban a folyamatban fog futni egy adott csomóponton. Ez azt jelenti, hogy bár az egyes szolgáltatáspéldányokat másképp konfigurálhatja, ez bonyolult. A szolgáltatásoknak rendelkezniük kell bizonyos jogkivonattal, amelyet a konfigurációs csomagon belüli konfigurációjuk kinézetéhez használnak. Általában ez csak a szolgáltatás neve. Ez jól működik, de a konfigurációt az adott alkalmazáspéldányon belüli egyéni nevű szolgáltatáspéldányok nevével párja. Ez zavaró és nehezen kezelhető, mivel a konfiguráció általában egy tervezési idő összetevő alkalmazáspéldány-specifikus értékeket. Több szolgáltatás létrehozása mindig azt jelenti, több alkalmazás frissítéseket az információk módosítása a konfigurációs csomagok, vagy újak üzembe helyezését, hogy az új szolgáltatások is keresse meg a konkrét információkat. Gyakran könnyebb létrehozni egy teljesen új elnevezett alkalmazáspéldányt. Ezután az alkalmazás paramétereisegítségével beállíthatja a szolgáltatásokhoz szükséges konfigurációt. Így az elnevezett alkalmazáspéldányon belül létrehozott összes szolgáltatás örökölhet bizonyos konfigurációs beállításokat. Ha például egyetlen konfigurációs fájllal rendelkezik, amely minden ügyfél beállításait és testreszabásait használja, például titkos kulcsokat vagy ügyfélerőforrás-korlátokat, ehelyett minden ügyfélhez más-más alkalmazáspéldány tartozik, amely ezekkel a beállításokkal rendelkezik. Felülbírálva. 
  * Az új alkalmazás frissítési határként szolgál
    * A Service Fabric különböző elnevezett alkalmazáspéldányok a frissítés határaként szolgálnak. Egy elnevezett alkalmazáspéldány frissítése nem befolyásolja azt a kódot, amelyet egy másik elnevezett alkalmazáspéldány futtat. A különböző alkalmazások a végén fut különböző változatai ugyanazt a kódot ugyanazon a csomóponton. Ez lehet egy tényező, ha kell, hogy egy skálázási döntést, mert kiválaszthatja, hogy az új kódot kell követnie ugyanazt a frissítéseket, mint egy másik szolgáltatás, vagy sem. Tegyük fel például, hogy egy hívás érkezik a kezelői szolgáltatás, amely felelős egy adott ügyfél számítási feladatok dinamikus létrehozásával és törlésével. Ebben az esetben azonban a hívás egy _új_ ügyfélhez társított számítási feladatra szól. A legtöbb ügyfél szereti, ha nem csak a korábban felsorolt biztonsági és konfigurációs okok miatt különítik el egymást, hanem azért is, mert nagyobb rugalmasságot biztosít a szoftver adott verzióinak futtatása és a frissítés időpontjának kiválasztása tekintetében. Létrehozhat egy új alkalmazáspéldányt is, és létrehozhatja a szolgáltatást egyszerűen a szolgáltatások azon mennyiségének további particionálásához, amelyeket bármely frissítés érinteni fog. A különálló alkalmazáspéldányok nagyobb részletességet biztosítanak az alkalmazásfrissítések során, és engedélyezik az A/B tesztelést és a kék/zöld üzemelő példányokat. 
  * A meglévő alkalmazáspéldány megtelt
    * A Service Fabric alkalmazásban az [alkalmazás kapacitása](service-fabric-cluster-resource-manager-application-groups.md) egy olyan koncepció, amely az adott alkalmazáspéldányok számára rendelkezésre álló erőforrások mennyiségének szabályozására használható. Például dönthet úgy, hogy egy adott szolgáltatás kell egy másik példányt létrehozni a méretezéshez. Azonban ez az alkalmazáspéldány kapacitása egy bizonyos metrika. Ha ez az adott ügyfél vagy számítási feladat továbbra is több erőforrást kell biztosítani, akkor növelheti az adott alkalmazás meglévő kapacitását, vagy létrehozhat egy új alkalmazást. 

## <a name="scaling-at-the-partition-level"></a>Méretezés a partíció szintjén
A Service Fabric támogatja a particionálást. A particionálás a szolgáltatást több logikai és fizikai szakaszra osztja fel, amelyek mindegyike egymástól függetlenül működik. Ez akkor hasznos, állapotalapú szolgáltatások, mivel a replikák egyetlen készlete sem kezeli az összes hívást, és egyszerre kezeli az összes állapotot. A [particionálás áttekintése](service-fabric-concepts-partitioning.md) a támogatott particionálási sémák típusairól tartalmaz tájékoztatást. Az egyes partíciók replikái egy fürt csomópontjai között vannak elosztva, elosztva a szolgáltatás terhelését, és biztosítva, hogy sem a szolgáltatás egésze, sem a partíció egyetlen hibapont nem rendelkezik. 

Vegyünk egy szolgáltatást, amely egy tartományú particionálási séma egy alacsony kulcs 0, egy magas kulcs 99, és a partíció száma 4. Egy háromcsomópontos fürtben a szolgáltatás négy replikával rendelkezhet, amelyek az erőforrásokat osztják meg az egyes csomópontokon, ahogy az itt látható:

<center>

![Partícióelrendezés három csomón](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Ha növeli a csomópontok számát, a Service Fabric áthelyezi a meglévő replikák egy részét. Tegyük fel például, hogy a csomópontok száma négyre nő, és a replikák újraelosztásra kerülnek. Most a szolgáltatás most már három replikák fut minden csomóponton, mindegyik tartozó különböző partíciókat. Ez lehetővé teszi a jobb erőforrás-kihasználtságot, mivel az új csomópont nem hideg. Általában azt is javítja a teljesítményt, mivel minden szolgáltatás több erőforrás áll rendelkezésre.

<center>

![Négy csomós partícióelrendezés](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>Méretezés a Service Fabric-fürterőforrás-kezelő és a metrikák használatával
[Metrikák,](service-fabric-cluster-resource-manager-metrics.md) amelyek a szolgáltatások erőforrás-felhasználás a Service Fabric. A metrikák használata lehetőséget ad a fürterőforrás-kezelőnek a fürt elrendezésének átszervezésére és optimalizálására. Előfordulhat például, hogy a fürtben rengeteg erőforrás található, de előfordulhat, hogy nem lesznek hozzárendelve a jelenleg munkát okozó szolgáltatásokhoz. A metrikák használata lehetővé teszi, hogy a fürterőforrás-kezelő átszervezze a fürtöt annak érdekében, hogy a szolgáltatások hozzáférjenek a rendelkezésre álló erőforrásokhoz. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Méretezés csomópontok hozzáadásával és eltávolításával a fürtből 
Egy másik lehetőség a Service Fabric méretezése a fürt méretének módosítása. A fürt méretének módosítása azt jelenti, hogy csomópontokat ad hozzá vagy távolít el a fürt egy vagy több csomóponttípusához. Vegyünk például egy olyan esetet, amikor a fürt összes csomópontja forró. Ez azt jelenti, hogy a fürt erőforrásai szinte minden felhasznált. Ebben az esetben további csomópontok hozzáadása a fürthöz a legjobb módja a méretezésnek. Miután az új csomópontok csatlakoztak a fürthöz, a Service Fabric-fürt erőforrás-kezelő helyezi át a szolgáltatásokat, így kevesebb teljes terhelést a meglévő csomópontokon. Állapotnélküli szolgáltatások példányszám = -1, több szolgáltatáspéldány automatikusan jön létre. Ez lehetővé teszi, hogy egyes hívások a meglévő csomópontokról az új csomópontokra lépjenek. 

További információt a [Fürtméretezés](service-fabric-cluster-scaling.md)című témakörben talál.

## <a name="choosing-a-platform"></a>Platform kiválasztása

Az operációs rendszerek közötti végrehajtási különbségek miatt a Service Fabric Windows vagy Linux használatával való használata az alkalmazás méretezésének létfontosságú része lehet. Az egyik lehetséges akadály a szakaszos naplózás végrehajtása. Service Fabric windowsos használ kernel-illesztőprogramot egy-egy gép napló, állapotalapú szolgáltatás replikák között megosztott. Ez a napló súlya körülbelül 8 GB. A Linux viszont minden egyes replika esetében 256 MB-os átmeneti naplót használ, így kevésbé ideális az olyan alkalmazások számára, amelyek maximalizálni szeretnék az adott csomóponton futó könnyű szolgáltatásreplikák számát. Ezek a különbségek az ideiglenes tárolási követelmények potenciálisan tájékoztatja a kívánt platform service fabric-fürt üzembe helyezéséhez.

## <a name="putting-it-all-together"></a>Végső összeállítás
Fogjunk minden ötletet, amit itt megbeszéltünk, és beszéljünk egy példát. Vegye figyelembe a következő szolgáltatást: olyan szolgáltatást próbál felépíteni, amely címjegyzékként működik, és ragaszkodik a nevekhez és a kapcsolattartási adatokhoz. 

Közvetlenül elöl van egy csomó kérdést kapcsolatos skála: Hány felhasználó fogsz volna? Hány névjegyet tárolnak az egyes felhasználók? Kipróbálás -hoz kiszámít ez minden ki mikor vagy álló megjelöl -a szolgáltatás először van bonyolult. Tegyük fel, hogy egyetlen statikus szolgáltatást szeretne egy adott partíciószámmal. A rossz partíciószám kiválasztásának következményei később méretezési problémákat okozhatnak. Hasonlóképpen, még akkor is, ha a megfelelő számot választja, előfordulhat, hogy nem rendelkezik minden szükséges információval. Például azt is meg kell döntenie, hogy a fürt mérete elöl, mind a csomópontok száma és azok mérete. Általában nehéz megjósolni, hogy egy szolgáltatás hány erőforrást fog felhasználni az élettartama során. Azt is nehéz tudni, hogy idő előtt a forgalmi minta, hogy a szolgáltatás valóban látja. Például, lehet, hogy az emberek hozzá, és távolítsa el a kapcsolatok csak az első dolog, reggel, vagy talán ez egyenletesen oszlik el a nap folyamán. Ennek alapján előfordulhat, hogy ki kell bővítenie és dinamikusan kell kiskáláznia. Lehet, hogy meg tudja tanulni megjósolni, hogy mikor kell kibővítenie és bekell lépnie, de akárhogy is, valószínűleg reagálnia kell a szolgáltatás erőforrás-felhasználásának változására. Ez magában foglalhatja a fürt méretének módosítását annak érdekében, hogy több erőforrást biztosítson, ha a meglévő erőforrások használatának átszervezése nem elegendő. 

De miért is próbálja felvenni egy partícióséma ki az összes felhasználó számára? Miért korlátozza magát egy szolgáltatásra és egy statikus fürtre? A valós helyzet általában dinamikusabb. 

A méretezéssorán vegye figyelembe a következő dinamikus mintát. Előfordulhat, hogy a helyzethez kell igazítania:

1. Ahelyett, hogy megpróbálna egy particionálási sémát választani mindenki számára, hozzon létre egy "kezelői szolgáltatást".
2. A kezelői szolgáltatás feladata, hogy megtekintse az ügyfelek adatait, amikor feliratkoznak a szolgáltatásra. Ezután attól függően, hogy az információ a menedzser szolgáltatás hozzon létre egy példányát a _tényleges_ kapcsolat-tárolási szolgáltatás _csak az adott ügyfél számára._ Ha speciális konfigurációt, elkülönítést vagy frissítéseket igényelnek, dönthet úgy is, hogy egy alkalmazáspéldányt hoz létre ehhez az ügyfélhez. 

Ez a dinamikus létrehozási minta számos előnnyel jár:

  - Nem próbálja kitalálni a megfelelő partíciók száma az összes felhasználó előre, vagy hozzon létre egy szolgáltatás, amely végtelenül skálázható minden saját. 
  - A különböző felhasználóknak nem kell azonos partíciószámmal, replikaszámmal, elhelyezési megkötésekkel, metrikákkal, alapértelmezett terhelésekkel, szolgáltatásnevekkel, dns-beállításokkal vagy a szolgáltatás vagy alkalmazás szintjén megadott egyéb tulajdonságokkal rendelkezniük. 
  - További adatszegmentálást kap. Minden ügyfélnek saját példánya van a szolgáltatásról
    - Minden ügyfélszolgálat konfigurálható másképpen, és több vagy kevesebb erőforrást biztosít, több vagy kevesebb partíciót vagy replikákat szükség szerint a várható méretezés alapján.
      - Tegyük fel például, hogy az ügyfél fizetett a "Gold" rétegért – több replikát vagy nagyobb partíciószámot kaphatnak, és potenciálisan a metrikákon és az alkalmazáskapacitásokon keresztül a szolgáltatásaikhoz dedikált erőforrásokat kaphatnak.
      - Vagy azt mondják, hogy információt adtak, jelezve, hogy a kapcsolatok száma szükséges volt "Kicsi" - akkor kap csak néhány partíciót, vagy akár fel egy megosztott szolgáltatáskészlet más ügyfelekkel.
  - Nem futtat egy csomó szolgáltatáspéldányt vagy replikát, miközben arra vár, hogy az ügyfelek megjelenjenek
  - Ha egy ügyfél valaha is elhagyja, majd eltávolítja az adatokat a szolgáltatás ból olyan egyszerű, mint miután a vezető törli a szolgáltatást vagy alkalmazást, hogy az általa létrehozott.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmairól az alábbi cikkekben talál további információt:

* [A Service Fabric-szolgáltatások elérhetősége](service-fabric-availability-services.md)
* [Particionálási szolgáltatás fabric szolgáltatások](service-fabric-concepts-partitioning.md)
