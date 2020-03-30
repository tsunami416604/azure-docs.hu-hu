---
title: Fürterőforrás-kezelő – felügyeleti integráció
description: A fürterőforrás-kezelő és a Service Fabric Management közötti integrációs pontok áttekintése.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 50751c7d23797a597dc5e2d209c1e3eecf6f7a40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79258745"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Fürterőforrás-kezelő integrációja a Service Fabric fürtkezelésével
A Service Fabric fürterőforrás-kezelő nem hajt frissítéseket a Service Fabric, de részt vesz. A fürterőforrás-kezelő első alkalommal segíti a felügyeletet a fürt és a benne lévő szolgáltatások kívánt állapotának nyomon követésével. A fürterőforrás-kezelő állapotjelentéseket küld, ha nem tudja a fürtöt a kívánt konfigurációba helyezni. Ha például nincs elegendő kapacitás, a fürterőforrás-kezelő állapotfigyelmeztetéseket és a problémát jelző hibákat küld. Egy másik integrációs elem a frissítések működéséhez kapcsolódik. A fürterőforrás-kezelő némileg módosítja a működését a frissítések során.  

## <a name="health-integration"></a>Egészségügyi integráció
A fürterőforrás-kezelő folyamatosan nyomon követi a szolgáltatások elhelyezéséhez megadott szabályokat. Azt is nyomon követi a fennmaradó kapacitást az egyes metrikák a csomópontokon és a fürtben és a fürt egészében. Ha nem felel meg ezeknek a szabályoknak, vagy ha nincs elegendő kapacitás, állapotfigyelmeztetések és hibák kerülnek kibocsátásra. Ha például egy csomópont kapacitásfeletti, és a fürterőforrás-kezelő szolgáltatások áthelyezésével próbálja meg kijavítani a helyzetet. Ha nem tudja kijavítani a helyzetet, akkor egy állapotjelzőt bocsát ki, amely jelzi, hogy melyik csomópont kapacitás feletti, és mely metrikákhoz.

Az Erőforrás-kezelő állapotra vonatkozó figyelmeztetéseinek egy másik példája az elhelyezési korlátozások megsértése. Ha például megadott egy elhelyezési megkötést `“NodeColor == Blue”`(például ) , és az Erőforrás-kezelő észleli a megkötés megsértését, állapotfigyelmeztetést ad ki. Ez igaz az egyéni megkötésekre és az alapértelmezett megkötésekre (például a tartalék tartomány ra és a tartomány frissítési tartományra vonatkozó megkötésekre).

Íme egy példa egy ilyen egészségügyi jelentésre. Ebben az esetben az állapotjelentés a rendszerszolgáltatás egyik partíciójára szolgál. Az állapotüzenet azt jelzi, hogy a partíció replikái ideiglenesen túl kevés frissítési tartományba vannak csomagolva.

```posh
PS C:\Users\User > Get-ServiceFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


PartitionId           : 00000000-0000-0000-0000-000000000001
AggregatedHealthState : Warning
UnhealthyEvaluations  :
                        Unhealthy event: SourceId='System.PLB', Property='ReplicaConstraintViolation_UpgradeDomain', HealthState='Warning', ConsiderWarningAsError=false.

ReplicaHealthStates   :
                        ReplicaId             : 130766528804733380
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577821
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528854889931
                        AggregatedHealthState : Ok

                        ReplicaId             : 130766528804577822
                        AggregatedHealthState : Ok

                        ReplicaId             : 130837073190680024
                        AggregatedHealthState : Ok

HealthEvents          :
                        SourceId              : System.PLB
                        Property              : ReplicaConstraintViolation_UpgradeDomain
                        HealthState           : Warning
                        SequenceNumber        : 130837100116930204
                        SentAt                : 8/10/2015 7:53:31 PM
                        ReceivedAt            : 8/10/2015 7:53:33 PM
                        TTL                   : 00:01:05
                        Description           : The Load Balancer has detected a Constraint Violation for this Replica: fabric:/System/FailoverManagerService Secondary Partition 00000000-0000-0000-0000-000000000001 is
                        violating the Constraint: UpgradeDomain Details: UpgradeDomain ID -- 4, Replica on NodeName -- Node.8 Currently Upgrading -- false Distribution Policy -- Packing
                        RemoveWhenExpired     : True
                        IsExpired             : False
                        Transitions           : Ok->Warning = 8/10/2015 7:13:02 PM, LastError = 1/1/0001 12:00:00 AM
```

Itt van, amit ez az egészségügyi üzenet azt mondja nekünk:

1. Minden a replikák maguk egészséges: Mindegyik AggregatedHealthState : Ok
2. A frissítési tartomány terjesztési megkötése jelenleg sérül. Ez azt jelenti, hogy egy adott frissítési tartomány több replikával rendelkezik erről a partícióról, mint kellene.
3. Melyik csomópont tartalmazza a replika okozza a jogsértést. Ebben az esetben ez a csomópont a neve "Node.8"
4. Azt jelzi, hogy éppen folyamatban van-e frissítés ehhez a partícióhoz ("Jelenleg frissítés -- false")
5. A szolgáltatás terjesztési házirendje: "Terjesztési házirend -- Csomagolás". Erre az `RequireDomainDistribution` [elhelyezési szabályzat](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing)vonatkozik . A "Csomagolás" azt jelzi, hogy ebben az esetben a DomainDistribution _nem_ volt szükséges, így tudjuk, hogy az elhelyezési házirend nincs megadva ehhez a szolgáltatáshoz. 
6. Amikor a jelentés történt - 2015.08.10.

Az ilyen információk figyelmeztetik a tüzet a termelésben, hogy tudassa Önvel, hogy valami rosszul sült el, és a rossz frissítések észlelésére és megállítására is használják. Ebben az esetben szeretnénk megtudni, hogy ki tudjuk-e deríteni, hogy az Erőforrás-kezelőnek miért kellett a replikákat a frissítési tartományba csomagolnia. A csomagolás általában átmeneti, mert a többi frissítési tartomány csomópontjai például nem voltak megfelelőek.

Tegyük fel, hogy a fürterőforrás-kezelő megpróbál elhelyezni bizonyos szolgáltatásokat, de nincsenek olyan megoldások, amelyek működnek. Ha a szolgáltatások nem helyezhetők el, annak általában a következő okok valamelyike miatt történik:

1. Néhány átmeneti feltétel lehetetlenné tette a szolgáltatáspéldány vagy a replika helyes elhelyezését
2. A szolgáltatás elhelyezési követelményei kielégíthetetlenek.

Ezekben az esetekben a fürterőforrás-kezelő ből származó állapotjelentések segítenek meghatározni, hogy miért nem helyezhető el a szolgáltatás. Ezt a folyamatot kényszereltávolítási szekvencia-nak nevezzük. Ennek során a rendszer végigvezeti a szolgáltatásra ható konfigurált korlátozásokon, és rögzíti, hogy mit szüntetnek meg. Így, ha a szolgáltatások nem helyezhetők el, láthatja, hogy mely csomópontok at szüntették meg, és miért.

## <a name="constraint-types"></a>Kényszertípusok
Beszéljünk az egyes különböző korlátozások ezekben az állapotjelentésekben. Látni fogja az ezekhez a korlátozásokhoz kapcsolódó állapotüzeneteket, ha a replikák nem helyezhetők el.

* **ReplicaExclusionStatic** és **ReplicaExclusionDynamic**: Ezek a megkötések azt jelzik, hogy a megoldás elutasításra került, mert két szolgáltatásobjektum ot kell elhelyezni ugyanazon a partíción. Ez nem engedélyezett, mert a csomópont meghibásodása túlságosan hatással lenne a partícióra. ReplicaExclusionStatic és ReplicaExclusionDynamic szinte ugyanaz a szabály, és a különbségek nem igazán számít. Ha a ReplicaExclusionStatic vagy a ReplicaExclusionDynamic megkötést tartalmazó megkötési eltávolítási sorozatot lát, a fürterőforrás-kezelő úgy véli, hogy nincs elég csomópont. Ehhez még több megoldásra van szükség az érvénytelen elhelyezések használatához, amelyek nem engedélyezettek. A sorozat egyéb korlátai általában megmondják, hogy miért a csomópontok at likvidálódnak az első helyen.
* **Elhelyezésmegkötés:** Ha ezt az üzenetet látja, az azt jelenti, hogy megszüntettünk néhány csomópontot, mert azok nem feleltek meg a szolgáltatás elhelyezési korlátainak. Az üzenet részeként nyomon követjük a jelenleg konfigurált elhelyezési korlátozásokat. Ez akkor normális, ha elhelyezési megkötés van definiálva. Ha azonban az elhelyezési megkötés helytelenül okozza a túl sok csomópont megszüntetését, akkor ezt a figyelmébe választaná.
* **NodeCapacity**: Ez a megkötés azt jelenti, hogy a fürterőforrás-kezelő nem tudta elhelyezni a replikákat a jelzett csomópontokon, mert az kapacitás fölé helyezné őket.
* **Affinitás:** Ez a megkötés azt jelzi, hogy nem tudtuk elhelyezni a replika az érintett csomópontokon, mert ez az affinitási megkötés megsértését okozná. További információ az affinitásról ebben a [cikkben](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md) található
* **FaultDomain** és **UpgradeDomain**: Ez a megkötés kiküszöböli a csomópontokat, ha a replika a jelzett csomópontokon egy adott hiba vagy frissítési tartomány ban történő csomagolást okozna. A megkötést megvitató példák a [hiba- és frissítési tartománymegkötésekre és az ebből eredő viselkedésre](service-fabric-cluster-resource-manager-cluster-description.md) vonatkozó témakörben találhatók.
* **PreferredLocation**: Általában nem kell látni ezt a megkötés eltávolítja a csomópontokat a megoldásból, mivel alapértelmezés szerint optimalizálásként fut. Az előnyben részesített helymegkötés is jelen van a frissítések során. A frissítés során arra szolgál, hogy a szolgáltatásokat visszahelyezze oda, ahol a frissítés megkezdésekor voltak.

## <a name="blocklisting-nodes"></a>Blocklisting csomópontok
Egy másik állapotüzenet a fürterőforrás-kezelő jelentései szerint a csomópontok blokkolása esetén jelenik meg. A blocklisting ideiglenes megkötésként is felfogható, amely automatikusan jelentkezik az Ön számára. A csomópontok blokklistára kerülnek, amikor ismétlődő hibákat tapasztalnak az adott szolgáltatástípus példányainak indításakor. A csomópontok szolgáltatásonkénti alapon vannak felsorolva. Előfordulhat, hogy egy csomópont blokklistán szerepel az egyik szolgáltatástípushoz, de egy másikhoz nem. 

Látni fogja blocklisting kick-ban gyakran a fejlesztés során: néhány hiba okozza a szolgáltatás gazdagép összeomlik indításkor. A Service Fabric néhányszor megpróbálja létrehozni a szolgáltatásgazdat, és a hiba folyamatosan előfordul. Néhány kísérlet után a csomópont letiltja a blokklistát, és a fürterőforrás-kezelő megpróbálja máshol létrehozni a szolgáltatást. Ha ez a hiba több csomóponton is megtörténik, lehetséges, hogy a fürt összes érvényes csomópontja blokkolva lesz. Blocklisting is eltávolíthatja annyi csomópontot, hogy nem elég sikeresen indítsa el a szolgáltatást, hogy megfeleljen a kívánt skála. Általában további hibákat vagy figyelmeztetéseket fog látni a fürterőforrás-kezelőből, jelezve, hogy a szolgáltatás a kívánt replika- vagy példányszám alatt van, valamint az állapotüzenetek, amelyek jelzik, hogy mi a hiba, ami az első blokklistához vezet. Hely.

A blocklisting nem állandó feltétel. Néhány perc múlva a csomópont törlődik a tiltólistáról, és a Service Fabric újra aktiválhatja a szolgáltatásokat az adott csomóponton. Ha a szolgáltatások továbbra is sikertelenek, a csomópont ismét blokklistára kerül az adott szolgáltatástípushoz. 

### <a name="constraint-priorities"></a>Megszorítási prioritások

> [!WARNING]
> A megkötési prioritások módosítása nem ajánlott, és jelentős káros hatással lehet a fürtre. Az alábbi információk az alapértelmezett megkötési prioritásokra és azok viselkedésére vonatkoznak. 
>

Az összes ilyen korlátok, akkor lehet, hogy gondolt "Hé - Úgy gondolom, hogy a hiba domain korlátok a legfontosabb dolog az én-m rendszer. Annak érdekében, hogy a tartalék tartomány megkötése ne sérüljön, hajlandó vagyok más korlátozásokat megsérteni."

A kényszerek különböző prioritási szintekkel konfigurálhatók. Ezek a következők:

   - "kemény" (0)
   - "puha" (1)
   - "optimalizálás" (2)
   - "off" (-1). 
   
A legtöbb megkötés alapértelmezés szerint szigorú megkötésként van konfigurálva.

A kényszerek prioritásának módosítása nem gyakori. Voltak idők, amikor a megszorítás prioritások at kell változtatni, általában a munka körül néhány más hiba vagy viselkedés, amely hatással volt a környezetre. Általában a rugalmasság a megszorítás prioritási infrastruktúra nagyon jól működött, de nem szükséges gyakran. Az idő nagy részében minden ül az alapértelmezett prioritásokat. 

A prioritási szintek nem jelentik azt, hogy egy adott _megkötés_ sérül, és nem fog nak mindig teljesülni. A megszorítási prioritások a megkötések érvényesítésének sorrendjét határozzák meg. A prioritások határozzák meg a kompromisszumokat, ha lehetetlen minden korlátozást kielégíteni. Általában az összes korlátozás térhet el, kivéve, ha valami más történik a környezetben. Néhány példa a kényszermegsértésekhöz vezető esetekre az ütköző megkötések vagy az egyidejű hibák nagy száma.

Speciális helyzetekben módosíthatja a megkötési prioritásokat. Tegyük fel például, hogy biztosítani szeretné, hogy az affinitás mindig sérüljön, ha szükséges a csomópont kapacitásproblémáinak megoldásához. Ennek eléréséhez beállíthatja az affinitási megkötés prioritását "soft" (1) értékre, és a kapacitásmegkötést "kemény" értékre (0) állíthatja.

A különböző megkötések alapértelmezett prioritási értékeit a következő konfiguráció határozza meg:

ClusterManifest.xml fájl

```xml
        <Section Name="PlacementAndLoadBalancing">
            <Parameter Name="PlacementConstraintPriority" Value="0" />
            <Parameter Name="CapacityConstraintPriority" Value="0" />
            <Parameter Name="AffinityConstraintPriority" Value="0" />
            <Parameter Name="FaultDomainConstraintPriority" Value="0" />
            <Parameter Name="UpgradeDomainConstraintPriority" Value="1" />
            <Parameter Name="PreferredLocationConstraintPriority" Value="2" />
        </Section>
```

a ClusterConfig.json keresztül önálló telepítésekhez vagy template.json az Azure által üzemeltetett fürtökhöz:

```json
"fabricSettings": [
  {
    "name": "PlacementAndLoadBalancing",
    "parameters": [
      {
          "name": "PlacementConstraintPriority",
          "value": "0"
      },
      {
          "name": "CapacityConstraintPriority",
          "value": "0"
      },
      {
          "name": "AffinityConstraintPriority",
          "value": "0"
      },
      {
          "name": "FaultDomainConstraintPriority",
          "value": "0"
      },
      {
          "name": "UpgradeDomainConstraintPriority",
          "value": "1"
      },
      {
          "name": "PreferredLocationConstraintPriority",
          "value": "2"
      }
    ]
  }
]
```

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Tartaléktartomány- és frissítési tartománymegkötések
A fürterőforrás-kezelő azt szeretné, hogy a szolgáltatások a hiba és a frissítési tartományok között legyenek elosztva. Ezt a fürterőforrás-kezelő motorján belüli megkötésként modellezi. A használatukról és azok viselkedéséről a [fürtkonfigurációról](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior)szóló cikkben olvashat bővebben.

Előfordulhat, hogy a fürterőforrás-kezelőnek néhány replikát kell egy frissítési tartományba csomagolnia a frissítések, hibák vagy egyéb megszorítás-megsértések kezeléséhez. A hibás vagy frissítési tartományokba történő csomagolás általában csak akkor történik meg, ha a rendszerben több hiba vagy más lemorzsolódás van, ami megakadályozza a megfelelő elhelyezést. Ha még ezekben a helyzetekben is meg `RequireDomainDistribution` szeretné akadályozni a csomagolást, használhatja az [elhelyezési szabályzatot.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing) Vegye figyelembe, hogy ez hatással lehet a szolgáltatás rendelkezésre állására és megbízhatóságára, ezért alaposan fontolja meg.

Ha a környezet megfelelően van konfigurálva, az összes korlátozás tartjuk teljes mértékben, még a frissítések során. A legfontosabb dolog az, hogy a fürt erőforrás-kezelő figyeli a megkötések. Ha szabálysértést észlel, azonnal jelenti, és megpróbálja kijavítani a problémát.

## <a name="the-preferred-location-constraint"></a>Az előnyben részesített helymegkötés
A PreferredLocation megkötés egy kicsit más, mivel két különböző felhasználási. Ennek a korlátozásnak az egyik használata az alkalmazásfrissítések során. A fürterőforrás-kezelő a frissítések során automatikusan kezeli ezt a megkötést. Annak biztosítására szolgál, hogy a frissítések befejezéseesetén a replikák visszatérjenek a kezdeti helyükre. A PreferredLocation megkötés másik használata az [ `PreferredPrimaryDomain` elhelyezési házirendre történik.](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md) Mindkettő optimalizálás, és így a PreferredLocation megkötés az egyetlen megkötés, amely alapértelmezés szerint "Optimalizálás" értékre van állítva.

## <a name="upgrades"></a>Frissítések
A fürterőforrás-kezelő az alkalmazás- és fürtfrissítések során is segítséget nyújt, amelyek során két feladat közül lehet elsajátulni:

* annak biztosítása, hogy a fürt szabályai ne sérüljenek
* próbálja segíteni a frissítés simán megy

### <a name="keep-enforcing-the-rules"></a>A szabályok kényszerítésének megtartása
A legfontosabb dolog, hogy tudatában legyünk, hogy a szabályok - a szigorú korlátozások, mint az elhelyezési korlátok és kapacitások - még mindig érvényesíteni a frissítések során. Az elhelyezési korlátozások biztosítják, hogy a számítási feladatok csak ott fussanak, ahol engedélyezettek, még a frissítések során is. Ha a szolgáltatások nagymértékben korlátozottak, a frissítések hosszabb időt vehetnek igénybe. Ha a szolgáltatás vagy a csomópont fut le egy frissítést lehet, hogy kevés lehetőség, hogy hol mehet.

### <a name="smart-replacements"></a>Intelligens cserék
Amikor egy frissítés elindul, az erőforrás-kezelő pillanatképet készít a fürt aktuális elrendezéséről. Az egyes frissítési tartományok befejeződése után megpróbálja visszaküldeni az adott frissítési tartományban lévő szolgáltatásokat az eredeti elrendezésükre. Így legbőlegelhetően két átmenet van egy szolgáltatáshoz a frissítés során. Van egy lépés az érintett csomópontból, és egy lépés vissza. A fürt vagy szolgáltatás visszaküldése a frissítés előtti állapotba azt is biztosítja, hogy a frissítés ne befolyásolja a fürt elrendezését. 

### <a name="reduced-churn"></a>Csökkentett lemorzsolódás
A frissítés során egy másik dolog az, hogy a fürterőforrás-kezelő kikapcsolja a kiegyensúlyozást. A kiegyensúlyozás megakadályozása megakadályozza a frissítésre adott szükségtelen reakciókat, például a szolgáltatások áthelyezése a frissítéshez kiürített csomópontokba. Ha a kérdéses frissítés fürtfrissítés, akkor a teljes fürt nem kiegyensúlyozott a frissítés során. A megszorítás-ellenőrzések aktívak maradnak, csak a mérőszámok proaktív kiegyensúlyozásán alapuló mozgás le van tiltva.

### <a name="buffered-capacity--upgrade"></a>Pufferelt kapacitás & frissítés
Általában azt szeretné, hogy a frissítés akkor is befejeződjen, ha a fürt korlátozott vagy közel van a teljeshez. A fürt kapacitásának kezelése a szokásosnál is fontosabb a frissítések során. A frissítési tartományok számától függően a kapacitás 5 és 20 százaléka közötti kapacitást kell áttelepíteni, amikor a frissítés végighalad a fürtön. Ennek a munkának mennie kell valahová. Ez az, ahol a [pufferelt kapacitások](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) fogalma hasznos. A pufferelt kapacitás tartjuk a normál működés során. A fürterőforrás-kezelő szükség esetén a frissítések során a teljes kapacitásukig (a puffer felhasználása) töltheti ki a csomópontokat.

## <a name="next-steps"></a>További lépések
* Kezdje az elejétől, és [kapjon bevezetést a Szolgáltatásháló-fürt erőforrás-kezelőjébe](service-fabric-cluster-resource-manager-introduction.md)
