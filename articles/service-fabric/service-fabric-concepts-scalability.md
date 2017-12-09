---
title: "Méretezhetőséget biztosít a Service Fabric szolgáltatások |} Microsoft Docs"
description: "Ismerteti a Service Fabric szolgáltatások méretezése"
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: 
ms.assetid: ed324f23-242f-47b7-af1a-e55c839e7d5d
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 6dc89bda31af35e4c7eb0f2255db301b39ac05eb
ms.sourcegitcommit: b07d06ea51a20e32fdc61980667e801cb5db7333
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2017
---
# <a name="scaling-in-service-fabric"></a>A Service Fabric skálázás
Az Azure Service Fabric megkönnyíti a méretezhető alkalmazások hozhat létre a szolgáltatások, partíciók és a fürt csomópontjait a replikák kezelése. Számos különféle munkaterheléshez fut ugyanazon a hardveren lehetővé teszi, hogy a maximális erőforrás-használat, de rugalmas hogyan úgy dönt, hogy a munkaterhelés vertikális is biztosít. 

A Service Fabric skálázás több módon valósítható meg:

1. Létrehozásában és eltávolításában állapotmentes szolgáltatáspéldány skálázása
2. Létrehozásában és eltávolításában új méretezhetővé nevű szolgáltatások
3. Az alkalmazáspéldányok létrehozásában és eltávolításában új méretezhetővé nevű
4. A particionált szolgáltatások méretezése
5. Méretezhetővé hozzáadása és eltávolítása a csomópontot a fürtből 
6. A fürt erőforrás-kezelő metrikák skálázás

## <a name="scaling-by-creating-or-removing-stateless-service-instances"></a>Létrehozásában és eltávolításában állapotmentes szolgáltatáspéldány skálázása
Állapotmentes szolgáltatások egyik legegyszerűbb módja Service Fabric belül méretezési működik. Egy állapotmentes szolgáltatások létrehozásakor kap arra, hogy adja meg egy `InstanceCount`. `InstanceCount`határozza meg, hogy a szolgáltatás kód hány futó példánya jön létre, amikor a szolgáltatás indítása. Tegyük fel, például, hogy nincsenek-e 100 csomópontokat a fürthöz. Tételezzük is fel, hogy a szolgáltatás hozza létre az `InstanceCount` 10. Futásidőben a kód 10 futó példányokat összes válhat túl elfoglalt (vagy nem sikerült elég foglalt). Egy adott munkaterhelés vertikális módja a példányok számát. Például néhány kódrészletek, figyelés vagy felügyeleti módosíthatja a meglévő példányok száma, 50, illetve 5, attól függően, hogy a munkaterhelés kell a vagy horizontális skálázása a terhelés alapján. 

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
### <a name="using-dynamic-instance-count"></a>Dinamikus példányszám használatával
Kifejezetten állapotmentes szolgáltatásokhoz, a Service Fabric segítségével automatikus módosítása a példányszámot. Ez lehetővé teszi, hogy a dinamikusan méretezése a rendelkezésre álló csomópontok száma a szolgáltatás. Ez a viselkedés programba úgy is, hogy a példányok száma -1 =. InstanceCount = -1 egy utasítást a Service Fabric, amely szerint "Az állapotmentes szolgáltatások Futtatás minden csomóponton." Ha megváltozik a csomópontok számát, a Service Fabric automatikusan módosítja a példányok száma kereséséhez, győződjön meg arról, hogy fut-e a szolgáltatás az összes érvényes csomóponton. 

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

## <a name="scaling-by-creating-or-removing-new-named-services"></a>Létrehozásában és eltávolításában új méretezhetővé nevű szolgáltatások
A megnevezett példánya egy adott típusú példány szolgáltatás (lásd: [Service Fabric-alkalmazás életciklusa](service-fabric-application-lifecycle.md)) a fürt egyes elnevezett alkalmazáspéldány belül. 

Új elnevezett szolgáltatáspéldány létrehozott (vagy eltávolítása) válik több vagy kevesebb foglalt szolgáltatásként. Ez lehetővé teszi a kérelmek további szolgáltatáspéldány általában így csökkenti a meglévő szolgáltatások a terhelés lesz elosztva. Szolgáltatások létrehozásakor a Service Fabric fürt erőforrás-kezelő a szolgáltatásokat helyezi a fürt egy elosztott módon. A pontos lépéseket kell teljesítenie szabályozza a [metrikák](service-fabric-cluster-resource-manager-metrics.md) a fürt és más elhelyezési szabály. Szolgáltatások számos különböző módon hozható létre, de a legtöbb felügyeleti műveletek, például valaki hívása keresztül [ `New-ServiceFabricService` ](https://docs.microsoft.com/powershell/module/servicefabric/new-servicefabricservice?view=azureservicefabricps), vagy a kód hívása [ `CreateServiceAsync` ](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.servicemanagementclient.createserviceasync?view=azure-dotnet). `CreateServiceAsync`még nem hívható a más szolgáltatásokkal, a fürtön belül.

Szolgáltatások létrehozása dinamikusan kívüli bármilyen típusú forgatókönyvben is használható, és a megszokott mintát követi. Vegye figyelembe például egy állapotalapú szolgáltatás, amely egy adott munkafolyamat jelöli. Jelölő munka hívások fogja jelenik meg a szolgáltatáshoz, és ez a szolgáltatás állapotra vált, hogy a munkafolyamat-rekord folyamatban van a lépések végrehajtása. 

Hogyan lenne az adott szolgáltatáshoz méretezési? A szolgáltatás kell valamilyen, több-bérlős és a hívásokat fogadni és ugyanabban a munkafolyamatban számos különböző példányai lépései egyszerre indítsa. Azonban, amely teheti a kód összetettebb, mert már az összes különböző szakaszaiban, és különböző ügyfelektől származó ugyanabban a munkafolyamatban, számos különböző példányai foglalkoznia. Emellett egyszerre több munkafolyamatok kezelése nem oldja meg a skála problémát. Ennek oka az, bármikor ezt a szolgáltatást egy adott számítógép illeszkedő túl sok erőforrást fog használni. Ez a minta nem épített számos szolgáltatás az elsőként is nehézséget okoz bizonyos rejlő szűk vagy lassulást miatt kódjukat. Ilyen jellegű problémák esetén a szolgáltatás nem is működik, amikor az nyomon követéséhez egyidejű munkafolyamatok száma nagyobb lekérdezi.  

A megoldás, ha a szolgáltatás minden különböző szeretné nyomon követni a munkafolyamat-példányhoz egy példánya. Ez egy nagy mintát, és működik-e a szolgáltatás állapot nélküli és állapotalapú. Ez a minta használatához nincs általában egy másik szolgáltatás, amely a "Munkaterhelés-kezelő szolgáltatás". Ez a szolgáltatás folyamat fogadhatók kérések és más szolgáltatások irányíthatja az ezeket a kérelmeket. A kezelő dinamikusan létrehozni a munkaterhelés szolgáltatás egy példánya, az üzenet fogadásakor, és akkor továbbítja a kérelmeket az ezekbe a szolgáltatásokba. A kezelő szolgáltatás is kapnak visszahívások, amikor egy adott munkafolyamat szolgáltatás befejezi a feladatot. Amikor a kezelő megkapja ezeket a visszahívásokat azt nem, hogy a munkafolyamat-szolgáltatás példányának törlése, vagy hagyhatja, ha a várt több hívást. 

Az ilyen típusú manager speciális verzióit is létrehozhat az általa felügyelt szolgáltatások készleteinek. A készlet segítségével, győződjön meg arról, hogy ha az új kérelem érkezik nem kell várja meg a szolgáltatás lépett fel. Ehelyett a kezelő csak válasszon egy munkafolyamat-szolgáltatás, amely nincs jelenleg foglalt a készletből, vagy véletlenszerűen irányításához. Megőrzi az elérhető szolgáltatások készlete teszi kezelési új kérelem gyorsabb, mivel a kevésbé valószínű, hogy rendelkezik-e a kérelem várnia kell hoz létre egy új szolgáltatás. Új szolgáltatások létrehozásának kész, de nem szabad és azonnali. A készlet csökkentheti a kérelem rendelkezik kiszolgálását előtti várakozási időt. Gyakran megjelenik a kezelő és a készlet mintát amikor válaszidők lényeges, hogy a legtöbb. A kérelem üzenetsorba helyezését és a szolgáltatás létrehozása a háttérben és _majd_ átadja azt is egy népszerű manager mintát hoz létre, és néhány nyomon követése munka mennyiségét, hogy a szolgáltatás jelenleg alapján-szolgáltatások törlése függőben . 

## <a name="scaling-by-creating-or-removing-new-named-application-instances"></a>Az alkalmazáspéldányok létrehozásában és eltávolításában új méretezhetővé nevű
Létrehozása és törlése teljes alkalmazáspéldányok hasonlít a minta létrehozása és-szolgáltatások törlése. Ebben a mintában van néhány kezelő szolgáltatás, amely lehetővé teszi a döntést a kérelmek lát, és az információt a fürtben található más szolgáltatásokkal fogad. 

Ha egy új példányának létrehozásakor nevű alkalmazás használja az egy már meglévő alkalmazásban egy új elnevezett szolgáltatáspéldány létrehozása helyett? Néhány esetben van:

  * Az új alkalmazás-példány néhány egyedi azonosítóval, vagy a biztonsági beállítások futtatásához szükséges azonosítójú ügyfél.
    * A Service Fabric megadhatja, hogy más kódot csomagok adott identitások futtatásához. Indítsa el a kód egyazon csomag különböző identitásokat, az aktiválási folyamatokat kell különböző alkalmazáspéldányok fordul elő. Fontolja meg egy meglévő ügyfél üzembe helyezett munkaterhelések esetében eset. Ezek is futhat egy adott identitást, figyelheti és azok más erőforrások, például a távoli adatbázisokhoz vagy más rendszerekkel való hozzáférés szabályozása. Ebben az esetben ha egy új ügyfél jelentkezik be, valószínűleg nem szeretne aktiválni kódjukat ugyanabban a környezetben (folyamat terület). Sikertelen, amíg ez megnehezíti a szolgáltatáskód hibáit, amelyek egy adott azonosító kontextusában fognak működni. Általában rendelkeznie kell további biztonsági, izolációs és identity management kódot. Különböző helyett nevű szolgáltatáspéldány belül az azonos alkalmazáspéldány és ugyanezt a folyamatot, ezért terület, elnevezett Service Fabric-alkalmazás példány is használható. Így könnyebben identitások környezetek meghatározásához.
  * Konfigurációs módszert is szolgál az új alkalmazás-példány
    * Alapértelmezés szerint minden egy alkalmazáspéldányt belül egy adott szolgáltatáshoz típusú nevű szolgáltatás előfordulása fog futni az egy folyamatban adott csomóponton. Ez azt jelenti, hogy közben minden szolgáltatáspéldány másképp is konfigurálhat, ezzel ezért nem bonyolult. Szolgáltatások néhány megkeresheti a config belül a konfigurációs csomag használata token kell rendelkeznie. Általában ez a csak a szolgáltatás nevét. A szolgáltatás megfelelően működik, de az adott alkalmazáspéldány belül egyedi névvel ellátott szolgáltatáspéldány nevéhez konfigurációjának párok. Ez lehet egyértelmű és kezelése, mert a konfiguráció általában egy tervezési idő összetevő alkalmazás példányt adott értékek. További szolgáltatások mindig létrehozásának azt jelenti, hogy a módosításra belül a konfigurációs csomagokat vagy újakat telepítéséhez, úgy, hogy az új szolgáltatások is kereshet az információk további alkalmazásfrissítéseket. Gyakran sokkal jobban hozzon létre egy új elnevezett alkalmazáspéldányt. Az alkalmazás paramétereit használja majd bármilyen konfiguráció esetén szükség a szolgáltatások beállítása. Ily módon az összes szolgáltatást, amely belül létrehozott nevű alkalmazáspéldány is örökölje a konfigurációs beállításokat. Például ahelyett, hogy a beállításokat és a testreszabások minden ügyfél, például a titkokat vagy felhasználói erőforrás korlátokat, egyetlen konfigurációs fájlban inkább akkor egy másik alkalmazáspéldány ezekkel a beállításokkal minden ügyfél esetében felülbírálható. 
  * Az új alkalmazás verziófrissítési határként szolgál
    * A Service Fabric belül különböző elnevezett alkalmazáspéldányok frissítés határok szolgál. Egy nevesített alkalmazáspéldány frissítése nem befolyásolja a kódot, hogy fut-e egy másik nevesített alkalmazáspéldányt. A különböző alkalmazások kat, ugyanazt a kódot különböző verzióit futtató ugyanazon a csomóponton. Ez akkor lehet egy tényező, ha meg kell győződnie méretezési döntés, mert dönthet úgy, hogy az új kódot az azonos frissítése egy másik szolgáltatásként kell követnie, vagy nem. Tegyük fel, hogy egy hívás érkezik-e a kezelő szolgáltatás, amely felelős az adott ügyfél munkaterheléseinek skálázás hoz létre, és dinamikusan-szolgáltatások törlése. Ebben az esetben azonban a hívás nem a társított munkaterhelés egy _új_ ügyfél. A legtöbb ügyfél például a korábban felsorolt különítve egymástól nem csak a biztonsági okokból és konfigurációs, de a szoftver adott verzióját futtató, és mikor lesz frissítve kiválasztása nagyobb rugalmasságot biztosít. Előfordulhat, hogy hozzon létre egy új alkalmazás-példányt, és hozza létre csupán be további partíciót a szolgáltatás az a szolgáltatásokat érintő bármilyen egy frissítést fog. Külön alkalmazáspéldányok adja meg a nagyobb részletességgel alkalmazás frissítése során, és is engedélyezheti A / B tesztelés és a kék/zöld központi telepítéseket. 
  * A meglévő alkalmazáspéldány megtelt.
    * A Service Fabric [alkalmazás kapacitás](service-fabric-cluster-resource-manager-application-groups.md) , amelyekkel szabályozható, hogy adott alkalmazáspéldányok rendelkezésre álló erőforrások fogalom. Dönthet például úgy, hogy egy adott szolgáltatáshoz kell rendelkeznie ahhoz, hogy méretezhető létrehozott egy másik példánya. Azonban ez alkalmazáspéldány bizonyos metrika kapacitása kívül esik. Ha az adott ügyfél vagy a munkaterhelés továbbra is adható több erőforrást, majd sikerült növelheti a meglévő alkalmazás vagy hozzon létre egy új alkalmazást. 

## <a name="scaling-at-the-partition-level"></a>A partíció szintű skálázás
A Service Fabric támogatja a particionálást. Particionálás felosztja a szolgáltatás több logikai és fizikai szakaszokra, amelyek egymástól függetlenül működnek. Ez akkor hasznos, állapotalapú szolgáltatással, mert nincs beállítva replikák rendelkezik az összes hívást, valamint egyszerre kezelése összes állapotát. A [áttekintése particionálás](service-fabric-concepts-partitioning.md) információt nyújt a particionálási rendszerek által támogatott típusú. Mindegyik partíció replikáinak vannak elosztva a csomópontok egy fürtben, hogy a szolgáltatás betöltése terjesztésére, és győződjön meg arról, hogy sem a szolgáltatás egészére vagy partícióval rendelkezik-e a hibaérzékeny pontok kialakulását. 

Fontolja meg a 0 alsó kulcsa, 99 magas kulcs és a partíciók száma a 4 a ranged particionálási sémát használó szolgáltatásokat. Egy három csomópontos fürtben a szolgáltatás esetleg leírva négy replikával, amely minden egyes csomóponton az erőforrások megosztása, ahogy az itt látható:

<center>
![A három csomópont partícióelrendezés](./media/service-fabric-concepts-scalability/layout-three-nodes.png)
</center>

Növeli a csomópontok számát, a Service Fabric áthelyezi a meglévő replikákat némelyike van. Például most mondja ki a csomópontok növekszik a négy és a replikák száma beolvasása terjeszti. A szolgáltatás most már minden csomóponton, mindegyik különböző partíciók tartozó futó három replikákat. Ez lehetővé teszi az erőforrás-kihasználást, mivel az új csomópont nem cold. Általában azt is javítja a teljesítményt, ha minden egyes szolgáltatás további számára elérhető erőforrások.

<center>
![A négy csomópont partícióelrendezés](./media/service-fabric-concepts-scalability/layout-four-nodes.png)
</center>

## <a name="scaling-by-using-the-service-fabric-cluster-resource-manager-and-metrics"></a>A Service Fabric fürt erőforrás-kezelő és a metrikák skálázás
[Metrikák](service-fabric-cluster-resource-manager-metrics.md) hogyan szolgáltatások express az erőforrás-felhasználás, hogy a Service Fabric vannak. Mérőszámainkat lehetővé a fürt erőforrás-kezelő átrendezéséhez, és optimalizálja a fürt elrendezését. Például lehet bőven a fürterőforrások, de azok előfordulhat, hogy nem osztható ki a szolgáltatásokat, amelyeket a rendszer jelenleg végez műveletet. Mérőszámainkat, lehetővé teszi, hogy a fürt erőforrás-kezelő átszervezése a fürtöt, győződjön meg arról, hogy van-e a szolgáltatások a rendelkezésre álló erőforrások eléréséhez. 


## <a name="scaling-by-adding-and-removing-nodes-from-the-cluster"></a>Méretezhetővé hozzáadása és eltávolítása a csomópontot a fürtből 
A Service Fabric méretezéshez másik lehetőség is a fürt méretének módosításához. A fürt méretének módosítása azt jelenti, hogy csomópontokat hozzáadni vagy eltávolítani egy vagy több csomóponttípus a fürtben. Vegyük példaként egy olyan esetben, ahol a fürt csomópontjaihoz minden gyakran használt adatok. Ez azt jelenti, hogy a fürt erőforrásait szinte minden használni. Ebben az esetben a fürt további fürtcsomópontok hozzáadásához legegyszerűbben méretezése. Után az új csomópontok csatlakoztatását a fürthöz a Service Fabric fürt erőforrás-kezelő áthelyezése szolgáltatások őket, ami azt eredményezi, hogy a meglévő csomópontok kevesebb teljes terhelése. Az állapotmentes szolgáltatások példányok száma = -1, további példányok automatikusan jönnek létre a szolgáltatás. Ez lehetővé teszi, hogy néhány hívás az új csomópontot a meglévő csomópontok áthelyezése. 

Hozzáadása és eltávolítása, csomópontok a fürthöz a pedig a Service Fabric Azure Resource Manager PowerShell-modul segítségével konfigurálhatók.

```posh
Add-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName  -NumberOfNodesToAdd 5 
Remove-AzureRmServiceFabricNode -ResourceGroupName $resourceGroupName -Name $clusterResourceName -NodeType $nodeTypeName -NumberOfNodesToRemove 5
```

## <a name="putting-it-all-together"></a>A teljes kép
Megtudhatja, hogy minden ötleteire, azt itt már tárgyalt, és egy példán keresztül kommunikál. Vegye figyelembe a következő szolgáltatást: próbál egy szolgáltatás, amely különbséglemezként funkcionál nevekre üzem címjegyzék build és elérhetőségét. 

Előre jobbra a álló, lemezcsoport típusú méretezése kapcsolatos kérdéseit rendelkezik: hány felhasználó lesz a rendelkezik? Hány névjegyek fogja tárolni a minden felhasználó? . Az összes ki ábra próbál mikor van, először a szolgáltatás állandó is nehézkes. Tegyük fel, volt lesz a nyissa meg egy statikus szolgáltatásnak egy adott partícióra száma. A megfelelő partíciószám kiadási következményeinek eredményezhet méretezési problémákba később. Hasonlóképpen még akkor is, ha a jobb oldali szám lehet, hogy nincs minden információt válasszon van szüksége. Például is kiválasztja, hogy előre, a fürt méretét a csomópontok száma és a mérete. Általában nehezen becsülhető, a szolgáltatás felhasználásához az élettartama során lesz erőforrásoknak a számát is. Azt is nehéz lehet tudni, hogy időben a forgalom mintát, amely a szolgáltatás ténylegesen látja. Például lehet, hogy a felhasználók hozzáadásához és eltávolításához a thing csak először reggel, vagy lehet, hogy azt van elosztva a nap folyamán. Szükség lehet, és dinamikusan méretezési ennek alapján. Lehet, hogy szerezhet és méretezést fog, de akár valószínűleg fog hálózatierőforrás-fogyasztás módosítása a szolgáltatás által reagálni kell előre jelezni. Ez magába foglaló ahhoz, hogy ha a meglévő erőforrások átrendezése nem elegendőek, adja meg a további erőforrások a fürt méretének módosítása. 

De miért még próbál egyetlen partícióséma válasszon az összes felhasználó számára? Korlátozza egy szolgáltatás, és egy statikus fürt miért? A valós helyzet általában több dinamikus. 

A skála kiépítéséhez, vegye figyelembe a következő dinamikus mintát. Az Ön helyzetéhez történő lehet szükség:

1. Nem próbálja particionálási sémát válassza ki a Mindenki előre, hozhat létre. a "manager"szolgáltatás.
2. A feladat a kezelő szolgáltatás segítségével megvizsgálhatók ügyféladatok, amikor azok Regisztrálás a service. Attól függően, hogy ezt az információt a kezelő szolgáltatás példányt létrehozni, majd a _tényleges_ ügyfél-tároló szolgáltatás _csak az adott ügyfélhez tartozó_. Ha speciális konfigurációja, elkülönítés vagy frissítések van szükségük, eldöntheti azt is lépett fel egy alkalmazáspéldányt az ügyfél. 

A dinamikus létrehozása számos előnyt mintát:

  - Nem a megfelelő partíciók száma az összes felhasználó számára szükséges előre kitalálni vagy egyetlen szolgáltatás, amely minden a saját végtelenül skálázható próbál. 
  - Különböző felhasználóknak nem kell ugyanazon a partíciók száma, replikaszám, egy elhelyezési korlátozás, metrikákat, alapértelmezett terhelések, szolgáltatásnevek, DNS-beállítások vagy a szolgáltatás vagy alkalmazás szinten megadott tulajdonságok. 
  - Ettől kezdve további adatok Szegmentálás. Minden egyes ügyfélnek van saját példányát a szolgáltatás
    - Minden egyes ügyfélszolgálat másképp konfigurálhatók és több vagy kevesebb erőforrást, partíciók vagy a replikákat a várható mérete alapján szükség szerint több vagy kevesebb kap.
      - Például tegyük fel például az ügyfél, a "Gold" réteg - kifizette azok sikerült további replikák vagy nagyobb partíciók száma és potenciálisan erőforrások dedikált szolgáltatásuk metrikák és az alkalmazás kapacitásának keresztül.
      - Vagy tegyük fel például, hogy megadott Megadja, hogy azok szükséges ügyfelek számát volt "Kicsi",-csak néhány partíciók visszajelzést kap, vagy más ügyfelekkel megosztott szolgáltatás készletbe még ütemezésbe helyezheti.
  - Nem futtatja egy csoportját egy szolgáltatáspéldány vagy replikákat az ügyfelek számára jelenik meg várakozás közben
  - Az ügyfél kikerül, majd eltávolítja a szolgáltatásból adataikat esetén más dolga, mint hogy a kezelő törlése az adott szolgáltatás vagy alkalmazás, amely az jön létre.

## <a name="next-steps"></a>Következő lépések
A Service Fabric fogalmakat további információkért tekintse meg a következő cikkeket:

* [A Service Fabric-szolgáltatások rendelkezésre állása](service-fabric-availability-services.md)
* [A Service Fabric szolgáltatások particionálás](service-fabric-concepts-partitioning.md)
