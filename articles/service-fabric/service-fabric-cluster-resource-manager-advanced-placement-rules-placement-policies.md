---
title: Szolgáltatásháló fürterőforrás-kezelője – elhelyezési házirendek
description: A Service Fabric-szolgáltatások további elhelyezési szabályzatainak és szabályainak áttekintése
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 9aea157d03f344e07a81f0588d3e0127f17ca75d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75834431"
---
# <a name="placement-policies-for-service-fabric-services"></a>Elhelyezési szabályzatok a szolgáltatásháló-szolgáltatásokhoz
Az elhelyezési szabályzatok olyan további szabályok, amelyek bizonyos konkrét, kevésbé gyakori esetekben a szolgáltatáselhelyezésszabályozására használhatók. Néhány példa ezekre a forgatókönyvekre:

- A Service Fabric-fürt földrajzi távolságokat ölel fel, például több helyszíni adatközpontot vagy az Azure-régiók között
- A környezet több geopolitikai vagy jogi ellenőrzésen átívelő területet ölel fel, vagy más olyan esetet, ahol a szükséges irányelvhatárokat kell érvényesítenie
- A nagy távolságok vagy a lassabb vagy kevésbé megbízható hálózati kapcsolatok miatt kommunikációs teljesítmény- vagy késési szempontok merülnek fel
- Bizonyos számítási feladatokat a legjobb erőfeszítésként kell elhelyeznie, akár más munkaterhelésekkel, akár az ügyfelek közelében.

A legtöbb követelmény a fürt tartalék tartományaként ábrázolt fürt fizikai elrendezéséhez igazodik. 

A speciális elhelyezési házirendek, amelyek segítenek kezelni ezeket a forgatókönyveket a következők:

1. Érvénytelen tartományok
2. Szükséges tartományok
3. Előnyben részesített tartományok
4. Replikacsomagolás lehetővé nem engedése

Az alábbi vezérlők többsége konfigurálható a csomópont tulajdonságai és elhelyezési megkötésein keresztül, de néhány bonyolultabb. A szolgáltatások egyszerűsítése érdekében a Service Fabric fürterőforrás-kezelő biztosítja ezeket a további elhelyezési házirendeket. Az elhelyezési házirendek név szerinti szolgáltatáspéldány alapon vannak konfigurálva. Dinamikusan is frissíthetők.

## <a name="specifying-invalid-domains"></a>Érvénytelen tartományok megadása
**Az InvalidDomain** elhelyezési házirend lehetővé teszi annak megadását, hogy egy adott tartalék tartomány érvénytelen egy adott szolgáltatáshoz. Ez a házirend biztosítja, hogy egy adott szolgáltatás soha ne futjön egy adott területen, például geopolitikai vagy vállalati politikai okokból. Több érvénytelen tartomány is megadható külön házirendeken keresztül.

<center>

![Érvénytelen tartománypélda][Image1]
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
## <a name="specifying-required-domains"></a>Szükséges tartományok megadása
A szükséges tartományelhelyezési házirend megköveteli, hogy a szolgáltatás csak a megadott tartományban található. Több szükséges tartomány külön házirendeken keresztül is megadható.

<center>

![Példa a szükséges tartományra][Image2]
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

## <a name="specifying-a-preferred-domain-for-the-primary-replicas-of-a-stateful-service"></a>Egy állapotalapú szolgáltatás elsődleges replikáinak előnyben részesített tartományának megadása
Az elsődleges elsődleges tartomány megadja azt a tartalék tartományt, amelyben az elsődleges tartományt el helyezi. Az elsődleges ebben a tartományban végzi, ha minden kifogástalan állapotban van. Ha a tartomány vagy az elsődleges replika meghibásodik vagy leáll, az Elsődleges egy másik helyre kerül, ideális esetben ugyanabban a tartományban. Ha ez az új hely nem az előnyben részesített tartományban van, a fürterőforrás-kezelő a lehető leghamarabb visszahelyezi azt az előnyben részesített tartományba. Természetesen ez a beállítás csak akkor van értelme az állapotalapú szolgáltatások. Ez a szabályzat a leghasznosabb az Azure-régiókban vagy több adatközpontban átnyúló fürtökben, de olyan szolgáltatásokkal rendelkezik, amelyek egy adott helyen elhelyezést részesítenek előnyben. Az elsődleges beállítások felhasználókhoz vagy más szolgáltatásokhoz közel tartása segít alacsonyabb késést biztosítani, különösen az olvasások esetében, amelyeket alapértelmezés szerint az elsődleges ek kezelik.

<center>

![Elsődleges elsődleges tartományok és feladatátvétel][Image3]
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

## <a name="requiring-replica-distribution-and-disallowing-packing"></a>Replikaterjesztés megkövetelése és a csomagolás leengedése
Replikák _általában_ között oszlik meg a hiba és a frissítési tartományok, ha a fürt kifogástalan állapotban van. Vannak azonban olyan esetek, amikor egy adott partícióhoz egynél több replika is ideiglenesen egyetlen tartományba csomagolva végződhet. Tegyük fel például, hogy a fürt kilenc csomópontot rendelkezik három tartalék tartományban, fd:/0, fd:/1 és fd:/2. Tegyük fel azt is, hogy a szolgáltatás három replikát rendelkezik. Tegyük fel, hogy az fd:/1 és fd:/2 replikákhoz használt csomópontok leálltak. Normális esetben a fürterőforrás-kezelő inkább más csomópontok ugyanazon tartalék tartományokban. Ebben az esetben tegyük fel, hogy kapacitásproblémák miatt az adott tartományok ban lévő többi csomópont egyike sem volt érvényes. Ha a fürterőforrás-kezelő cserereplikákat hoz létre, akkor az fd:/0 csomópontjait kell választania. Ezzel azonban _olyan_ helyzetet teremt, amelyben a tartalék tartomány megkötése sérül. A replikák csomagolása növeli annak az esélyét, hogy a teljes replikakészlet leállhat vagy elveszhet. 

> [!NOTE]
> A korlátozásokkal és a korlátokkal kapcsolatos általános tudnivalókért tekintse meg [ezt a témakört.](service-fabric-cluster-resource-manager-management-integration.md#constraint-priorities)
>

Ha valaha is látott egy egészségügyi`The Load Balancer has detected a Constraint Violation for this Replica:fabric:/<some service name> Secondary Partition <some partition ID> is violating the Constraint: FaultDomain`üzenetet, mint például " ", akkor már megüt ez a feltétel, vagy valami ilyesmi. Általában csak egy vagy két replikák vannak csomagolva ideiglenesen. Mindaddig, amíg egy adott tartományban kevesebb, mint replikák kvóruma van, biztonságban van. A csomagolás ritka, de megtörténhet, és általában ezek a helyzetek átmenetiek, mivel a csomópontok visszatérnek. Ha a csomópontok nem marad nak le, és a fürt erőforrás-kezelő kell építeni csere, általában vannak más csomópontok érhetők el az ideális tartalék tartományokban.

Egyes számítási feladatok inkább mindig rendelkezik a célreplikák száma, még akkor is, ha kevesebb tartományba vannak csomagolva. Ezek a munkaterhelések teljes egyidejű állandó tartományi hibák ellen fogadnak, és általában helyreállíthatják a helyi állapotot. Más számítási feladatok inkább az állásidőt korábban, mint a kockázat helyességét vagy adatvesztését. A legtöbb éles számítási feladatok futnak több mint három replikák, több mint három tartalék tartományok és számos érvényes csomópontok egy tartalék tartományban. Emiatt az alapértelmezett viselkedés alapértelmezés szerint lehetővé teszi a tartomány csomagolását. Az alapértelmezett viselkedés lehetővé teszi a normál kiegyensúlyozás és feladatátvétel kezelni ezeket a szélsőséges esetekben, még akkor is, ha ez azt jelenti, ideiglenes tartomány csomagolása.

Ha le szeretné tiltani az ilyen csomagolást egy `RequireDomainDistribution` adott számítási feladathoz, megadhatja a szolgáltatás házirendjét. Ha ez a házirend be van állítva, a fürterőforrás-kezelő nem biztosít két replikát ugyanabból a partícióból ugyanabban a hiba- vagy frissítési tartományban.

Kód:

```csharp
ServicePlacementRequireDomainDistributionPolicyDescription distributeDomain = new ServicePlacementRequireDomainDistributionPolicyDescription();
serviceDescription.PlacementPolicies.Add(distributeDomain);
```

PowerShell:

```posh
New-ServiceFabricService -ApplicationName $applicationName -ServiceName $serviceName -ServiceTypeName $serviceTypeName –Stateful -MinReplicaSetSize 3 -TargetReplicaSetSize 3 -PartitionSchemeSingleton -PlacementPolicy @("RequiredDomainDistribution")
```

Most lehetséges lenne ezeket a konfigurációkat használni a földrajzilag nem átnyúló fürt szolgáltatásokhoz? Megteheted, de nincs jó oka. A szükséges, érvénytelen és előnyben részesített tartománykonfigurációkat el kell kerülni, kivéve, ha a forgatókönyvek megkövetelik őket. Nincs értelme, hogy megpróbálja kényszeríteni egy adott számítási feladatok futtatásához egyetlen állványon, vagy inkább a helyi fürt egyes szegmense a másikkal szemben. A különböző hardverkonfigurációkat a tartalék tartományok között kell elosztani, és normál elhelyezési megkötések és csomóponttulajdonságok segítségével kell kezelni.

## <a name="next-steps"></a>További lépések
- A szolgáltatások konfigurálásával kapcsolatos további [tudnivalókért](service-fabric-cluster-resource-manager-configure-services.md)

[Image1]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-invalid-placement-domain.png
[Image2]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-required-placement-domain.png
[Image3]:./media/service-fabric-cluster-resource-manager-advanced-placement-rules-placement-policies/cluster-preferred-primary-domain.png
