---
title: A Reliable Actors felügyeleti állapot |} A Microsoft Docs
description: Útmutatás a Reliable Actors állapot felügyelt, megőrzött és magas rendelkezésre állás érdekében replikálva.
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
ms.openlocfilehash: 77fa369a3085a3d11d5bf03406b4ddb885a24009
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "57847399"
---
# <a name="reliable-actors-state-management"></a>Reliable actors – állapotkezelés
Reliable Actors egyszálas objektumok, amelyek is magába foglalja, logikai és a állapotban. Szolgáltatásokat Reliable actors futtatni, mert ezek is-állapot karbantartásához megbízhatóan azonos adatmegőrzés és replikációs mechanizmusok vonatkoznak. Ezzel a módszerrel actors ne veszítse el állapotuk Újraaktiválási szemétgyűjtés után, vagy ha áthelyezett erőforrás terheléselosztási vagy a frissítések miatt a fürtben található csomópontok között, a hibák után.

## <a name="state-persistence-and-replication"></a>Állapot megőrzését és a replikáció
Az összes Reliable Actors számítanak *állapotalapú* mivel minden egyes színész példány képez le egy egyedi azonosítót. Ez azt jelenti, hogy ugyanazt az aktor Azonosítót ismételt hívások aktor ugyanazon legyenek irányítva. Állapot nélküli rendszerben ezzel szemben az ügyfél hívásai nem biztos, hogy ugyanarra a kiszolgálóra irányítva minden alkalommal. Ebből kifolyólag aktorszolgáltatások a mindig állapotalapú szolgáltatások.

Annak ellenére, hogy az aktorok minősülnek állapotalapú, amely nem jelenti azt, hogy azok állapot megbízhatóan kell tárolnia. Actors használhatja állapot megőrzését szintjét, és a replikáció a tárhellyel kapcsolatos követelmények alapján az adatokat:

* **Megőrzött állapot**: Állapot a rendszer megőrzi a lemezen, és három vagy több replikát a rendszer replikálja. Megőrzött állapota a legtöbb tartós állapota beállítást, ahol állapot megőrizheti a teljes fürt szolgáltatáskimaradás keresztül.
* **Ideiglenes állapot**: Állapot három vagy több replikával replikálni, és csak a memóriában tárolja. Ideiglenes állapot Csomóponthiba és aktor meghibásodása ellen, és a frissítések és az erőforrás terheléselosztási során rugalmasságot biztosít. Azonban állapota nincs megőrizve a lemezre. Így az összes replika egyszerre is elvesznek, ha az állapot elvész is.
* **Nem megőrzött állapot**: Állapot nem replikált vagy lemez, csak akkor használnak, amelyeket nem kell megbízhatóan-állapot karbantartásához actors írt.

Adatmegőrzés minden szintje egyszerűen egy másik *riasztásiállapot-szolgáltató* és *replikációs* a szolgáltatás konfigurációját. Állapot írt e lemezt az állapot-szolgáltató--egy megbízható szolgáltatás, amely tárolja az állapot az összetevőt függ. Replikációs attól függ, hány replikák szolgáltatás van telepítve. Akárcsak a Reliable Services, a szolgáltató és a replika száma is könnyen állítható be kézzel. Az actors keretrendszerben biztosítja, mint egy attribútumot, ha egy szereplő használt, egy alapértelmezett állapotszolgáltató automatikusan kiválasztja, és automatikusan létrehozza a replika száma beállításait, hogy a valamelyik ezek három perzisztenciabeállítások. Származtatott osztály nem öröklik a StatePersistence attribútum, minden egyes szereplőtípus StatePersistence kellően kell megadnia.

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
A beállítás, amely tárolja az adatokat lemezen, és automatikusan a szolgáltatások replika száma 3 állapota szolgáltató használ.

### <a name="volatile-state"></a>Ideiglenes állapot
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
Ez a beállítás a memória-csak állapotszolgáltató használja, és beállítja a replika száma 3.

### <a name="no-persisted-state"></a>Nem megőrzött állapot
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
Ez a beállítás a memória-csak állapotszolgáltató használ, és beállítja a replika száma 1-re.

### <a name="defaults-and-generated-settings"></a>Alapértelmezett beállítások és a létrehozott beállítások
Ha a a `StatePersistence` attribútum, egy állapotszolgáltató automatikusan ki van jelölve, futásidőben az aktor szolgáltatás elindulásakor. A replika száma azonban állítja be a fordítás során a Visual Studio aktor build tools. Nástroje sestavení Pro automatikusan létrehozhat egy *alapértelmezett szolgáltatást* ApplicationManifest.xml az aktorszolgáltatás. Paraméterek jön létre **minimális replikakészlet méretét** és **cél replikakészlet méretét**.

Manuálisan módosíthatja ezeket a paramétereket. Azonban minden alkalommal, amikor a `StatePersistence` attribútuma megváltozik, a paraméterek értékre van állítva az alapértelmezett replika készlet mérete a kiválasztott `StatePersistence` attribútum, felülírva a korábbi értéket. Más szóval a ServiceManifest.xml a megadott értékek *csak* módosítása összeállítás során felül a `StatePersistence` attribútum értéke.

```xml
<ApplicationManifest xmlns:xsd="https://www.w3.org/2001/XMLSchema" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="Application12Type" ApplicationTypeVersion="1.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
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

## <a name="state-manager"></a>Állapotkezelő
Minden egyes színész példány rendelkezik a saját állapotkezelője: egy szótárt-szerű adatszerkezet, amely megbízhatóan tárolja az kulcs-érték pár. Az állapot-kezelő egy állapotszolgáltató burkolója. Függetlenül attól, amely adatmegőrzési beállítást használja adatok tárolására használható. Nem biztosít bármely garantálja, hogy egy futó aktorszolgáltatás módosítható-e egy ideiglenes (a memória-csak) állapot beállításából származó adatok megőrzése működés közbeni frissítésének keresztül megőrzött állapot beállítását. Azonban célszerű lehet módosítani a futó szolgáltatás száma.

Állapotkezelő kulcsok karakterláncokat kell lennie. Értékek általános és állhat bármilyen, beleértve az egyéni típusokat. Az állapot Managerben tárolt értékek kell lennie adategyezményben szerializálható, mert más csomópontokon történő, hálózaton keresztüli replikálás során előfordulhat, hogy továbbítani, és a lemez, egy szereplő állapot megőrzése beállítás függően előfordulhat, hogy lehet írni.

Az állapot manager állapot, a megbízható szótárban található hasonló kezelése közös szótár metódusokat tárja fel.

Aktorok állapotának felügyelete példákért olvassa el a [hozzáférés, mentse, és távolítsa el a Reliable Actors állapot](service-fabric-reliable-actors-access-save-remove-state.md).

## <a name="best-practices"></a>Ajánlott eljárások
Íme, néhány ajánlott eljárások és hibaelhárítási tippek az aktorok állapotának kezeléséhez.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Győződjön meg arról, az aktorok állapotának, amennyire csak lehetséges, részletes
Ez fontos a teljesítmény és az alkalmazás erőforrás-használatát. Bármely, a "nevű állapota" az aktor írási/frissítés történik, az egész érték a "nevesített állapot" megfelelő szerializált, és a másodlagos replikákat a hálózaton keresztül küldött.  A másodlagos példány hozható létre írja a helyi lemez és az elsődleges replika vissza választ. Az elsődleges nyugtázás a másodlagos replikák kvórum kap, amikor az állapot ír a helyi lemezre. Tegyük fel, hogy az érték egy osztály, amely 20 tagok és a méret 1 MB. Akkor is, ha valamely osztály, amely csak módosított méret 1 KB-os, teljes mentést a szerializálási és a hálózati és lemez írási költséget kellene fizetnie, a teljes 1 MB. Ehhez hasonlóan egy gyűjtemény (például egy lista, tömböt vagy szótár) érték esetén kell fizetnie a költségeket a teljes gyűjteményt még akkor is, ha módosítja annak tagjainak egyike. Az aktor osztálya StateManager adapterének van egy szótárhoz hasonlóan. Az adatok struktúrája aktorállapot felett tento slovník jelölő mindig meg kell modellezniük.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>Az aktor életciklus-megfelelően kezelése
Egyértelmű házirend állapota az egyes partíciók egy aktor szolgáltatás méretének kezelése kell rendelkeznie. Az aktorszolgáltatás kell actors rögzített számú rendelkezik, és használja fel őket a lehető legnagyobb mértékben. Ha folyamatosan új actors, törölnie kell azokat azok munkahelyi befejezése után. Az actors keretrendszerben minden egyes színész, hogy létezik néhány metaadatokat tárol. Az aktor az állam törlése nem távolítja el, hogy az aktor metaadatait. Törölnie kell az aktor (lásd: [aktorok és állapotuk törlése](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)) eltávolítja az összes adatot készül azt tárolja a rendszer. Egy további ellenőrzést, mint az aktorszolgáltatás kell lekérdezése (lásd: [aktorok enumerálása](service-fabric-reliable-actors-enumerate.md)) egy while egyszer, hogy a várt tartományon belül vannak actors száma.
 
Ha valaha jelenik meg, hogy az Aktorszolgáltatás adatbázis mérete meghaladja a várt növekszik, győződjön meg arról, hogy az előző útmutatást követi. Ha követi ezeket az irányelveket, és továbbra is adatbázis fájl mérete probléma merül fel, akkor [hozzon létre egy támogatási jegyet](service-fabric-support.md) kaphat segítséget a termék csapatával.

## <a name="next-steps"></a>További lépések

Reliable Actors tárolt, az állapot szerializálni kell a lemezre írt és magas rendelkezésre állású replikált előtt. Tudjon meg többet [Aktor típusszerializáció](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

Ezután tudjon meg többet [Actors diagnosztizálása és teljesítményfigyelése](service-fabric-reliable-actors-diagnostics.md).
