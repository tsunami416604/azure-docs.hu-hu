---
title: Service Fabric fürt Resource Manager - elhelyezési házirendek |} A Microsoft Docs
description: További elhelyezési házirendeket és a szabályok a Service Fabric-szolgáltatások áttekintése
services: service-fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: ''
ms.assetid: 5c2d19c6-dd40-4c4b-abd3-5c5ec0abed38
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 2858628874dc9955db5084ef5732d85acd6e7fc1
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56729788"
---
# <a name="placement-policies-for-service-fabric-services"></a>A service fabric-szolgáltatások elhelyezési házirendek
Elhelyezési házirendeket, amelyek segítségével szabályozhatja az egyes konkrét, kevésbé-közös forgatókönyvekben a szolgáltatások elhelyezését további szabályok. Néhány példa, illetve a forgatókönyvek a következők:

- A Service Fabric-fürt kiterjedő földrajzi távokat, például a több helyszíni adatközpont vagy Azure-régiók között
- A környezet kiterjedő geopolitikai vagy jogi vezérlőelem több területet, vagy néhány egyéb házirend esetében ki kell kényszerítenie határok
- Kommunikációs teljesítmény vagy a késés megfontolások miatt nagy távolságra vagy a lassabb vagy kevésbé megbízható hálózati kapcsolatok
- Bizonyos munkaterhelések közös elhelyezésű, ajánlott annak érdekében, vagy más számítási feladatokkal, vagy az ügyfelek számára közelségi megőrzése

Ezek a követelmények a legtöbb igazodva szerinti a tartalék tartományok a fürt a fürt fizikai elrendezését. 

A speciális elhelyezési házirendeket, amelyek segítenek ezek a forgatókönyvek a következők:

1. Érvénytelen tartomány
2. A tartományok megadása kötelező
3. Elsődleges tartomány
4. Replika csomagolási kapcsolatai

Az alábbi vezérlők a legtöbb csomópont-tulajdonságok és elhelyezési korlátozások konfigurálása sikerült, de néhány összetettebb. A Service Fabric fürterőforrás-kezelő egyszerűbbé teszik a dolgokat, biztosít ezen további elhelyezési házirendeket. Elhelyezési házirendeket egy elnevezett szolgáltatás példány alapon vannak konfigurálva. Akkor is frissíthető dinamikusan.

## <a name="specifying-invalid-domains"></a>Érvénytelen tartomány megadása
A **InvalidDomain** elhelyezési házirend lehetővé teszi, hogy egy adott tartalék tartomány érvénytelen adott szolgáltatásokhoz. Ez a házirend biztosítja, hogy egy adott szolgáltatás egy bizonyos területen, például a geopolitikai vagy vállalati házirendek miatt soha ne fusson. Érvénytelen több tartomány segítségével különböző házirendeket lehet megadni.

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
A szükséges tartományi elhelyezési házirend megköveteli, hogy megtalálható-e a szolgáltatás csak a megadott tartományban. Több kötelező tartomány különböző házirendeket keresztül adható meg.

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
Az előnyben részesített elsődleges tartomány elhelyezése az elsődleges, a tartalék tartományának megadása. Az elsődleges fejeződik be a tartomány minden kifogástalan esetén. Ha a tartomány vagy az elsődleges replika meghibásodik, vagy leáll, az elsődleges áthelyezi egy másik helyre, ideális ugyanabban a tartományban. Ha az új hely nem előnyben részesített a tartományban, a fürterőforrás-kezelő tér vissza az elsődleges tartomány minél hamarabb. Természetesen ez a beállítás csak értelme az állapotalapú szolgáltatások esetében. Ez a szabályzat akkor a leghasznosabb, amely az Azure-régióban vannak felölelt fürtök, vagy több adatközpont, de a szolgáltatások, amelyek inkább az elhelyezési bizonyos helyen vannak. Elsődleges tartja a felhasználók vagy más szolgáltatások közelében biztosít a kisebb késés, különösen az olvasási, amely alapértelmezés szerint elsődleges kezeli.

<center>
![Előnyben részesített elsődleges tartományok és feladatátvétel][Image3]
</center>

```csharp
ServicePlacementPreferPrimaryDomainPolicyDescription primaryDomain = new ServicePlacementPreferPrimaryDomainPolicyDescription();
primaryDomain.DomainName = "fd:/EastUS/";
serviceDescription.PlacementPolicies.Add(primaryDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("PreferredPrimaryDomain,fd:/EastUS")
```

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Replika terjesztési igénylő és csomagolási kapcsolatai
Replikák _általában_ elosztva hibatűrési és frissítési tartományokba, amikor a fürt állapota kifogástalan. Vannak azonban esetekben, ahol egynél több replikát egy adott partíció esetében előfordulhat, hogy végül ideiglenesen csomagolt egy tartományba. Például tegyük fel, hogy a fürt három tartalék tartományt, fd kilenc csomópontok rendelkezik: / 0, fd: / 1- es fd: / 2. Is tegyük fel, hogy a szolgáltatás rendelkezik-e a három replika készül. Tegyük fel, amely ezeket az fd-replikákhoz használt csomópontok: / 1 és fd: / 2 csökkent. Általában a fürterőforrás-kezelő inkább más csomópontok ezek azonos tartalék tartományokban. Ebben az esetben tegyük miatt nehézségekbe ütközhet az ezeket a tartományokat a többi csomópont sem érvényes. A fürterőforrás-kezelő létrehozta ezeket a replikákat a lecserélendő, ha azt kellene csomópontok kiválasztása az fd: / 0. Azonban ez _, amely_ hoz létre egy olyan helyzetet, ahol a tartalék tartomány korlátozás sérül. Replikák növeli annak esélyét, hogy a teljes replika beállítása sikerült leáll vagy megszakadt. 

> [!NOTE]
> További információ a korlátozások és a korlátozás prioritások általában, tekintse meg [ebben a témakörben](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities).
>

Ha valaha megtudhatta, egészségügyi üzenet például a "`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`", akkor ezt az állapotot, vagy valami, mint hogy eléri. Általában csak egy vagy két replika van csomagolt együtt ideiglenesen. Mindaddig, amíg vannak kevesebb mint egy kvórum replikák egy adott tartományban, akkor biztonságos voltát. Balí se ritkán fordul elő, de akkor fordulhat elő, és ezekben a helyzetekben általában átmeneti, mivel a csomópontok tér vissza. Ha a csomópontok maradjon le, és a fürterőforrás-kezelő cseréjére létre kell hoznia, általában nincsenek más csomópontok elérhető az ideális tartalék tartományokban.

Bizonyos munkaterhelések inkább, mindig a replikákat, célszáma kellene akkor is, ha azok kevesebb tartományokban vannak elhelyezve. Ezeket a feladatokat is tudásához teljes egyidejű állandó tartomány meghibásodásokkal szemben, és általában helyre tudja állítani a helyi állapotot. Más számítási feladatok inkább igénybe az állásidő kockázati helyességét, és adatvesztés, mint korábban. A legtöbb éles számítási feladatok futtatása több mint három replika, több mint három tartalék tartományt, és számos érvényes csomópontok maximális száma a tartalék tartomány. Emiatt az alapértelmezett viselkedés lehetővé teszi, hogy tartományi csomagolási alapértelmezés szerint. Alapértelmezés szerint lehetővé teszi, hogy a normál terheléselosztási és feladatátvételi ilyen szélsőséges esetek kezeléséhez akkor is, ha azt jelenti, hogy ideiglenes tartomány csomagolási.

Ha szeretné tiltani egy adott számítási feladathoz tartozó ilyen csomagolási, megadhatja a `RequireDomainDistribution` házirend a szolgáltatásban. Ha ez a házirend be van állítva, a fürterőforrás-kezelő biztosítja, hogy két replika. az ugyanazon a partíción futtassa az azonos tartalék vagy frissítési tartományban.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Most azt volna lehetséges ezen konfigurációk használata a szolgáltatások a fürtben, amely nem földrajzilag felölelt? Próbálja meg, de nem áll egy remek kivételt túl. A szükséges érvénytelen és előnyben részesített tartomány konfigurációk kerülni kell, ha a forgatókönyvek hitelesíteni. Azt nem értelme bármely próbál kényszerítése egy adott számítási feladatok futtatása az egyetlen állványon, vagy inkább a helyi fürt egyes szegmens egy másikkal szemben. Különböző hardverkonfigurációk kell tartalék tartomány között elosztani és normál elhelyezési korlátozások és csomópont-tulajdonságok keresztül kezeli.

## <a name="next-steps"></a>További lépések
- További információt a szolgáltatások konfigurálása [megismerheti a szolgáltatások konfigurálása](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
