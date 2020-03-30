---
title: Egyéni Service Fabric-állapotjelentések hozzáadása
description: Bemutatja, hogyan küldhet egyéni állapotjelentéseket az Azure Service Fabric állapotentitások. Ajánlásokat ad a minőségi egészségügyi jelentések megtervezéséhez és végrehajtásához.
author: oanapl
ms.topic: conceptual
ms.date: 2/28/2018
ms.author: oanapl
ms.openlocfilehash: d00f740085b15bdb5fe698a069d97f168507f31f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75451582"
---
# <a name="add-custom-service-fabric-health-reports"></a>Egyéni Service Fabric-állapotjelentések hozzáadása
Az Azure Service Fabric egy [állapotmodellt](service-fabric-health-introduction.md) vezet be, amelynek célja a nem megfelelő állapotú fürt és alkalmazás feltételek megjelölése adott entitásokon. Az egészségügyi modell **egészségügyi riporterek** (rendszer-összetevők és figyelők) használ. A cél az egyszerű és gyors diagnózis és javítás. Szolgáltatás írók kell gondolni előre az egészségre. Minden olyan feltételt, amely hatással lehet az egészségre kell jelenteni, különösen akkor, ha segíthet a root közelében felmerülő problémák megjelölése. Az állapotinformációk időt és energiát takaríthatnak meg a hibakereséssel és a vizsgálatsal. A hasznosság különösen egyértelmű, ha a szolgáltatás a felhőben (magán- vagy Azure-ban) nagy méretekben működik.

A Service Fabric-riporterek figyelik az azonosított érdeklődési feltételeket. Ezekről a feltételekről a helyi nézetük alapján számolnak be. Az [állapottároló](service-fabric-health-introduction.md#health-store) összesíti az összes adatkezelő által küldött állapotadatokat annak megállapítására, hogy az entitások globálisan kifogástalanok-e. A modell célja, hogy gazdag, rugalmas, és könnyen használható. Az állapotjelentések minősége határozza meg a fürt állapotnézetének pontosságát. A nem megfelelő állapotú problémákat helytelenül mutató hamis pozitív pozitív adatok negatívan befolyásolhatják a frissítéseket vagy más, állapotadatokat használó szolgáltatásokat. Ilyen szolgáltatások például a javítási szolgáltatások és a riasztási mechanizmusok. Ezért néhány gondolat van szükség, hogy a jelentések, hogy elfog feltételeket az érdeklődés a lehető legjobb módon.

Az állapotjelentések tervezéséhez és megvalósításához a figyelőknek és a rendszerösszetevőknek:

* Határozza meg az őket érdeklő feltételt, a figyelés módját, valamint a fürt vagy az alkalmazás működésére gyakorolt hatást. Ezen információk alapján döntse el az állapotjelentés tulajdonságát és állapotát.
* Határozza meg azt az [entitást,](service-fabric-health-introduction.md#health-entities-and-hierarchy) amelyre a jelentés vonatkozik.
* Határozza meg, hogy hol történik a jelentés, a szolgáltatáson belül, vagy egy belső vagy külső figyelő.
* Adjon meg egy forrást, amely az újságíró azonosítására szolgál.
* Válasszon jelentési stratégiát rendszeres időközönként vagy átmenetek. Az ajánlott módszer rendszeres időközönként, mivel egyszerűbb kódot igényel, és kevésbé hajlamos a hibákra.
* Határozza meg, hogy a nem megfelelő állapotok jelentésének mennyi ideig kell az egészségtárolóban maradnia, és hogyan kell törölni. Ezen információk alapján határozza meg, hogy a jelentés mikor él, és távolítsa el a lejárati viselkedést.

Mint említettük, a jelentés a következő képpen végezhető el:

* A figyelt Service Fabric szolgáltatás replika.
* Belső figyelők egy Service Fabric-szolgáltatás (például egy Service Fabric állapotmentes szolgáltatás, amely figyeli a feltételeket és jelentéseket a központi üzembe helyezés. A figyelők telepíthetők egy összes csomópont, vagy affinitized a figyelt szolgáltatás.
* Belső figyelők, amelyek a Service Fabric-csomópontokon futnak, de *nem* implementálva Service Fabric-szolgáltatásokként.
* Külső figyelők, amelyek az erőforrást a Service Fabric-fürtön *kívülről* (például a figyelési szolgáltatás, például a Gomez) mintavételezi.

> [!NOTE]
> A rendszerösszetevők által küldött állapotjelentések a dobozból kitöltődnek. További [információ: A rendszerállapot-jelentések használata hibaelhárításhoz.](service-fabric-understand-and-troubleshoot-with-system-health-reports.md) A felhasználói jelentéseket olyan [állapotentitásokon](service-fabric-health-introduction.md#health-entities-and-hierarchy) kell elküldeni, amelyeket a rendszer már létrehozott.
> 
> 

Miután az állapotjelentés kialakítása egyértelmű, az állapotjelentések könnyen elküldhetők. A [FabricClient](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient) segítségével jelentheti az állapotot, ha a fürt nem [biztonságos,](service-fabric-cluster-security.md) vagy ha a hálóügyfél rendszergazdai jogosultságokkal rendelkezik. A jelentéskészítés az API-n keresztül végezhető el a [FabricClient.HealthManager.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth), a PowerShellen keresztül vagy a REST használatával. Konfigurációs gombok kötegjelentések a jobb teljesítmény érdekében.

> [!NOTE]
> A jelentés állapota szinkron, és csak az ügyféloldalon végzett érvényesítési munkát jelöli. Az a tény, hogy a jelentést `Partition` az `CodePackageActivationContext` állapotügyfél vagy a vagy objektumok elfogadják, nem jelenti azt, hogy a tárolóban van alkalmazva. A rendszer aszinkron módon küldi el, és valószínűleg más jelentésekkel együtt kötegeli. A kiszolgálón történő feldolgozás továbbra is sikertelen lehet: a sorszám elavult lehet, a jelentést alkalmazó entitás törlődik stb.
> 
> 

## <a name="health-client"></a>Állapot ügyfél
Az állapotjelentések et az állapotkezelő egy állapotügyfélen keresztül küldi el, amely a hálóügyfélben él. Az állapotkezelő menti a jelentéseket az egészségügyi tárolóban. Az állapotügyfél a következő beállításokkal konfigurálható:

* **HealthReportSendInterval**: A jelentés ügyfélhez való hozzáadása és az állapotkezelőnek való elküldése közötti késleltetés. A jelentések egyetlen üzenetbe kötegelésére szolgál, nem pedig egy üzenet küldésére minden egyes jelentéshez. A kötegelés javítja a teljesítményt. Alapértelmezett: 30 másodperc.
* **HealthReportRetrySendInterval**: Az az időszak, amikor az állapotügyfél újraküldi a felhalmozott állapotjelentéseket az állapotkezelőnek. Alapértelmezett: 30 másodperc, minimum: 1 másodperc.
* **HealthOperationTimeout**: Az állapotkezelőnek küldött jelentésüzenet időeltelési időszaka. Ha egy üzenet időtúljár, az állapotügyfél újrapróbálkozik, amíg az állapotkezelő nem erősíti meg, hogy a jelentés feldolgozása történt. Alapértelmezett: két perc.

> [!NOTE]
> A jelentések kötegelt, a háló ügyfél életben kell tartani legalább a HealthReportSendInterval annak érdekében, hogy azok küldése. Ha az üzenet elvész, vagy az állapotkezelő nem tudja alkalmazni őket átmeneti hibák miatt, a hálóügyfelet hosszabb ideig életben kell tartani, hogy újra próbálkozhasson.
> 
> 

Az ügyfél pufferelése figyelembe veszi a jelentések egyediségét. Ha például egy adott hibás jelentéskészítő másodpercenként 100 jelentést jelent ugyanazon entitás ugyanazon tulajdonságán, a jelentések helyére az utolsó verzió kerül. Legtöbb ilyen jelentés létezik az ügyfélvárólistában. Ha a kötegelés konfigurálva van, a health Managernek küldött jelentések száma csak egy küldési időköz. Ez a jelentés az utolsó hozzáadott jelentés, amely a gazdálkodó egység legfrissebb állapotát tükrözi.
Adja meg a `FabricClient` konfigurációs paramétereket, ha a [FabricClientSettings](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclientsettings) a kívánt értékeket adja át az állapottal kapcsolatos bejegyzésekhez.

A következő példa létrehoz egy hálókli-ügyfelet, és megadja, hogy a jelentéseket a hozzáadáskor kell elküldeni. Az újrapróbálkozások és a hibák 40 másodpercenként történnek.

```csharp
var clientSettings = new FabricClientSettings()
{
    HealthOperationTimeout = TimeSpan.FromSeconds(120),
    HealthReportSendInterval = TimeSpan.FromSeconds(0),
    HealthReportRetrySendInterval = TimeSpan.FromSeconds(40),
};
var fabricClient = new FabricClient(clientSettings);
```

Azt javasoljuk, hogy tartsa meg `HealthReportSendInterval` az alapértelmezett háló ügyfél beállításait, amely 30 másodpercre van állítva. Ez a beállítás optimális teljesítményt biztosít a kötegelésnek köszönhetően. A kritikus jelentéseket, amelyeket a `HealthReportSendOptions` lehető `true` leghamarabb el kell küldeni, használja az Immediate a [FabricClient.HealthClient.ReportHealth](https://docs.microsoft.com/dotnet/api/system.fabric.fabricclient.healthclient.reporthealth) API-val. Az azonnali jelentések megkerülik a kötegelési időközt. Használja ezt a zászlót óvatosan; azt akarjuk, hogy kihasználják az egészségügyi ügyfél kötegelés, amikor csak lehetséges. Azonnali küldés akkor is hasznos, ha a háló ügyfél bezárása (például a folyamat megállapította, érvénytelen állapot, és le kell állítani a mellékhatások megelőzése érdekében). Ez biztosítja a legjobb erőfeszítést küld a felhalmozott jelentések. Ha egy jelentést azonnali jelzővel ad hozzá, az állapotügyfél az utolsó küldés óta összesen halmozott jelentést kötegeli.

Ugyanazok a paraméterek adhatók meg, ha a fürthöz a PowerShellen keresztül jön létre kapcsolat. A következő példa kapcsolatot indít egy helyi fürttel:

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

Az API-hoz hasonlóan a `-Immediate` `HealthReportSendInterval` jelentések is elküldhetők a kapcsoló használatával, amelyet az értéktől függetlenül azonnal el lehet küldeni.

A REST a jelentéseket a Service Fabric átjáró, amely egy belső háló kliens. Alapértelmezés szerint ez az ügyfél úgy van beállítva, hogy 30 másodpercenként kötegelt jelentéseket küldjön. A kötegelt időközt a `HttpGatewayHealthReportSendInterval` `HttpGateway`fürt konfigurációs beállításával módosíthatja a programon. Mint említettük, egy jobb lehetőség `Immediate` az, hogy küldje el a jelentéseket igaz. 

> [!NOTE]
> Annak érdekében, hogy a jogosulatlan szolgáltatások ne tudjanak állapotot jelenteni a fürt entitásai ellen, konfigurálja úgy a kiszolgálót, hogy csak a védett ügyfelektől érkező kéréseket fogadjon. A `FabricClient` jelentéskészítéshez használt rendszernek engedélyeznie kell a fürttel való kommunikációhoz szükséges biztonságot (például Kerberos vagy tanúsítványhitelesítés sel). További információ a [fürtbiztonságról](service-fabric-cluster-security.md).
> 
> 

## <a name="report-from-within-low-privilege-services"></a>Jelentés alacsony jogosultsági szintű szolgáltatásokból
Ha a Service Fabric-szolgáltatások nem rendelkeznek rendszergazdai hozzáféréssel a fürthöz, `CodePackageActivationContext`az aktuális környezetből jelentheti az entitások állapotát a vagyon keresztül. `Partition`

* Állapotnélküli szolgáltatások esetén használja [az IStatelessServicePartition.ReportInstanceHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatelessservicepartition.reportinstancehealth) szolgáltatást az aktuális szolgáltatáspéldány jelentéséhez.
* Állapotalapú szolgáltatások esetén az [IStatefulServicePartition.ReportReplicaHealth](https://docs.microsoft.com/dotnet/api/system.fabric.istatefulservicepartition.reportreplicahealth) használatával jelentsen az aktuális replika.
* Az [IServicePartition.ReportPartitionHealth használatával](https://docs.microsoft.com/dotnet/api/system.fabric.iservicepartition.reportpartitionhealth) jelentsen az aktuális partícióentitásról.
* Használja [codePackageActivationContext.ReportApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportapplicationhealth) az aktuális alkalmazás jelentéséhez.
* Használja [a CodePackageActivationContext.ReportDeployedApplicationHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedapplicationhealth) használatával jelentést készít az aktuális csomóponton telepített aktuális alkalmazásról.
* Használja [codePackageActivationContext.ReportDeployedServicePackageHealth](https://docs.microsoft.com/dotnet/api/system.fabric.codepackageactivationcontext.reportdeployedservicepackagehealth) jelentés egy szolgáltatási csomagot az alkalmazás telepített az aktuális csomóponton.

> [!NOTE]
> Belsőleg az `Partition` alapértelmezett `CodePackageActivationContext` beállításokkal konfigurált állapotügyfél és a hold. Az [állapotfigyelő ügyfél](service-fabric-report-health.md#health-client)magyarázata szerint a jelentések kötegelésre kerülnek, és időzítőre kerülnek. A tárgyakat életben kell tartani, hogy esélye legyen elküldeni a jelentést.
> 
> 

Megadhatja, `HealthReportSendOptions` hogy mikor `Partition` `CodePackageActivationContext` küld jelentéseket és egészségügyi API-kat. Ha kritikus jelentéseket kap, amelyeket a `HealthReportSendOptions` lehető `true`leghamarabb el kell küldeni, használja az Immediate ( azonnali) lehetőséget. Azonnali jelentések megkerülik a kötegelési időköz a belső állapotügyfél. Mint már említettük, használja ezt a zászlót óvatosan; azt akarjuk, hogy kihasználják az egészségügyi ügyfél kötegelés, amikor csak lehetséges.

## <a name="design-health-reporting"></a>Állapotjelentés tervezése
A kiváló minőségű jelentések létrehozásának első lépése a szolgáltatás állapotát befolyásoló feltételek azonosítása. Minden olyan feltétel, amely segíthet a szolgáltatás vagy a fürt problémáinak megjelölésében, amikor elindul - vagy még jobb, mielőtt a probléma megtörténik - potenciálisan dollármilliárdokat takaríthat meg. Az előnyök közé tartozik a kevesebb leállási idő, kevesebb éjszakai órákban töltött problémák és javítási kérdések, és a nagyobb ügyfél-elégedettség.

Egyszer a feltételek van identified, házőrző író szükség -hoz kiszámít a legjobb út -hoz idegen rádióadást figyel őket részére egyensúly között felső és hasznosság. Vegyünk például egy olyan szolgáltatást, amely összetett számításokat végez, amelyek ideiglenes fájlokat használnak egy megosztáson. A figyelő figyelheti a megosztást annak érdekében, hogy elegendő hely áll-e rendelkezésre. Ez tudna hallgatni részére hirdetések -ból reszelő vagy címtár megváltozik. Figyelmeztetést jelenthet, ha elér egy előzetes küszöbértéket, és hibát jelezhet, ha a megosztás megtelt. Figyelmeztetés esetén a javítási rendszer megkezdheti a régebbi fájlok tisztítását a megosztáson. Hiba esetén a javítási rendszer áthelyezheti a szolgáltatás replikáját egy másik csomópontra. Figyelje meg, hogy a feltétel állapota hogyan van leírva az állapot: az állapot állapota, amely kifogástalannak (ok) vagy nem kifogástalannak (figyelmeztetés vagy hiba) tekinthető.

Egyszer a figyelés részlet van készlet, egy házőrző író szükséges -hoz kiszámít hogyan -hoz eszköz a házőrző. Ha a feltételek a szolgáltatáson belül határozhatók meg, a figyelő maga is a figyelt szolgáltatás része lehet. A szolgáltatáskód például ellenőrizheti a megosztás használatát, majd minden alkalommal jelentést tehet, amikor fájlt próbál írni. Ennek a megközelítésnek az az előnye, hogy a jelentéskészítés egyszerű. Ügyelni kell arra, hogy a watchdog hibák ne befolyásolják a szolgáltatás működését.

A figyelt szolgáltatáson belüli jelentéskészítés nem mindig lehetséges. Előfordulhat, hogy a szolgáltatáson belüli figyelő nem képes észlelni a feltételeket. Lehet, hogy nem rendelkezik a meghatározáshoz szükséges logikával vagy adatokkal. A körülmények figyelemmel kísérése magas lehet. Előfordulhat, hogy a feltételek nem egy szolgáltatásra jellemzőek, hanem a szolgáltatások közötti interakciókra is hatással vannak. Egy másik lehetőség az, hogy a figyelők a fürtben külön folyamatokként. A házőrzők figyelemmel kísérik a feltételeket, és jelentést tesznek, anélkül, hogy bármilyen módon befolyásolnák a fő szolgáltatásokat. Például ezek a figyelők lehet megvalósítani állapotnélküli szolgáltatások ugyanabban az alkalmazásban, telepített minden csomóponton, vagy ugyanazon a csomóponton, mint a szolgáltatás.

Néha a fürtben futó figyelő sem választható. Ha a figyelt feltétel a szolgáltatás rendelkezésre állása vagy funkcionalitása, ahogy a felhasználók látják, a legjobb, ha a figyelők ugyanazon a helyen vannak, mint a felhasználói ügyfelek. Ott ugyanúgy tesztelhetik a műveleteket, ahogy a felhasználók nevezik őket. Például rendelkezhet egy figyelő, amely a fürtön kívül él, a szolgáltatás kéréseket ad ki, és ellenőrzi az eredmény késését és helyességét. (Egy számológép szolgáltatás, például, nem 2 +2 vissza 4 ésszerű időn kívül?)

Miután a figyelő adatait véglegesítették, el kell döntenie, hogy a forrásazonosító, amely egyedileg azonosítja azt. Ha több, azonos típusú figyelők élnek a fürtben, különböző entitásokon kell jelentést tenniük, vagy ha ugyanazon az entitáson jelentenek, különböző forrásazonosítót vagy tulajdonságot kell használniuk. Így a jelentéseik egymás mellett létezhetnek. Az állapotjelentés tulajdonságának rögzítenie kell a figyelt állapotot. (A fenti példában a tulajdonság lehet **ShareSize**.) Ha több jelentés vonatkozik ugyanarra a feltételre, a tulajdonságnak tartalmaznia kell néhány dinamikus információt, amely lehetővé teszi a jelentések egymás mellett élését. Ha például több megosztást kell figyelni, a tulajdonság neve lehet **ShareSize-sharename**.

> [!NOTE]
> *Ne* használja a health store állapotinformációk megőrzéséhez. Csak az egészséggel kapcsolatos információkat kell egészségre vonatkozóan jelenteni, mivel ez az információ hatással van a gazdálkodó egység állapotértékelésére. Az egészségboltot nem általános célú tárolóként tervezték. Az állapotértékelési logikát használja az összes adat összesítéséhez az állapotba. Az állapothoz nem kapcsolódó információk (például az OK állapotú jelentéskészítési állapot) küldése nem befolyásolja az összesített állapotot, de negatívan befolyásolhatja az állapottároló teljesítményét.
> 
> 

A következő döntési pont az, hogy melyik entitásról kell jelentést tenni. Az idő nagy részében a feltétel egyértelműen azonosítja az entitást. Válassza ki a lehető legjobb részletességű entitást. Ha egy feltétel hatással van a partíció összes replikájára, a partíción jelentsen, ne a szolgáltatáson. Vannak olyan sarok esetek, ahol több gondolatra van szükség, mégis. Ha a feltétel hatással van egy entitás, például egy replika, de a vágy az, hogy a feltétel megjelölve több, mint a replika élettartama, majd jelenteni kell a partíción. Ellenkező esetben a replika törlésekor a health Store törli az összes jelentést. Watchdog írók kell gondolni az élettartama a szervezet és a jelentés. Egyértelműnek kell lennie, hogy mikor kell törölni egy jelentést egy üzletből (például ha egy entitáson jelentett hiba már nem érvényes).

Nézzünk egy példát, amely összeállítja az általam leírt pontokat. Fontolja meg egy Service Fabric-alkalmazást, amely egy fő állapotalapú állandó szolgáltatásból és másodlagos állapotnélküli szolgáltatásokból áll, amelyek minden csomóponton üzembe helyezhetők (minden feladattípushoz egy másodlagos szolgáltatástípus). A főkiszolgáló nak van egy feldolgozási várólistája, amely a másodlagos akna által végrehajtandó parancsokat tartalmazza. A másodlagos fájlok végrehajtják a bejövő kérelmeket, és visszaküldik a nyugtázási jeleket. Az egyik feltétel, amely figyelhető a teljes feldolgozási várólista hossza. Ha a fő várólista hossza eléri a küszöbértéket, a rendszer figyelmeztetést jelent. A figyelmeztetés azt jelzi, hogy a másodlagos nem tudja kezelni a terhelést. Ha a várólista eléri a maximális hosszt, és a parancsok eldobásra kerülnek, a rendszer hibát jelez, mivel a szolgáltatás nem tudja helyreállítani. A jelentések a **QueueStatus**tulajdonságon lehetnek. A figyelő a szolgáltatáson belül él, és rendszeres időközönként elküldi a fő elsődleges replikára. Az élet ideje két perc, és 30 másodpercenként rendszeresen elküldjük. Ha az elsődleges leáll, a jelentés automatikusan törlődik a boltból. Ha a szolgáltatás replika működik, de holtpontra jutott, vagy más problémák, a jelentés lejár a health store. Ebben az esetben az entitás kiértékelése hiba.

Egy másik feltétel, amely nyomon követhető a feladat végrehajtási ideje. A főkiszolgáló a feladatokat a feladattípus alapján osztja el a másodlagos akta számára. A tervtől függően a főkiszolgáló letudta a feladat állapotának másodlagos lekérdezését. Azt is megvárhatja, hogy a másodlagos adandó jeleket küldjön vissza, amikor elvégezték. A második esetben ügyelni kell arra, hogy észleljék azokat a helyzeteket, amikor a másodlagos aktorok elpusztulnak vagy az üzenetek elvesznek. Az egyik lehetőség az, hogy a főkiszolgáló pingkérelmet küld ugyanannak a másodlagosnak, amely visszaküldi az állapotát. Ha nem érkezik meg állapot, a főkiszolgáló hibának tekinti, és átütemezi a feladatot. Ez a viselkedés feltételezi, hogy a feladatok idempotens.

A figyelt feltétel lefordítható figyelmeztetésként, ha a feladat nem történik meg egy bizonyos idő alatt (**t1**, például 10 perc). Ha a feladat nem fejeződik be időben **(t2**, például 20 perc), a figyelt feltétel hibaként lefordítható. Ez a jelentés többféleképpen is elvégezhető:

* A fő elsődleges replika rendszeresidőközönként jelentést tesz magáról. A várólistában lévő összes függőben lévő feladathoz egy tulajdonság ot használhat. Ha legalább egy feladat hosszabb időt vesz igénybe, a **PendingTasks** tulajdonság jelentésállapota figyelmeztetés vagy hiba. Ha nincsenek függőben lévő feladatok, vagy az összes feladat végrehajtása megkezdődött, a jelentés állapota rendben van. A feladatok állandóak. Ha az elsődleges leáll, az újonnan előléptetett elsődleges továbbra is megfelelően jelentést.
* Egy másik figyelő folyamat (a felhőben vagy külső) ellenőrzi a feladatokat (kívülről, a kívánt feladat eredménye alapján), hogy lássa, hogy befejeződtek-e. Ha nem tartják be a küszöbértékeket, a rendszer jelentést küld a főszolgáltatásról. A rendszer jelentést küld minden olyan feladatról, amely tartalmazza a feladatazonosítót, például a **PendingTask+taskId**. A jelentéseket csak a nem megfelelő állapotok esetén szabad elküldeni. Állítsa be az élő időt néhány percre, és jelölje meg a jelentéseket, amelyeket el kell távolítani, amikor lejárnak, hogy biztosítsa a karbantartást.
* A másodlagos, amely végrehajtja a feladat jelentések, ha a vártnál tovább tart futtatni. A PendingTasks tulajdonság szolgáltatáspéldányáról **számol be.** A jelentés rámutat a szolgáltatáspéldány, amely problémákat, de nem rögzíti a helyzetet, ahol a példány meghal. Akkor a jelentéseket kitakarították. Jelentheti a másodlagos szolgáltatást. Ha a másodlagos befejezi a feladatot, a másodlagos példány törli a jelentést az üzletből. A jelentés nem rögzíti azt a helyzetet, amikor a nyugtázási üzenet elvész, és a feladat nem fejeződött be a mester szempontjából.

A jelentés készítése azonban a fent leírt esetekben történik, a jelentések az alkalmazások állapotának értékelése kor kerülnek rögzítésre az alkalmazások állapotában.

## <a name="report-periodically-vs-on-transition"></a>Jelentés rendszeres időközönként és az átmenetről
Az állapotjelentési modell használatával a figyelők rendszeres időközönként vagy átmenetek jelentéseket küldhetnek. A figyelőjelentések ajánlott módja rendszeres időközönként, mert a kód sokkal egyszerűbb és kevésbé hajlamos a hibákra. A házőrzőknek arra kell törekedniük, hogy a lehető legegyszerűbbek legyenek, hogy elkerüljék a hibás jelentéseket kiváltó hibákat. A nem *megfelelő állapotú* jelentések az állapotértékelésekre és az állapoton alapuló forgatókönyvekre, például a frissítésekre is hatással vannak. A helytelen *kifogástalan állapotú* jelentések elrejtik a fürtben lévő problémákat, amelyek nem kívánatosak.

Az időszakos jelentéskészítéshez a figyelő időzítővel valósítható meg. Időzítő visszahívása, a figyelő ellenőrizheti az állapotot, és az aktuális állapot alapján jelentést küldhet. Nincs szükség arra, hogy megnézze, melyik jelentést korábban küldték el, vagy hogy bármilyen optimalizálást eszközöltél az üzenetküldés szempontjából. Az állapotügyfél kötegelési logikával rendelkezik a teljesítmény elősegítésére. Míg az állapotügyfél életben marad, addig vizsgálja újra, amíg a jelentést az állapottároló vagy a figyelő nem hoz létre egy újabb jelentést ugyanazzal az entitással, tulajdonsággal és forrással.

Az átmenetek jelentéséhez az állapot gondos kezelésére van szükség. A figyelő figyel bizonyos feltételeket, és csak akkor jelenti, ha a feltételek megváltoznak. Ennek a megközelítésnek az előnye, hogy kevesebb jelentésre van szükség. A hátránya az, hogy a logikája a házőrző összetett. A figyelőnek fenn kell tartania a feltételeket vagy a jelentéseket, hogy azok megvizsgálhatók legyenek az állapotváltozások meghatározásához. Feladatátvételkor ügyelni kell a hozzáadott jelentésekkel, de még nem küldve az egészségügyi tárolóba. A sorszámnak folyamatosan növeksznie kell. Ha nem, a jelentések et elavultként utasítja el a rendszer. Azokban a ritka esetekben, amikor adatvesztés merül fel, szinkronizálásra lehet szükség az adat-előde állapota és az állapottároló állapota között.

Az átmenetek jelentésének van értelme a `Partition` `CodePackageActivationContext`saját magukról beszámoló szolgáltatások számára, keresztül vagy . Amikor a helyi objektum (replika vagy telepített szolgáltatáscsomag / telepített alkalmazás) eltávolítása, az összes jelentést is eltávolítja. Ez az automatikus karbantartás ellazítja a riporter és az egészségügyi áruház közötti szinkronizálás szükségességét. Ha a jelentés a szülő partíció vagy szülő alkalmazás, ügyelni kell a feladatátvétel, hogy elkerüljék az elavult jelentések a health Store.If the report is for parent partition or parent application, care must be taken on failover to avoid elavult reports in the health store. A megfelelő állapot fenntartásához és a jelentés tárolóból való törléséhez logikát kell hozzáadni, ha már nincs rá szükség.

## <a name="implement-health-reporting"></a>Állapotjelentés végrehajtása
Miután az entitás és a jelentés részletei egyértelműek, az állapotjelentések küldése az API-n, a PowerShellen vagy a REST-en keresztül végezhető el.

### <a name="api"></a>API
Az API-n keresztüli jelentéshez létre kell hoznia egy állapotjelentést, amely az entitástípusra vonatkozik, amelyről jelentést kíván jelenteni. Adja át a jelentést egy állapotügyfélnek. Másik lehetőségként hozzon létre egy állapotinformációt, `Partition` `CodePackageActivationContext` és adja át a helyes jelentési módszerek, vagy jelentést az aktuális entitások.

A következő példa a fürtön belüli figyelőrendszeres jelentéskészítést mutatja be. A figyelő ellenőrzi, hogy egy külső erőforrás érhető-e el egy csomóponton belül. Az erőforrásra az alkalmazáson belüli szolgáltatásjegyzék szükséges. Ha az erőforrás nem érhető el, az alkalmazáson belüli többi szolgáltatás továbbra is megfelelően működhet. Ezért a jelentés 30 másodpercenként kerül elküldésre az üzembe helyezett szolgáltatáscsomag-entitáson.

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
Állapotjelentések küldése a **Send-ServiceFabric*EntityType*HealthReport**segítségével.

A következő példa a csomópont CPU-értékeinek rendszeres jelentését mutatja be. A jelentéseket 30 másodpercenként kell elküldeni, és két percük van hátra. Ha lejárnak, a jelentéstelő problémákat, így a csomópont kiértékelése hiba. Ha a processzor egy küszöbérték felett van, a jelentés figyelmeztetési állapottal rendelkezik. Ha a processzor a beállított időnél nagyobb küszöbérték felett marad, a rendszer hibaként jelenti. Ellenkező esetben a riporter az OK állapotot küldi.

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

A következő példa egy átmeneti figyelmeztetést jelent egy kópián. Először megkapja a partíció azonosítóját, majd a replika-azonosítót az érdeklődési alatt lévő szolgáltatáshoz. Ezután jelentést küld a **PowershellWatcher-től** a **ResourceDependency**tulajdonságról. A jelentés csak két percig érdekes, és automatikusan törlődik az üzletből.

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
Állapotjelentések küldése rest használatával POST-kérelmek, amelyek a kívánt entitáshoz, és a szervezetben az állapotjelentés leírása. Megtudhatja például, hogyan küldhet [REST-fürt állapotjelentéseket](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-cluster) vagy [szolgáltatásállapot-jelentéseket.](https://docs.microsoft.com/rest/api/servicefabric/report-the-health-of-a-service) Minden entitás támogatott.

## <a name="next-steps"></a>További lépések
Az állapotadatok alapján a szolgáltatásírók és a fürt/alkalmazás rendszergazdái kigondolhatják, hogyan lehet felhasználni az adatokat. Például az állapotállapot alapján riasztásokat állíthatnak be, hogy súlyos problémákat kapjanak, mielőtt kimaradásokat váltanának ki. A rendszergazdák a javítási rendszereket is beállíthatják a problémák automatikus megoldásához.

[Bevezetés a Service Fabric állapotfigyelésébe](service-fabric-health-introduction.md)

[A Service Fabric állapotjelentésének megtekintése](service-fabric-view-entities-aggregated-health.md)

[A szolgáltatás állapotának jelentése és ellenőrzése](service-fabric-diagnostics-how-to-report-and-check-service-health.md)

[Rendszerállapot-jelentések használata hibaelhárításhoz](service-fabric-understand-and-troubleshoot-with-system-health-reports.md)

[A szolgáltatások helyi figyelése és diagnosztikája](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

[A Service Fabric alkalmazásfrissítése](service-fabric-application-upgrade.md)

