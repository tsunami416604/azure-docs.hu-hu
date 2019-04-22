---
title: Egyéni Service Fabric-állapotjelentések hozzáadása |} A Microsoft Docs
description: Ismerteti, hogyan lehet egyéni rendszerállapot-jelentések küldése Azure Service Fabric health entitásokhoz. Javaslatok, amelyek segítenek a rendszerállapot-jelentések minőségi megtalálható.
services: service-fabric
documentationcenter: .net
author: oanapl
manager: chackdan
editor: ''
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 49ebf4ab95816a3da2f74a464b12b46de6228456
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59280553"
---
# <a name="add-custom-service-fabric-health-reports"></a>Egyéni Service Fabric-állapotjelentések hozzáadása
Az Azure Service Fabric mutatja be egy [állapotmodell](service-fabric-health-introduction.md) célzó jelző nem megfelelő állapotú fürt és az alkalmazás feltételeket az adott entitásokon. Használja az állapotközpontú modellről **egészségügyi jelentéskészítők** (rendszer összetevőit és watchdogs). A cél, egyszerű és gyors diagnosztizálása és javítás. Szolgáltatás írók kell health szolgáltatással kapcsolatos előzetes költségek. Tetszőleges feltételt, amely hatással lehet a health kell jelenteni, különösen akkor, ha a legfelső szintű közeli jelző problémák megkönnyíti. Az egészségügyi információk takaríthat meg időt és energiát a Hibakeresés és kivizsgálása érdekében. Az üzletmenetet különösen törlése után a szolgáltatás nem működik és ipari méretekben a felhőben (magán- vagy Azure).

A Service Fabric-jelentéskészítők nevű figyelő azonosítja a lényeges feltételek. Ezek a helyi nézetén alapuló feltételek jelentést. A [health Store adatbázisban](service-fabric-health-introduction.md#health-store) összesíti az összes jelentéskészítők által küldött határozza meg, hogy globálisan kifogástalan állapotban-e entitásokat egészségügyi adatokat. A minta célja gazdag, rugalmas és könnyen használható. A rendszerállapot-jelentések minőségét meghatározza, hogy a fürt állapotának nézete pontosságát. Vakriasztások tévesen megjelenítése a nem megfelelő állapotú problémák is negatív hatással a frissítések vagy egészségügyi adatokat használó egyéb szolgáltatásokba. Ilyen szolgáltatás például a következők: javítása és a riasztási mechanizmusokat. Jelentések készítése, fontos a lehető legjobb módon feltételek rögzítő ezért néhány gondolat van szükség.

Watchdogs együttműködve tervezhetik és valósíthatják állapotfigyelő jelentési, és a rendszer összetevőit kell:

* Adja meg az őket érdeklő feltétel, a módszer figyelhető és hatását a fürt vagy az alkalmazás funkcióit. Ezen információ alapján döntse el, az egészségügyi jelentés tulajdonság és állapotát.
* Határozza meg a [entitás](service-fabric-health-introduction.md#health-entities-and-hierarchy) , amely a jelentés vonatkozik.
* Határozza meg, ahol a jelentéskészítési van kész, az a szolgáltatásban vagy egy belső vagy külső figyelő.
* Adja meg a jelentéskészítő azonosítására szolgáló forrás.
* Válassza ki a jelentéskészítési stratégia, rendszeres időközönként vagy az átmenetek. Ajánlott módja rendszeres időközönként, hogy egyszerűbb kódot igényel, és kevesebb a hibalehetőség.
* Határozza meg, mennyi ideig a jelentés a nem megfelelő állapotú feltételek kell marad a a health Store adatbázisban, és hogyan jelölését. Ezen információk alapján döntse el, a jelentés idő live és a remove-a-lejárati viselkedését.

Ahogy említettük, reporting teheti meg:

* A figyelt Service Fabric-szolgáltatás replikája.
* Belső watchdogs telepített Service Fabric-szolgáltatás (például egy Service Fabric állapotmentes szolgáltatás, amely figyeli a feltételek és jelentések problémák). A watchdogs lehet az összes csomóponton telepítve, vagy a figyelt szolgáltatás affinitized is lehet.
* A Service Fabric-csomópontokon futtatni, de belső watchdogs *nem* megvalósítva, Service Fabric-szolgáltatások.
* Külső watchdogs, amely az erőforrás a mintavételi *kívül* a Service Fabric-fürt (például például Gomez figyelési szolgáltatás).

> [!NOTE]
> Beépített megjelenik a fürt rendszerállapot-jelentések, a rendszer összetevők által küldött. További információk: [használatával rendszerállapot-jelentések hibaelhárítási](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). A felhasználó jelentéseket kell küldeni [egészségügyi entitások](service-fabric-health-introduction.md#health-entities-and-hierarchy) , amely már a rendszerben hozták létre.
> 
> 

Egyszer az egészségügyi jelentéskészítő Tervező nincs bejelölve, rendszerállapot-jelentések küldhetők egyszerűen. Használhat [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) jelentés állapotába. Ha a fürt nem [biztonságos](service-fabric-cluster-security.md) , vagy ha a fabric-ügyfél rendszergazdai jogosultságokkal rendelkezik. Jelentéskészítés végezhető el az API által használatával [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), a PowerShell vagy REST-en keresztül. Konfigurációs belül kötegelt jobb teljesítmény érdekében a jelentésekre.

> [!NOTE]
> A jelentés állapotának szinkron, és azt jelenti, hogy csak az ügyféloldalon érvényesítési működik. Az a tény, hogy a jelentés az egészségügyi ügyfél elfogadja vagy a `Partition` vagy `CodePackageActivationContext` objektumok nem jelenti azt, hogy alkalmazásának az áruházban. Valószínűleg más jelentésekre kötegelt és aszinkron módon elküldött. A feldolgozás a kiszolgáló továbbra is sikertelenek lehetnek: lehet, hogy a sorozatszám elavult, az entitást, amelyen a jelentés a alkalmazni kell lett törölve, stb.
> 
> 

## <a name="health-client"></a>Állapot ügyfél
A rendszerállapot-jelentések küldése a az ügyfélállapot-kezelő egy rendszerállapot-ügyfelet, amely lakik belsejében a fabric-ügyfél használatával. A health manager jelentéseket menti a health Store adatbázisban. Az állapot-ügyfél a következő beállításokkal konfigurálhatók:

* **HealthReportSendInterval**: A health manager továbbítja a jelentést kerül az ügyfél és az idő közötti késleltetés. A batch-jelentésekhez használt, egy üzenet, nem pedig egy üzenet küldése az egyes jelentések. A kötegelés javítja a teljesítményt. Alapértelmezett: 30 másodperc.
* **HealthReportRetrySendInterval**: A health manager jelenti az időközt, amelyen az állapotfigyelő létrehozása után újraküldi összesített állapotát. Alapértelmezett: 30 másodperc, minimális: 1 másodperc.
* **HealthOperationTimeout**: Az időkorlát a health manager küldött jelentés üzenet. Egy üzenet túllépi az időkorlátot, ha az állapotfigyelő ügyfél azt újrapróbálkozik, mindaddig, amíg a health manager megerősíti, hogy a jelentés feldolgozása megtörtént. Alapértelmezett: két perc.

> [!NOTE]
> Ha a jelentések kötegelni vannak, a fabric ügyfél kell tartani fenn legalább a HealthReportSendInterval annak érdekében, hogy azok elküldéséhez. Ha az üzenet elveszik, vagy az ügyfélállapot-kezelő nem tudja alkalmazni őket átmeneti hibák miatt, a fabric ügyfél kell tartandó már neki, és próbálkozzon újra.
> 
> 

Az egyedi-e a jelentések figyelembe veszi az ügyfélen pufferelés vesz igénybe. Például ha egy adott hibás riporter jelent meg ugyanaz az entitás ugyanazon tulajdonságát másodpercenként 100 jelentést, a jelentések helyettesíti a legutóbbi verzióra. Legfeljebb egy ilyen jelentés létezik, az ügyfél-üzenetsorban. Ha kötegelés van konfigurálva, a health manager küldött jelentések száma / küldési időköz csak az egyik. Ez a jelentés az utolsó új jelentést, amely tükrözi a legfrissebb állapotba az entitás nem.
Adja meg a konfigurációs paramétereket amikor `FabricClient` jön létre átadásával [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) a health kapcsolatos bejegyzéseket a kívánt értékekkel.

Az alábbi példa létrehoz egy fabric ügyfél, és itt adhatja meg, hogy a jelentéseket küld-e, amikor hozzáadja őket. Az időtúllépések és az újrapróbálható hibák az újrapróbálkozások 40 másodpercenként fordulhat elő.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Javasoljuk, hogy az alapértelmezett fabric ügyfél tartja a beállításokat, amelyek beállítása `HealthReportSendInterval` 30 másodperc. Ez a beállítás biztosítja a kötegelés miatt optimális teljesítményét. Kritikus fontosságú jelentések, hogy minél hamarabb el kell küldeni, használja a `HealthReportSendOptions` az Immediate `true` a [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API-t. Azonnali jelentések kihagyása a kötegelés időköz. Ez a jelző; körültekintően szabad használni szeretnénk kihasználhatja az egészségügyi ügyfél kötegelés, amikor csak lehetséges. Azonnali küldés akkor is hasznos, amikor bezárja a fabric-ügyfél (például a folyamat állapota érvénytelen megállapítása és hatásai elkerülése érdekében állítsa le kell). Összesített jelentések legjobb küldés biztosítja. Azonnali jelölővel hozzáadásakor egy jelentés, az egészségügyi ügyfél kötegeli az összesített jelentések az utolsó küldés óta.

Ugyanazokat a paramétereket egy kapcsolatot a fürt PowerShell-lel létrehozásakor adható meg. A következő példa elindítja a helyi fürtöt, kapcsolatot:

```powershell
PS C:\> Connect-ServiceFabricCluster -HealthOperationTimeoutInSec 120 -HealthReportSendIntervalInSec 0 -HealthReportRetrySendIntervalInSec 40
True

ConnectionEndpoint   :
FabricClientSettings : {
                       ClientFriendlyName                   : PowerShell-1944858a-4c6d-465f-89c7-9021c12ac0bb
                       PartitionLocationCacheLimit          : 100000
                       PartitionLocationCacheBucketCount    : 1024
                       ServiceChangePollInterval            : 00:02:00
                       ConnectionInitializationTimeout      : 00:00:02
                       KeepAliveInterval                    : 00:00:20
                       HealthOperationTimeout               : 00:02:00
                       HealthReportSendInterval             : 00:00:00
                       HealthReportRetrySendInterval        : 00:00:40
                       NotificationGatewayConnectionTimeout : 00:00:00
                       NotificationCacheUpdateTimeout       : 00:00:00
                       }
GatewayInformation   : {
                       NodeAddress                          : localhost:19000
                       NodeId                               : 1880ec88a3187766a6da323399721f53
                       NodeInstanceId                       : 130729063464981219
                       NodeName                             : Node.1
                       }
```

Hasonlóképpen API-t, a jelentések elküldött használatával `-Immediate` kapcsoló azonnal elküldött, függetlenül attól, hogy a `HealthReportSendInterval` értéket.

A jelentések többi, a Service Fabric-átjáró, amely egy belső fabric ügyfél érkeznek. Alapértelmezés szerint az ügyfél konfigurálva a jelentések küldéséhez kötegelni Ez lehet 30 másodperc. A fürt konfigurációs beállítással módosíthatja a kötegelési időkö `HttpGatewayHealthReportSendInterval` a `HttpGateway`. Ahogy említettük, jobb megoldás-e küldeni a jelentéseket a `Immediate` igaz. 

> [!NOTE]
> Győződjön meg arról, hogy nem engedélyezett szolgáltatások nem készíthető jelentés az egészségügyi szemben az entitásokat a fürtben, hogy a kiszolgáló csak a biztonságos ügyfelektől érkező kérelmek fogadására konfigurálja. A `FabricClient` jelentés biztonsági engedélyeznie kell a fürt (például a Kerberos vagy Tanúsítványalapú hitelesítés) kommunikálni lehessen használni. Tudjon meg többet [biztonsági fürt](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Jelentés az alacsony jogosultságú-szolgáltatásokban
Ha a Service Fabric-szolgáltatások nem rendelkezik rendszergazdai hozzáféréssel a fürthöz, egészségügyi jelentheti az aktuális környezetben keresztül származó entitásokra `Partition` vagy `CodePackageActivationContext`.

* Az állapotmentes szolgáltatások esetében használjon [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) jelenti az aktuális szolgáltatáspéldányt.
* Az állapotalapú szolgáltatások esetében használjon [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) aktuális replika jelenti.
* Használat [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) az aktuális partíciót entitás jelenti.
* Használat [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) aktuální aplikaci jelenti.
* Használat [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) az aktuális csomóponton üzembe helyezve a jelenlegi alkalmazás jelenti.
* Használat [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) az aktuális csomóponton üzembe helyezett alkalmazás szolgáltatási csomag jelenti.

> [!NOTE]
> Belsőleg az `Partition` és a `CodePackageActivationContext` tartsa egy állapotfigyelő az ügyfél alapértelmezett beállításait. A mértékét a [egészségügyi ügyfél](service-fabric-report-health.md#health-client), jelentések kötegelni és időzítő küldött. Az objektumok életben kipróbálja a jelentés küldése kell tárolni.
> 
> 

Megadhat `HealthReportSendOptions` jelentéseit küldésekor `Partition` és `CodePackageActivationContext` health API-k. Ha kritikus fontosságú jelentések, hogy minél hamarabb el kell küldeni, használja a `HealthReportSendOptions` az Immediate `true`. Azonnali jelentések a kötegelés időköz a belső állapot ügyfél kihagyása. Ahogy korábban említettük, körültekintően szabad használni ezt a jelzőt; szeretnénk kihasználhatja az egészségügyi ügyfél kötegelés, amikor csak lehetséges.

## <a name="design-health-reporting"></a>Állapotfigyelő jelentési tervezése
Kiváló minőségű jelentéseket hozhat létre az első lépés a feltételeket, amelyek befolyásolhatják a szolgáltatás állapotának azonosítja. Tetszőleges feltételt, amely segíthet a szolgáltatásban vagy a fürt jelző problémák – és még jobb, mielőtt hiba történik – is potenciálisan több milliárd dollár mentéséhez. Az értékelemek közé tartozik a kisebb állásidő, kevesebb éjszakai töltött vizsgálatához és javítása, problémák és az újabb ügyfél-elégedettséget.

Miután a feltételeket a azonosítja, figyelő írók kell döntse el, a legjobb módszer a terhelés és használhatóságának közötti egyensúly megfigyelheti őket. Vegyük példaként egy olyan szolgáltatás, amely bizonyos ideiglenes fájlok a használó összetett számításokat végez. Egy figyelő figyelni a megosztáshoz, győződjön meg arról, hogy elegendő lemezterület áll rendelkezésre. Azt tudta figyelni az értesítéseket, fájl vagy könyvtár módosításait. Ha egy előzetes küszöbérték elérése után, és jelentse a hiba, ha a megosztás teljes, sikerült jelentést egy figyelmeztetés. Egy figyelmeztetés, a javítási rendszer indítható régebbi a-megosztáson található fájlok törlésével. A hiba a javítási rendszer a szolgáltatás replikája sikerült áthelyezése másik csomópontra. Vegye figyelembe, hogyan a feltétel állapotok tekintetében állapot leírása: a feltétellel, hogy lehessen venni Kifogástalan (ok), vagy nem megfelelő állapotú (figyelmeztetés vagy hiba) állapotát.

Miután beállította a figyelési részleteit, a figyelő írójának döntse el, hogyan valósíthat meg a figyelő. A feltételek a szolgáltatásban a lehet meghatározni, ha a figyelő a figyelt szolgáltatás részeként is lehet. Például a kódját is ellenőrizze a megosztás használatát, és jelentést készít minden alkalommal, amikor megpróbálja fájl írása. Ez a megközelítés előnye, hogy a jelentések is egyszerű. Ügyelni kell megakadályozza, hogy a figyelő hibák, mely negatív hatással a szolgáltatás funkcióit.

A jelentéskészítés a figyelt szolgáltatás lehetőség nem mindig. A figyelő a szolgáltatásban nem lehet képes észlelni a feltételeket. Még nem rendelkezik a logikai vagy a Hardvertulajdonságok az adatokat. Előfordulhat, hogy magas feltételek ellenőrzésének járó többletterhelést. A feltételek is előfordulhat, hogy nem kell egy adott szolgáltatáshoz, azonban inkább befolyásolhatja a szolgáltatások közötti interakciókat. Egy másik lehetőség, hogy watchdogs a fürt külön folyamatként. A watchdogs figyelheti a feltételeket és a jelentést, anélkül, hogy befolyásolná a fő szolgáltatások bármely módon. Például ezek watchdogs lehetett végrehajtani ugyanabban az alkalmazásban, az összes csomóponton, vagy a szolgáltatás ugyanazon csomóponton üzembe helyezett állapot nélküli szolgáltatásként.

Egyes esetekben a figyelő a fürtben futó lehetőség nem vagy. A figyelt feltétel látnak, a rendelkezésre állás vagy a szolgáltatás működésének lehetőség, ha a legjobb, ha a watchdogs ugyanazon a helyen, a felhasználó-ügyfélként. Itt, tesztelheti a műveletek ugyanúgy felhasználók hívja meg őket. Rendelkezhet például egy figyelő, amely a fürtön kívül él, a problémák az kérelmeket a szolgáltatás és a késleltetés és az eredmény helyességét ellenőrzi. (Egy Számológép szolgáltatáshoz, például nem 2 + 2 adja vissza: 4 ésszerű időn belül?)

Miután a figyelő részletei véglegesítése megtörtént, akkor meg kell határoznia egy adatforrás azonosítója, amely egyedileg azonosítja a. Ha több, azonos típusú watchdogs élő a fürt jelentenie kell a különböző entitások, vagy ha a jelentést, ugyanahhoz az entitáshoz, használja a különböző adatforrás azonosítója vagy tulajdonság. Ebben az esetben a saját jelentésükben egyszerre is használható. Az állapotjelentés tulajdonságát rögzíteni kell a figyelt feltétel. (A fenti példában a tulajdonság lehet **ShareSize**.) Ha ugyanez a feltétel több jelentést is vonatkozik, a tulajdonság lehetővé teszi, hogy a jelentések létezhet dinamikus információkkal kell tartalmaznia. Például, ha több megosztást kell figyelni, a tulajdonság neve lehet **ShareSize – sharename**.

> [!NOTE]
> Tegye *nem* használja a health Store adatbázisban tartani az állapotadatokat. Csak az állapotfigyelő szolgáltatással kapcsolatos információkat kell jelenteni állapotát, mivel ezek az információk hatással van a állapotának kiértékelését az entitások. A health Store adatbázisban nem úgy lett kialakítva, általános célú tárolóként. Az állapot az összes adatokat tudnak összesíteni állapotának kiértékelési logika használ. Összesített állapota egymástól független (például kimutatási OK rendszerállapot-állapot) egészségügyi adatok küldése nem érinti, de ez negatívan befolyásolhatja a health Store adatbázisban teljesítményét.
> 
> 

A következő, hogy melyik entitás jelentésre a. A legtöbb esetben a feltétel egyértelműen azonosítja az entitást. Válassza ki az entitás lehetséges granularitással ajánlott. Ha egy feltétel egy partíció összes replika hatással van, a partíció nem a szolgáltatás a jelentést. Nincsenek ahol további gondolkodási van szükség, azonban esetekben. Ha a feltétel egy entitás, például egy replikát, hatással van, de a törekszik, hogy a feltétel több, mint a replika élettartama kockázatosként megjelölt felhasználókról, majd a partíción kell megadni. Ellenkező esetben a replika törlése esetén a health Store adatbázisban törli azokat az összes jelentés. Figyelő írók kell gondolni az entitás- és a jelentés a élettartamának. Egyértelmű kell lennie, ha egy jelentés tisztítani kell (például ha egy entitás jelentett hiba már nem érvényes.) egy tárolóból.

Nézzük meg, amellyel együtt a pontok I ismertetett példa. Fontolja meg a Service Fabric-alkalmazás fő az állapotalapú állandó szolgáltatások és az összes csomóponton (egy másodlagos szolgáltatás típusa az egyes feladat) telepített másodlagos állapotmentes szolgáltatások állnak. A fő van, amely tartalmazza a másodlagos adatbázisok által végrehajtandó parancsokat feldolgozási várólista. A másodlagos példány hozható létre hajtsa végre a bejövő kéréseket, és küldhet vissza nyugtázással leállt. Egy feltételt, amely ellenőrizhető a feldolgozási várólista hossza. Ha a fő üzenetsor hossza eléri a küszöbértéket, a rendszer figyelmeztetést jelenti. A figyelmeztetés azt jelzi, hogy a másodlagos példány hozható létre nem tudja kezelni a terhelést. Ha a várólista eléri a maximális méretet, és parancsokat a rendszer elveti, hibát jelentett, mivel a szolgáltatás nem állítható helyre. A jelentések lehet tulajdonság **QueueStatus**. A figyelő a szolgáltatáson belül találhatók, és a fölérendelt elsődleges replikán időszakosan továbbítja. Time to live két percet, és rendszeres időközönként 30 másodpercenként továbbítja. Ha az elsődleges leáll, a jelentés automatikusan törlődnek store-ból. Szolgáltatás replika esetén azonban azt holtponti van, vagy más problémákba ütköznek, a jelentés lejár a health Store adatbázisban. Hiba történt, ebben az esetben az entitás lesz kiértékelve.

Egy másik feltétel, amely lehet megfigyelni a feladat végrehajtási ideje. A fő kiosztja a tevékenységeket a másodlagos példány hozható létre a tevékenység típusa alapján. Attól függően, a tervezési a fő sikerült elindítja a lekérdezést a másodlagos példány hozható létre, a feladat állapota. Azt is megvárhatja küldhet vissza nyugtázási történik, ha a másodlagos példány hozható létre. A második esetben ügyelni kell észlelni az olyan helyzetekben, ahol a másodlagos példány hozható létre die vagy üzenetek elvesznek. Az egyik lehetőség van a főkiszolgáló kérelmet kell küldenie egy pingelés azonos másodlagos, amely küldi vissza annak állapotát. Nincs állapot nem érkezett, ha a fő hiba tekinti, és a feladat átütemez. Ez a viselkedés azt feltételezi, hogy a feladatok idempotensek.

A figyelt feltétel is lefordított figyelmeztet, ha a feladat egy bizonyos idő alatt nem történik (**t1**, például 10 perc). Ha a feladat nem fejeződött be időben (**t2**, például 20 perc), a figyelt feltétel fordíthatók – hiba. Ehhez a jelentéshez többféle módon elvégezhető:

* Az eredeti elsődleges replika jelentések maga rendszeres időközönként. A várólista akkor is az összes függő tevékenység egy tulajdonságot. Ha legalább egy feladat eljárás tovább tart, a jelentés állapotának tulajdonság **PendingTasks** figyelmeztető vagy hibaüzenet, szükség szerint van. Ha nincsenek függőben lévő feladatok, vagy minden feladat végrehajtási elindult, a jelentés állapota OK. A feladatok olyan állandó. Ha az elsődleges leáll, az újonnan előléptetett elsődleges továbbra is megfelelően jelentést.
* Egy másik figyelő folyamat (a felhőben, vagy külső) ellenőrzi a feladatok (a kívül, kívánt feladat eredményei alapján), tekintse meg, ha azok végezhető el. Ha azok nem veszik figyelembe a küszöbértékeket, jelentést küld a szolgáltatás főkulcsának. Egy jelentés is elküldi a rendszer minden egyes tevékenységhez, amely magában foglalja a feladat azonosítóját, például **PendingTask + taskId**. Jelentések csak a nem kifogástalan állapotok küldendő adattípusokat. Élő néhány percre, és jelölje be a lejárat után karbantartása biztosítása érdekében el kell távolítani a jelentések idő beállítása.
* Feladat végrehajtása másodlagos jelentések amikor futtatni a vártnál tovább tart. Jelentést készít a tulajdonság a szolgáltatáspéldány **PendingTasks**. A jelentés pinpoints problémák adódtak a szolgáltatáspéldány, de nem rögzíti a helyzetet, ahol a példány elhalálozik. A jelentések ezután törlődnek. Ez a másodlagos service sikerült jelentést. A másodlagos befejezi a feladatot, ha a másodlagos példány törlése a jelentés az áruházból. A jelentés nem rögzíti a helyzetet, ahol a nyugtázás üzenet elveszik, és a feladat nem fejeződött be a fő szempontjából.

A fenti esetekben a jelentéskészítési megtörtént, azonban a jelentések rögzíti a rendszer alkalmazásállapot állapotának kiértékelésekor.

## <a name="report-periodically-vs-on-transition"></a>Jelentés rendszeres időközönként és a Váltás
Az állapotfigyelő jelentési modell használatával watchdogs jelentéseket küldhet a rendszeres időközönként vagy átmeneteket. Az ajánlott módszer a figyelő jelentéskészítési azért rendszeres időközönként, a kód, sokkal egyszerűbb, és kevesebb a hibalehetőség. A watchdogs kell arra törekszik, hogy helytelen jelentések kiváltó hibák elkerülése érdekében a lehető legegyszerűbb legyen. Helytelen *nem megfelelő állapotú* jelentések hatással, egészségügyi értékelések és forgatókönyvek alapján állapota, beleértve a frissítéseket. Helytelen *kifogástalan* jelentések elrejtése problémák a fürtben, amely nem megfelelő.

Az időszakos jelentéshez, a figyelő valósítható egy időzítővel. A egy időzítő visszahívás a figyelő ellenőrizze az állapotát, és küldjön jelentést a jelenlegi állapota alapján. Hiba esetén nem kell, melyik jelentés korábban lett elküldve, vagy bármely optimalizálások üzenetkezelési tekintetében. Az egészségügyi ügyfél rendelkezik kötegelés logikai és a teljesítmény érdekében. Az egészségügyi ügyfél tartási marad, amíg újra megpróbálja belsőleg mindaddig, amíg a jelentés elfogadja a health Store adatbázisban vagy a figyelő az ilyen entitás, tulajdonságot, és forrás újabb jelentést hoz létre.

Az átmenetek jelentéskészítéshez állapot gondos kezelése. A figyelő egyes feltételek figyeli, és jelentéseket, csak ha változnak az feltételek. A feje erre a megközelítésre, hogy kevesebb jelentések van szükség. A hátránya, hogy a figyelő logikájának összetett. A figyelő kell tartania a feltételek vagy a jelentésekben, úgy, hogy a módosítások meghatározásához vizsgálható. Feladatátvétel esetén ügyelni kell a jelentések szerepel, de még nem küldte el a health Store adatbázisban való. A szám folyamatosan növekvő konkurenciával kell kell lennie. Ha nem, a jelentések elavult, a rendszer elutasítja. Bizonyos ritkán előforduló esetekben, ahol keletkezett az adatvesztést a riporter állapotát és a health Store adatbázisban állapotát közötti szinkronizálás lehet szükség.

Keresztül, a reporting Services-jelentések átmenetek logikus `Partition` vagy `CodePackageActivationContext`. Ha a helyi objektum (replika vagy a telepített service-csomag / alkalmazás üzembe helyezése) van eltávolítva, az összes kapcsolódó jelentések is törlődnek. Ez az automatikus tisztítás visszaállítja riporter és a health Store adatbázisban közötti szinkronizálás szükséges. Ha a jelentés szülőpartíción vagy a szülő alkalmazás, ügyelni kell a feladatátvételi a health Store adatbázisban elavult jelentések elkerülése érdekében. Logika megfelelő állapotának fenntartásához, és törölje a jelentést a tároló már szükség van hozzá kell adni.

## <a name="implement-health-reporting"></a>Állapotfigyelő jelentési megvalósítása
Miután az entitás- és a jelentés részletes egyértelmű, rendszerállapot-jelentések küldése végezhető el az API, PowerShell vagy REST.

### <a name="api"></a>API
Jelentés az API-n keresztül, szüksége, hogy szeretne jelentést készíteni az entitástípus adott egészségügyi jelentés létrehozása. A jelentés egy állapotfigyelő ügyfél biztosít. Azt is megteheti, hozzon létre egy egészségügyi információk, és adja át azt a jelentéskészítési módszert megoldására `Partition` vagy `CodePackageActivationContext` a jelentés az aktuális entitást.

Az alábbi példa bemutatja a fürtön belül a figyelő a jelentési rendszeres. A figyelő ellenőrzi, hogy egy külső erőforrás elérhető egy csomóponton belül. Az erőforrás a szolgáltatásjegyzék az alkalmazáson belül van szükség. Az erőforrás nem érhető el, ha az alkalmazás egyéb szolgáltatásai is továbbra is megfelelően fog működni. Ezért a jelentés a telepített szolgáltatások csomag entitáson zajlik Ez lehet 30 másodperc.

```csharp
private static Uri ApplicationName = new Uri("fabric:/WordCount");
private static string ServiceManifestName = "WordCount.Service";
private static string NodeName = FabricRuntime.GetNodeContext().NodeName;
private static Timer ReportTimer = new Timer(new TimerCallback(SendReport), null, 30 * 1000, 30 * 1000);
private static FabricClient Client = new FabricClient(new FabricClientSettings() { HealthReportSendInterval = TimeSpan.FromSeconds(0) });

public static void SendReport(object obj)
{
    // Test whether the resource can be accessed from the node
    HealthState healthState = this.TestConnectivityToExternalResource();

    // Send report on deployed service package, as the connectivity is needed by the specific service manifest
    // and can be different on different nodes
    var deployedServicePackageHealthReport = new DeployedServicePackageHealthReport(
        ApplicationName,
        ServiceManifestName,
        NodeName,
        new HealthInformation("ExternalSourceWatcher", "Connectivity", healthState));

    // TODO: handle exception. Code omitted for snippet brevity.
    // Possible exceptions: FabricException with error codes
    // FabricHealthStaleReport (non-retryable, the report is already queued on the health client),
    // FabricHealthMaxReportsReached (retryable; user should retry with exponential delay until the report is accepted).
    Client.HealthManager.ReportHealth(deployedServicePackageHealthReport);
}
```

### <a name="powershell"></a>PowerShell
A rendszerállapot-jelentések küldése **küldési-ServiceFabric*EntityType*HealthReport**.

Az alábbi példában látható rendszeres jelentés készítését a CPU-értékeket egy csomóponton. A jelentéseket kell küldeni 30 másodpercenként, és két perc élettartamot rendelkeznek. Jár le, ha a jelentéskészítő veti fel, így hiba: a csomópontot abban az esetben. A Processzor-küszöbérték felett van, ha a jelentésnek figyelmeztetés állapotba. A Processzor a küszöbérték felett marad, több, mint a beállított időn, amikor azt hibát jelentett. Ellenkező esetben a riporter küld egy állapota OK.

```powershell
PS C:\> Send-ServiceFabricNodeHealthReport -NodeName Node.1 -HealthState Warning -SourceId PowershellWatcher -HealthProperty CPU -Description "CPU is above 80% threshold" -TimeToLiveSec 120

PS C:\> Get-ServiceFabricNodeHealth -NodeName Node.1
NodeName              : Node.1
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='CPU', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.FM
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 5
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Fabric node is up.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : CPU
                        HealthState           : Warning
                        SequenceNumber        : 130741236814913394
                        SentAt                : 4/21/2015 9:01:21 PM
                        ReceivedAt            : 4/21/2015 9:01:21 PM
                        TTL                   : 00:02:00
                        Description           : CPU is above 80% threshold
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:01:21 PM
```

Az alábbi példa egy replikát egy átmeneti figyelmeztetési jelentések. Először lekéri a Partícióazonosító és a másodpéldány-azonosító van érdekelné a szolgáltatáshoz. Ezután elküldi a jelentéseket a **PowershellWatcher** tulajdonság **ResourceDependency**. A jelentés a lényeges csak két percig, és a rendszer eltávolítja a tároló automatikusan.

```powershell
PS C:\> $partitionId = (Get-ServiceFabricPartition -ServiceName fabric:/WordCount/WordCount.Service).PartitionId

PS C:\> $replicaId = (Get-ServiceFabricReplica -PartitionId $partitionId | where {$_.ReplicaRole -eq "Primary"}).ReplicaId

PS C:\> Send-ServiceFabricReplicaHealthReport -PartitionId $partitionId -ReplicaId $replicaId -HealthState Warning -SourceId PowershellWatcher -HealthProperty ResourceDependency -Description "The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes." -TimeToLiveSec 120 -RemoveWhenExpired

PS C:\> Get-ServiceFabricReplicaHealth  -PartitionId $partitionId -ReplicaOrInstanceId $replicaId


PartitionId           : 8f82daff-eb68-4fd9-b631-7a37629e08c0
ReplicaId             : 130740415594605869
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='PowershellWatcher', Property='ResourceDependency', HealthState='Warning', ConsiderWarningAsError=false.

HealthEvents          :
                        SourceId              : System.RA
                        Property              : State
                        HealthState           : Ok
                        SequenceNumber        : 130740768777734943
                        SentAt                : 4/21/2015 8:01:17 AM
                        ReceivedAt            : 4/21/2015 8:02:12 AM
                        TTL                   : Infinite
                        Description           : Replica has been created.
                        RemoveWhenExpired     : False
                        IsExpired             : False
                        Transitions           : ->Ok = 4/21/2015 8:02:12 AM

                        SourceId              : PowershellWatcher
                        Property              : ResourceDependency
                        HealthState           : Warning
                        SequenceNumber        : 130741243777723555
                        SentAt                : 4/21/2015 9:12:57 PM
                        ReceivedAt            : 4/21/2015 9:12:57 PM
                        TTL                   : 00:02:00
                        Description           : The external resource that the primary is using has been rebooted at 4/21/2015 9:01:21 PM. Expect processing delays for a few minutes.
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : ->Warning = 4/21/2015 9:12:32 PM
```

### <a name="rest"></a>REST
A POST kérelmeket, nyissa meg a kívánt entitásra, és a szervezet rendelkezik az egészségügyi jelentés leírása REST használatával rendszerállapot-jelentések küldése. Például megtudhatja, hogyan küldhet REST [rendszerállapot-jelentések fürt](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) vagy [rendszerállapot-jelentések szolgáltatás](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Minden entitás támogatottak.

## <a name="next-steps"></a>További lépések
Az egészségügyi adatok alapján, szolgáltatás írók és a fürt/alkalmazás-rendszergazdák is felfoghatók módon fogyasztanak az információkat. Például azok állíthat be riasztásokat állapota alapján olvasásra súlyos problémákat, mielőtt azok valamilyen okból kimaradás lép kiváltó. A rendszergazdák is állíthat be javítási rendszerek automatikusan megoldhatja a problémákat.

[Bevezetés a Service Fabric állapotának figyelése](service-fabric-health-introduction.md)

[A Service Fabric-állapotjelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[Hogyan lehet szolgáltatási állapot jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Hibaelhárítás rendszerállapot-jelentések használata](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[A szolgáltatások helyi monitorozása és diagnosztizálása](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

