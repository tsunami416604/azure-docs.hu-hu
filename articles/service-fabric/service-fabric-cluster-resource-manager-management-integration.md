---
title: Service Fabric fürt Resource Manager - kezelésének integrációja |} A Microsoft Docs
description: A fürterőforrás-kezelő és a Service Fabric Management között az integrációs pontokat áttekintése.
services: service-fabric
documentationcenter: .net
author: masnider
manager: chackdan
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: c201945e94474d54b8a19918f3b55a0b40995a97
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58670311"
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Fürt resource manager-integráció Service Fabric-fürt kezelése
A Service Fabric fürterőforrás-kezelő nem meghajtó frissítése a Service Fabric, de részt vesz. Az első, amely segít a fürterőforrás-kezelő felügyeleti módja a fürt és a benne lévő szolgáltatások állapotának nyomon követése révén. A fürterőforrás-kezelő rendszerállapot-jelentések küldése során, a fürt nem állítható be a szükséges konfiguráció. Például ha nincs elegendő kapacitás a fürterőforrás-kezelő küld ki állapotával kapcsolatos figyelmeztetések és a probléma jelzik. Integráció egy másik részét rendelkezik, a frissítések működését. A fürterőforrás-kezelő működését kis mértékben változtatja frissítések során.  

## <a name="health-integration"></a>Állapot-integráció
A fürterőforrás-kezelő folyamatosan nyomon követi a szolgáltatásokat helyezi el a megadott szabályok. Azt is nyomon követi a fennmaradó kapacitás mindegyik metrikát a csomópontokon, és a fürt és a fürt teljes. Ha ezt nem tudja teljesíteni ezeket a szabályokat, vagy ha nincs elegendő kapacitás, rendelkezésre állapotával kapcsolatos figyelmeztetések és hibák. Például ha egy csomópont feletti kapacitás van, és a fürterőforrás-kezelő megpróbálja kijavítani az a helyzet szolgáltatásokat helyezi. Ha ezt nem tudja kijavítani a helyzet egy állapotfigyelési figyelmeztetése, jelezve, hogy melyik csomópont van, mint a kapacitást, és mely metrikák bocsát ki.

Az erőforrás-kezelő állapotával kapcsolatos figyelmeztetések egy másik példa, az elhelyezési korlátozások megsértésének. Ha például, ha meg van adva egy elhelyezési korlátozás (például `“NodeColor == Blue”`) és az erőforrás-kezelő észleli, hogy a korlátozás megsértését, azt bocsát ki egy állapotfigyelési figyelmeztetése. Ez a egyéni korlátozások és az alapértelmezett korlátozásokban (például a tartalék és frissítési tartomány korlátozásokkal).

Íme egy példa egy ilyen jelentés. Az állapotjelentés ebben az esetben a rendszer szolgáltatáspartíciók egyik van. Az egészségügyi üzenet azt jelzi, hogy az adott partíció a replikák átmenetileg túl kevés frissítési tartományok vannak elhelyezve.

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

Íme, mi az egészségügyi üzenet eredménytelen van:

1. Magukat minden replika megfelelő: Mindegyik rendelkezik AggregatedHealthState: OK
2. A frissítési tartomány terjesztési korlátozást jelenleg éppen sérül. Ez azt jelenti, hogy az adott frissítési tartományban van több replika ehhez a partícióhoz a kelleténél.
3. Melyik csomópont tartalmazza a replikát a megsértést okoz. Ebben az esetben a "Node.8" nevű csomópont legyen.
4. E frissítés pillanatnyilag folyamatban levő esetében ezt a particionálása ("jelenleg frissítése – a hamis")
5. A terjesztési házirend ezt a szolgáltatást: "Terjesztési házirend – csomagolási". Ez vonatkozik a `RequireDomainDistribution` [elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Csomagolási" azt jelzi, hogy ebben az esetben DomainDistribution _nem_ szükséges, hogy tudjuk, hogy elhelyezési szabályzat nincs megadva ehhez a szolgáltatáshoz. 
6. Ha a jelentés történt – 8/10/2015 7:13:02-kor

További információkat, például a powers riasztások olyan esetekre, hogy tudja hiba merült fel, és észlelheti és halt rossz frissítés is használható éles környezetben. Ebben az esetben tekintse meg, ha azt is döntse el, miért érdemes az erőforrás-kezelő a replikákat a frissítési tartományba, hogy kellett volna szeretnénk. A csomópontok a más frissítési tartományok volt, például általában csomagolási azért átmeneti.

Tegyük fel, a fürterőforrás-kezelő megpróbálja helyezze el az egyes szolgáltatások, de nincs a hozzáadott megoldások, amelyek működnek. Szolgáltatások nem helyezhető el, esetén általában a következő okok valamelyike:

1. Bizonyos átmeneti feltétel tette, hogy a szolgáltatás példányát és a replika megfelelően helyezze lehetetlen
2. A szolgáltatás-elhelyezési követelmények unsatisfiable.

Ebben az esetben a rendszerállapot-jelentések a fürt Resource Manager segítségével eldöntheti, hogy miért a szolgáltatás nem helyezhető el. Ez a folyamat a megkötés eltávolítási feladatütemezési nevezzük. Során, a rendszer végigvezeti a szolgáltatás és a rekordokat érintő, azok számára beállított korlátozó. Amikor a szolgáltatások nem tudják helyezni, így láthatja melyik csomópontokon is számolni, és miért.

## <a name="constraint-types"></a>Korlátozás típusa
Vegyük egyes ezeket a rendszerállapot-jelentések az másik korlátozások. Látni fogja a során a replika nem helyezhető el ezek a korlátozások kapcsolatos egészségügyi üzeneteket.

* **ReplicaExclusionStatic** és **ReplicaExclusionDynamic**: Ezek a korlátozások azt jelzi, hogy a megoldás el lett utasítva, mivel ugyanazon a partíción két szolgáltatás objektumait kell elhelyezni, ugyanazon a csomóponton. Ez nem engedélyezett, mert, majd az adott csomóponton hiba túlságosan hatással van a partíción. ReplicaExclusionStatic és ReplicaExclusionDynamic szinte ugyanaz a szabály, és nem igazán számít, a különbségeket. Ha egy korlátozást eltávolítási feladatütemezési ReplicaExclusionStatic vagy ReplicaExclusionDynamic korlátozást tartalmazó jelennek meg, a fürterőforrás-kezelő fenyegetésként észlel, hogy nincsenek-e elegendő csomópontok. Ehhez a fennmaradó megoldások használata ezen érvénytelen gyakorlatok, amelyek nem engedélyezettek. Az egyéb korlátozások a feladatütemezési fog általában mondja el, miért csomópont alatt kiküszöbölhetők az elsőként.
* **PlacementConstraint**: Ha ezt az üzenetet látja, az azt jelenti, hogy egyes csomópontok azt nem szükséges, mert a szolgáltatás-elhelyezési korlátozások nem egyeznek. A Microsoft nyomkövetési ki a jelenleg konfigurált elhelyezési korlátozások, ez az üzenet részeként. Ez a normál, ha egy elhelyezési korlátozás definiálva van. Azonban ha elhelyezési korlátozás helytelenül okozza-e túl sok csomópontot kell számolni, hogyan szeretné láthatja, hogy azt.
* **NodeCapacity**: Ezt a korlátot, az azt jelenti, hogy a fürterőforrás-kezelő nem tudta elhelyezni, a replikákat a jelzett csomópontokon mert, amely lenne kapacitás fölé helyezi.
* **Affinitás**: Ez a korlát, hogy azt nem sikerült elhelyezni a replikát az érintett csomópontok óta a kapcsolat korlátozás megsértését okozna. További információk a kapcsolat [Ez a cikk](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** és **UpgradeDomain**: Ennél a határértéknél kiküszöböli a csomópontok, ha adott tartalék és frissítési tartományokban lévő csomagolási helyezi el a replika a jelzett csomópontokon okozna. Néhány példa témák megvitatásához ennél a határértéknél jelennek meg a témakör a [hibatűrési és frissítési tartomány korlátozások és az eredményül kapott viselkedés](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: Ennél a határértéknél csomópontot távolíthat a megoldás, mivel azt futtatja az optimalizálás alapértelmezés szerint általában nem látható. Az előnyben részesített földrajzi megszorítás is megtalálható frissítések során. A frissítés során használatos szolgáltatások áthelyezése vissza, ahol voltak a frissítés indításakor.

## <a name="blocklisting-nodes"></a>Blocklisting csomópontok
Egy másik egészségügyi üzenet a fürterőforrás-kezelő jelentések akkor, ha csomópontok blocklisted. Ideiglenes korlátozásként, hogy a rendszer automatikusan alkalmazza az Ön számára blocklisting is felfoghatók. Csomópontok blocklisted kaphat, ha a példányok adott típusú szolgáltatás indításakor ismétlődő kudarcát tapasztalható. Csomópontok száma – szolgáltatástípus alapon blocklisted. Egy csomópont lehet, hogy egy szolgáltatás típus blocklisted, de nem egy másik. 

Látni fogja a fejlesztés során gyakran pillanattal blocklisting: néhány programhibája miatt a szolgáltatás indításkor összeomlik gazdagépet. A Service Fabric próbál hozza létre a szolgáltatás néhány alkalommal, és a hiba követi lépett fel. Néhány kísérlet után a csomópont lekéri blocklisted, és a fürterőforrás-kezelő megpróbálja máshol szolgáltatás létrehozásához. Ha a hiba több csomóponton tartja történik, lehetséges, hogy az összes érvényes mentése a fürtben lévő csomópontok blokkolva. Blocklisting, törölheti, hogy nincs elég sikeresen indíthatja el a szolgáltatást, hogy megfeleljen a kívánt méret sok csomópontok. Látni fogja, általában további hibaüzenetek vagy figyelmeztetések a fürterőforrás-kezelő, amely azt jelzi, hogy a szolgáltatás nem éri el a kívánt replika vagy a példányok száma, valamint jelezve, hogy mi a hiba, amely létrejöttéhez vezet, az első a blocklisting egészségügyi üzenetek Helyezze el.

Blocklisting nem állandó feltétel. Néhány perc múlva a csomópont a tiltólista törlődik, és a Service Fabric újra aktiválhatja a szolgáltatások a csomóponton. Szolgáltatások továbbra is sikertelen, ha a csomópont a service Type blocklisted újra. 

### <a name="constraint-priorities"></a>Megkötés prioritások

> [!WARNING]
> Megkötés prioritások módosítása nem ajánlott, és jelentős hátrányosan befolyásolhatja a fürtön. Az alábbi információkat az alapértelmezett megkötés prioritások és azok viselkedésének hivatkozás lett megadva. 
>

Az összes megkötés előfordulhat, hogy rendelkezik lett gondol "Hey – azt hiszem, hogy a tartalék tartomány korlátozások a legfontosabb, amit a rendszeren. Annak érdekében, hogy a tartalék tartomány megkötés megsértve nem, szeretnék egyéb megszorítások megsértő."

Korlátozások a különböző prioritási szintű konfigurálható. Ezek a következők:

   - "rögzített" (0)
   - "soft" (1)
   - "optimalizálási" (2)
   - "off" (-1). 
   
A korlátozó többsége alapértelmezés szerint szigorú korlátok van konfigurálva.

A korlátozások prioritásának módosítása használata nem szokványos. Eddig alkalommal ahol megkötés prioritások szükség módosításához, általában néhány egyéb programhiba vagy viselkedés, amely a környezet alapállapota megkerüléséhez. A korlátozás prioritását infrastruktúra a rugalmasságot általában a rendkívül jól működött, de gyakran nem szükséges. A legtöbbször minden, az alapértelmezett prioritásokat helyezkedik el. 

A prioritási szintet nem jelenti azt, amely egy adott korlátozás _fog_ megsértette, sem, amely azt mindig teljesülnek. Megkötés prioritások megadása egy rendelést, amelyben korlátozások érvényben vannak. Prioritások a kompromisszumot kínál a határozza meg, amikor nem felel meg az összes korlátozó lehet. A vonatkozó korlátozások lehet teljesíteni általában, kivéve, ha valami más környezetben történik. Néhány példa a megkötés megsértésének eredményezi a forgatókönyvek olyan ütköző tartoznak, illetve nagyszámú egyidejű hibák.

Speciális helyzetekben a megkötés prioritások módosíthatja. Tegyük fel például, hogy szeretne, győződjön meg arról, hogy kapcsolat mindig sérülnének csomópont kapacitás előforduló problémák megoldására szükség esetén. Ennek érdekében sikerült állítsa be a "soft" (1) kapcsolat korlátozás prioritását, és hagyja, a kapacitás megkötés beállítása "rögzített" (0).

Az alapértelmezett prioritás értékeit a különböző korlátozások vannak megadva a következő konfigurációt:

ClusterManifest.xml

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

az önálló verziója telepítéseinek ClusterConfig.json vagy Template.json az Azure-ban futó fürtök:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Tartalék tartomány és frissítési tartomány megkötései
A fürterőforrás-kezelő szeretné megtartani a szolgáltatások terjednek hibatűrési és frissítési tartományok között. Ez a fürt Resource Manager motorjában korlátozásként, modellek. További információ a használatukat, és adott viselkedését, tekintse meg a cikk a [fürtkonfiguráció](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

A fürterőforrás-kezelő előfordulhat, hogy néhány replikákat a frissítési tartomány, annak érdekében, hogy a frissítések, a hibák vagy más megkötés megsértésének foglalkozik. Tartalék vagy frissítési tartományokra csomagolási általában akkor fordul elő, csak ha több hibák vagy egyéb adatforgalom található, a rendszer megakadályozza, hogy a megfelelő elhelyezési. Ha szeretné, hogy az ezekben a helyzetekben során is csomagolási, használhat a `RequireDomainDistribution` [elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Vegye figyelembe, hogy ez lehetséges, hogy hatással a szolgáltatás rendelkezésre állása és megbízhatósága eltérő lehet mellékhatása, ezért fontolja meg alaposan.

Ha a környezet megfelelően van konfigurálva, minden korlátozó teljes tiszteletben tartják, még a frissítések során. A lényeg az, hogy a fürterőforrás-kezelő van megtekintését a megkötések. Ha azt észleli, hogy megsértést azonnal azt jelenti, és próbálja meg a probléma.

## <a name="the-preferred-location-constraint"></a>Az előnyben részesített földrajzi megszorítás
A PreferredLocation pedig kissé eltérő, mivel annak két különböző használja. Egy ennél a határértéknél használata alkalmazásfrissítések során. A fürterőforrás-kezelő automatikusan kezeli ezt a korlátozást a frissítések során. Annak érdekében, hogy a frissítés befejeződött, hogy replikák térjen vissza az eredeti helyükre esetén használatos. Más a PreferredLocation korlátozás használata esetében a [ `PreferredPrimaryDomain` elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Mindkét optimalizálást, és ezért a PreferredLocation pedig az egyetlen korlátozás alapértelmezés szerint a "Optimalizálási" értékre.

## <a name="upgrades"></a>Frissítések
A fürterőforrás-kezelő is segít során az alkalmazás és fürt frissítését, mely során két feladatot rendelkezik:

* Győződjön meg arról, hogy a szabályok a fürt ne legyenek veszélyben
* Próbálja ki a zökkenőmentességét frissítése érdekében

### <a name="keep-enforcing-the-rules"></a>A szabályok folyamatosan kényszerítése
Érdemes figyelembe vennie a főbb dolog, hogy a szabályok – a szigorú korlátok például elhelyezési korlátozások és a kapacitás - továbbra is érvényben vannak frissítések során. Elhelyezési korlátozások győződjön meg arról, hogy a számítási feladatok csak futnak, számukra engedélyezett, még akkor is a frissítések során. Amikor a Szolgáltatások erősen korlátozott, frissítések több időt vesz igénybe. Ha a szolgáltatás vagy a csomóponton futó állapotba kerül egy frissítés előfordulhat néhány lehetőség ahol folytathatja.

### <a name="smart-replacements"></a>Az intelligens csere
Ha a frissítés elindul, a Resource Manager egy pillanatképet készít a fürt jelenlegi elrendezésének. Minden frissítési tartomány befejeződött, mert megpróbálja adja vissza a szolgáltatások, amelyek az adott frissítési tartományban, azok eredeti elrendezése. Ezzel a módszerrel nincsenek legfeljebb két átmenetek egy szolgáltatás a frissítés során. Van egy áthelyezés meg az érintett csomópont, és egy helyezze vissza. Térjen vissza a fürt vagy a szolgáltatás hogyan lett a frissítés előtt is biztosítja, hogy a frissítés nem érinti a fürt elrendezését. 

### <a name="reduced-churn"></a>Csökkentett forgalom
Még egy dolog, hogy a frissítések során történik, hogy a fürterőforrás-kezelő kikapcsolja a terheléselosztást. Megakadályozza a terheléselosztás megakadályozza, hogy a szükségtelen reakciók, a frissítési szolgáltatások megéri voltak a frissítés üríteni csomópontok például. Ha a frissítés a kérdéses fürt frissítése, majd az egész fürt nem kiegyensúlyozott a frissítés során. Megkötés ellenőrzések addig maradnak aktívak, proaktív terheléselosztási mérőszámok alapján csak adatátviteli le van tiltva.

### <a name="buffered-capacity--upgrade"></a>Pufferelt kapacitás & frissítése
Általában érdemes a frissítés befejezéséhez még akkor is, ha a fürt szabványú vagy közel teljes. A fürt kapacitásának kezelése még ennél is fontosabbak frissítések során a szokásosnál. Frissítési tartományok számától függően 5 és 20 százalékkal kapacitás között kell migrálni a frissítés révén a fürt összesíti. A munka go valahol rendelkezik. Ekkor nyernek a hibabeszúrás fogalmát [kapacitások pufferelt](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) lehet hasznos. Normál működés során pufferelt kapacitás tiszteletben tartását. A fürterőforrás-kezelő előfordulhat, hogy töltse ki csomópontokat akár a teljes kapacitás (felhasználása a puffer) frissítések során szükség esetén.

## <a name="next-steps"></a>További lépések
* Elölről kezdődik, és [, a Service Fabric fürterőforrás-kezelő bemutatása](service-fabric-cluster-resource-manager-introduction.md)
