---
title: "Adja hozzá az egyéni Service Fabric állapotjelentések |} Microsoft Docs"
description: "Egyéni rendszerállapot-jelentések küldése az Azure Service Fabric állapotfigyelő entitások ismerteti. Ad javaslatokat megtervezése és minőségi állapotjelentések végrehajtása."
services: service-fabric
documentationcenter: .net
author: oanapl
manager: timlt
editor: 
ms.assetid: 0a00a7d2-510e-47d0-8aa8-24c851ea847f
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: 1cd429ed8252573f8e8c3ed11d6c841cba855b52
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="add-custom-service-fabric-health-reports"></a>Egyéni Service Fabric-állapotjelentések hozzáadása
Az Azure Service Fabric vezet be a [állapotmodell](service-fabric-health-introduction.md) úgy tervezték, hogy sérült fürt és az alkalmazás feltételeket az adott entitások jelzőt. A health modellje **állapotfigyelő jelentéskészítők** (rendszer összetevőit és watchdogs). Könnyű és gyors diagnosztikai és javítási célja. Szolgáltatás írók szükség az társaságuk vonatkozó állapotát. Minden olyan esetben, kedvezőtlen hatással lehet az egészségügyi kell kiválasztását, különösen akkor, ha segíthet megközelíti a legfelső szintű jelző problémák. Az állapotadatok is mentheti időt és erőfeszítést Hibakeresés és a vizsgálat. Támogató különösen akkor törölje a jelet, ha a szolgáltatás megfelelően működik, és a felhőben léptékű (magán- vagy Azure).

A Service Fabric-jelentéskészítők nevű figyelő meghatározott feltételek egyik fontos. Azok a helyi nézet alapján feltételek jelentést. A [a health Store adatbázisban](service-fabric-health-introduction.md#health-store) összefoglalja az entitások globálisan kifogástalan megállapításához összes jelentéskészítők által küldött egészségügyi adatokat. A modell olyan gazdag, rugalmas és könnyen használható. A rendszerállapot-jelentések minőségének meghatározza, hogy a fürt az állapotmegtekintő nézet pontosságát. Téves tévesen jeleníti meg a nem megfelelő negatívan befolyásolhatja a frissítések vagy az egészségügyi adatokat használó más szolgáltatásokba. Ezek a szolgáltatások többek között a javítása és a riasztási mechanizmusokat. Adja meg a jelentéseket, amelyek a lehető legjobb módon érdeklő feltételek rögzítése ezért néhány gondolat van szükség.

Watchdogs tervezése és megvalósítása reporting állapotát, és rendszerösszetevők kell:

* Határozza meg a feltételt, akkor is, a számítógép megfigyelés alatt áll módon és a hatás a fürt vagy az alkalmazás funkcióit. Ezen információ alapján döntse el, az egészségügyi jelentés tulajdonság és állapotát.
* Határozza meg a [entitás](service-fabric-health-introduction.md#health-entities-and-hierarchy) , amely a jelentés vonatkozik.
* Annak megállapítása, ahol a jelentéskészítési kész, a a szolgáltatáson belül vagy egy belső vagy külső figyelő.
* Definiáljon alapján határozza meg a jelentéskészítő.
* Válassza ki a jelentéskészítési stratégiát, vagy rendszeres időközönként, vagy a átmenetek. Az ajánlott módszer a rendszeres időközönként esetén, mert az egyszerűbb kód szükséges, és kevesebb a hibalehetőség.
* Határozza meg, hogy mennyi ideig a jelentés a nem megfelelő feltételeket kell marad a health Store adatbázisban, és hogyan jelölését. Ezen információk alapján döntse el, a jelentéskészítéskor élettartama, és távolítsa el a lejárati viselkedését.

Ahogy azt korábban említettük, reporting teheti meg:

* A figyelt Service Fabric-szolgáltatás replika.
* Belső watchdogs telepített Service Fabric szolgáltatás (például a Service Fabric állapotmentes szolgáltatások, amely figyeli a feltételek és a jelentések problémák). A watchdogs lehet telepíteni az összes csomópont, vagy is rendelhetők affinitás alapján a figyelt szolgáltatás.
* Futtassa a Service Fabric-csomóponton, de belső watchdogs *nem* valósul meg a Service Fabric-szolgáltatás.
* Külső watchdogs, hogy az erőforrást mintavételi *kívül* a Service Fabric-fürt (például figyelőszolgáltatás Gomez hasonlóan).

> [!NOTE]
> A fürt első használatkor a rendszer-összetevők által küldött állapotjelentések telepítéskor. További információ: [hibaelhárítási rendszerállapot használatával jelentéseket](service-fabric-understand-and-troubleshoot-with-system-health-reports.md). A felhasználói jelentések kell küldeni [állapotfigyelő entitások](service-fabric-health-introduction.md#health-entities-and-hierarchy) , amely létre lett hozva a rendszer.
> 
> 

Egyszer a állapotfigyelő Tervező egyértelmű jelentéskészítési, állapotjelentések küldhető könnyen. Használhat [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) jelentés állapotába. Ha a fürt nem [biztonságos](service-fabric-cluster-security.md) , vagy ha a fabric-ügyfélnek rendszergazdai jogosultságokkal rendelkezik. Jelentéskészítési végezhető el az API által használatával [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), PowerShell vagy a többi útján. Konfigurációs forgatógombját kötegelt javítja a teljesítményt a jelentésekre.

> [!NOTE]
> A jelentés állapotának szinkron, és azt jelenti, hogy csak az ügyfél oldali érvényesítési munka. Azt a tényt, hogy a jelentés a rendszerállapot-ügyfél által elfogadható vagy a `Partition` vagy `CodePackageActivationContext` objektumok nem jelenti azt, hogy van-e alkalmazva a tárolóban. Küldött aszinkron módon történik, és esetleg kötegelni más jelentésekre. A kiszolgáló terhelése is meghiúsulhat: a sorszám elavult lehet, amelyen a jelentés alkalmazni kell az entitás már törölt, stb.
> 
> 

## <a name="health-client"></a>Rendszerállapot-ügyfél
A rendszerállapot-jelentések küldése a állapotfigyelő ügyfél, amelyen belül a fabric-ügyfélnek él keresztül a health Store adatbázisban. A rendszerállapot-ügyfél az alábbi beállításokkal konfigurálható:

* **HealthReportSendInterval**: a jelentés hozzáadódik az ügyfél és az idő a health Store adatbázisban való továbbítás közötti késleltetés. Használt kötegelt jelentések egyetlen üzenetet, nem pedig egy üzenet küldése az egyes jelentések. A kötegelés javítja a teljesítményt. Alapértelmezett: 30 másodperc.
* **HealthReportRetrySendInterval**: az időköz, ahol a health újraküldi halmozott állapotfigyelő jelent a health Store adatbázisban. Alapértelmezett: 30 másodperc.
* **HealthOperationTimeout**: a health Store adatbázisban küldött jelentést tartalmazó üzenet megadott időkorlát. Üzenet időkorlátja lejár, ha az állapotfigyelő ügyfél azt újrapróbálkozik, amíg a health Store adatbázisban megerősíti, hogy a jelentés feldolgozása. Alapértelmezett: két perc.

> [!NOTE]
> A jelentések vannak kötegelni, amikor a fabric-ügyfélnek kell életben tartania számára legalább a HealthReportSendInterval annak érdekében, hogy elküldi őket. Ha az üzenet elveszik, vagy átmeneti hibák okozták a health Store adatbázisban nem alkalmazza, a fabric-ügyfélnek kell életben tartania már egészítse ki arra, hogy próbálja meg újra.
> 
> 

A jelentések egyediségét számításba venni az ügyfélen pufferelés vesz igénybe. Például ha egy adott rossz jelentéskészítői az jelentéskészítési ugyanahhoz a tulajdonsághoz, az ugyanaz az entitás 100 jelentések / másodperc, a jelentések váltják fel a legfrissebb verziója. Legfeljebb egy ilyen jelentés létezik-e az ügyfél várólistában. Ha kötegelés van konfigurálva, a health Store adatbázisban küldött jelentések száma csak egy küldési időszakonként. Ez a jelentés az utolsó hozzáadott jelentést, amely tükrözi a legfrissebb állapotba az entitás.
Adja meg a konfigurációs paraméterek amikor `FabricClient` jön létre úgy, hogy [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) a rendszerállapot-kapcsolatos bejegyzéseket a kívánt értékekkel.

Az alábbi példa létrehoz egy fabric-ügyfélnek, és meghatározza, hogy a jelentéseket küldhetnek-e, amikor hozzáadja őket. Időtúllépések és követően újra megkísérelhető a hibákról újrapróbálkozások 40 másodpercenként kerül sor.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Azt javasoljuk, hogy az alapértelmezett fabric-ügyfél beállításainak, amely `HealthReportSendInterval` 30 másodperc. Ez a beállítás biztosítja a kötegelés miatt optimális teljesítményt. A kritikus jelentések, amelyek a lehető leghamarabb el kell küldeni, használjon `HealthReportSendOptions` az Immediate `true` a [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API. Azonnali jelentések kihagyása esetén a kötegelési időköz. Ez a jelző körültekintően; használja azt szeretnénk, az egészségügyi ügyfél kötegelés, amikor csak lehetséges előnyeit. Azonnali küldés akkor hasznos, ha bezárja a fabric-ügyfélnek (például a folyamat állapota érvénytelen megállapítása és hatásai megelőzése érdekében leáll). Biztosítja a legjobb küldési halmozott jelentések. Amikor egy jelentés azonnali jelzővel ad hozzá, az egészségügyi ügyfél kötegek a halmozott jelentések utolsó küldési óta.

Paramétereket a PowerShell segítségével kapcsolódni a fürt létrehozásakor adható meg. A következő példa egy a helyi fürthöz való kapcsolódási kezdődik:

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

Hasonlóképpen API-t, hogy jelentéseket is küld a `-Immediate` kapcsoló azonnal elküldött, függetlenül attól, hogy a `HealthReportSendInterval` érték.

A többi a jelentések küldése a a Service Fabric-átjárón, amely rendelkezik egy belső fabric-ügyfélnek. Alapértelmezés szerint ez az ügyfél beállításai küldjön jelentést kötegelni 30 másodpercenként. A fürt konfigurációs beállítással módosíthatja a kötegelt időköz `HttpGatewayHealthReportSendInterval` a `HttpGateway`. Ahogy azt korábban említettük, a jobb megoldás-e jelentéseket küldeni `Immediate` igaz. 

> [!NOTE]
> Győződjön meg arról, hogy a nem hitelesített szolgáltatások nem tud jelenteni szembeni az entitásokat, a fürt, konfigurálja a kiszolgáló csak biztonságos ügyfelektől érkező kérelmek fogadására. A `FabricClient` jelentéskészítési rendelkezik biztonsági engedélyezve kell lennie képes kommunikálni a fürt (például a Kerberos- vagy Tanúsítványalapú hitelesítés). Tudjon meg többet az [fürt biztonsági](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Jelentés az alacsony jogosultságú szolgáltatások belül
Service Fabric-szolgáltatások nem rendelkeznek rendszergazdai hozzáférés a fürthöz, ha az aktuális környezetben keresztül entitásokat a állapotfigyelő jelentést `Partition` vagy `CodePackageActivationContext`.

* Az állapotmentes szolgáltatásokhoz használja [IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) az aktuális szolgáltatáspéldányt jelentést.
* Állapotalapú szolgáltatások esetén használjon [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) a jelentés az aktuális replika.
* Használjon [IServicePartition.ReportPartitionHealth](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) számára, az aktuális partíciót entitás.
* Használjon [CodePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) aktuális alkalmazás jelentést.
* Használjon [CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) számára, az adott alkalmazás telepítése az aktuális csomóponton.
* Használjon [CodePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) számára, az az aktuális csomópont telepített alkalmazás egy service-csomag.

> [!NOTE]
> Belsőleg az `Partition` és a `CodePackageActivationContext` állapotát az ügyfél alapértelmezett beállításokkal rendelkezik. Ahogy az a [állapotfigyelő ügyfél](service-fabric-report-health.md#health-client), jelentések vannak és időzítő küldi. Az objektumok életben is elküldheti a jelentést rendelkeznie kell tartani.
> 
> 

Megadhat `HealthReportSendOptions` keresztül jelentések küldéséhez `Partition` és `CodePackageActivationContext` rendszerállapot API-k. Ha kritikus jelentések, amelyek a lehető leghamarabb el kell küldeni, használja `HealthReportSendOptions` az Immediate `true`. Azonnali jelentések kihagyása esetén a belső állapot ügyfél kötegelési időköz. Ahogy korábban említettük, körültekintően; használja ezt a jelzőt azt szeretnénk, az egészségügyi ügyfél kötegelés, amikor csak lehetséges előnyeit.

## <a name="design-health-reporting"></a>Állapotfigyelő jelentéskészítés tervezése
Az első lépés a kiváló minőségű jelentések létrehozásához azonosítja a feltételeket, amelyek hatással lehet a szolgáltatás állapotát. Minden feltétel, amely segíthet a szolgáltatás vagy a fürt jelző problémák--és még jobban előtt történik, a probléma – is potenciálisan dolláros egy mentés. A következő előnyöket nyújtja kisebb állásidő, kevesebb éjszakai töltött vizsgálja, és problémákat, és magasabb szintű ügyfelek elégedettségének javítása.

Amennyiben a feltételeket a azonosítja, figyelő írók ki kell deríteni a legjobb módszer a terhelés és hasznosságát közötti egyensúly megfigyelheti őket. Tegyük fel, amelyet összetett számítások bizonyos megosztott fájlokat használó szolgáltatás. Egy figyelő figyelni a megosztás győződjön meg arról, hogy elegendő lemezterület áll rendelkezésre. Azt tudta figyelni az értesítéseket a fájl vagy könyvtár változások. Az sikerült jelentést egy figyelmeztetés, ha egy társaságuk küszöbérték elérésekor, és hibaüzenetet jelenít meg, ha a megosztás teljes. A figyelmeztetést, a javítási rendszer elindítása volt, törölje a régebbi, a megosztáson tárolt fájlok. A hiba a javítási rendszer a szolgáltatás replika sikerült áthelyezése másik csomópontra. Vegye figyelembe a módját ismerteti a a feltétel állapotok állapotfigyelő tekintetében: a feltétel tekinthető Kifogástalan (ok) vagy a nem megfelelő (figyelmeztetés vagy hiba) állapotát.

A figyelési részletek van beállítva, ha egy figyelő írójának, hogyan lehet a figyelő végrehajtásához. A feltételek a szolgáltatáson belül lehet határozni, ha a figyelő a figyelt szolgáltatás része lehet. Például a szolgáltatás kódot ellenőrizze a megosztás használatát, és jelentést készít a minden alkalommal, amikor megpróbálja fájl írása. Ez a megközelítés előnye, hogy a reporting felettébb egyszerű. Gondot kell fordítani a szolgáltatást érintő figyelő hibák megakadályozása.

A jelentéskészítési a figyelt szolgáltatás nincs mindig lehetőség. Előfordulhat, hogy a figyelő a szolgáltatáson belül nem észleli a. Nem lehet a programot vagy az adatokba, így a meghatározása. Növeli a feltételek ellenőrzésének magas lehet. A feltételek is nem lehet egy adott szolgáltatáshoz, de ehelyett érinti a szolgáltatások közötti kapcsolat. Egy másik lehetőség, hogy watchdogs a fürt különálló folyamatként. A watchdogs figyelheti a feltételek és a jelentést, az nem befolyásolja a fő szolgáltatások bármely olyan módon. Például a watchdogs sikerült implementálható ugyanabban az alkalmazásban, az összes olyan csomóponton, vagy a szolgáltatást ugyanazon csomópontjára telepített állapotmentes szolgáltatásokhoz.

Egyes esetekben a figyelő a fürtben futó lehetőség nem érhető el vagy. A figyelt feltétele a rendelkezésre állás vagy a szolgáltatás működésére, a felhasználók látni, akkor legjobb, ha a watchdogs ugyanazon a helyen, a felhasználó-ügyfélként. Hiba azok tesztelheti a műveletek ugyanúgy felhasználók keresheti őket. Lehet például egy figyelő, amely a fürtön kívüli él, kérelmeket állít ki a szolgáltatás és a késleltetés és helyességét az eredményt ellenőrzi. (A Számológép szolgáltatáshoz, például nem 2 + 2 vissza 4 elfogadható időn belül?)

Miután a figyelő részletek véglegesítése megtörtént, meg kell határoznia egy adatforrás azonosítója, amely egyedileg azonosítja a. Ha több, azonos típusú watchdogs élő a fürt a különféle entitásokat jelenteniük kell, vagy ha azok ugyanaz az entitás jelentést, használja a különböző adatforrás azonosítója vagy tulajdonság. Ezzel a módszerrel a jelentések egyszerre is használható. Az állapotjelentés tulajdonságának irányítsa a figyelt feltétel. (A fenti példában a tulajdonság lehet **ShareSize**.) Ha több jelentések Ugyanez vonatkozik, a tulajdonság néhány dinamikus információkkal rendelkezhetnek, amely lehetővé teszi a jelentések azonos kell tartalmaznia. Például, ha több megosztást kell figyelni, a tulajdonság neve lehet **ShareSize-megosztásnév**.

> [!NOTE]
> Tegye *nem* a health Store adatbázisban segítségével állapotadatait. Csak a rendszerállapot-kapcsolatos információkkal kell jelenteni állapotát, mivel ezek az információk hatással van az állapot kiértékelésekor az entitás. A health Store adatbázisban nem egy általános célú tárolóként úgy lett kialakítva. Állapotfigyelő értékelési logika állapotát az összes adat összesítésére használ. Elküldött adatok (például a jelentéskészítési OK állapotot az állapot) állapotfigyelő egymástól független nem befolyásolja a összesített állapotát, de ez negatívan befolyásolhatja a health Store adatbázisban teljesítményét.
> 
> 

A következő döntési tényező a jelentés az entitáshoz. Az idő, a következő feltételt: a legtöbb egyértelműen idetifies az entitás. Válassza ki az entitást a lehető legjobb lépésköz. Ha egy feltétel hatással van egy partíció összes replika, a partíció nem a szolgáltatás a jelentést. Ha további gondolat van szükség, azonban esetekben van. Ha a feltétel egy entitás, például egy replikát, hatással van, de a desire, hogy a feltétel meg van jelölve, a több, mint a replika élettartama időtartama, majd a partíción kell megadni. Ellenkező esetben a replika törlésekor a health Store adatbázisban a szükségtelenné vált a jelentéseket. Figyelő írók az entitáshoz és a jelentés a élettartamának kell gondolni. Törölje a jelet kell, amikor egy jelentés tisztítani kell a tárolóból (például ha már nem érvényes egy entitás jelentett hiba).

Egy példa, amely az együtt a pontok I leírt vizsgáljuk meg. Vegye figyelembe, hogy a Service Fabric-alkalmazás fő állapot-nyilvántartó állandó szolgáltatás és a másodlagos állapotmentes szolgáltatásokhoz (egy másodlagos szolgáltatás típusa az egyes feladatok) összes csomópontjára telepíti állnak. A fő van hajthatják végre a másodlagos adatbázist, parancsokat tartalmazó feldolgozási várólista. A másodlagos adatbázisok a bejövő kérelmeket, és hátsó nyugtázási jelek küldésére. Egy olyan feltételt, amely ellenőrizhető a feldolgozási sor hossza. Ha a fő várólista hossza eléri a, figyelmeztetés az elvártnak. A figyelmeztetés azt jelzi, hogy a másodlagos adatbázist nem tudja kezelni a terhelés. Ha a várólista eléri a maximális méretet, és dobja parancsok, egy hibát jelzett, mert a szolgáltatás nem állítható helyre. A jelentések lehet tulajdonság **QueueStatus**. A figyelő él belül a szolgáltatást, majd a fő elsődleges replikán rendszeresen továbbítja. Az élettartamnak két percet, és rendszeres időközönként 30 másodpercenként továbbítja azokat. Ha az elsődleges leáll, a jelentés automatikusan törlődnek áruházból. A szolgáltatás replika esetén azonban azt van e holtpontba, vagy egyéb problémákat tapasztal, a jelentés lejár a health Store adatbázisban. Ebben az esetben az entitás: hiba ki lesz értékelve.

Egy másik figyelhető feltétele feladat végrehajtási ideje. A fő osztja el a feladatokat a másodlagos adatbázist, a tevékenység típusa alapján. Attól függően, hogy a tervező a fő sikerült kérdezze le a másodlagos adatbázist, a feladatok állapotát. Másodlagos partíciója hátsó nyugtázási jelek küld, ha azok végzett sikerült is megvárhatja. A második esetben gondot kell fordítani észlelni az olyan helyzetekben, ahol a másodlagos adatbázisok die vagy üzenetek elvesznek. Egy elem a főkiszolgáló ping kérelem küldése, ha ugyanaz a másodlagos, amely küld vissza az állapotát. Nincs állapot érkezik, ha a fő tartja hibát, és a feladat reschedules. Ez a viselkedés feltételezi, hogy a feladatok az idempotent.

A figyelt feltétel fordítható figyelmeztetés, ha a feladatot nem végezheti el a bizonyos ideig (**t1**, például 10 perc). Ha a feladat nem fejeződött be időben (**t2**, például 20 perc), a figyelt feltétel fordítható – hiba. Ehhez a jelentéshez végezhető többféle módon:

* Az eredeti elsődleges replika jelentések maga rendszeres időközönként. A várólista akkor is az összes függő tevékenység egy tulajdonságot. Ha legalább egy feladat eljárás tovább tart, a jelentés állapotának tulajdonság **PendingTasks** figyelmeztető vagy hibaüzenet, szükség szerint van. Ha nincs folyamatban lévő tevékenységek, vagy minden feladat végrehajtási, a jelentés állapota OK gombra. A feladatok olyan állandó. Ha az elsődleges leáll, az újonnan előléptetett elsődleges továbbra is megfelelően jelentést.
* Egy másik figyelő folyamat (a felhőben, vagy külső) ellenőrzi a feladatok (a kívül, eredményei alapján a kívánt feladat) Ha teljesítése megjelenítéséhez. Ha nem veszik figyelembe a küszöbértékeket, hogy jelentést küld el a fő szolgáltatásban. Egy jelentés is lett van küldve, amely magában foglalja a feladatazonosítót a feladat, például minden tevékenység **PendingTask + taskId**. Jelentések csak nem kifogástalan állapotok kellene elküldeni. Élő néhány percet, és jelölje be a karbantartási biztosításához lejárat után el kell távolítani a jelentések idő beállítása.
* A másodlagos feladat éppen futó futtassa a vártnál hosszabb ideig tart, amikor jelentéseket. Azt a szolgáltatáspéldány tulajdonság jelentések **PendingTasks**. A jelentés pinpoints a szolgáltatáspéldány, amely problémák léptek fel, de ez a helyzet, ahol a példány elhalálozik nem rögzíti. A jelentések majd megtisztítva. Ez a másodlagos service sikerült jelentést. Ha a másodlagos befejezi a feladatot, a másodlagos példány törlése a jelentés az áruházból. A jelentés nem rögzíti a helyzet, ahol a nyugtázási üzenetet elvész, és a feladat nem fejeződött be a fő szempontjából.

A jelentéskészítési végezheti el a fent leírt eseteket, azonban a jelentések a lesznek rögzítve az alkalmazás állapotának, állapotának kiértékelésekor.

## <a name="report-periodically-vs-on-transition"></a>Jelentés rendszeres időközönként és átmenet
Az állapotfigyelő jelentéskészítő modell segítségével watchdogs küldhet jelentések rendszeres időközönként vagy átmenetek. Az ajánlott módszer a figyelő reporting oka rendszeres időközönként, a kód sokkal egyszerűbb, és kevesebb a hibalehetőség. A watchdogs lehető legegyszerűbb helytelen jelentések kiváltó hibák elkerülése érdekében kell szükség. Helytelen *sérült* jelentések hatással lehet a rendszerállapot-értékelések és forgatókönyvek alapján állapota, beleértve a frissítéseket. Helytelen *kifogástalan* jelentések elrejtése problémák a fürt, amely nem kívánatos.

Az időszakos, a figyelő egy időzítővel valósítható meg. Egy időzítő visszahíváskor a figyelő állapota, és küldjön jelentést a jelenlegi állapota alapján. Nincs szükség az tekintse meg, melyik jelentés korábban küldött vagy bármely optimalizálásokat üzenetküldési tekintetében. Az állapotfigyelő ügyfél rendelkezik, kötegelés logika teljesítmény érdekében. A rendszerállapot-ügyfél aktív marad, amíg újbóli belső addig, amíg a jelentés elfogadja a health Store adatbázisban vagy a figyelő ilyen entitás, tulajdonságot, és forrás újabb jelentést hoz létre.

A átmenetek jelentéskészítéshez gondos kezelésére vonatkozó állapotát. A figyelő bizonyos feltételek figyeli, és csak a feltételek megváltozása jelenti. A feje a hátránya az, hogy kevesebb jelentések van szükség. A hátránya az, hogy a figyelő logikájának összetett. A figyelő kezelnie kell a feltételek vagy a jelentéseket, hogy azok állapotváltozások meghatározásához lehet megvizsgálni. Feladatátvétel, a gondot kell fordítani szerepel, de a health Store adatbázisban még nem küldött jelentésekkel. A sorszám egyre növekvő kell lennie. Ha nem, a jelentések szerint elavult utasítja el. Bizonyos ritkán előforduló esetekben, ahol adatvesztés van szükség a jelentési állapota és a health Store adatbázisban állapotának közötti szinkronizálás lehet szükség.

Jelentés készítését átmenetek szabálykészletében keresztül, a reporting Services `Partition` vagy `CodePackageActivationContext`. Ha a helyi objektum (replika vagy a telepített szervizcsomag / alkalmazást telepített) van eltávolítva, a jelentések is törlődnek. Az automatikus tisztítás visszaállítja jelentéskészítői és a health Store adatbázisban közötti szinkronizálás szükséges. Ha a jelentés szülő alkalmazás vagy a szülőpartíció számára, kell ügyelni feladatátvevő a health Store adatbázisban elavult jelentések elkerülése érdekében. A helyes állapotban, és törölje a jelentés többé nincs szükség, ha a programot kell felvenni.

## <a name="implement-health-reporting"></a>Állapotfigyelő reporting megvalósítása
Miután az entitás és a jelentés adatainak törlése, rendszerállapot-jelentések küldése végezhető el az API-t, a PowerShell vagy a REST.

### <a name="api"></a>API
Hogy jelentse az API-n keresztül, létrehozásához szükséges egy jelentés jellemző szeretne jelentést készíteni, entity Type típusként. A jelentésnek a rendszerállapot-ügyfeleknek. Azt is megteheti, hozzon létre egy állapottal kapcsolatos adatok, és adja át a jelentéskészítési módszert megoldására `Partition` vagy `CodePackageActivationContext` aktuális entitások jelentést.

A következő példa bemutatja a figyelő a fürtön belül jelentések rendszeres. A figyelő ellenőrzi, hogy egy külső forrásból elérhető csomóponton belül. Az erőforrás által a szolgáltatás jegyzék az alkalmazáson belül van szükség. Ha az erőforrás nem érhető el, egyéb szolgáltatásokat az alkalmazásban továbbra is megfelelően működjön. Ezért a jelentés a telepített service-csomag entitás zajlik 30 másodpercenként.

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

A következő példa bemutatja rendszeres jelentés készítését a csomópont CPU-értékek. A jelentések küldjön 30 másodpercenként, és egyszerre élettartama pedig két perc rendelkeznek. Járnak, ha a jelentéskészítő problematikus, hiba: a csomópont kiértékelése történik. Ha a CPU küszöbértéket, a jelentésben van figyelmeztetési állapotot. Ha a CPU-t továbbra is a küszöbérték fölött hibát jelentett több, mint a beállított időn. Ellenkező esetben a jelentéskészítő elküldi a állapota az OK gombra.

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

Az alábbi példa hiányára átmeneti replikán. Az első lekérdezi a Partícióazonosító és másodpéldány-azonosító iránt érdeklődik, a szolgáltatás. Ezután elküldi a jelentést a **PowershellWatcher** tulajdonság **ResourceDependency**. A jelentés egyik fontos csak két percig, és a rendszer eltávolítja az áruház automatikusan.

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
Nyissa meg a kívánt entitáshoz és a rendszerállapot-jelentés leírása a szervezet rendelkezik POST kérelmek használatával REST állapotjelentések küldése. Például tekintse meg a többi küldése [állapotjelentések fürt](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) vagy [állapotjelentések szolgáltatás](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service). Minden entitások támogatottak.

## <a name="next-steps"></a>További lépések
A rendszerállapot-adatok alapján, szolgáltatás írók és a fürt/rendszergazdái is gondolja, hogy többféleképpen felhasználhatják az információkat. Például azok állíthat be riasztásokat állapot alapján komoly problémát catch ahhoz, azok kiváltó kimaradások esetén. Rendszergazdák is beállíthatnak javítási rendszerek automatikusan problémáinak megoldásával kapcsolatban.

[Bevezetés a Service Fabric állapotának figyelése](service-fabric-health-introduction.md)

[A Service Fabric rendszerállapot-jelentések megtekintése](service-fabric-view-entities-aggregated-health.md)

[Jelentés és a szolgáltatás állapotának ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Rendszerállapot-jelentések használata a hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[Figyelése és diagnosztizálása helyileg szolgáltatások](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric-alkalmazás frissítése](service-fabric-application-upgrade.md)

