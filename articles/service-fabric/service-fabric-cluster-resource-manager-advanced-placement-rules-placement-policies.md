---
title: Service Fabric fürt erőforrás-kezelő – elhelyezési házirendeket |} Microsoft Docs
description: További elhelyezési házirendeket és a Service Fabric szolgáltatások szabályainak áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: bdf10f1d9199500d4f5bda3c5a05a3d67ff65541
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
---
# <a name="placement-policies-for-service-fabric-services"></a>Elhelyezési házirendeket a service fabric szolgáltatásokhoz
Elhelyezési házirendeket és a további szabályok, annak a szabályozására, szolgáltatáselhelyezés bizonyos meghatározott, kevésbé-közös esetekben használható. Néhány példa a azokra a következők:

- A Service Fabric-fürt által felölelt földrajzi távolság, például több helyszíni adatközpontot vagy Azure-régiók között
- A környezet geopolitikai vagy jogi vezérlő több területet, vagy valamilyen egyéb házirend esetében is ki kell kényszerítenie határok
- Kommunikációs teljesítményt és késést szempontot nagy távolságra vagy a lassabb vagy kevésbé megbízható hálózati kapcsolat miatt
- Szeretne rögzíteni munkaterhelések közös elhelyezésű, mint a lehető legjobb rendezését, vagy egyéb munkaterhelésekkel való vagy közelében, az ügyfél számára

A jelentős része igazodnak a fizikai elrendezését a fürt a fürt a tartalék tartományok ábrázolva. 

A speciális elhelyezési házirendeket, amelyek segítenek a forgatókönyvek a következők:

1. Érvénytelen tartományok
2. Szükséges tartományok
3. Előnyben részesített tartományokat
4. A replika csomagolási letiltása

Az alábbi funkciókat a legtöbb sikerült konfigurálni a csomópont-tulajdonságok és elhelyezési korlátozás keresztül, de néhány bonyolultabb. Ahhoz, hogy egyszerűbb dolog, a Service Fabric fürt erőforrás-kezelő biztosít a további elhelyezési házirendeket. Elhelyezési házirendek / nevű szolgáltatás példány alapon. Akkor is frissíthető dinamikusan.

## <a name="specifying-invalid-domains"></a>Érvénytelen tartományok megadása
A **InvalidDomain** elhelyezési házirend lehetővé teszi, hogy egy adott tartalék tartomány érvénytelen egy adott szolgáltatáshoz. Ez a házirend biztosítja, hogy egy adott szolgáltatáshoz soha nem egy adott területen, például a geopolitikai vagy vállalati házirendek miatt. Érvénytelen többtartományos külön házirendek keresztül adható meg.

<center>
![Érvénytelen tartomány – példa][Image1]
</center>

Kód:

```csharp
ServicePlacementInvalidDomainPolicyDescription invalidDomain = new ServicePlacementInvalidDomainPolicyDescription();
invalidDomain.DomainName = "fd:/DCEast"; //regulations prohibit this workload here
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("InvalidDomain,fd:/DCEast”)
```
## <a name="specifying-required-domains"></a>Adja meg a szükséges tartományok
A szükséges tartományi elhelyezési házirend szükséges, hogy megtalálható-e a szolgáltatás csak a megadott tartományban. Több szükséges tartomány külön házirendek keresztül adható meg.

<center>
![Kötelező: Példa][Image2]
</center>

Kód:

```csharp
ServicePlacementRequiredDomainPolicyDescription requiredDomain = new ServicePlacementRequiredDomainPolicyDescription();
requiredDomain.DomainName = "fd:/DC01/RK03/BL2";
serviceDescription.PlacementPolicies.Add(requiredDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomain,fd:/DC01/RK03/BL2")
```

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Állapotalapú szolgáltatás elsődleges replika az elsődleges tartomány megadása
Az előnyben részesített elsődleges tartomány az elsődleges helyezhető el a tartalék tartomány megadása Az elsődleges fejeződik be a tartomány minden kifogástalan esetén. Ha a tartomány vagy az elsődleges másodpéldány nem sikerül, vagy áll le, az elsődleges áthelyezi néhány más helyre, ideális ugyanabban a tartományban. Ha az új hely nem előnyben részesített a tartományban, a fürt erőforrás-kezelő tér vissza az előnyben részesített tartományi lehető leghamarabb. Természetesen ez a beállítás csak értelme állapotalapú szolgáltatások. Ez a házirend olyan fürtökben, amelyek az Azure-régiók is átnyúlhatnak a leghasznosabb, vagy több adatközpontot azonban vannak olyan szolgáltatások, amelyek egy adott helyen elhelyezési inkább. Elsődleges tartása megközelíti a felhasználók és más szolgáltatások segítségével adja meg kisebb késést biztosít, különösen az olvasások, amely alapértelmezés szerint elsődleges kezeli.

<center>
![Előnyben részesített elsődleges tartományok és a feladatátvétel][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(invalidDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Replika terjesztési igénylő és csomagolási letiltása
Replika _általában_ elosztott hiba és a frissítési tartományok között, ha a fürt állapota kifogástalan. Azonban vannak esetek, ahol adott partíció egynél több replikát is szükségessé tehet ideiglenesen csomagolt egy tartományba. Például tegyük fel, hogy rendelkezik-e a fürt kilenc csomópontok a három tartalék tartományok, fd: / 0, fd: / 1 és fd: / 2. Tételezzük is fel, hogy a szolgáltatás rendelkezik-e a három replikákat. Tegyük fel, hogy a csomópontok azokat fd található replikák volt használatban lévő: 1 és fd: / 2 csökkent. A fürt erőforrás-kezelő általában inkább az azonos tartalék tartományokban többi csomópontjának. Ebben az esetben Tételezzük fel kapacitás problémái miatt az azokban a tartományokban a többi csomópont sem érvényes. Ha a fürt erőforrás-kezelő létrehozta cserékhez említett-replikákhoz, azt kell választania a csomópontok a fd: / 0. Azonban ez _, amely_ hoz létre olyan helyzet, ahol a tartalék tartomány korlátozás sérül. Replikák növeli annak esélyét, hogy a teljes replika beállítása sikerült leáll vagy elvesznek. 

> [!NOTE]
> További információ a korlátozások és a korlátozás prioritások általában, tekintse meg [ebben a témakörben](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Ha már legalább egyszer megtekintett állapotfigyelő üzenet például a "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", akkor ezt az állapotot, vagy azt hasonlót már elérte. Általában csak egy vagy két replikák vannak csomagolva együtt ideiglenesen. Mindaddig, amíg nincsenek egy adott tartományban replikák kvórum nem lépi-e, tehát biztonságos. Csomagolási ritkán fordul elő, de akkor fordulhat elő, és általában ezekben a helyzetekben átmeneti mivel a csomópontok térjen vissza. Ha a csomópontok le marad, és a fürt erőforrás-kezelő cserékhez létrehozásához meg kell, általában nincsenek más csomópontok érhető el az ideális tartalék tartományok.

Bizonyos alkalmazások és szolgáltatások inkább, mindig használjon a replikákat, cél mennyiségű akkor is, ha azok csomagolt, kevesebb tartományokra. Az ilyen terhelések vannak fogadások teljes egyidejű állandó tartomány-hibákkal szemben, és általában helyre tudja állítani a helyi állapotát. Egyéb munkaterhelések ahelyett, hogy kellene az állásidő rendszernél kockázat nézetet, illetve az adatvesztés. A legtöbb termelési számítási feladatokhoz háromnál több replikákat, több mint három tartalék tartományok és tartalék tartomány sok érvényes csomópontok futtassa. Emiatt az alapértelmezett viselkedés lehetővé teszi, hogy tartományi csomagolási alapértelmezés szerint. Az alapértelmezett viselkedés lehetővé teszi, hogy normál terheléselosztási és feladatátvételi szélsőséges esetben kezelni akkor is, ha ez azt jelenti, hogy ideiglenes tartomány csomagolási.

Ha le szeretné tiltani egy adott munkaterhelés számára ilyen csomagolási, megadhatja a `RequireDomainDistribution` házirend a szolgáltatásban. Ha ez a házirend be van állítva, a a fürt erőforrás-kezelő biztosítja, a partícióra két replika ugyanabban a tartományban hiba vagy frissítési futnak.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Most akkor lehet, amely földrajzilag nem ölel fürtben szolgáltatáshoz használja ezeket a beállításokat? Meg lehetett, de nincs nagy OK túl. A szükséges, érvénytelen, mind az elsődleges tartomány konfigurációk kivéve, ha a forgatókönyvek használatához el kell kerülni. Azt nem célszerű bármely a próbálja egy adott munkaterhelés egyetlen szekrényben futtatásához, vagy inkább a helyi fürt egyes szegmens másikkal kényszerítése. Különböző hardverkonfigurációk legyen elosztva a tartalék tartományok és kezelt normál elhelyezési korlátozás és a csomópont tulajdonságait.

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásáról [további információ a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
