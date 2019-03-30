---
title: A Service Fabric-szolgáltatások méretezhetőség |} A Microsoft Docs
description: Ismerteti a Service Fabric-szolgáltatások méretezése
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 14a7389fe562b5f3206b81411d2224257051c636
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58666647"
---
# <a name="scaling-in-service-fabric"></a>A Service Fabricben méretezése
Az Azure Service Fabric megkönnyíti a skálázható alkalmazások készítését a szolgáltatások, a partíciók és a egy fürt csomópontjait a replikák kezelése. Számos számítási feladatok futtatása ugyanazon a hardveren való lehetővé teszi, hogy a legnagyobb erőforrás-használatot, de hogyan úgy dönt, hogy a számítási feladatok skálázása rugalmasságot is biztosít. A Channel 9-videók ismerteti, hogyan hozhat létre méretezhető mikroszolgáltatás-alkalmazások:

> [!VIDEO https://channel9.msdn.com/Events/Connect/2017/T116/player]

A Service Fabric méretezés több módon valósítható meg:

1. Skálázás állapotmentes szolgáltatás példányainak eltávolításával vagy létrehozása
2. Szolgáltatások létrehozása, illetve eltávolításával új skálázás nevű
3. Az alkalmazáspéldányok létrehozásában és eltávolításában új skálázása nevű
4. Skálázás particionált szolgáltatások használatával
5. Skálázás hozzáadásával és eltávolításával a csomópontot a fürtből 
6. Azáltal, hogy a fürterőforrás-kezelő mérőszámok segítségével

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Skálázás állapotmentes szolgáltatás példányainak eltávolításával vagy létrehozása
Az egyik legegyszerűbb módja lévő Service Fabric állapotmentes szolgáltatások együttműködik. Állapotmentes szolgáltatás létrehozásakor kap arra, hogy adja meg egy `InstanceCount`. `InstanceCount` határozza meg, hány példányban fut a szolgáltatás kód jön létre, amikor a szolgáltatás elindul. Tegyük fel, például, hogy nincsenek-e 100 csomópont a fürthöz. Is tegyük fel, hogy a szolgáltatás létrejött-e az egy `InstanceCount` 10. Futásidőben a kód 10 futó példányokat összes válhat túlságosan elfoglalt (vagy sikerült nem lehet foglalt elég). Egy adott munkaterhelés módja a példányok számát. Például bizonyos kódrészleteket figyelési és felügyeleti a meglévő példányok száma 50-re, vagy módosíthatja az 5, attól függően, hogy a számítási feladatok kell skálázni vagy a terhelés alapján. 

C#:

```csharp
StatelessServiceUpdateDescription updateDescription = new StatelessServiceUpdateDescription(); 
updateDescription.InstanceCount = 50;
await fabricClient.ServiceManager.UpdateServiceAsync(new Uri("fabric:/app/service"), updateDescription);
```

PowerShell:

```posh
Update-ServiceFabricService -Stateless -ServiceName $serviceName -InstanceCount 50
```
### <a name="using-dynamic-instance-count"></a>A dinamikus példányszám használatával
Kifejezetten a állapotmentes szolgáltatások esetében a Service Fabric módosítása a példányszám automatikus megoldást kínál. Ez lehetővé teszi, hogy a szolgáltatás dinamikus méretezést a rendelkezésre álló csomópontok számát. A választható, ez a viselkedés módja állítsa a példányszámot, 1. InstanceCount = -1 egy utasítást a Service Fabric, amely szerint "Az állapotmentes szolgáltatás futtatása minden csomóponton." Ha módosítja a csomópontok számát, a Service Fabric automatikusan módosítja a példányok száma egyezik meg, hogy annak biztosítása, hogy a szolgáltatás fut-e az összes érvényes csomóponton. 

C#:

```csharp
StatelessServiceDescription serviceDescription = new StatelessServiceDescription();
//Set other service properties necessary for creation....
serviceDescription.InstanceCount = -1;
await fc.ServiceManager.CreateServiceAsync(serviceDescription);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName -Stateless -PartitionSchemeSingleton -InstanceCount "-1"
```

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Szolgáltatások létrehozása, illetve eltávolításával új skálázás nevű
Elnevezett szolgáltatáspéldányokban egy adott példányán, a szolgáltatás típusa (lásd: [Service Fabric-alkalmazás életciklusa](service-fabric-application-lifecycle.md)) a fürt egyes névvel ellátott alkalmazáspéldány belül. 

Új elnevezett szolgáltatáspéldányok létrehozhatják (vagy eltávolítása) válik több vagy kevesebb foglalt szolgáltatásként. Ez lehetővé teszi a kérelmek általában lehetővé teszi a terhelés csökkentése érdekében a meglévő szolgáltatások további szolgáltatáspéldányok között lehetnek elosztva. Szolgáltatások létrehozásakor, a Service Fabric fürterőforrás-kezelő szolgáltatásokat a fürt egy elosztott módon helyezi. A pontos döntések vonatkoznak rájuk a [metrikák](service-fabric-cluster-resource-manager-metrics.md) a fürt és más elhelyezési szabályok. Szolgáltatások számos különböző módon hozható létre, de a leggyakoribb felügyeleti műveleteket, például valaki hívása keresztül [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), vagy a kód hívással [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync` még nem hívható meg a belül a fürtben futó más szolgáltatások.

Szolgáltatások létrehozása dinamikusan összes is használható, és egy gyakori trend. Vegyük példaként egy állapotalapú szolgáltatás, amely egy adott munkafolyamat jelöli. Elvégzendő munkát megjelenítő hívásokat fog jelennek meg a szolgáltatáshoz, és hajtsa végre a lépéseket, hogy a munkafolyamat és -rekordhalmazok fejlődéshez fogja ezt a szolgáltatást. 

Hogyan tenné a skála adott szolgáltatást? A szolgáltatás több-bérlős valamilyen formában kell, és a hívásokat fogadni és indíthat egyszerre ugyanabban a munkafolyamatban számos különböző példányai lépéseit. Azonban, amely teheti a kód óta összetettebb most kell foglalkoznia a ugyanabban a munkafolyamatban, számos különböző példányai és a különböző ügyfelek különböző szakaszaiban. Ezenkívül egyszerre több munkafolyamat kezelése nem oldja meg a méretezési csoport problémába. Ennek oka az, bármikor ezt a szolgáltatást ahhoz, hogy egy adott gép elférjen túl sok erőforrást használnak fel. Ez a minta nem épített számos szolgáltatás az elsőként is nehézséget okoz miatt bizonyos rejlő szűk keresztmetszetet vagy a lassulás-kódját. Az ilyen típusú problémák esetén a szolgáltatás nem működik olyan jól, nyomon követéséhez egyidejű munkafolyamatok száma nagyobb beolvasása során.  

A megoldás, ha a szolgáltatás minden másik példányát szeretné nyomon követni a munkafolyamat-példány létrehozása. Ez nagyszerű mintát, működik-e a szolgáltatási állapot nélküli vagy állapotalapú. Ez a minta használatához általában egy másik szolgáltatás, amely a "Számítási feladat Manager szolgáltatás" nincs. Ez a szolgáltatás feladata kérések fogadása és irányíthatja a más szolgáltatások ezeket a kérelmeket. A kezelő dinamikusan hozhat létre a számítási feladatok szolgáltatás egy példányának, amikor megkapja az üzenetet, és akkor továbbítja a kérelmeket, ezeket a szolgáltatásokat. A kezelő szolgáltatás akkor is kapnak a visszahívások, amikor egy adott munkafolyamat-szolgáltatás befejezi a feladatot. Amikor a kezelő megkapja ezeket visszahívások ez sikerült, hogy a munkafolyamat-szolgáltatás példányának törlése, vagy hagyja üresen, ha a rendszer várt több hívást. 

Az ilyen típusú manager speciális verziói is létrehozhat a szolgáltatások, az általa felügyelt készletek. A készlet segítségével, győződjön meg arról, hogy amikor egy új kérelem érkezik, nem kell Várjon, amíg a szolgáltatás üzembe helyezése. Ehelyett a kezelő csak válasszon ki egy munkafolyamat-szolgáltatás, amely nem jelenleg foglalt a készletből, vagy véletlenszerűen irányítani. Elérhető szolgáltatások egy készlete tartja teszi kezelési új kérelem gyorsabban, mivel ez kevésbé valószínű, hogy rendelkezik-e a kérés várnia kell hoz létre egy új szolgáltatás. Új szolgáltatás létrehozása nem a gyors, de nem ingyenes vagy azonnali. A készlet segít a kérés rendelkezik kiszolgált előtti várakozási idő minimalizálása érdekében. Gyakran láthatja a manager és a készlet minta amikor válaszidők számít, hogy a legtöbbet. Üzenetsor-kezelési a kérelmet, és a szolgáltatás létrehozása a háttérben és _majd_ átadásával is népszerű manager mintát, szerint hoz létre, és néhány nyomon követheti a munka mennyisége a szolgáltatás jelenleg alapján szolgáltatások törlése függőben . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Az alkalmazáspéldányok létrehozásában és eltávolításában új skálázása nevű
A minta létrehozása és törlése a szolgáltatások létrehozása és törlése a teljes alkalmazáspéldányok hasonlít. Ebben a mintában nincs néhány manager szolgáltatást, hogy megszűnjön a más szolgáltatások a fürtben fogadja a kéréseket, amelyek jelenjenek meg, és az információk alapján döntés. 

Mikor hozzon létre egy új elnevezett alkalmazás-példányt használ, egy már létező alkalmazásban egy új elnevezett szolgáltatáspéldány létrehozása helyett? Néhány esetben van:

  * Az új alkalmazás-példány van egy ügyfél, amelynek kódot kell futtatni, néhány egyedi azonosítóval vagy biztonsági beállításai alatt.
    * A Service Fabric lehetővé teszi a különböző kódcsomagok futtatásához az adott identitások meghatározása. Indítsa el a kód egyazon csomag különböző identitásokat, az aktiválás kell különböző alkalmazáspéldányok fordulnak elő. Vegyük azt az esetet egy meglévő ügyfél üzembe helyezett számítási feladatok esetében. Ezek is futhat egy adott identitás, figyelheti és azok más erőforrások, például távoli adatbázis vagy a más rendszerekkel való hozzáférésének szabályozása. Ebben az esetben amikor új vevő regisztrál, valószínűleg nem kívánja kódját ugyanabban a környezetben (folyamat terület) aktiválásához. Próbálja meg, amíg ez megnehezíti a szolgáltatás kódjához való működésre egy adott identitás kontextusában. Általában rendelkeznie kell további biztonsági, elkülönítési és identity management kódot. Különböző helyett nevű alkalmazás ugyanazon belül szolgáltatáspéldányok és ugyanezt a folyamatot, így helyet, nevesített Service Fabric-alkalmazás példány is használható. Ez megkönnyíti az identitások környezetek meghatározásához.
  * A konfiguráció azt jelenti, hogy is szolgál az új alkalmazás-példány
    * Alapértelmezés szerint az összes alkalmazáspéldány belül egy adott szolgáltatás típusa az elnevezett szolgáltatás példányainak fog futni ugyanazt a folyamatot egy adott csomóponton. Ez azt jelenti, hogy minden szolgáltatáspéldány eltérően is konfigurálhat, amíg ez így bonyolult. Szolgáltatások néhány jogkivonat használata keresse ki a konfigurációs belül egy csomagot kell rendelkeznie. Ez általában a csak a szolgáltatás nevét. Ez jól működik, de azt párok belüli adott alkalmazáspéldány egyedi névvel ellátott szolgáltatás-példánya nevét a konfigurációt. Ez lehet zavaró és nehéz kezelni, mivel a konfigurációs általában egy tervezési idő összetevő alkalmazás példány egyedi értékekkel. Módosíthatja az adatokat a konfigurációs csomagokat belül, vagy újakat telepítése úgy, hogy az új szolgáltatások a saját konkrét információkat kereshet további alkalmazásfrissítések mindig a további szolgáltatások létrehozását jelenti. Sokszor célszerű létrehozni egy teljesen új elnevezett alkalmazáspéldány. Ezután használhatja az alkalmazás paramétereit bármilyen konfigurációs szükség, a szolgáltatások beállítása. Ezzel a módszerrel a szolgáltatásokból, amelyek hoz létre a rendszer, amely az összes alkalmazáspéldány nevű örökölheti a konfigurációs beállításokat. Például a beállításokat, és minden ügyfél, például a titkos kulcsok vagy ügyfelek erőforrás-korlátozásaival, testreszabásainak egyetlen konfigurációs fájlon nem szeretne helyette egy másik alkalmazás példánnyal rendelkezik minden egyes ügyfél ezekkel a beállításokkal felülbírálható. 
  * Egy frissítési határként szolgál az új alkalmazás
    * A Service Fabric, található különböző elnevezett alkalmazáspéldányok frissítés határként szolgál. Egy frissítés egy elnevezett alkalmazás-példány nem érinti a kódot, amely egy másik nevesített alkalmazáspéldány fut-e. A különböző alkalmazások különböző verzióit ugyanazt a kódot az ugyanazon csomópontokon futó lesz végül. Ez lehet egy tényező, ha végre kell hajtania egy méretezési döntés, mert kiválaszthatja-e vagy sem az új kódot kell követniük a frissítéseket, amelyek azonos egy másik szolgáltatás. Tegyük fel például, hogy egy hívás érkezik-e a kezelő szolgáltatás, amely egy adott ügyfél munkaterheléseinek méretezése a létrehozásával és a szolgáltatások törlése dinamikusan felelős. Ebben az esetben azonban a hívás nem társított számítási egy _új_ ügyfél. A legtöbb ügyfél, nem csak a biztonsági és okokból konfigurációs egymástól különítve, előzőleg felsorolt, de mivel a szoftver adott verzióját futtató és a kiválasztása, amikor frissíti, nagyobb rugalmasságot biztosít. Előfordulhat, hogy hozzon létre egy új alkalmazás-példányt, és csupán be további partíciót a szolgáltatás létrehozása a szolgáltatásokat érintő bármilyen egy frissítést fog mennyisége. Külön alkalmazáspéldányok nagyobb részletességgel adja meg, amikor alkalmazásfrissítések és is lehetővé teszi A / B tesztelés és a kék és zöld központi telepítések. 
  * A meglévő alkalmazáspéldány megtelt.
    * A Service Fabricben [alkalmazás kapacitásának](service-fabric-cluster-resource-manager-application-groups.md) fogalom, amely segítségével szabályozhatja az adott alkalmazáspéldányok rendelkezésre álló erőforrások mennyiségét. Például dönthet úgy, hogy egy adott szolgáltatáshoz kell méretezni létrehozott egy másik példánya. Az alkalmazás példánya azonban csak egy bizonyos metrika a kapacitása. Ha az adott ügyfél vagy a számítási feladatok továbbra is lehet engedélyezni több erőforrást, majd sikerült növelheti a meglévő alkalmazás vagy hozzon létre egy új alkalmazást. 

## <a name="scaling-at-the-partition-level"></a>A partíció szintjén méretezése
Service Fabric támogatja a particionálást. Szolgáltatás particionálása felosztja a egymástól függetlenül működik, amelyek mindegyike több logikai és fizikai szakaszra. Ez akkor hasznos, az állapotalapú szolgáltatások, mert nincs beállítva replikák, összes hívást, és minden, az állam egyszerre kezelheti. A [particionálás áttekintése](service-fabric-concepts-partitioning.md) információt nyújt a particionálási sémákat támogatott típusú. A replikákat az egyes partíciók vannak elosztva a csomópontok a fürtben, a szolgáltatás terhelés elosztása és annak biztosítása, hogy sem a szolgáltatás egészére vagy minden olyan partíció rendelkezik-e olyan hibaérzékeny pont. 

Fontolja meg az alacsony kulcsa, 0, egy 99-es felső kulcs és a egy partíciók száma a 4-ből egy ranged particionálási sémát használó szolgáltatás. Egy három csomópontos fürtben a szolgáltatás előfordulhat, hogy lesznek elrendezve a négy replikákat, amely ugyanazokat az erőforrásokat, minden egyes csomóponton, ahogy az itt látható:

<center>

![Három csomóponttal partícióelrendezés](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Ha növeli a csomópontok számát, a Service Fabric kerül át a meglévő replikák némelyike van. Például hozzunk tegyük csomópontok növekszik négy és a replikák számának lekérése újra szétosztódnak. Most már a szolgáltatás most már rendelkezik minden csomóponton, minden a különböző partíciók tartozó futó, három replika készül. Ez lehetővé teszi az erőforrás-kihasználást, mivel az új csomópont nem offline. Általában is javítja a teljesítményt, mert minden szolgáltatásnak van további számára elérhető erőforrások.

<center>

![A négy csomópont partícióelrendezés](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>A Service Fabric-fürt Resource Manager és a metrikák méretezése
[Metrikák](service-fabric-cluster-resource-manager-metrics.md) módját a szolgáltatások az erőforrás-használat, a Service Fabric express vannak. A fürterőforrás-kezelő mérőszámok segítségével biztosít átrendezése és optimalizálni az elrendezést, a fürt lehetőséget. Például előfordulhat, a fürtben lévő erőforrásokat rengeteg, de, előfordulhat, hogy nem kell felosztani a szolgáltatásokat, amelyek jelenleg végeznek munkát. Mérőszámok segítségével lehetővé teszi, hogy a fürterőforrás-kezelő átszervezése a fürtöt, győződjön meg arról, hogy a szolgáltatások rendelkezik elérhető erőforrásokhoz való hozzáférés. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Skálázás hozzáadásával és eltávolításával a csomópontot a fürtből 
A Service Fabric méretezése egy másik lehetőség, hogy a fürt méretének módosítása. A fürt méretének módosítása azt jelenti, hozzáadása vagy eltávolítása, csomópontok egy vagy több csomóponttípus a fürtben. Vegyük példaként egy olyan esetet, ahol a csomópontok a fürtben lévő összes gyakran használt adatok. Ez azt jelenti, hogy a fürt erőforrásainak szinte az összes felhasznált. Ebben az esetben további csomópontokat ad hozzá a fürthöz a legjobb módszer méretezése. Után az új csomópontok csatlakoztatását a fürthöz a Service Fabric fürterőforrás-kezelő áthelyezi szolgáltatások őket, a meglévő csomópontok kevesebb teljes terhelése eredményez. Példányok száma az állapotmentes szolgáltatások esetében = -1, több szolgáltatás példányok automatikusan jönnek létre. Ez lehetővé teszi a használatával helyezheti át a meglévő csomópontok az új csomópontok az egyes hívások. 

További információkért lásd: [fürtméretezés](service-fabric-cluster-scaling.md).

## <a name="putting-it-all-together"></a>Végső összeállítás
Nézzük meg, hogy itt már tárgyalt és a egy példán keresztül kommunikáljon az összes ötleteit. Vegye figyelembe a következő szolgáltatás: szolgáltatás, amely a nevek üzem címjegyzék készítése és a kapcsolattartási adatokat próbált. 

Jobb meghozni rendelkezésére álló, lemezcsoport típusú méretezési kapcsolatos kérdések: Hány felhasználó lesz a rendelkezik? Hány névjegyek fogja tárolni a minden felhasználó? Próbálja ki az összes. ábra ezt Ha állandó, az első alkalommal a szolgáltatás is nehézkes. Tegyük fel, is intézni fog egy adott partíciók száma az egyetlen statikus szolgáltatással. Kiválasztotta a megfelelő partíciók száma következményeinek eredményezheti, hogy a méretezési csoport problémába később. Hasonlóképpen még akkor is, ha a esetleg nincs megfelelő száma összes adatot kivételezési van szüksége. Ha például is dönthet arról, hogy előre, a fürt méretét a csomópontok számát és azok méretéről osztályé, mind a. Fontos általában nehezen becsülhető, hány erőforrások szolgáltatásként történik az élettartamuk felhasználásához. Azt is nehézkes lehet a forgalom minta, amely a szolgáltatás ténylegesen látja előre tudni. Például esetleg személyek hozzáadásához és eltávolításához a thing csak először a reggel, vagy esetleg azt oszlik el egyenlően a nap folyamán. Ez akkor lehet, hogy dinamikusan szeretné méretezni horizontális fel- és alapján. Talán tudhat előrejelzi, mikor kell a horizontális skálázás fog, de mindkét módszer valószínűleg fog reagálni azokra, erőforrás-használat módosítása a szolgáltatás által kell. Ehhez szükség lehet ahhoz, hogy további erőforrásokat szolgáltatni a meglévő erőforrások átszervezése nem elég a fürt méretének módosítása. 

De miért is próbál egyetlen partícióséma válasszon minden felhasználó számára? Korlátozza egy szolgáltatás és a egy statikus fürt miért? A valós helyzet akkor általában több dinamikus. 

Méretezési csoport létrehozását, vegye figyelembe a következő dinamikus mintának. Az adott helyzethez alkalmazkodni lehet szükség:

1. Helyett válassza ki a particionálási séma kidolgozásához meghozni mindenki számára, szolgáltatásokat a "manager".
2. A kezelő szolgáltatás a feladat az ügyfél-információkat tekintse meg, amikor regisztrál a szolgáltatásra. Attól függően, ezt az információt a kezelő szolgáltatás hozzon létre egy példányt, majd a _tényleges_ forduljon társzolgáltatás _csak az adott ügyfélhez tartozó_. Ha az adott konfigurációs, elkülönítés, illetve frissítése igényelnek, azt is eldöntheti alkalmazáspéldány üzembe helyezését az ügyfélhez. 

A dinamikus létrehozása minta számos előnnyel jár:

  - Nem szeretne meghozni az összes felhasználó számára a megfelelő partíciók száma kitalálni vagy egyetlen szolgáltatásokat a végtelenségig méretezhető, mindezt a saját. 
  - Más felhasználóknak nem kell, hogy az azonos partíciók száma, száma, elhelyezési korlátozások, metrikák, alapértelmezett terhelések, szolgáltatásnevek, dns-beállítások vagy a szolgáltatás vagy alkalmazás szinten megadott tulajdonságok. 
  - További adatok Szegmentálás révén. Minden ügyfél saját példányát a szolgáltatás rendelkezik
    - Minden ügyfélszolgálati eltérően konfigurálhatók és több vagy kevesebb erőforrást, partíciók vagy a replikákat a várható mérete alapján szükség szerint több vagy kevesebb kapnak.
      - Például tegyük fel, hogy az ügyfél a "Gold" szint – fizetős sikerült kapnak további replikák vagy nagyobb partíciók száma és potenciálisan erőforrások dedikált mérőszámok és az alkalmazás kapacitásának keresztül szolgáltatásokba.
      - Vagy tegyük fel, hogy megadja, hogy azok szükséges ügyfelek száma volt "Kicsi" kapott – csak néhány partíciók kap, vagy más ügyfelekkel megosztott szolgáltatás készletbe még elhelyezni.
  - Nem futtatunk egy szolgáltatáspéldány vagy replikák többféle amíg várakozik az ügyfelek számára jelennek meg
  - Ha egy ügyfél kikerül, majd eltávolítja az adataikat a szolgáltatás rendkívül egyszerű, hogy a kezelő törlése az adott szolgáltatás vagy alkalmazás, amely létrehozza azt.

## <a name="next-steps"></a>További lépések
A Service Fabric fogalmakról további információkért tekintse meg a következő cikkeket:

* [Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [Service Fabric-szolgáltatások particionálása](service-fabric-concepts-partitioning.md)
