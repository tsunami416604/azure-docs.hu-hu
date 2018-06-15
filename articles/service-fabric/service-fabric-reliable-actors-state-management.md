---
title: 'Felügyeleti állapot: Reliable Actors |} Microsoft Docs'
description: Útmutatás a Reliable Actors állapot felügyelt, megőrzött és a magas rendelkezésre állás érdekében replikálva.
services: service-fabric
documentationcenter: .net
author: vturecek
manager: timlt
editor: ''
ms.assetid: 37cf466a-5293-44c0-a4e0-037e5d292214
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 3cab4d87eacc7bce17da64cda213086c262179a8
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34206198"
---
# <a name="reliable-actors-state-management"></a>Megbízható szereplője állapotkezelés
Reliable Actors egyszálas objektumok, amellyel skálája ágyazható be mind a programot, és a állapotban. Szereplője Reliable Services futtatni, mert azok is-állapot karbantartásához megbízhatóan azonos adatmegőrzési és replikációs mechanizmusok vonatkoznak. Ezzel a módszerrel szereplője ne veszítse el állapotukra hiba esetén a szemétgyűjtés után vagy a terheléselosztás erőforrás vagy frissítés miatt fürt csomópontjai között áthelyezett Újraaktiválási után.

## <a name="state-persistence-and-replication"></a>Állapot megőrzését és a replikáció
Minden Reliable Actors minősülnek *állapotalapú alkalmazások és szolgáltatások* mivel egyes szereplő példányok leképezve egy egyedi azonosítót. Ez azt jelenti, hogy az azonos szereplő Azonosítóval ismételt hívása szereplő ugyanazon legyenek átirányítva. Állapot nélküli rendszerben ellentétben ügyfél hívások nem garantált, hogy ugyanarra a kiszolgálóra irányítsa minden alkalommal. Emiatt aktorszolgáltatások a mindig állapotalapú szolgáltatások.

Annak ellenére, hogy gyakrabban minősülnek állapotfüggő, hogy nem jelenti azt, azok állapot megbízhatóan kell tárolnia. Szereplője használhatja az állapot megőrzését szintjét, és a replikáció a tárhellyel kapcsolatos követelmények alapján az adatokat:

* **A megőrzött állapot**: állapot megőrizve a lemez- és három vagy több replikákat a rendszer replikálja. Megőrzött állapota a legtöbb tartós állapot tárolási lehetőség, ahol állapota is megőrizni a teljes fürt szolgáltatáskimaradás keresztül.
* **"Volatile" állapot**: állapot három vagy több replikák replikálja, és csak a memóriában tárolja. "Volatile" állapot Csomóponthiba és szereplő hibák ellen, és a frissítések és az erőforrás terheléselosztás során tűrőképességet biztosít. Azonban nem őrzi meg állapot lemezre. Így az összes replika egyszerre is elvesznek, ha az állapot elvész is.
* **Megőrzött állapot nélküli**: állapot nem replikált vagy lemezre, csak a szereplőket, amely nem megbízható állapotban kell írni.

Minden egyes adatmegőrzési szintje egyszerűen egy másik *állapotszolgáltató* és *replikációs* a szolgáltatás konfigurációját. E állapot írni lemezre függ az állapotszolgáltató--tároló állapota működnie az összetevőt. Replikáció működése függ a szolgáltatása hány replikák üzembe helyezéséhez. Csakúgy, mint a Reliable Services, az állapotszolgáltató és a replikaszám egyszerűen megadhatók manuálisan. Aktor keretében biztosít attribútum, amely, amikor egy szereplő használja, a rendszer automatikusan kijelöli egy alapértelmezett állapotszolgáltató, és automatikusan létrehozza a valamelyik három adatmegőrzési beállítások replikaszám beállításait. Származtatott osztály nem örökli a StatePersistence attribútum, szereplő típusonkénti biztosítania kell a StatePersistence szintje.

### <a name="persisted-state"></a>Megőrzött állapot
```csharp
[StatePersistence(StatePersistence.Persisted)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Persisted)
class MyActorImpl  extends FabricActor implements MyActor
{
}
```  
A beállítás a lemezen tárolja az adatokat, és automatikusan beállítja a szolgáltatás replikáinak száma 3 állapota szolgáltató használ.

### <a name="volatile-state"></a>"Volatile" állapota
```csharp
[StatePersistence(StatePersistence.Volatile)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.Volatile)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Ez a beállítás a memória-csak állapotszolgáltató használja, és beállítja a replikáinak száma 3.

### <a name="no-persisted-state"></a>Nincs olyan megőrzött állapot
```csharp
[StatePersistence(StatePersistence.None)]
class MyActor : Actor, IMyActor
{
}
```
```Java
@StatePersistenceAttribute(statePersistence = StatePersistence.None)
class MyActorImpl extends FabricActor implements MyActor
{
}
```
Ez a beállítás a memória-csak állapotszolgáltató használ, és a replikáinak száma 1 értékűre állítja be.

### <a name="defaults-and-generated-settings"></a>Alapértelmezett és a létrehozott beállítások
Ha a a `StatePersistence` attribútum, egy állapotszolgáltató automatikusan ki van jelölve, futási időben a szereplő szolgáltatás elindulásakor. A replika száma azonban állítja be fordítás során a Visual Studio szereplő összeállítási eszközök. A build tools automatikus generálása a *alapértelmezett szolgáltatás* ApplicationManifest.xml szereplő szolgáltatása esetében. Paraméterek létrejönnek az **a replikakészlet minimális mérete** és **cél replika méretének beállítása**.

Ezek a paraméterek manuálisan módosíthatja. Azonban minden alkalommal, amikor a `StatePersistence` attribútum módosul, a paraméterei vannak beállítva az alapértelmezett értékre replika készlet mérete a kiválasztott `StatePersistence` attribútum, felülírja az előző értéket. Más szóval ServiceManifest.xml beállított értékei *csak* felülírását build idő, amikor módosítja a `StatePersistence` attribútum értéke.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <Parameters>
      <Parameter Name="MyActorService_PartitionCount" DefaultValue="10" />
      <Parameter Name="MyActorService_MinReplicaSetSize" DefaultValue="3" />
      <Parameter Name="MyActorService_TargetReplicaSetSize" DefaultValue="3" />
   </Parameters>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MyActorPkg" ServiceManifestVersion="1.0.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MyActorService" GeneratedIdRef="77d965dc-85fb-488c-bd06-c6c1fe29d593|Persisted">
         <StatefulService ServiceTypeName="MyActorServiceType" TargetReplicaSetSize="[MyActorService_TargetReplicaSetSize]" MinReplicaSetSize="[MyActorService_MinReplicaSetSize]">
            <UniformInt64Partition PartitionCount="[MyActorService_PartitionCount]" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatefulService>
      </Service>
   </DefaultServices>
</ApplicationManifest>
```

## <a name="state-manager"></a>Állapot manager
Minden szereplő példány rendelkezik saját állapotkezelője: a szótár-szerű adatszerkezet, amely megbízhatóan tárolja az kulcs/érték párok. Az állapotkezelő a állapotszolgáltató csomagolásának. Függetlenül attól, amely adatmegőrzési beállítást használja adatok tárolására használható. Nem biztosít semmilyen garantálja, hogy egy futó szereplő szolgáltatás módosítható-e egy "volatile" (a memória-csak) állapota beállítást a működés közbeni frissítés során megőrzi az adat a megőrzött állapot beállítását. Azonban célszerű lehet módosítani egy futó szolgáltatással replika száma.

Állapot manager kulcsok karakterláncoknak kell lenniük. Értékek általános és állhat bármilyen, beleértve az egyéni típusok. Az állapotkezelő tárolt értékekkel kell lennie adategyezmény szerializálható, mert előfordulhat, hogy hálózaton át továbbítani a többi csomópontra replikáció során, és előfordulhat, hogy lemezre lehet írni, attól függően, hogy egy szereplő állapota adatmegőrzési beállítást.

Az állapotkezelő közös szótár módszerek állapot, a megbízható szótárban található hasonló kezeléséről mutatja.

Példák a aktorállapot kezelésére, olvassa el a [hozzáférés, mentse, és távolítsa el a Reliable Actors állapot](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Ajánlott eljárások
Az alábbiakban néhány ajánlott eljárásokról és a hibaelhárítási tippek a aktorállapot kezelése.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Ellenőrizze a aktorállapot szerint a lehető részletes
Ez elengedhetetlen a teljesítmény- és erőforrás-kihasználtságának megtekintéséhez az alkalmazás. Ha írási/módosításairól a "nevű állapota" egy szereplő, az egész érték az "elnevezett állapotban" megfelelő szerializált és a másodlagos replikákat a hálózaton keresztül küldött.  A másodlagos adatbázist írják helyi lemezek és a válasz vissza az elsődleges másodpéldány. Ha az elsődleges a nyugtázás a másodlagos replikák másodlagosak kap, az állapot ír a helyi lemezen. Tegyük fel, hogy az érték egy osztályt, amely 20 tagok és a méret 1 MB. Akkor is, ha egy osztály tagja, amely csak módosított méret 1 KB, a befejezési fel a szerializálás és a hálózati és lemez írási műveletek költségét fizető a a teljes 1 MB. Ehhez hasonlóan a gyűjtemény (például egy lista, a tömb vagy a szótár) érték esetén kell fizetnie a költségeket a teljes gyűjteményt akkor is, ha módosítja az tagok egyikét. Az aktor osztály a StateManager felület dictionary hasonlít. Mindig a aktorállapot fölött a szótár képviselő adatszerkezet kell modell.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Az aktor életciklus-megfelelően kezelése
Törölje a jelet házirend minden partíció szereplő szolgáltatási állapot méretét kezelésével kapcsolatos kell rendelkezésre állnia. Az aktor szolgáltatás kell szereplője rögzített száma, és használja fel őket a lehető jelentős. Ha folyamatosan hozzon létre új szereplője, törölnie kell azokat azokat munkájuk befejezése után. Az aktor keretrendszer minden létező szereplő néhány metaadatokat tárol. Egy szereplő összes állapotának törlése nem távolítja el, hogy szereplő metaadatait. Törölni kell a szereplő (lásd: [szereplője és állapotukra](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) az adatok eltávolítása készül azt tárolja a rendszer. Egy további ellenőrzést, meg kell lekérdezni a szereplő szolgáltatást (lásd: [szereplője számbavétele](service-fabric-reliable-actors-platform.md)) egy while egyszer annak biztosítását, hogy a szám szereplője a várt tartományon belül.
 
Ha valaha is látható, hogy szereplő szolgáltatásnak adatbázis mérete meghaladja a várt növekszik, győződjön meg arról, hogy betartják-e az előző útmutatást. Ha követi ezeket az irányelveket, és a fájlok méretéből adódó problémákat adatbázis továbbra is, akkor [támogatási jegy megnyitása](service-fabric-support.md) Ha segítséget szeretne kérni a termék csoporttal.

## <a name="next-steps"></a>További lépések

A Reliable Actors tárolt állapotban kell szerializálni a lemezre írva, és a magas rendelkezésre állású replikált előtt. További információ [szereplő típus szerializálási](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

A következő további információ [szereplő diagnosztika és teljesítményfigyelés](service-fabric-reliable-actors-diagnostics.md).
