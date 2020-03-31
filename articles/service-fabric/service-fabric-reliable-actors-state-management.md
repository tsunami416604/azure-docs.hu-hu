---
title: Megbízható szereplők állami menedzsment
description: Azt ismerteti, hogyan megbízható szereplők állapota felügyelt, megőrzött és replikált magas rendelkezésre állás érdekében.
author: vturecek
ms.topic: conceptual
ms.date: 11/02/2017
ms.author: vturecek
ms.openlocfilehash: 9962d4333e458243670d1005ad2ccfbc0bb7c92a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75348922"
---
# <a name="reliable-actors-state-management"></a>Megbízható szereplők állami menedzsment
A Reliable Actors egyszálas objektumok, amelyek logikai és állapot-beágyazási. Mivel a szereplők megbízható szolgáltatásokon futnak, megbízhatóan karbantarthatják az állapotot ugyanazon adatmegőrzési és replikációs mechanizmusok használatával. Így a szereplők nem veszítik el az állapotukat a hibák után, a szemétgyűjtés után újraaktiváláskor, vagy amikor erőforrás-egyensúly vagy frissítés miatt egy fürt csomópontjai között kerülnek áthelyezésre.

## <a name="state-persistence-and-replication"></a>Állapotmegőrzés és replikáció
Minden megbízható szereplők *állapotalapúnak minősülnek,* mert minden aktor példány leképezi egy egyedi azonosítót. Ez azt jelenti, hogy az azonos aktorazonosítóhoz érkező ismételt hívások ugyanarra az aktorpéldányra vannak irányítva. Egy állapotmentes rendszerben ezzel szemben az ügyfélhívások nem garantáltak, hogy minden alkalommal ugyanarra a kiszolgálóra irányíthatók. Ebből az okból az aktor szolgáltatások mindig állapotalapú szolgáltatások.

Annak ellenére, hogy a szereplők állapotalapúnak minősülnek, ez nem jelenti azt, hogy megbízhatóan kell tárolniuk az állapotot. A szereplők az adattárolási követelményeik alapján választhatják ki az állapotmegőrzési és replikációs szintet:

* **Megőrzött állapot**: Az állapot lemezre marad, és három vagy több replikára replikálódik. A megőrzött állapot a legtartósabb állapottárolási lehetőség, ahol az állapot teljes fürtkimaradás on keresztül is megmaradhat.
* **Felejtő állapot**: Az állapot három vagy több replikára replikálódik, és csak a memóriában marad. Az illékony állapot rugalmasságot biztosít a csomópont-meghibásodás és az aktor meghibásodása, valamint a frissítések és az erőforrás-kiegyenlítés során. Az állapot azonban nem marad meg a lemezen. Tehát, ha az összes replikák elvesznek egyszerre, az állam is elveszett.
* **Nincs megőrzött állapot:** Az állapot nem replikálódik vagy nem íródik lemezre, csak olyan szereplőkhöz használható, amelyeknek nem kell megbízhatóan fenntartaniuk az állapotot.

A perzisztencia minden szintje egyszerűen egy másik *állapotszolgáltató* és a szolgáltatás *replikációs* konfigurációja. Az, hogy az állapot lemezre van-e írva, az állapotszolgáltatótól függ – az állapotot tároló megbízható szolgáltatás összetevőjéről. A replikáció attól függ, hogy egy szolgáltatás hány replikával van telepítve. A Megbízható szolgáltatásokhoz, az állapotszolgáltató és a replikaszám is könnyen beállítható manuálisan. Az aktor keretrendszer biztosít egy attribútumot, amely egy aktoron használva automatikusan kiválaszt egy alapértelmezett állapotszolgáltatót, és automatikusan létrehozza a replikaszám beállításait a három adatmegőrzési beállítás egyikének eléréséhez. Az StatePersistence attribútum nem öröklődik a származtatott osztálytól, minden aktor típusnak meg kell adnia az statePersistence szintjét.

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
Ez a beállítás olyan állapotszolgáltatót használ, amely adatokat tárol a lemezen, és automatikusan 3-ra állítja a szolgáltatásreplika számát.

### <a name="volatile-state"></a>Változékony állapot
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
Ez a beállítás csak a memóriában lévő állapotszolgáltatót használja, és a replikaszámot 3-ra állítja.

### <a name="no-persisted-state"></a>Nincs megőrzött állapot
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
Ez a beállítás csak a memóriában lévő állapotszolgáltatót használja, és a replikaszámot 1-re állítja.

### <a name="defaults-and-generated-settings"></a>Alapértelmezések és létrehozott beállítások
Az attribútum használatakor `StatePersistence` az állapotszolgáltató automatikusan kiválasztódik az Ön számára futásidőben, amikor az aktor szolgáltatás elindul. A replikaszámát azonban a Visual Studio aktora készítő eszközei fordításkor állítják be. A buildeszközök automatikusan létrehoznak egy *alapértelmezett szolgáltatást* az ApplicationManifest.xml fájlban az aktorszolgáltatáshoz. Paraméterek jönnek létre a **min replikakészlet méretére** és **a célreplikakészlet méretére.**

Ezeket a paramétereket manuálisan is módosíthatja. De minden `StatePersistence` alkalommal, amikor az attribútum megváltozik, a paraméterek a kijelölt `StatePersistence` attribútum alapértelmezett replikakészlet-méretértékére vannak beállítva, felülírva a korábbi értékeket. Más szóval a ServiceManifest.xml fájlban megadott értékek *csak* felülírhatók a `StatePersistence` létrehozáskor, amikor módosítja az attribútum értékét.

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

## <a name="state-manager"></a>Államkezelő
Minden aktorpéldánynak saját állapotkezelője van: egy szótárszerű adatstruktúra, amely megbízhatóan tárolja a kulcs-/értékpárokat. Az állapotkezelő egy állami szolgáltató körül van. Használhatja az adatok tárolására, függetlenül attól, hogy melyik adatmegőrzési beállítást használja. Nem nyújt garanciát arra, hogy a futó aktorszolgáltatás módosítható egy felejtő (csak a memóriában lévő) állapotbeállításról egy működés közbeni frissítéssel megőrzött állapotbeállításra, miközben megőrzi az adatokat. A futó szolgáltatás replikaszámának módosítása azonban módosítható.

Az állapotkezelő kulcsoknak karakterláncoknak kell lenniük. Az értékek általánosak, és bármilyen típusúak lehetnek, beleértve az egyéni típusokat is. Az állapotkezelőben tárolt értékeknek szerializálhatónak kell lenniük, mert a replikáció során a hálózaton keresztül más csomópontokra továbbíthatók, és az aktor állapotmegőrzési beállításától függően lemezre írhatók.

Az állapotkezelő a Megbízható szótárban találhatóhoz hasonló általános szótári módszereket tár fel az állapot kezeléséhez.

Az aktorállapot kezelésének példáihoz olvassa el [az Access, a Mentés és a Reliable Actors állapot eltávolítását.](service-fabric-reliable-actors-access-save-remove-state.md)

## <a name="best-practices"></a>Ajánlott eljárások
Íme néhány javasolt gyakorlat és hibaelhárítási tippek az aktor állapotának kezeléséhez.

### <a name="make-the-actor-state-as-granular-as-possible"></a>Az aktor állapotának a lehető legszemcsésebbé alakítása
Ez kritikus fontosságú az alkalmazás teljesítmény- és erőforrás-használata szempontjából. Ha egy aktor "elnevezett állapotára" van írási/frissítési lehetőség, az adott "elnevezett állapotnak" megfelelő teljes érték szerializálódik, és a hálózaton keresztül elküldi a másodlagos replikáknak.  A másodlagos írási helyi lemezre, és válaszoljon vissza az elsődleges replika. Amikor az elsődleges kap nyugtázást a másodlagos replikák kvórumától, az állapotot a helyi lemezre írja. Tegyük fel például, hogy az érték egy 20 tagú és 1 MB méretű osztály. Még akkor is, ha csak az egyik osztálytagot módosította, amely 1 KB méretű, végül megkell fizetnie a szerializálás és a hálózat és a lemez írási költségeit a teljes 1 MB-ra. Hasonlóképpen, ha az érték egy gyűjtemény (például egy lista, tömb vagy szótár), akkor fizeti a teljes gyűjtemény költségét, még akkor is, ha módosítja az egyik tagja. Az aktorosztály StateManager felülete olyan, mint egy szótár. Mindig modellezze az aktorállapotot képviselő adatstruktúrát a szótár tetején.
 
### <a name="correctly-manage-the-actors-life-cycle"></a>A szereplő életciklusának helyes kezelése
Egy aktorszolgáltatás minden egyes partícióján az állapot méretének kezelésével kapcsolatban egyértelmű házirendet kell rendelkeznie. Az aktor szolgáltatásnak meghatározott számú szereplővel kell rendelkeznie, és a lehető legnagyobb mértékben újra fel kell használnia őket. Ha folyamatosan új szereplőket hoz létre, törölnie kell őket, miután elvégezték a munkájukat. Az aktor keretrendszer tárolja az egyes szereplő, amely létezik néhány metaadatot. Az aktor összes állapotának törlése nem távolítja el az adott szereplő metaadatait. Törölnie kell az aktor (lásd: [a szereplők és állapotuk törlése)](service-fabric-reliable-actors-lifecycle.md#manually-deleting-actors-and-their-state)a rendszerben tárolt összes információ eltávolításához. Egy további ellenőrzés, le kell kérdeznie az aktor szolgáltatás [(lásd: a szereplők felsorolása)](service-fabric-reliable-actors-enumerate.md)egyszer-egyszer, hogy megbizonyosodjon arról, hogy az szereplők száma a várt tartományon belül.
 
Ha valaha is látja, hogy az adatbázis fájl mérete egy aktor szolgáltatás növekszik a várt méretnél nagyobb, győződjön meg arról, hogy az előző irányelveket követi. Ha követi ezeket az irányelveket, és továbbra is adatbázisfájl-méret problémák, meg kell [nyitnia egy támogatási jegyet](service-fabric-support.md) a termék csapat, hogy segítséget.

## <a name="next-steps"></a>További lépések

A Reliable Actors-ben tárolt állapotot szerializálni kell a lemezre írt és a magas rendelkezésre állás érdekében replikálódó állapotot. További információ az [Aktor típusszerializálásáról.](service-fabric-reliable-actors-notes-on-actor-type-serialization.md)

Ezután tudjon meg többet [a szereplő diagnosztikáról és a teljesítményfigyelésről.](service-fabric-reliable-actors-diagnostics.md)
