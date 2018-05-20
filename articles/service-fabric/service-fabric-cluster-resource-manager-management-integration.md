---
title: Service Fabric fürt erőforrás-kezelő - felügyelet integrálását |} Microsoft Docs
description: Az integrációs pontjainak között a fürt erőforrás-kezelő és a Service Fabric Management áttekintése.
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 956cd0b8-b6e3-4436-a224-8766320e8cd7
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 3f93ca94d5aa3e95637a53a4c8fe3d9d264dd58c
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="cluster-resource-manager-integration-with-service-fabric-cluster-management"></a>Fürt resource manager integrálása a Service Fabric-fürt felügyeleti
A Service Fabric fürt erőforrás-kezelő nem meghajtója frissítések Service Fabric azonban részt vesz. Az első, amelyek a fürt erőforrás-kezelő segítségével felügyeleti módja a kívánt állapot, a fürt és a benne lévő szolgáltatások nyomon követése révén. A fürt erőforrás-kezelő elküldi állapotjelentések száma, amikor azt a fürt nem állítható be a kívánt konfigurációs. Például ha nincs elég kapacitás a fürt erőforrás-kezelő elküldi állapotfigyelő figyelmeztetésekről és hibákról, amely jelzi, a probléma. Integráció más adat frissítések működése az rendelkezik. A fürt erőforrás-kezelő működése némileg megváltoztatja frissítéskor.  

## <a name="health-integration"></a>Rendszerállapot-integráció
A fürt erőforrás-kezelő folyamatosan nyomon követi a szolgáltatások elhelyezéséhez megadott szabályok. Azt is nyomon követi az egyes mérőszám kapacitása a csomópontokon és a fürt és a fürt egész. Ha azt nem elégíti ki ezeket a szabályokat, vagy ha nincs elég kapacitás, az egészségügyi figyelmeztetések és hibák kibocsátott. Ha például egy csomópont kapacitás és a fürt erőforrás-kezelő megpróbálja kijavítani a problémát szolgáltatások áthelyezésével. Ha nem, akkor a hierarchiakezelő elhárította állapotfigyelő figyelmeztetés azt jelzi, hogy melyik csomópont kapacitás, és melyik metrikáihoz bocsát ki.

Az erőforrás-kezelő állapotának figyelmeztetések egy másik példája egy elhelyezési korlátozás megsértése. Például, ha meg van adva egy elhelyezési korlátozás (például `“NodeColor == Blue”`) és az erőforrás-kezelő észleli, hogy a korlátozás megsértését, azt egy állapotfigyelési figyelmeztetése bocsát ki. Ez az egyéni típusmegkötéseket és az alapértelmezett korlátozásokban (például a tartalék tartomány és a frissítési tartomány korlátozások).

Íme egy példa egy ilyen állapotjelentése. Ebben az esetben az állapotjelentést van egy, a rendszer szolgáltatás a partíciók száma. A health az üzenet azt jelzi, az adott partíció a replikák ideiglenesen vannak csomagolt túl kevés frissítés tartományokra.

```posh
PS C:\Users\User > Get-WindowsFabricPartitionHealth -PartitionId '00000000-0000-0000-0000-000000000001'


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

Mi az egészségügyi üzenet ezzel azt jelzi, az itt található:

1. Magukat minden replika megfelelő: mindegyik rendelkezik-e AggregatedHealthState: Ok
2. A frissítési tartomány terjesztési megkötés jelenleg éppen sérül. Ez azt jelenti, hogy egy adott frissítés tartomány rendelkezik további replikák ehhez a partícióhoz a kelleténél.
3. Melyik csomópont tartalmazza a hiba, amely a replika. Ebben az esetben a csomópont "Node.8" nevű
4. Frissítés e jelenleg folyik a ez partícióazonosító ("jelenleg frissítése – false")
5. A szoftverterjesztési házirendben, ezt a szolgáltatást: "Terjesztési házirend--csomagolási". Ez vonatkozik a `RequireDomainDistribution` [elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). "Csomagolási" azt jelzi, hogy ebben az esetben DomainDistribution _nem_ szükséges, hogy tudjuk, hogy elhelyezési házirend nincs megadva ehhez a szolgáltatáshoz. 
6. Ha a jelentés történt – 8/10/2015 7 13:02 du.:

Információkat, például a powers riasztásokat, amelyek érvényesítést, hogy valami nem megfelelő állapotba került, és ismeri fel és rossz frissítések halt is tudja éles környezetben. Ebben az esetben tekintse meg, ha azt kitalálja, hogy miért az erőforrás-kezelő a replikák csomag frissítése tartományába kellett volna szeretnénk. Általában csomagolási nem átmeneti, mert a csomópontok a más frissítési tartományok lettek, például.

Tegyük fel, a fürt erőforrás-kezelő megpróbálja helyezze el az egyes szolgáltatások, de nincs bármely megoldások, amelyek működnek. Szolgáltatások nem helyezhető el, esetén általában a következő okok valamelyike:

1. Néhány átmeneti állapotról van lehetetlenné helyezze el a szolgáltatáspéldány, vagy a replika megfelelően
2. A szolgáltatás elhelyezésének vonatkozó követelmények nincsenek unsatisfiable.

Ebben az esetben a fürt erőforráskezelő állapotjelentések segítségével meghatározhatja, hogy miért a szolgáltatás nem helyezhető el. Ez a folyamat a korlátozás eltávolítási feladatütemezési hívása. Során, a rendszer végigvezeti a szolgáltatás és a rekordok azok kiküszöbölik érintő konfigurált korlátozásait. Ha szolgáltatások nem tudja elhelyezni, így látható csomópontok szüntetni volt, és miért.

## <a name="constraint-types"></a>Korlátozás típusok
Most szolgáltatással kapcsolatban egyes állapotfigyelő jelentésekben megjelenő különböző korlátozásait. Replikák nem helyezhető el a megkötéseket kapcsolatos egészségügyi üzenetek jelenik meg.

* **ReplicaExclusionStatic** és **ReplicaExclusionDynamic**: ezek a megkötések azt jelzi, hogy a megoldás vissza lett utasítva, mert két szolgáltatás objektumokat tartalmazó partícióra kell ugyanazon a csomóponton elhelyezni. Ez nem engedélyezett, mert, majd az adott csomópont hibát túlságosan befolyásolná a adott partíció. ReplicaExclusionStatic és ReplicaExclusionDynamic szinte ugyanaz a szabály és a különbségek valóban nem számít. Ha Ön egy korlátozás eltávolítási sorozatot tartalmazó ReplicaExclusionStatic vagy ReplicaExclusionDynamic megkötés, a fürt erőforrás-kezelő úgy értelmezi, hogy nincs-e elég csomópont. Ehhez a többi megoldások használatára ezek érvénytelen adatközpontokon, amelyek nem engedélyezettek. Az egyéb korlátozások a feladatütemezési fog általában adja meg, miért csomópont alatt kiküszöbölhetők az elsőként.
* **PlacementConstraint**: Ez az üzenet jelenik meg, ha azt jelenti, egyes csomópontok azt szüntetni, mert azok nem felel meg a szolgáltatás egy elhelyezési korlátozás. Azt nyomkövetés a jelenleg konfigurált placement Constraints korlátozásokat ki, ez az üzenet részeként. Ez nem rendellenes, ha egy elhelyezési korlátozás definiálva van. Azonban ha elhelyezési korlátozás helytelenül hatására túl sok csomópontot meg kell szüntetni azt, hogyan szeretné észlel.
* **NodeCapacity**: ezt a korlátot, az azt jelenti, hogy a fürt erőforrás-kezelő nem sikerült elhelyezni a replikákat a jelzett csomópontokon mert, célszerű keresztül kapacitás helyezni.
* **Kapcsolat**: Ez a korlát, hogy azt nem sikerült helyezze a replikát az érintett csomópontok, mert a kapcsolat korlátozás megsértését okozna. További információk a kapcsolat [Ez a cikk](service-fabric-cluster-resource-manager-advanced-placement-rules-affinity.md)
* **FaultDomain** és **UpgradeDomain**: ennél a határértéknél megszünteti a csomópont, ha egy adott hiba vagy a frissítési tartomány csomagolási helyezi el a replika a jelzett csomópontokon okozna. Néhány példa ennél a határértéknél megvitatása jelenjenek meg a témakör a [hiba és a frissítés tartomány típusmegkötéseket és az eredményül kapott viselkedéstől](service-fabric-cluster-resource-manager-cluster-description.md)
* **PreferredLocation**: általában nem szabad lásd a csomópontok eltávolítása a megoldás, mert alapértelmezés szerint az optimalizálás fut ennél a határértéknél. Az előnyben részesített földrajzi megszorítás is megtalálható frissítéskor. A frissítés során azt történő áthelyezéséhez használja szolgáltatások vissza az adott voltak a frissítés indításakor.

## <a name="blocklisting-nodes"></a>Blocklisting csomópontok
Egy másik állapotfigyelő üzenet az erőforrás-kezelő fürt jelentések akkor, ha csomópontok blocklisted. Az eltolásokat tekintheti blocklisting meg automatikusan alkalmazott ideiglenes korlátozásként. Csomópontok blocklisted jelenik meg, ha az adott szolgáltatás típusú példányok elindításakor az ismétlődő sikertelen ügyfeleken. Csomópontok blocklisted /-szolgáltatás típusa alapján. Egy csomópont lehet egy service Type blocklisted, de nem egy másik. 

Látni fogja, fejlesztés során gyakran indítsa blocklisting: néhány hiba hatására a szolgáltatás az indításkor összeomolhat. A szolgáltatásgazda létrehozása néhány alkalommal megkísérli a Service Fabric, és a probléma tartja lépett fel. Több próbálkozás után a csomópont lekérdezi blocklisted, és a fürt erőforrás-kezelő megpróbálja létrehozni a szolgáltatást a rendszerben található. A hiba több csomóponton tartja történik, akkor lehetséges, hogy minden érvényes fel a fürtben lévő csomópont tiltsa le. Blocklisting is, hogy nincs elegendő sikeresen indíthatja el a szolgáltatást, hogy megfeleljenek a kívánt méretezési sok csomópontok mindegyikének eltávolítását. Általában további hibákat vagy figyelmeztetéseket a az erőforrás-kezelőből jelzi, hogy a szolgáltatás nem éri el a kívánt replika vagy a példányok száma, valamint állapotfigyelő üzenetek azt jelzi, hogy mi a hiba, amely kialakulásához vezet a blocklisting az elsőként megjelenik.

Blocklisting nincs állandó feltétel. Néhány perc múlva a csomópont eltávolítják a blocklist, és a Service Fabric újra aktiválhatja a szolgáltatások ezen a csomóponton. Ha szolgáltatások továbbra is sikertelen, a csomópont nem az adott szolgáltatás blocklisted ismét. 

### <a name="constraint-priorities"></a>Korlátozás prioritások

> [!WARNING]
> Korlátozás prioritások módosítása nem ajánlott, és jelentős hátrányosan befolyásolhatja a fürtön. Az alábbi információkat esetén van megadva az alapértelmezett megkötés prioritások és azok viselkedését hivatkozását. 
>

Az összes megkötés akkor előfordulhat, hogy rendelkezik lett végezni "Hey – úgy vélem, hogy tartalék tartomány korlátozások-e a legfontosabb dolog, a rendszer. A tartalék tartomány megkötés megsértve nem biztosítására szeretnék egyéb megkötések megsértő."

Megkötések konfigurálható különböző prioritási szintet. Ezek a következők:

   - "rögzített" (0)
   - "soft" (1)
   - "optimalizálási" (2)
   - "off" (-1). 
   
A megkötések többsége alapértelmezés szerint rögzített korlátozások van konfigurálva.

A megkötések prioritásának módosítása ritka. Nem történt alkalommal ahol megkötés prioritások szükséges módosításához általában kerülő néhány más hiba vagy a környezet befolyásolta működésére. A korlátozás prioritás infrastruktúra a rugalmasságot általában a jól működött, de gyakran nem szükséges. Az esetek többségében mindent helyezkedik el, a alapértelmezett prioritásait. 

A prioritási szintet nem jelenti azt, hogy egy adott korlátozás _fog_ sikeres, és nem lesz mindig kell megfelelnie. Korlátozás prioritások megadása egy sorrendet, amelyben megkötések lépnek érvénybe. Prioritások a mellékhatásokkal határozza meg, amikor nem elégíti ki az összes megkötések. A megkötések általában kielégíthetők, kivéve, ha valami más továbblép a környezetben. Néhány példa a forgatókönyvek, amelyek korlátozások megsértésének olyan ütköző tartoznak, illetve nagyszámú egyidejű hibák.

A speciális esetben megkötés prioritásokra módosíthatja. Tegyük fel, hogy szeretné, győződjön meg arról, hogy affinitás volna mindig sérti-amikor erre szükség van a csomópont-kapacitás problémák megoldásában. Ennek érdekében sikerült prioritást kapjon a kapcsolat korlátozást a "soft" (1), és hagyja, a kapacitás megkötés beállítása "rögzített" (0).

Az alapértelmezett prioritás értékeit a különböző megkötések vannak megadva a következő config:

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

az önálló verziója telepítéseinek művelet vagy az Azure-Template.json üzemeltetett fürtök:

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

## <a name="fault-domain-and-upgrade-domain-constraints"></a>Tartalék tartomány és a frissítési tartomány megkötések
A fürt erőforrás-kezelő szeretné tartani hiba és a frissítési tartományok között felülbírálásokkal szolgáltatások. Ez a fürt Resource Manager motor belül korlátozásként modellek el. További információt talál azok használata és az adott viselkedést, tekintse meg a cikk a [fürtkonfiguráció](service-fabric-cluster-resource-manager-cluster-description.md#fault-and-upgrade-domain-constraints-and-resulting-behavior).

A fürt erőforrás-kezelő szükség lehet néhány replikák csomag egy frissítési tartományba frissítéseket, vagy a más korlátozások megsértésének kezelése érdekében. Csak akkor, ha nincsenek több hibákat vagy olyan más forgalom a rendszerrel, amely megakadályozza a megfelelő elhelyezési csomagolási hiba vagy a frissítési tartományok a szokásos módon történik. Ha ezekben a helyzetekben esetén csomagolási megakadályozása, használhatja a `RequireDomainDistribution` [elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md#requiring-replica-distribution-and-disallowing-packing). Vegye figyelembe, hogy ez lehetséges, hogy hatással a szolgáltatás rendelkezésre állása és megbízhatósága, egyik mellékhatása, ezért fontolja meg alaposan.

Ha a környezet megfelelően van konfigurálva, minden korlátozó teljesen betartását, még a frissítés alatt. A kulcs dolog, hogy a fürt erőforrás-kezelő van figyeli a megkötések. Amikor azt észleli, hogy megsértése azonnal azt jelenti, és próbálja meg a probléma elhárításához.

## <a name="the-preferred-location-constraint"></a>Az előnyben részesített földrajzi megszorítás
A PreferredLocation pedig az kissé eltérő, mert már van két különböző használ. Egy ennél a határértéknél használata alkalmazás frissítéskor. A fürt erőforrás-kezelő automatikusan kezeli ennél a határértéknél frissítéskor. Annak érdekében, hogy ha a frissítés be nem fejeződik, hogy a replikákat vissza az eredeti helyükre szolgál. A más a PreferredLocation megkötés használatos a [ `PreferredPrimaryDomain` elhelyezési házirend](service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies.md). Mindkét esetben optimalizálás, és ezért a PreferredLocation pedig a egyetlen megkötés alapértelmezés szerint "Optimalizálási" értékűre állítva.

## <a name="upgrades"></a>Frissítések
A fürt erőforrás-kezelő is segít alkalmazás és a fürt frissítése során, amely rendelkezik a két feladat során:

* Győződjön meg arról, hogy a fürt szabályok ne legyenek veszélyben
* a frissítés zökkenőmentesek segítségével próbálja

### <a name="keep-enforcing-the-rules"></a>A szabályok tartsa kényszerítése
Vegye figyelembe a fő lépés, hogy a szabályok – például egy elhelyezési korlátozás és a kapacitások a szigorú korlátozásokat - továbbra is érvényesek frissítéskor. Egy elhelyezési korlátozás győződjön meg arról, hogy a munkaterhelések csak futni, ahol azok csatlakozhatnának a, akár a frissítések során. Ha a Szolgáltatások erősen korlátozott, frissítések több időt vesz igénybe. Amikor a szolgáltatás vagy a csomópont futó állapotba kerül frissítési néhány lehetőség, ahol folytathatja a lehet.

### <a name="smart-replacements"></a>Intelligens cserékhez
Ha a frissítés elindul, az erőforrás-kezelő pillanatképet készít a a fürt aktuális elrendezésének. Minden frissítési tartomány befejezése megkísérli térjen vissza az eredeti megállapodás frissítési tartományban lévő szolgáltatások. Ezzel a módszerrel nincsenek legfeljebb két átmenetek szolgáltatás a frissítés során. Az érintett csomópont egy kilép, és egy helyezze vissza. Állítja azt vissza a fürt vagy a szolgáltatás hogyan lett a frissítés előtt is biztosítja a frissítés nem befolyásolja a fürt elrendezését. 

### <a name="reduced-churn"></a>Csökkentett forgalom
Egy másik olyan dolog, ami a frissítések során történik, hogy a fürt erőforrás-kezelő kikapcsolja a terheléselosztás. Megakadályozza a terheléselosztás megakadályozza, hogy a szükségtelen reakciók, a frissítési szolgáltatások áthelyezése is szerepelnek, a frissítés csomópontokból hasonlóan. Ha a szóban forgó frissítés a fürt frissítése, majd a teljes fürt nem eloszlik a frissítés során. Korlátozás ellenőrzések addig maradnak aktívak, csak a mozgás metrikák proaktív terheléselosztás alapján le van tiltva.

### <a name="buffered-capacity--upgrade"></a>A pufferelt kapacitás & frissítése
Általában érdemes a frissítés, még akkor is, ha a fürt korlátozza vagy megközelíti a teljes befejezéséhez. A kapacitás, a fürt kezelése fontos még több frissítéskor megy végbe. Attól függően, hogy a frissítési tartományok száma 5 és 20 százalékát kapacitás között kell áttelepíteni, a frissítés a fürt áthalad. Munka valahol lépjen ki. Ez akkor, ha a fogalmát [kapacitások pufferelt](service-fabric-cluster-resource-manager-cluster-description.md#buffered-capacity) hasznos. A pufferelt kapacitás tiszteletben tartják normál működés során. A fürt erőforrás-kezelő előfordulhat, hogy töltse ki csomópont, akár a teljes kapacitásának (a puffer fel) frissítéskor szükség esetén.

## <a name="next-steps"></a>További lépések
* Indítsa el az elejétől és [Bevezetés a Service Fabric fürt Resource Manager](service-fabric-cluster-resource-manager-introduction.md)
